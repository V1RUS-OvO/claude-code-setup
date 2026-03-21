---
name: terraform
description: Terraform infrastructure-as-code patterns, module design, state management, security best practices, and common provider configurations (AWS/GCP/Azure/Docker). Use when writing Terraform configs, designing infrastructure modules, managing state, or troubleshooting plan/apply issues.
argument-hint: [resource/module/provider to configure]
allowed-tools: Read, Edit, Write, Bash, Glob, Grep
---

# Terraform / Infrastructure as Code

## 项目结构

### 单环境结构
```
infra/
  main.tf          # 主资源定义
  variables.tf     # 变量声明
  outputs.tf       # 输出值
  versions.tf      # provider 版本锁定
  terraform.tfvars # 变量值（不提交到 git）
  .terraform.lock.hcl  # 依赖锁文件（提交到 git）
```

### 多环境结构（推荐）
```
infra/
  modules/
    vpc/
      main.tf
      variables.tf
      outputs.tf
    eks/
      main.tf
      variables.tf
      outputs.tf
  envs/
    dev/
      main.tf       # 调用模块
      terraform.tfvars
    staging/
      main.tf
      terraform.tfvars
    prod/
      main.tf
      terraform.tfvars
```

## HCL 核心语法

### versions.tf — 版本锁定
```hcl
terraform {
  required_version = ">= 1.6.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
    random = {
      source  = "hashicorp/random"
      version = "~> 3.0"
    }
  }

  # 远程 state（推荐生产环境）
  backend "s3" {
    bucket         = "my-tfstate-bucket"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-locks"
  }
}
```

### variables.tf — 变量声明
```hcl
variable "environment" {
  description = "Deployment environment (dev/staging/prod)"
  type        = string

  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}

variable "instance_count" {
  description = "Number of EC2 instances"
  type        = number
  default     = 1
}

variable "allowed_cidrs" {
  description = "CIDR blocks allowed to access the service"
  type        = list(string)
  default     = []
}

variable "tags" {
  description = "Common resource tags"
  type        = map(string)
  default     = {}
}

# 敏感变量 — 不会出现在 plan 输出中
variable "db_password" {
  description = "Database password"
  type        = string
  sensitive   = true
}
```

### outputs.tf — 输出值
```hcl
output "vpc_id" {
  description = "VPC ID"
  value       = aws_vpc.main.id
}

output "db_endpoint" {
  description = "Database connection endpoint"
  value       = aws_db_instance.main.endpoint
  sensitive   = true  # 不在 CLI 中明文显示
}
```

### locals — 局部变量
```hcl
locals {
  name_prefix = "${var.project}-${var.environment}"
  common_tags = merge(var.tags, {
    Environment = var.environment
    ManagedBy   = "terraform"
    Project     = var.project
  })
}

resource "aws_instance" "web" {
  # ...
  tags = local.common_tags
}
```

## 模块化

### 模块定义
```hcl
# modules/vpc/variables.tf
variable "cidr_block" {
  type    = string
  default = "10.0.0.0/16"
}

# modules/vpc/main.tf
resource "aws_vpc" "this" {
  cidr_block           = var.cidr_block
  enable_dns_hostnames = true
  tags = { Name = var.name }
}

resource "aws_subnet" "public" {
  count             = length(var.availability_zones)
  vpc_id            = aws_vpc.this.id
  cidr_block        = cidrsubnet(var.cidr_block, 8, count.index)
  availability_zone = var.availability_zones[count.index]
}

# modules/vpc/outputs.tf
output "vpc_id"    { value = aws_vpc.this.id }
output "subnet_ids" { value = aws_subnet.public[*].id }
```

### 调用模块
```hcl
module "vpc" {
  source = "../../modules/vpc"
  # 或 Terraform Registry:
  # source  = "terraform-aws-modules/vpc/aws"
  # version = "~> 5.0"

  cidr_block         = "10.0.0.0/16"
  availability_zones = ["us-east-1a", "us-east-1b"]
  name               = local.name_prefix
}

# 使用模块输出
resource "aws_instance" "web" {
  subnet_id = module.vpc.subnet_ids[0]
}
```

## 常用表达式

```hcl
# count — 创建多个同类资源
resource "aws_instance" "web" {
  count         = var.instance_count
  instance_type = "t3.micro"
  tags = { Name = "web-${count.index}" }
}

# for_each — 用 map/set 创建资源（推荐，比 count 稳定）
resource "aws_iam_user" "team" {
  for_each = toset(["alice", "bob", "charlie"])
  name     = each.key
}

# dynamic block — 动态生成嵌套块
resource "aws_security_group" "web" {
  name = "web-sg"

  dynamic "ingress" {
    for_each = var.ingress_rules
    content {
      from_port   = ingress.value.from_port
      to_port     = ingress.value.to_port
      protocol    = ingress.value.protocol
      cidr_blocks = ingress.value.cidr_blocks
    }
  }
}

# 条件表达式
instance_type = var.environment == "prod" ? "t3.large" : "t3.micro"

# for 表达式
output "instance_ids" {
  value = [for i in aws_instance.web : i.id]
}

# 字符串模板
name = "${var.project}-${var.environment}-${random_id.suffix.hex}"
```

## 状态管理

```bash
# 查看当前状态
terraform state list
terraform state show aws_instance.web

# 导入已有资源到 state
terraform import aws_instance.web i-1234567890abcdef0

# 从 state 移除资源（不销毁）
terraform state rm aws_instance.web

# 移动/重命名 state 中的资源
terraform state mv aws_instance.web aws_instance.app

# 刷新 state（与实际基础设施同步）
terraform refresh

# 强制解锁（谨慎使用）
terraform force-unlock LOCK_ID
```

### 远程 State 后端
```hcl
# S3 + DynamoDB 锁（AWS）
backend "s3" {
  bucket         = "my-tf-state"
  key            = "${var.environment}/terraform.tfstate"
  region         = "us-east-1"
  encrypt        = true
  dynamodb_table = "tf-state-locks"
}

# GCS（GCP）
backend "gcs" {
  bucket = "my-tf-state"
  prefix = "terraform/state"
}

# Terraform Cloud
backend "remote" {
  organization = "my-org"
  workspaces { name = "my-workspace" }
}
```

## 工作流

```bash
# 初始化（下载 provider、模块）
terraform init
terraform init -upgrade  # 升级 provider

# 格式化代码
terraform fmt -recursive

# 验证语法
terraform validate

# 查看变更计划
terraform plan
terraform plan -var-file=prod.tfvars
terraform plan -out=tfplan  # 保存计划

# 应用变更
terraform apply
terraform apply tfplan      # 应用保存的计划
terraform apply -auto-approve  # 跳过确认（CI/CD 中用）

# 销毁资源
terraform destroy
terraform destroy -target=aws_instance.web  # 只销毁特定资源

# 只操作特定资源
terraform apply -target=module.vpc
terraform plan -target=aws_instance.web
```

## 安全最佳实践

```hcl
# 1. 永远不要硬编码凭证
# BAD
provider "aws" {
  access_key = "AKIAIOSFODNN7EXAMPLE"
  secret_key = "wJalrXUtnFEMI/K7MDENG"
}
# GOOD — 用环境变量或 IAM role
provider "aws" {
  region = var.aws_region
  # 自动读取 AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY
}

# 2. 敏感输出标记 sensitive
output "password" {
  value     = random_password.db.result
  sensitive = true
}

# 3. S3 bucket 加密和访问控制
resource "aws_s3_bucket_server_side_encryption_configuration" "default" {
  bucket = aws_s3_bucket.main.id
  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "AES256"
    }
  }
}

resource "aws_s3_bucket_public_access_block" "default" {
  bucket                  = aws_s3_bucket.main.id
  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}

# 4. 最小权限 IAM
resource "aws_iam_role_policy" "app" {
  role = aws_iam_role.app.id
  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect   = "Allow"
      Action   = ["s3:GetObject", "s3:PutObject"]
      Resource = "${aws_s3_bucket.main.arn}/*"
    }]
  })
}
```

## .gitignore

```gitignore
# 本地 tfvars（含敏感值）
*.tfvars
!example.tfvars

# Terraform 工作目录
.terraform/
.terraform.tfstate
.terraform.tfstate.backup

# 崩溃日志
crash.log
crash.*.log

# 生成的计划文件
tfplan
*.tfplan

# 覆盖文件
override.tf
override.tf.json
*_override.tf
*_override.tf.json
```

## 常用 Provider 速查

### Docker（本地开发）
```hcl
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 3.0"
    }
  }
}

resource "docker_image" "nginx" {
  name = "nginx:latest"
}

resource "docker_container" "web" {
  name  = "web"
  image = docker_image.nginx.image_id
  ports {
    internal = 80
    external = 8080
  }
}
```

### AWS 常用资源
```hcl
# EC2
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t3.micro"
  subnet_id     = module.vpc.subnet_ids[0]
  tags          = local.common_tags
}

# RDS
resource "aws_db_instance" "main" {
  identifier        = "${local.name_prefix}-db"
  engine            = "postgres"
  engine_version    = "15"
  instance_class    = "db.t3.micro"
  allocated_storage = 20
  db_name           = var.db_name
  username          = var.db_username
  password          = var.db_password
  skip_final_snapshot = var.environment != "prod"
}

# data source — 查询已有资源
data "aws_ami" "ubuntu" {
  most_recent = true
  owners      = ["099720109477"] # Canonical
  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-22.04-amd64-server-*"]
  }
}
```

## 调试技巧

```bash
# 详细日志
export TF_LOG=DEBUG
export TF_LOG_PATH=./terraform.log

# 查看 provider schema
terraform providers schema -json | jq

# 检查特定资源计划
terraform plan -target=aws_instance.web -refresh=false

# 控制台交互调试
terraform console
> var.environment
> cidrsubnet("10.0.0.0/16", 8, 0)
```

## 推荐工具

| 工具 | 用途 |
|------|------|
|
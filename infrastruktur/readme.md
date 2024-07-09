Infrastuktur Server Frontend

![alt text](https://github.com/aureezzhenx/devops3/blob/main/infrastruktur/Untitled%20Diagram.drawio.png)

Menggunakan Terraform sebagai Infrastuktur As Code untuk meng-instalasi instance t2.small ap-southeast-3 di VPC baru dengan security group port SSH hanya bisa diakses oleh IP tertentu (alasan keamanan), melakukan konfigurasi auto scalling Amazon Load Balancer terhadap server frontend dengan max_size 4. Penggunaan load balancer di instance frontend bertujuan untuk menghindari error 505 dikala high traffic dengan automasi melakukan server baru dari sistem load balancer AWS.

```
mkdir terraform-aws-autoscaling-lb-vpc
cd terraform-aws-autoscaling-lb-vpc
touch main.tf
```

main.tf
```
# Provider AWS
provider "aws" {
  region = "ap-southeast-3"
}

# Buat VPC baru
resource "aws_vpc" "frontend_vpc" {
  cidr_block = "10.0.0.0/16"
  enable_dns_support = true
  enable_dns_hostnames = true

  tags = {
    Name = "frontend-vpc"
  }
}

# Subnet di dalam VPC
resource "aws_subnet" "frontend_subnet" {
  vpc_id     = aws_vpc.frontend_vpc.id
  cidr_block = "10.0.1.0/24"
  availability_zone = "ap-southeast-3"  # Ganti dengan availability zone yang diinginkan

  tags = {
    Name = "frontend-subnet"
  }
}

# Security Group untuk SSH hanya dari IP tertentu
resource "aws_security_group" "ssh_access" {
  name        = "ssh_access_sg"
  description = "Allow SSH access from specific IP"
  vpc_id      = aws_vpc.frontend_vpc.id
  
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["<IP_ADDRESS>/32"]  # Ganti <IP_ADDRESS> dengan IP yang diizinkan
  }
  
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

# Launch Configuration
resource "aws_launch_configuration" "frontend" {
  name_prefix          = "frontend-lc"
  image_id             = "ami-0c55b159cbfafe1f0"  # CentOS 7 AMI ID
  instance_type        = "t2.small"
  security_groups      = [aws_security_group.ssh_access.id]
  key_name             = "jouzie"  # Ganti dengan nama key pair yang ada di AWS
  user_data            = <<-EOF
                          #!/bin/bash
                          yum install -y nginx
                          systemctl start nginx
                          systemctl enable nginx
                          EOF
}

# Auto Scaling Group
resource "aws_autoscaling_group" "frontend" {
  desired_capacity     = 2
  max_size             = 4
  min_size             = 1
  launch_configuration = aws_launch_configuration.frontend.id
  vpc_zone_identifier  = [aws_subnet.frontend_subnet.id]
}

# Load Balancer
resource "aws_lb" "frontend" {
  name               = "frontend-lb"
  internal           = false
  load_balancer_type = "application"
  
  security_groups    = [aws_security_group.ssh_access.id]
  subnets            = [aws_subnet.frontend_subnet.id]
  
  enable_deletion_protection = false  # Untuk memungkinkan penghapusan load balancer
  
  tags = {
    Name = "frontend-lb"
  }
}

# Target Group untuk Load Balancer (Port 80)
resource "aws_lb_target_group" "http_target_group" {
  name     = "http-tg"
  port     = 80
  protocol = "HTTP"
  vpc_id   = aws_vpc.frontend_vpc.id
  
  health_check {
    port        = 80
    protocol    = "HTTP"
    timeout     = 10
    interval    = 30
    healthy_threshold = 3
    unhealthy_threshold = 3
  }
}

# Target Group HTTPS (Port 443)
resource "aws_lb_target_group" "https_target_group" {
  name     = "https-tg"
  port     = 443
  protocol = "HTTPS"
  vpc_id   = "aws_vpc.frontend_vpc.id" 
  
  health_check {
    port        = 443
    protocol    = "HTTPS"
    timeout     = 10
    interval    = 30
    healthy_threshold = 3
    unhealthy_threshold = 3
  }
}

# Listener HTTP (Port 80)
resource "aws_lb_listener" "http_listener" {
  load_balancer_arn = aws_lb.http.arn
  port              = 80
  protocol          = "HTTP"
  
  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.http_target_group.arn
  }

# Listener HTTPS (Port 443)
resource "aws_lb_listener" "https_listener" {
  load_balancer_arn = aws_lb.example.arn
  port              = 443
  protocol          = "HTTPS"
  ssl_policy        = "ELBSecurityPolicy-2016-08"
  
  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.https_target_group.arn
  }
}
```


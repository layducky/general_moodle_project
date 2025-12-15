# Moodle AI Platform - Tích hợp AI vào Hệ thống Quản lý Học tập

## Tổng quan Dự án

Dự án xây dựng nền tảng Moodle LMS tích hợp AI, triển khai trên AWS với kiến trúc microservices, CI/CD pipeline tự động và hệ thống giám sát metrics.

![Architecture Diagram](Moodle_LMS.drawio.png)

## Kiến trúc Hệ thống

### **CI/CD Pipeline**
- **Dev Computer** → Push code lên **GitHub**
- **GitHub Actions** tự động build và tạo Docker images
- **Docker Hub** lưu trữ images
- Server tự động pull images mới và rebuild containers

### **Server 1: AWS Moodle Server**
- **Moodle App** (Port 80/443): Ứng dụng LMS chính, tích hợp AI features
- **MariaDB** (Port 3306): Database lưu trữ dữ liệu
- Moodle gửi request đến Kong Gateway qua endpoint `/api/generate`

### **Server 2: AWS Gateway & Metric Server**
- **Kong AI API Gateway** (Port 8000/8001): 
  - Route và quản lý requests từ Moodle đến Ollama AI
  - Xử lý authentication, rate limiting, logging
  - Database: PostgreSQL
- **Prometheus** (Port 9090): Thu thập và lưu trữ metrics
- **Grafana** (Port 3000): Dashboard trực quan hóa metrics và monitoring

### **Server 3: AWS Ollama AI Server**
- **Ollama AI** (Port 11434): Chạy AI models (qwen2.5-coder:1.5b, v.v.)
- **Ollama Exporter** (Port 9178): Export metrics để Prometheus thu thập

## Repositories

### 1. [KongGateway](https://github.com/layducky/KongGateway)
**Chức năng**: Khởi động Kong AI API Gateway & Ollama AI Server

**Bao gồm**:
- Kong Gateway với AI plugins
- Ollama AI Server và models
- Prometheus & Grafana monitoring
- Docker Compose configurations

### 2. [moodle-k8s-project](https://github.com/nguyendangcuong201004/moodle-k8s-project)
**Chức năng**: Source code và Docker packaging của Moodle

**Bao gồm**:
- Source code Moodle tích hợp AI features
- Dockerfile và docker-compose
- CI/CD workflows với GitHub Actions
- MariaDB configuration

### 3. [moodle-k8s-infra](https://github.com/nguyendangcuong201004/moodle-k8s-infra)
**Chức năng**: Infrastructure as Code (IaC) với Terraform & Kubernetes

**Bao gồm**:
- Terraform scripts provision AWS infrastructure
- Kubernetes manifests (deployments, services)
- Network và security configurations
- AWS resources: VPC, EKS, RDS, Load Balancers

## Luồng Hoạt động

### Development & CI/CD Flow
```
Developer → Push Code → GitHub → GitHub Actions Build
                                         ↓
                                  Docker Images
                                         ↓
                                   Docker Hub
                                         ↓
                            Pull & Deploy to AWS Servers
```

### AI Request Flow
```
User → Moodle App → POST /api/generate
                          ↓
              Kong Gateway (8000)
                          ↓
         Route & Authentication
                          ↓
        Ollama Exporter (9178)
                          ↓
          Ollama AI (11434)
                          ↓
      Process with AI Model
                          ↓
        JSON Response ← ← ← ← ← User
```

### Monitoring Flow
```
Kong Gateway → Metrics → Prometheus (9090) → Query Data → Grafana (3000)
                              ↑
Ollama Exporter → Metrics → ←
```

## Công nghệ Sử dụng

- **Frontend/Backend**: Moodle LMS (PHP)
- **Database**: MariaDB
- **API Gateway**: Kong Gateway
- **AI Engine**: Ollama (qwen2.5-coder:1.5b)
- **Monitoring**: Prometheus + Grafana
- **Container**: Docker & Docker Compose
- **Orchestration**: Kubernetes (K8s)
- **Infrastructure**: Terraform
- **Cloud Provider**: AWS (VPC, EKS, EC2, RDS)
- **CI/CD**: GitHub Actions

---

**Dự án này thể hiện**: Kiến trúc microservices, DevOps practices (CI/CD, IaC), Cloud deployment, AI integration, và Monitoring & Observability.
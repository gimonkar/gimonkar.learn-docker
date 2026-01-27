# Host simple SpringBoot app on ECS using ALB

Below is a **cost-efficient, step-by-step** path to run your Spring Boot container on **ECS Fargate behind an Application Load Balancer (ALB)**, using the image you already pushed to **ECR**.

I’ll give you:

1. the **architecture + cost notes** (so you don’t accidentally spend big),
2. **console steps** (easiest for learning), and
3. an **optional Terraform infra template** (repeatable).
---

## 0) Before you start (quick app/container checklist)
### Your app needs to listen on a known port
Most Spring Boot apps listen on **8080** by default. Confirm:

- `server.port=8080`  (optional; default is 8080)
- Your endpoints:
    - `GET /api/health`  → returns `health is good` 
    - `GET /api/greet`  → returns greeting

### Your container must expose that port
In your Dockerfile, ensure something like:

```dockerfile
EXPOSE 8080
```
Health check path we’ll use on ALB: `**/api/health**` 

---

## 1) Cost-efficient architecture (important)
### What costs money here
- **ALB is usually the biggest fixed cost** (hourly + LCU).
- **Fargate tasks** cost based on vCPU/Memory time.
- **CloudWatch logs** can cost (small at low volume).
### Cost-saving choices (that still meet “ALB + Fargate”)
- **One service, desired count = 1**
- **Smallest Fargate size**: `0.25 vCPU / 0.5 GB`  (good for hello-world)
- **No NAT Gateway** (NAT can be costly):
    - Put tasks in **public subnets**
    - Enable **Assign public IP = ON**

- **HTTP only (port 80)** for learning (skip ACM/HTTPS for now)
- Optional: **Fargate Spot** (cheaper, but can be interrupted—fine for learning)
---

## 2) Infrastructure you will create
### Networking (VPC)
- 1 VPC
- 2 **public subnets** (different AZs)
- Internet Gateway + public route table
### Security groups
- **ALB SG**: inbound `80`  from `0.0.0.0/0` , outbound all
- **ECS Task SG**: inbound `8080`  from **ALB SG only**, outbound all
### ECS
- Cluster (Fargate)
- Task Definition:
    - Image: your ECR image URI
    - Container port: 8080
    - Logs to CloudWatch

- Service:
    - Desired tasks: 1
    - Attach to ALB Target Group
    - Health check path: `/api/health` 

---

## 3) Step-by-step using AWS Console (recommended for learning)
### Step A — Create VPC (fast way)
1. Go to **VPC → Create VPC**
2. Choose **VPC and more**
3. Set:
    - **Number of AZs:** 2
    - **Public subnets:** 2
    - **Private subnets:** 0 ✅ (cost saving)
    - **NAT gateways:** None ✅ (cost saving)

4. Create.
You now have a VPC with 2 public subnets + internet routing.

---

### Step B — Create Security Groups
Go to **EC2 → Security Groups**.

#### 1) ALB Security Group (e.g., `sg-alb-greeting`)
Inbound rules:

- HTTP `80`  from `0.0.0.0/0` 
Outbound:

- Allow all (default)
#### 2) ECS Task Security Group (e.g., `sg-ecs-greeting`)
Inbound rules:

- Custom TCP `8080`  **source = sg-alb-greeting** (select SG, not CIDR)
Outbound:

- Allow all (default)
---

### Step C — Create the Application Load Balancer
Go to **EC2 → Load Balancers → Create Load Balancer → Application Load Balancer**.

1. Name: `alb-greeting` 
2. Scheme: **Internet-facing**
3. IP address type: IPv4
4. Listener: **HTTP : 80**
5. VPC: select the VPC you created
6. Subnets: select **both public subnets**
7. Security group: select `sg-alb-greeting` 
#### Target Group
Create a new target group:

- Type: **IP**
- Name: `tg-greeting` 
- Protocol: HTTP
- Port: **8080**
- Health check:
    - Path: `**/api/health**` 
    - Success codes: `200` 

- Create target group.
Back in the ALB creation:

- Listener rule: forward to `tg-greeting` 
- Create ALB.
---

### Step D — Create ECS Cluster
Go to **ECS → Clusters → Create cluster**

- Cluster name: `greeting-cluster` 
- Infrastructure: **AWS Fargate (serverless)**
- Create.
---

### Step E — Create Task Definition
Go to **ECS → Task definitions → Create new task definition**

1. Launch type: **Fargate**
2. Task definition family: `greeting-task` 
3. CPU/Memory: **0.25 vCPU + 0.5 GB** ✅
4. Task role: none (for now)
5. Execution role:
    - Create/choose **ecsTaskExecutionRole** (AWS wizard usually creates this)
    - This role allows pulling from ECR + writing logs

#### Container definition
- Name: `greeting` 
- Image URI: your **ECR image** (e.g. `xxxxxxxx.dkr.ecr.ap-south-1.amazonaws.com/greeting:latest` )
- Port mappings: **8080 (TCP)**
- Log configuration:
    - Log driver: `awslogs` 
    - Create log group: `/ecs/greeting` 
    - Region: your region
    - Stream prefix: `ecs` 

Create task definition.

---

### Step F — Create ECS Service (attach to ALB)
Go to **ECS → Clusters → greeting-cluster → Services → Create**

1. Compute options: **Launch type = Fargate**
2. Task definition: `greeting-task` 
3. Service name: `greeting-service` 
4. Desired tasks: **1** ✅
#### Networking
- VPC: your VPC
- Subnets: select **both public subnets**
- Security group: select `sg-ecs-greeting` 
- **Auto-assign public IP: ON** ✅ (prevents needing NAT)
#### Load balancing
- Turn on **Application Load Balancer**
- Select your ALB: `alb-greeting` 
- Listener: `80:HTTP` 
- Target group: select `tg-greeting` 
- Container: `greeting:8080` 
#### Health check grace period
- Set: **60 seconds** (safe for Spring Boot)
Create service.

---

### Step G — Test it
1. Go to **EC2 → Load Balancers → alb-greeting**
2. Copy **DNS name**
3. Test:
- `http://<ALB-DNS>/api/health` 
- `http://<ALB-DNS>/api/greet` 
If health check fails:

- Confirm container listens on 8080
- Confirm endpoint path is `/api/health` 
- Check ECS task logs in CloudWatch (`/ecs/greeting` )
- Check security groups (ALB SG open 80, task SG allows 8080 from ALB SG)



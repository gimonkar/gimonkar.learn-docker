# gimonkar.learn-docker
This repo having the documentation basics of docker

# Docker Notes

# 🧭 Docker Learning Roadmap (DevOps + Java Focus)
## PHASE 0 – Before Docker (Very Important)
## PHASE 1 – Docker Fundamentals (Beginner)
## PHASE 2 – Docker for Java & Spring Boot
## PHASE 3 – Docker Compose (Real Projects)
## PHASE 4 – Docker Networking & Volumes
## PHASE 5 – Docker in DevOps (CI/CD, Best Practices)
## PHASE 6 – Advanced Docker (Security, Optimization, Intern-level Interview Prep)


# 🔹 PHASE 0: Why Docker Exists (Foundation)
### ❌ Problem before Docker
Imagine this:

- Your Spring Boot app works on **your laptop**
- It fails on **QA server**
- Ops says: “Java version mismatch”
 “Port already in use”
 “Missing dependency” 
This is called:

>  **“It works on my machine” problem** 

### ✅ Docker Solution
Docker **packages everything together**:

- Application code
- Java version
- Dependencies
- OS-level configs
👉 Result:
 **Run anywhere, same behavior**



# 🔹 PHASE 1: Core Docker Concepts (Beginner)
## 1️⃣ What is Docker?
Docker is a **containerization platform**.

### Container = ?
A **container** is:

- A lightweight, isolated environment
- Runs your app with its dependencies
💡 Think:

- **VM** → Heavy, full OS
- **Container** → Lightweight, shares OS kernel
### VM vs Docker (Interview Gold ⭐)
| VM | Docker |
| ----- | ----- |
| Heavy | Lightweight |
| Full OS | Shares host OS |
| Slow startup | Fast startup |
| GBs | MBs |
---

## 2️⃣ Docker Architecture (Very Important)
Docker uses **client-server architecture**.

```
You (Docker CLI)
     |
Docker Daemon (dockerd)
     |
Images → Containers
```
### Components:
- **Docker Client** → `docker run` , `docker build` 
- **Docker Daemon** → Does actual work
- **Docker Image** → Blueprint
- **Docker Container** → Running instance
---

## 3️⃣ Image vs Container (Must Be Clear)
### 🧱 Image
- Read-only template
- Like **Java class**
### 🏃 Container
- Running instance of image
- Like **Java object**
📌 Example:

```bash
docker run nginx
```
- `nginx`  → Image
- Running nginx → Container
# 🔹 PHASE 2: Docker Basics (Hands-on)
## 5️⃣ Your First Docker Commands
### Check Docker version
```bash
docker --version
```
### Pull an image
```bash
docker pull nginx
```
### Run a container
```bash
docker run nginx
```
### Run in background
```bash
docker run -d nginx
```
### List running containers
```bash
docker ps
```
### List all containers
```bash
docker ps -a
```
---

## 6️⃣ Port Mapping (Very Important)
By default, container ports are **internal**.

```bash
docker run -d -p 8080:80 nginx
```
Meaning:

- Host port `8080` 
- Container port `80` 
👉 Access via browser:

```
http://localhost:8080
```
# 🔹 PHASE 3: Docker for Java & Spring Boot (Core DevOps Skill)


- I have created the spring boot project and created the get request function that returns the string that "Welcome to the get function" 
- Then i have created the jar file : `docker1-0.0.1-SNAPSHOT.jar` 
![image.png](https://eraser.imgix.net/workspaces/Jq1069p59Rl7sIj4mDPD/N2lSj4izkAhshqbeBgwRahIEQAg1/image_-wHCZSqQCmlnQZSWs1pV1.png?ixlib=js-3.8.0 "image.png")



## 7️⃣ Dockerizing a Spring Boot App
### Typical Spring Boot App
```bash
java -jar app.jar
```
### Dockerfile (Beginner Version)
```dockerfile
FROM openjdk:17
WORKDIR /app
COPY target/app.jar app.jar
EXPOSE 8080
CMD ["java", "-jar", "app.jar"]
```
### Build image
```bash
docker build -t springboot-app .
```
### Run container
```bash
docker run -d -p 8080:8080 springboot-app
```
![image.png](https://eraser.imgix.net/workspaces/Jq1069p59Rl7sIj4mDPD/N2lSj4izkAhshqbeBgwRahIEQAg1/image_dUjnXCbpWoLJHcq9j2Dpu.png?ixlib=js-3.8.0 "image.png")



👉 This is **real DevOps work**, not theory.

---

## 8️⃣ Dockerfile Explained (Line by Line)
| Line | Meaning |
| ----- | ----- |
| FROM openjdk:17 | Base image |
| WORKDIR /app | Working directory |
| COPY | Copy jar |
| EXPOSE | Inform port |
| CMD | Startup command |


# 🔹 PHASE 4: Docker Compose (Real Projects)
## Why Docker Compose?
Real apps have:

- Spring Boot
- MySQL
- Redis
- Kafka
You don’t want to run 5 commands manually.

### docker-compose.yml
```yaml
version: '3'
services:
  app:
    image: springboot-app
    ports:
      - "8080:8080"
    depends_on:
      - db

  db:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: testdb
```
### Run everything
```bash
docker-compose up -d
```
# 🐳 STEP-BY-STEP: PUSH & PULL YOUR EXISTING IMAGE
## 🔹 STEP 1: Verify the Image Exists Locally
Run:

```bash
docker images
```
You should see:

```
docker1   latest   78bbf7fc4f62
```
✅ Good — image is ready.

---

## 🔹 STEP 2: Login to Docker Hub
```bash
docker login
```
Enter:

- Docker Hub **username**
- Password / access token
You must see:

```
Login Succeeded
```
⚠️ If this fails, push will NOT work.

---

## 🔹 STEP 3: Tag Your Image (MOST IMPORTANT STEP)
Docker Hub **requires** this format:

```
<dockerhub-username>/<repo-name>:<tag>
```
### Example (replace username with YOUR Docker Hub username):
```bash
docker tag docker1:latest <your-username>/docker1:1.0
```
📌 Example:

```bash
docker tag docker1:latest gauravgimonkar/docker1:1.0
```
Now verify:

```bash
docker images
```
You should see **two tags pointing to same ID**:

```
docker1                    latest   78bbf7fc4f62
gauravgimonkar/docker1     1.0      78bbf7fc4f62
```
✔ Same ID = same image, just different names

---

## 🔹 STEP 4: Push Image to Docker Hub
```bash
docker push <your-username>/docker1:1.0
```
Example:

```bash
docker push gauravgimonkar/docker1:1.0
```
You’ll see:

- Layers uploading
- Some layers may say **“already exists”** (normal)
✅ Push successful.

---

![image.png](https://eraser.imgix.net/workspaces/Jq1069p59Rl7sIj4mDPD/N2lSj4izkAhshqbeBgwRahIEQAg1/image_CisP4rRwrEqGyuA6QAzKG.png?ixlib=js-3.8.0 "image.png")

## 🔹 STEP 5: Verify on Docker Hub
1. Go to [﻿https://hub.docker.com](https://hub.docker.com/) 
2. Open your profile
3. You’ll see:
```
<your-username>/docker1
```
Your image is now **publicly available** 🎉

---

![image.png](https://eraser.imgix.net/workspaces/Jq1069p59Rl7sIj4mDPD/N2lSj4izkAhshqbeBgwRahIEQAg1/image_3RDjjHZ72Vf7vFvU4Cuhm.png?ixlib=js-3.8.0 "image.png")



# 📥 PULL THE SAME IMAGE (TEST IT)
## 🔹 STEP 6: Pull Image from Docker Hub
```bash
docker pull <your-username>/docker1:1.0
```
Example:

```bash
docker pull gauravgimonkar/docker1:1.0
```
![image.png](https://eraser.imgix.net/workspaces/Jq1069p59Rl7sIj4mDPD/N2lSj4izkAhshqbeBgwRahIEQAg1/image_j75lVhmcYnSHuPGC2nAPm.png?ixlib=js-3.8.0 "image.png")

---

## 🔹 STEP 7: Run the Pulled Image
```bash
docker run -d -p 8080:8080 <your-username>/docker1:1.0
```
![image.png](https://eraser.imgix.net/workspaces/Jq1069p59Rl7sIj4mDPD/N2lSj4izkAhshqbeBgwRahIEQAg1/image_nshtGygJslyEKfLnp9734.png?ixlib=js-3.8.0 "image.png")

**Output:**

![image.png](https://eraser.imgix.net/workspaces/Jq1069p59Rl7sIj4mDPD/N2lSj4izkAhshqbeBgwRahIEQAg1/image_plW2tQhL6McvM5eailSTC.png?ixlib=js-3.8.0 "image.png")

✔ If container runs → push & pull worked perfectly

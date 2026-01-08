# 🚀 Containerized Portfolio – Docker Mini Project

This mini project demonstrates how to **deploy a static portfolio website using Docker**, starting from transferring files to an EC2 instance, running the site using **bind mounts**, and finally **creating and pushing a Docker image to Docker Hub**.

---

## 📌 Project Overview

**Objective:**

* Host a static portfolio website using **Nginx inside a Docker container**
* Learn Docker fundamentals: bind mounts, image creation, and Docker Hub push

**Tech Stack:**

* AWS EC2 (Amazon Linux)
* Docker
* Nginx
* Docker Hub

---

## 🗂️ Project Architecture

```
Local Machine
   │
   ├── Portfolio Files (HTML, CSS, JS)
   │
   └── SCP
        ↓
AWS EC2 Instance
   │
   ├── /home/ec2-user/portfolio
   │        │
   │        └── Bind Mount
   │              ↓
   │        Docker Container (Nginx)
   │
   └── Docker Image → Docker Hub
```

---

## 🔹 Step 1: Prepare Portfolio on Local Machine

Ensure your portfolio contains an **index.html** file:

```
portfolio/
 ├── index.html
 ├── css/
 ├── js/
 └── images/
```

> ⚠️ Nginx serves `index.html` by default. This file is mandatory.

---

## 🔹 Step 2: Transfer Portfolio to EC2 using SCP

From your **local machine**, run:

```bash
scp -i key.pem -r portfolio ec2-user@<EC2_PUBLIC_IP>:/home/ec2-user/
```

Verify on EC2:

```bash
ls /home/ec2-user/portfolio
```

---

## 🔹 Step 3: Fix Permissions (Important)

Docker bind mounts preserve host permissions. Nginx needs read access.

```bash
sudo chown -R ec2-user:ec2-user /home/ec2-user/portfolio
sudo chmod -R 755 /home/ec2-user/portfolio
```

---

## 🔹 Step 4: Run Portfolio using Docker Bind Mount

Run Nginx container with bind mount:

```bash
docker run -d \
  -p 80:80 \
  --name portfolio \
  -v /home/ec2-user/portfolio:/usr/share/nginx/html:ro \
  nginx
```

Verify:

```bash
docker ps
docker exec -it portfolio ls /usr/share/nginx/html
```

Access in browser:

```
http://<EC2_PUBLIC_IP>
```

---

## 🔹 Step 5: Create Docker Image from Running Container

Commit the running container as an image:

```bash
docker commit portfolio your-dockerhub-username/portfolio:1.0
```

Verify image:

```bash
docker images
```

---

## 🔹 Step 6: Login to Docker Hub

```bash
docker login
```

Enter Docker Hub username and password.

---

## 🔹 Step 7: Push Image to Docker Hub

```bash
docker push your-dockerhub-username/portfolio:1.0
```

🎉 Image successfully pushed to Docker Hub.

---

## 🔹 Step 8: Pull & Run Image from Docker Hub (Verification)

On any server with Docker:

```bash
docker pull your-dockerhub-username/portfolio:1.0
docker run -d -p 80:80 your-dockerhub-username/portfolio:1.0
```

---

## ✅ Final Outcome

* Portfolio hosted via **Dockerized Nginx**
* Docker image created from running container
* Image pushed to **Docker Hub**
* Ready for ECS / Kubernetes deployment

---

## 📘 Key Learnings

* Difference between **bind mount** and **Docker image**
* Importance of file permissions in Docker
* Real-world Docker workflow
* Image lifecycle: build → tag → push → pull

---

## 🔗 Future Enhancements

* Use Dockerfile instead of `docker commit`
* Deploy on AWS ECS
* Add HTTPS using Nginx + Certbot
* CI/CD using GitHub Actions

---

⭐ **If you found this project helpful, feel free to star and fork!**

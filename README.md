# Docker–Ansible 3-Tier Application Project Requirements

This project demonstrates deploying a full **3-Tier Web Application** using **Docker, Docker Compose, and Ansible**. The infrastructure and application delivery are implemented entirely as **Code (Infrastructure as Code + Configuration as Code)**.

---

## Architecture Components

### 1. Frontend (React + Nginx)
- React-based user interface
- Nginx web server serving static content
- Nginx configured as reverse proxy for backend API at `/api`

### 2. Backend (.NET API)
- .NET 8.0 based RESTful API
- Handles business logic and database operations
- Exposes endpoints for CRUD operations

### 3. Database (MySQL)
- MySQL 8.0 database server
- Persistent data storage using Docker volumes
- Secured with Docker secrets

---

# ✅ Project Requirements

## 1️⃣ Docker Requirements

### 1. React + Nginx Dockerfile (Frontend)

A **multi-stage Dockerfile** must be used.

#### ✅ Stage 1: Build Stage

* Base Image: `node:18`
* Build command:

  ```bash
  npm install
  npm run build
  ```
* Output: Static React build files

#### ✅ Stage 2: Nginx Runtime Stage

* Base Image: `nginx:alpine`
* Copy static build output to:

  ```bash
  /usr/share/nginx/html
  ```
* Copy a custom `nginx.conf` that:

  * Serves the React frontend
  * Acts as a **reverse proxy for the backend at `/api`**

---

### 2. .NET Backend Dockerfile

A **multi-stage Dockerfile** must be used.

#### ✅ Build Stage

* Base Image:

  ```
  mcr.microsoft.com/dotnet/sdk:8.0
  ```
* Build command:

  ```bash
  dotnet publish -c Release -o /app/publish --no-restore
  ```
  - Output: Published DLL artifacts

#### ✅ Runtime Stage

* Base Image:

  ```
  mcr.microsoft.com/dotnet/aspnet:8.0
  ```
* Entry point:

  ```bash
  dotnet API.dll
  ```

---

## 2️⃣ Docker Compose Requirements

The Docker Compose file must contain **three services**:

1. `frontend`
2. `backend`
3. `db` (MySQL 8.0)

---

### ✅ MySQL Configuration

* Image:

  ```yaml
  mysql:8.0
  ```

* Volume must be created for persistent storage:

  ```yaml
  db-data:/var/lib/mysql
  ```

---

### ✅ Networks

* One **private network** between:

  * Frontend ↔ Backend

* One **private network** between:

  * Backend ↔ Database

---

### ✅ Docker Secrets

Database password must be secured using Docker secrets:

```yaml
secrets:
  db_password:
    file: ./db_password.txt
```

---

### ✅ Environment Variables Configuration

#### 📌 Backend Environment Variables

```yaml
environment:
  DB_HOST: <db service name>
  DB_PORT: 3306
  DB_NAME: <chosen database name>
  DB_USER: root
  DB_PASSWORD_FILE: /run/secrets/db_password
```

#### 📌 Database Environment Variables

```yaml
environment:
  - MYSQL_ROOT_PASSWORD_FILE=/run/secrets/db_password
  - MYSQL_DATABASE=<chosen database name>
```

---

# 3️⃣ Ansible Automation Requirements

All deployment steps must be fully automated using **Ansible Playbook**.

---

## ✅ Ansible Tasks Flow

### 1. Install Docker

* Ensure Docker is installed on the local machine

---

### 2. Docker Hub Login

* Must use:

  ```yaml
  community.docker.docker_login
  ```

---

### 3. Build & Push Docker Images

* Must push **both frontend and backend images**
* Must be implemented in **one looping task** using:

  ```yaml
  community.docker.docker_image
  ```

---

### 4. Create Database Secret File Dynamically

Ansible must create the database password file dynamically:

```yaml
- name: Create db_password.txt with variable content
  copy:
    dest: ./db_password.txt
    content: "{{ DB_PASSWORD }}"
```

---

### 5. Run Docker Compose

The playbook must run Docker Compose using:

```yaml
community.docker.docker_compose_v2
```

⚠️ If the module is not available, the collection must be updated first:

```bash
ansible-galaxy collection install community.docker --force
```

---

### 6. Delete Secret File After Deployment

After deploying the containers, Ansible must remove the secret file:

```yaml
- file:
    path: ./db_password.txt
    state: absent
```

---

# 4️⃣ Ansible Variables Structure

## ✅ vars/main.yml

```yaml
DOCKER_USERNAME: "<your docker hub username>"

images:
  - name: "{{ DOCKER_USERNAME }}/fronend-react"
    tag: "latest"
    path: "./React-Frontend"

  - name: "{{ DOCKER_USERNAME }}/backend-dotnet-api"
    tag: "latest"
    path: "./DotNet-Backend"
```

---

## ✅ vars/secrets.yml

```yaml
DB_PASSWORD: "<your database password>"
DOCKER_PASSWORD: "<your docker hub password>"
```

---

## ✅ Secrets Encryption

The secrets file must be encrypted using:

```bash
ansible-vault encrypt vars/secrets.yml
```

This ensures that **all credentials are fully secured**.

---

## ✅ Final Execution Command

```bash
ansible-playbook playbook.yml
```

After successful execution:

* All images must be built and pushed
* Containers must be running
* The full 3-tier app must be accessible

---

# ✅ Submission Requirements

You must create a **GitHub Repository** that contains the following:

### 📁 Required Files & Structure

* ✅ Frontend Dockerfile
* ✅ Backend Dockerfile
* ✅ `docker-compose.yml`
* ✅ `playbook.yml`
* ✅ `vars/main.yml`
* ✅ `vars/secrets.yml` (encrypted with Ansible Vault)

---

### 📁 Screenshots Folder

You must include a folder in the repository containing:

* ✅ Screenshot of the application running at:

  ```
  http://localhost:8080
  ```

* ✅ Screenshot proving database connectivity by:

  * Creating a **product with your name** from the UI
  * Verifying that the product is successfully stored in the database

---

## 📤 Final Submission

After completing the project:

* Push the full project to a **GitHub repository**
* Send the **GitHub repository link via email** to:

```
mohamedosama.route@gmail.com
```

---

✅ This project validates your skills in:

* Docker & Multi-stage Builds
* Docker Compose Networking & Secrets
* Ansible Automation
* Secure Credentials Handling with Vault
* Full 3-Tier Application Deployment

🚀 Good luck with your submission!
trigger pipeline

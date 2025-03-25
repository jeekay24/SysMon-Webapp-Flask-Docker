# Docker Productivity Toolkit - README

## 🚀 Overview
This project demonstrates how to efficiently use **Docker** to boost productivity when developing, testing, and deploying a **Flask application with Redis**. It showcases various **Docker tools** like:

- **Docker Desktop** (GUI for managing containers)
- **Docker Compose** (Multi-container orchestration)
- **Docker Compose Watch** (Live code reloading)

## 📌 Prerequisites
- Install **[Docker Desktop](https://www.docker.com/products/docker-desktop/)**
- Install **Python 3.9+**
- Install **Redis** (Optional, since it will run inside a container)
- Install **pytest** (for running automated tests)

## 📂 Project Structure
```
.
├── app.py                  # Flask application
|__ templates               # html file
├── Dockerfile              # Docker instructions to build image
├── docker-compose.yml      # Multi-container setup
├── .dockerignore           # Ignore unnecessary files
├── requirements.txt        # Python dependencies
├── test_app.py             # Testcases using Testcontainers
└── README.md               # Documentation
```

---

## ⚙️ Step 1: Run the Application Locally

```bash
cd docker-project
pip3 install -r requirements.txt
python3 app.py  # Run Flask app on localhost:5001
```

Check the application at **http://localhost:5001**.

Test Redis connection:
```bash
redis-cli
KEYS *
LRANGE metrics 0 -1
```

---

## 🐳 Step 2: Containerize with Docker

### 1️⃣ Create a `Dockerfile`
```dockerfile
# Use the official Python image
FROM python:3.9

# Set working directory
WORKDIR /app

# Copy files
COPY . .

# Install dependencies
RUN pip install --no-cache-dir flask psutil redis

# Expose the port
EXPOSE 5001

# Run the application
CMD ["flask", "run", "--host=0.0.0.0", "--port=5001", "--debug"]
```

### 2️⃣ Build & Run the Docker Container
```bash
docker build -t flask-monitor .
docker run -p 5001:5001 flask-monitor
```

---

## 🛠️ Step 3: Use Docker Compose for Multi-Container Setup

### 1️⃣ Create `docker-compose.yml`
```yaml

services:
  web:
    build: .
    ports:
      - "5001:5001"
    environment:
      - REDIS_HOST=redis
      - REDIS_PORT=6379
      - FLASK_APP=app.py
      - FLASK_ENV=development
      - FLASK_RUN_HOST=0.0.0.0
      - FLASK_RUN_PORT=5001
    depends_on:
      - redis
    develop:
      watch:
        - action: sync
          path: .
          target: /app
    command: flask run

  redis:
    image: "redis:latest"
    ports:
      - "6379:6379"
```

### 2️⃣ Run the Services
```bash
docker compose up -d
```

### 3️⃣ Stop & Remove Containers
```bash
docker compose down
```

---

## 🔄 Step 4: Enable Live Code Reloading with Docker Compose Watch
```bash
docker compose watch
```

This will sync code changes automatically without rebuilding the container!



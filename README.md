# How to Create a Dockerfile, Build a Docker Image, and Run It for Python Apps

## Directory

* [1. Basic idea](#1-basic-idea)
* [2. General project structure](#2-general-project-structure)
* [3. Example A: Regular Python script](#3-example-a-regular-python-script)
* [4. Example B: Flask app](#4-example-b-flask-app)
* [5. Example C: FastAPI app](#5-example-c-fastapi-app)
* [6. General Dockerfile pattern for Python apps](#6-general-dockerfile-pattern-for-python-apps)
* [7. Commands summary](#7-commands-summary)
* [8. Recommended `.dockerignore`](#8-recommended-dockerignore)
* [9. Common issues](#9-common-issues)
* [10. One-page quick reference](#10-one-page-quick-reference)
* [11. Short explanation to send with it](#11-short-explanation-to-send-with-it)

---

# How to Create a Dockerfile, Build a Docker Image, and Run It for Python Apps

This guide shows how to containerize and run three common types of Python applications:

* a regular Python script (`.py`)
* a Flask app
* a FastAPI app

---

## 1. Basic idea

The workflow is the same in all cases:

```text
Python code → Dockerfile → Docker image → Docker container
```

* **Dockerfile**: a text file with instructions for Docker
* **Docker image**: the packaged environment built from the Dockerfile
* **Docker container**: a running instance of that image

---

## 2. General project structure

A typical project folder looks like this:

```text
project/
├── app.py
├── requirements.txt
└── Dockerfile
```

If the app has multiple files, keep them in the same project folder and copy them into the image.

---

## 3. Example A: Regular Python script

### Files

```text
project/
├── app.py
└── Dockerfile
```

### `app.py`

```python
print("Hello from Docker")
```

### `Dockerfile`

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY app.py .

CMD ["python", "app.py"]
```

### Build the image

```bash
docker build -t my-python-script .
```

### Run the image

```bash
docker run --rm my-python-script
```

### What happens

Docker starts a container and runs:

```bash
python app.py
```

---

## 4. Example B: Flask app

### Files

```text
project/
├── app.py
├── requirements.txt
└── Dockerfile
```

### `app.py`

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello from Flask in Docker!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

### `requirements.txt`

```txt
flask
```

### `Dockerfile`

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

### Build the image

```bash
docker build -t my-flask-app .
```

### Run the image

```bash
docker run --rm -p 5000:5000 my-flask-app
```

### Open in browser

```text
http://localhost:5000
```

### Notes

* `EXPOSE 5000` documents the port used by the app.
* `-p 5000:5000` maps the host port to the container port.
* `host="0.0.0.0"` is required so Flask is reachable outside the container.

---

## 5. Example C: FastAPI app

### Files

```text
project/
├── app.py
├── requirements.txt
└── Dockerfile
```

### `app.py`

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello from FastAPI in Docker!"}
```

### `requirements.txt`

```txt
fastapi
uvicorn
```

### `Dockerfile`

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Build the image

```bash
docker build -t my-fastapi-app .
```

### Run the image

```bash
docker run --rm -p 8000:8000 my-fastapi-app
```

### Open in browser

Application root:

```text
http://localhost:8000
```

Automatic docs:

```text
http://localhost:8000/docs
```

### Notes

* FastAPI is commonly served with **Uvicorn**.
* `app:app` means:

  * first `app` = filename `app.py`
  * second `app` = FastAPI application object inside the file

---

## 6. General Dockerfile pattern for Python apps

A common pattern is:

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "app.py"]
```

For web apps, add:

```dockerfile
EXPOSE <port>
```

And adjust the `CMD` if needed.

---

## 7. Commands summary

### Build image

```bash
docker build -t image-name .
```

### Run container

```bash
docker run --rm image-name
```

### Run with port mapping

```bash
docker run --rm -p host_port:container_port image-name
```

### List images

```bash
docker images
```

### List running containers

```bash
docker ps
```

### Stop a running container

```bash
docker stop <container_id>
```

---

## 8. Recommended `.dockerignore`

To avoid copying unnecessary files into the image, create a `.dockerignore` file:

```txt
__pycache__/
*.pyc
*.pyo
*.pyd
.venv/
venv/
.env
.git
.gitignore
```

This keeps images smaller and cleaner.

---

## 9. Common issues

### Issue: App works locally but not in Docker

For Flask or FastAPI, make sure the app binds to:

```text
0.0.0.0
```

not `127.0.0.1`.

### Issue: Dependencies not found

Make sure `requirements.txt` includes all needed packages.

### Issue: Browser cannot access app

Make sure:

* the app listens on the correct port
* the Dockerfile exposes that port
* `docker run` includes `-p host:container`

### Issue: Wrong FastAPI startup command

Use:

```bash
uvicorn app:app --host 0.0.0.0 --port 8000
```

unless your filename or app object name is different.

---

## 10. One-page quick reference

### Regular Python script

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY app.py .
CMD ["python", "app.py"]
```

```bash
docker build -t my-python-script .
docker run --rm my-python-script
```

### Flask app

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]
```

```bash
docker build -t my-flask-app .
docker run --rm -p 5000:5000 my-flask-app
```

### FastAPI app

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000"]
```

```bash
docker build -t my-fastapi-app .
docker run --rm -p 8000:8000 my-fastapi-app
```


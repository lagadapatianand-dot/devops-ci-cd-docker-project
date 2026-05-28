## Project Overview

This project demonstrates a basic but practical DevOps CI/CD workflow using Docker and GitHub Actions.

The goal of this project was to create a simple Python Flask application, containerize it using Docker, test it locally, and then automate the build and validation process using a GitHub Actions pipeline.

This project helped me understand how modern DevOps pipelines work in real-world software delivery, especially around containerization, automated testing, and CI/CD workflow automation.

## Tech Stack Used

- Python
- Flask
- Docker
- GitHub Actions
- GitHub
- Pytest
- Docker Hub
- YAML

## Project Architecture

Developer pushes code to GitHub
        |
        v
GitHub Actions pipeline starts
        |
        v
Install Python dependencies
        |
        v
Run unit tests using Pytest
        |
        v
Build Docker image
        |
        v
Run Docker container
        |
        v
Validate health endpoint
        |
        v
Push Docker image to Docker Hub

#Project Structure

devops-ci-cd-docker-project/
│
├── app/
│   ├── app.py
│   └── requirements.txt
│
├── tests/
│   └── test_app.py
│
├── .github/
│   └── workflows/
│       └── ci-cd.yml
│
├── Dockerfile
├── .dockerignore
├── .gitignore
└── README.md

#What I Built

I built a simple Flask application with two endpoints:

/         - Returns application information
/health   - Returns application health status

The /health endpoint is used inside the CI/CD pipeline to verify that the Docker container is running correctly.

#Step-by-Step Implementation

#Step 1: Created the Flask Application

I started by creating a basic Python Flask application.

The application returns a simple JSON response from the home route and exposes a health check route.
@app.route("/")
def home():
    return {
        "message": "Hello from DevOps CI/CD Docker Project!",
        "version": "1.0.0",
        "environment": "local"
    }

@app.route("/health")
def health():
    return {
        "status": "healthy"
    }
This helped me understand how a simple web application can expose endpoints that can later be tested automatically in a pipeline.

#Step 2: Added Python Dependencies

I created a requirements.txt file to manage the Python dependencies required for the application.
flask
pytest
This made it easier to install dependencies both locally and inside the GitHub Actions pipeline.

#Step 3: Added Unit Tests

I added basic unit tests using Pytest to validate that both the home endpoint and health endpoint are working correctly.

#The test checks:

The / endpoint returns HTTP status code 200
The /health endpoint returns HTTP status code 200
The /health endpoint returns healthy status
This helped me understand the importance of automated testing before building and deploying an application.

#Step 4: Created the Dockerfile

I created a Dockerfile to containerize the Flask application.
The Dockerfile performs the following actions:
Uses a Python base image
Sets the working directory
Copies the dependency file
Installs Python dependencies
Copies the application code
Exposes port 5000
Starts the Flask application
FROM python:3.12-slim

WORKDIR /app

COPY app/requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY app/ .

EXPOSE 5000

CMD ["python", "app.py"]
This helped me understand how Docker packages an application and its dependencies into a portable container image.

#Step 5: Built the Docker Image Locally

I built the Docker image locally using:
docker build -t devops-flask-app .
This confirmed that the Dockerfile was correct and the image could be built successfully.

#Step 6: Ran the Docker Container Locally

After building the image, I ran the container using:
docker run -d -p 5000:5000 --name flask-devops-app devops-flask-app
Then I tested the application using:
curl http://localhost:5000
curl http://localhost:5000/health
This helped me understand how port mapping works between the local machine and the Docker container.

#Step 7: Created the GitHub Actions Workflow

I created a GitHub Actions workflow file inside:
.github/workflows/ci-cd.yml
The pipeline performs the following steps:
Checks out the source code
Sets up Python
Installs dependencies
Runs unit tests
Builds the Docker image
Runs the Docker container
Tests the /health endpoint
Logs in to Docker Hub
Tags the Docker image
Pushes the Docker image to Docker Hub
This helped me understand how CI/CD pipelines automate repetitive DevOps tasks.

#Step 8: Added Docker Hub Integration

I created a Docker Hub repository and added Docker Hub credentials as GitHub Actions secrets.
The secrets used were:
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
This allowed the GitHub Actions workflow to securely authenticate with Docker Hub without hardcoding credentials in the repository.

#Step 9: Pushed the Docker Image to Docker Hub

After successful tests and container validation, the pipeline tags and pushes the Docker image to Docker Hub.
The image is pushed with two tags:
latest
github commit SHA
This helped me understand image versioning and why unique image tags are useful in CI/CD pipelines.

#Problems Faced and How I Solved Them

#Problem 1: Docker Container Name Already in Use

While testing locally, I got an error saying the container name was already in use.
Example error:
docker: Error response from daemon: Conflict. The container name is already in use.
Cause
I had already created a container with the same name earlier. Even if the container was stopped, Docker still kept the container name reserved.
Solution
I checked existing containers:
docker ps -a
Then I removed the old container:
docker rm flask-devops-app
After that, I ran the container again:
docker run -d -p 5000:5000 --name flask-devops-app devops-flask-app

#What I Learned

I learned that Docker container names must be unique, and stopped containers still exist until they are removed.

#Problem 2: Application Was Not Accessible on localhost

At one point, the container was running, but I could not access the application from the browser or curl.
Example error:
curl: (7) Failed to connect to localhost port 5000
Cause
The Flask app must listen on 0.0.0.0 inside the container. If it listens only on 127.0.0.1, it may not be reachable from outside the container.
Solution
I updated the Flask app startup configuration:
app.run(host="0.0.0.0", port=5000)
Then I rebuilt and reran the Docker image:
docker build -t devops-flask-app .
docker run -d -p 5000:5000 --name flask-devops-app devops-flask-app

#What I Learned

I learned that containerized applications should bind to 0.0.0.0 so they can accept traffic from outside the container.

#Problem 3: Port Mapping Confusion

Initially, I was confused about how Docker ports work.
I used:
docker run -p 5000:5000 devops-flask-app
Explanation
The first 5000 is the host machine port.
The second 5000 is the container port.
localhost:5000  →  container:5000

#What I Learned

I learned how Docker port mapping allows traffic from my local machine to reach the application running inside the container.

#Problem 4: GitHub Actions Workflow Failed Due to Test Issues

The pipeline failed when running tests.
Cause
The test file could not properly import the Flask app because the application code was inside the app/ folder.
Solution
I updated the test file to include the application path:
import sys
import os

sys.path.insert(0, os.path.abspath(os.path.join(os.path.dirname(__file__), "../app")))

from app import app

#What I Learned

I learned that project structure matters in Python applications and that test files must be able to correctly import application modules.

#Problem 5: Docker Hub Login Failed in GitHub Actions

The pipeline failed while trying to log in to Docker Hub.
Cause
The Docker Hub credentials were either missing or incorrectly added in GitHub repository secrets.
Solution
I added the correct secrets in GitHub:
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
Then I used those secrets in the workflow:
username: ${{ secrets.DOCKERHUB_USERNAME }}
password: ${{ secrets.DOCKERHUB_TOKEN }}

#What I Learned

I learned how GitHub Actions secrets are used to securely store sensitive values like usernames, tokens, and passwords.

#Problem 6: Docker Image Was Built but Not Pushed

The pipeline successfully built the Docker image but did not push it to Docker Hub.
Cause
The image was not tagged with the correct Docker Hub repository name.
Docker Hub requires the image name format:
dockerhub-username/repository-name:tag
Solution
I tagged the image properly before pushing:
docker tag devops-flask-app:${{ github.sha }} username/devops-flask-app:latest
docker tag devops-flask-app:${{ github.sha }} username/devops-flask-app:${{ github.sha }}
Then I pushed it:
docker push username/devops-flask-app:latest
docker push username/devops-flask-app:${{ github.sha }}

#What I Learned

I learned that Docker images must be correctly tagged before they can be pushed to a remote registry.
CI/CD Pipeline Explanation
The GitHub Actions pipeline is triggered when code is pushed to the main branch or when a pull request is opened against main.
on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main
The pipeline first validates the application by running tests. If the tests pass, it builds a Docker image. After that, it runs the container and checks the health endpoint.
Only after successful validation does it push the image to Docker Hub.
This ensures that broken code is not published as a Docker image.

#What I Learned From This Project

Through this project, I learned:
How to create a simple Flask application
How to write basic unit tests using Pytest
How to create a Dockerfile
How to build Docker images locally
How to run Docker containers locally
How Docker port mapping works
How to debug Docker container issues
How to create a GitHub Actions workflow
How to automate testing using CI
How to automate Docker image publishing using CD
How to use GitHub Actions secrets
How to push Docker images to Docker Hub
How to structure a beginner-friendly DevOps project
How CI/CD improves software delivery reliability

#Commands Used

#Build Docker Image
docker build -t devops-flask-app .

#Run Docker Container

docker run -d -p 5000:5000 --name flask-devops-app devops-flask-app

#Check Running Containers

docker ps

#Check All Containers

docker ps -a

#Stop Container

docker stop flask-devops-app

#Remove Container

docker rm flask-devops-app

#Test Application

curl http://localhost:5000

curl http://localhost:5000/health

#Run Tests

pytest

#Final Outcome

By the end of this project, I successfully built a complete CI/CD workflow for a Dockerized Flask application.
The project includes:
A working Flask application
Unit tests
Dockerfile
Docker image build process
Local container testing
GitHub Actions CI/CD pipeline
Docker Hub image publishing
Documentation of issues and solutions

This project demonstrates the basic workflow used by DevOps engineers to automate application build, test, containerization, and image publishing.

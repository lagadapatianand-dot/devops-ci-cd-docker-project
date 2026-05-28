# DevOps CI/CD Docker Project

This project demonstrates a complete CI/CD workflow for a Dockerized Python Flask application using GitHub Actions. The application exposes a simple API with a health check endpoint and is packaged into a Docker container for consistent local and pipeline-based execution.

The GitHub Actions pipeline automatically installs dependencies, runs unit tests, builds the Docker image, runs the container, validates the health endpoint, and publishes the image to Docker Hub after successful validation.

## Key Features

- Dockerized Python Flask web application
- Automated CI/CD pipeline using GitHub Actions
- Container build and runtime validation
- Health check endpoint testing
- Docker Hub image publishing

## Tech Stack

- Python
- Flask
- Docker
- GitHub Actions
- Docker Hub
- Git/GitHub

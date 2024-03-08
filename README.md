# Task Scheduler Demo

This project demonstrates a task scheduler built with Python and Flask, containerized with Docker, and orchestrated using Kubernetes with Helm charts, including a MariaDB database.

## Prerequisites

Before starting, ensure you have the following installed on your system:
- **Docker**: For creating and managing containers.
- **Kubernetes Cluster**: Minikube, or any Kubernetes cluster you have access to.
- **Helm**: For managing Kubernetes applications.
- **kubectl**: For interacting with your Kubernetes cluster.
- **Access to a Terminal/Command Prompt**: For executing commands.

## Setup Instructions

### Step 1: Clone the Repository

Clone this repository to your local machine using the following commands:

`git clone https://github.com/UncleWeeds/cback-helm-test`

`cd cback-helm-test` 

### Step 2: Start Your Kubernetes Cluster

Ensure your Kubernetes cluster is running. If you're using Minikube, you can start it with the following command:

`minikube start`

### Step 3: Local Setup for Task 1 (Without Docker and Kubernetes)

Before proceeding with Docker and Kubernetes, you can test the Flask application locally by following these steps:

`python3 -m venv venv`

`source venv/bin/activate`

`pip install -r requirements.txt`

`export FLASK_APP=run.py`

`export FLASK_ENV=development`

`flask run`

For testing purposes, You can use these commands:

To test all the tasks:

`curl -X GET http://127.0.0.1:5000/tasks/`

To create a Normal Task:

`curl -X POST http://127.0.0.1:5000/tasks/ 
-H 'Content-Type: application/json' 
-d '{"name": "One-time Task", "execution_time": "2024-03-07T20:30:00"}'`

For Recurring Tasks: 

`curl -X POST http://127.0.0.1:5000/tasks/ 
-H 'Content-Type: application/json' 
-d '{"name": "Daily Task", "execution_time": "2024-03-07T20:35:00", "recurrence": "daily"}'`

To get a single task: 

`curl -X GET http://127.0.0.1:5000/tasks/1`

To delete a task: 

`curl -X DELETE http://127.0.0.1:5000/tasks/<id>`


### Step 4: Docker Setup

Before deploying the application, you'll need to build a Docker image. Ensure Docker is installed and running on your system. Then, follow these steps:

**Build the Docker Image**

   Navigate to the root directory of the project where the `Dockerfile` is located, and run the following command to build your Docker image:

   `docker build -t task-scheduler .`

   `docker run -p 5000:5000 task-scheduler` (This command runs the task-scheduler Docker image as a container, mapping port 5000 of the container to port 5000 on your host, allowing you to access the application at http://localhost:5000.)

### Step 5: Kubernetes Setup

After setting up Docker and building your image, the next step is to deploy the application to a Kubernetes cluster. This guide assumes you have a running Kubernetes cluster and `kubectl` configured to communicate with your cluster.

1. **Deploy the Application**

   Use the provided Kubernetes YAML files to deploy the Task Scheduler application and its required components. Navigate to the `kubernetes` directory and apply the configurations:

  `kubectl apply -f scheduler.yaml`

  `kubectl port-forward svc/task-scheduler-service 5000:5000` ( This command forwards port 5000 of the task-scheduler-service service to port 5000 on your localhost. You can now access the Task Scheduler application by navigating to http://localhost:5000 in your web browser.)

   ### Deploying and Managing Kubernetes Jobs

The Kubernetes jobs allow you to perform CRUD operations on the tasks within the Task Scheduler application. Follow these steps to deploy and manage these jobs.

1. **Deploying Jobs**

   To deploy a job, use the `kubectl apply` command with the job YAML file. For example, to fetch all tasks, you can deploy the `fetch-all-tasks-job.yaml`:

   `kubectl apply -f kubernetes/fetch-all-tasks-job.yaml`

   `kubectl get jobs`

   `kubectl get pods --selector=job-name=fetch-all-tasks-job`

   `kubectl logs <pod-name>`

### Deploying with Helm

This project includes a Helm chart for deploying the Task Scheduler application and its dependent services, including the MariaDB database, into a Kubernetes cluster. Follow these steps to deploy the application using Helm.

#### Prerequisites

- Helm installed on your system.
- Access to a Kubernetes cluster with `kubectl` configured to manage it.

#### Steps to Deploy

1. **Update Helm Dependencies**

   Navigate to the `task-scheduler-helm` directory and update the Helm dependencies:

   `cd task-scheduler-helm`
   
   `helm dependency update`

   `helm upgrade --install task-scheduler-release .`

   `helm list`

   `kubectl get all`

   Similarly to Kubernetes Jobs, you can check out the logs for the pod that is being created to get the details regarding the job status and work done by it.

   For testing purpose for helm job you can use this commands:

    `helm upgrade --install task-scheduler-release path/to/helm-chart`

   `helm upgrade --install task-scheduler-release path/to/helm-chart \
  --set task.name="New Task Name",task.executionTime="2024-02-02T12:00:00"`

   

  

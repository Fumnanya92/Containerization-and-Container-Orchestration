# Containerization-and-Container-Orchestration
Basic Frontend Application with Docker and Kubernetes

### 1. Hypothetical Use Case

You are developing a simple static website (HTML and CSS) for a company's landing page. The goal is to containerize this application using Docker, deploy it to a Kubernetes cluster, and access it through Nginx.

### 2. Tasks

#### Task 1: Set up your project

1. Create a new project directory:
    ```bash
    mkdir my-landing-page
    cd my-landing-page
    ```

2. Inside the directory, create an HTML file ('index.html') and a CSS file ('styles.css'):

    - `index.html`
        ```html
        <!DOCTYPE html>
        <html lang="en">
        <head>
            <meta charset="UTF-8">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>Company Landing Page</title>
            <link rel="stylesheet" href="styles.css">
        </head>
        <body>
            <header>
                <h1>Welcome to Our Company</h1>
            </header>
            <main>
                <p>This is our landing page.</p>
            </main>
            <footer>
                <p>&copy; 2024 Our Company</p>
            </footer>
        </body>
        </html>
        ```

    - `styles.css`
        ```css
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            height: 100vh;
        }

        header, footer {
            background-color: #333;
            color: white;
            text-align: center;
            padding: 1em 0;
        }

        main {
            flex-grow: 1;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        p {
            font-size: 1.2em;
        }
        ```

#### Task 2: Initialize Git

1. Initialize a Git repository in your project directory:
    ```bash
    git init
    ```
![image](https://github.com/user-attachments/assets/61a1e883-ec81-463b-8743-8bbe368dc60f)



#### Task 3: Git Commit

1. Add and commit your initial code to the Git repository:
    ```bash
    git add .
    git commit -m "Initial commit with HTML and CSS files"
    ```
![image](https://github.com/user-attachments/assets/f65796f2-9a83-4589-9741-25fb8ea612a6)



#### Task 4: Dockerize the application

1. Create a Dockerfile specifying Nginx as the base image:
    - `Dockerfile`
```
# Use the official NGINX base image
FROM nginx:latest

# Set the working directory in the container
WORKDIR  /usr/share/nginx/html/

# Copy the local HTML file to the NGINX default public directory
COPY landingPage/* /usr/share/nginx/html/

# Expose port 80 to allow external access
EXPOSE 80

# No need for CMD as NGINX image comes with a default CMD to start the server
```

![image](https://github.com/user-attachments/assets/379da9a5-8167-4d43-af71-bac02471487c)


#### Task 5: Push to Docker Hub
check if docker is installed
![image](https://github.com/user-attachments/assets/9b0425c1-a8ad-48c8-8dff-db534808d823)

```
# To install docker
apt install docker.io
```
![image](https://github.com/user-attachments/assets/48e9e203-8bf3-46d0-a127-9a0a977cfe07)



1. Log in to Docker Hub:
    ```bash
    docker login
    ```

2. Build your Docker image:
    ```bash
    docker build -t dockerfile .
 ```
![image](https://github.com/user-attachments/assets/1e0a4f85-f39c-462d-ac2d-81a4f8fadb18)


4. Push your Docker image to Docker Hub:
    ```bash
    docker push your-dockerhub-username/frontend:latest
    ```
![image](https://github.com/user-attachments/assets/2a75bcb5-2fd8-4ef5-a297-58643d0e6096)


#### Task 6: Set up a Kind Kubernetes Cluster

1. Install Kind (Kubernetes in Docker):
`This command refreshes the Debian-based system`
```
sudo apt-get update
```
`Download and Install minikube`
```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
```
![image](https://github.com/user-attachments/assets/e7966074-f313-4f8c-b6e8-7400824ad209)
```
i**If that doesn't work use the command bellow**
```
sudo dpkg -i minikube_latest_amd64.deb
```
![image](https://github.com/user-attachments/assets/d323528d-2828-4f79-8242-239428fd334c)

`Using the docker driver based on user configuration The "docker" driver should not be used with root privileges. 
`exit root user`
`return into working directory `
cd /home/ubuntu/Landing-page/Containerization-and-Container-Orchestration

**Add your user to the `docker` group:**
```
sudo usermod -aG docker $USER
```

**Apply the new group membership:**
```
newgrp docker
```

**Verify Docker version:**
```
docker version
```

![image](https://github.com/user-attachments/assets/5b5b4e99-845f-4d05-86b6-492b4b2ff78a)

**Start Minikube:**
```
minikube start --driver=docker
```
**verify**
```
docker ps -a
```
![image](https://github.com/user-attachments/assets/bfe10187-d20f-4304-b2e3-4c9ec30b82d9)

**use the command below to download the kubernetes commandline(kubectl) too to interact with kubernetes cluster**
```
sudo snap install kubectl --classic
```
**to verify**
```
kubectl get nodes

```

#### Task 7: Deploy to Kubernetes

1. Create a Kubernetes Deployment YAML file specifying the image and desired replicas:

    - `deployment.yaml`
        ```yaml
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: my-landing-page-deployment
        spec:
          replicas: 2
          selector:
            matchLabels:
              app: my-landing-page
          template:
            metadata:
              labels:
                app: my-landing-page
            spec:
              containers:
              - name: nginx
                image: your-dockerhub-username/my-landing-page:latest
                ports:
                - containerPort: 80
        ```
![image](https://github.com/user-attachments/assets/42f3a7b6-b8b2-454b-8915-a018bed5717c)


2. Apply the deployment to your cluster:
    ```bash
    kubectl apply -f deployment.yaml
    ```

#### Task 8: Create a Service (ClusterIP)


1. Create a Kubernetes Service YAML file specifying the type as ClusterIP:

    - `service.yaml`
        ```yaml
        apiVersion: v1
        kind: Service
        metadata:
          name: my-landing-page-service
        spec:
          selector:
            app: my-landing-page
          ports:
            - protocol: TCP
              port: 80
              targetPort: 80
          type: ClusterIP
        ```
![image](https://github.com/user-attachments/assets/53b71d42-f50e-49df-a2db-382a1dc59964)


2. Apply the service to your cluster:
    ```bash
    kubectl apply -f service.yaml
    ```

**verify Deployment**
```
kubectl get deployments

kubectl get services

```
![image](https://github.com/user-attachments/assets/98024c55-9414-47e5-bf95-d6640037f947)

![image](https://github.com/user-attachments/assets/198d48d1-ffb2-4398-aed3-efe3e8f21ac8)
    

#### Task 9: Access the Application

1. Port-forward to the service to access the application locally:
    ```bash
    kubectl port-forward service/my-landing-page-service 8080:80
    ```
    docker run -p 8080:80 [dockerfile]

2. Open your browser and visit `http://Host-IP:8080` to view your simple frontend application.


# Minikube Assessment: Java Application with MySQL Database

## Project Objective

This project demonstrates the deployment of a Spring Boot backend application that connects to a MySQL database on a local Kubernetes cluster using **Minikube**. The application exposes a REST endpoint that fetches user data from the database, showcasing a typical microservice architecture.

## Prerequisites

  * **Java 17** and **Maven** for building the Spring Boot application.
  * **Docker** for containerization.
  * **Minikube** and **kubectl** for managing the Kubernetes cluster.
  * **Bash** environment.

-----

## Project Structure

Your project is organized as a standard Maven-based Spring Boot application. The key files and directories are:

  * `src/main/java/com/example/userservice/`: Contains the Java source code.
      * `UserserviceApplication.java`: The main entry point for the Spring Boot application.
      * `controller/UserController.java`: Defines the `GET /users` REST endpoint.
      * `model/User.java`: The JPA entity representing the `users` table.
      * `repository/UserRepository.java`: The Spring Data JPA repository for database interaction.
  * `src/main/resources/`: Contains configuration and resource files.
      * `application.properties`: Spring Boot application properties.
      * `data.sql`: The SQL script used to pre-populate the `users` table on startup.
  * `pom.xml`: The Maven build file, including dependencies for Spring Web, Spring Data JPA, MySQL Connector, and Actuator.
  * `Dockerfile`: The instructions for building the Docker image of the Spring Boot application.
  * `config-secret.yaml`: Defines the Kubernetes **ConfigMap** and **Secret** for database configuration and credentials.
  * `mysql-deployment.yaml`: Defines the **PersistentVolumeClaim**, **Deployment**, and **Service** for the MySQL database.
  * `java-app-deployment.yaml`: Defines the **Deployment** for the Spring Boot application.
  * `java-app-service.yaml`: Defines the **Service** to expose the Java application externally via a `NodePort`.

-----

## Deployment Instructions

Follow these steps to deploy the application on your local machine using Minikube.

### Step 1: Start Minikube

Ensure your Minikube cluster is running and configured correctly.

```bash
minikube start
```

### Step 2: Prepare the Docker Environment

To build the Docker image directly inside the Minikube environment, configure your shell to use the Minikube Docker daemon. This is crucial for Minikube to find the image in the cluster's local registry.

```bash
eval $(minikube docker-env)
```

### Step 3: Build the Docker Image

Navigate to the project root directory and build the Docker image for your Spring Boot application.

```bash
docker build -t userservice:1.0 .
```

After the image is built, you can optionally switch your Docker environment back to the default by running `eval $(minikube docker-env -u)`.

### Step 4: Deploy Kubernetes Resources

Apply the Kubernetes YAML files in the correct order to create the necessary resources.

1.  **Deploy the ConfigMap and Secret**:
    ```bash
    kubectl apply -f config-secret.yaml
    ```
    This provides the database configuration and credentials securely.
2.  **Deploy the MySQL Database and Service**:
    ```bash
    kubectl apply -f mysql-deployment.yaml
    ```
    This creates the database pod and a stable internal service for it.
3.  **Deploy the Java Application**:
    ```bash
    kubectl apply -f java-app-deployment.yaml
    ```
    This creates the pod for your Spring Boot application.
4.  **Deploy the Java Application Service**:
    ```bash
    kubectl apply -f java-app-service.yaml
    ```
    This exposes your application externally via a `NodePort`.

### Step 5: Verify the Deployment

Check the status of all deployed resources to ensure they are healthy.

```bash
kubectl get all
```

You should see all your pods with a **`READY` status of `1/1`**.

### Step 6: Test the API Endpoint

Finally, test the `GET /users` endpoint to confirm the application is working as expected.

1.  Get the Minikube IP address:

    ```bash
    minikube ip
    ```

2.  Get the `NodePort` assigned to your application:

    ```bash
    kubectl get service java-app-service
    ```

3.  Use `curl` to access the endpoint with the obtained IP and port (e.g., `30000`).

    ```bash
    curl http://<minikube-ip>:30000/users
    ```

If successful, you will receive a JSON response with the user data, confirming that the entire stack is functional.
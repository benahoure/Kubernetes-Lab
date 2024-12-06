# Professional Card on Docker Containers using Kubernetes by Ben Ahoure

This project demonstrates how to use Kubernetes to deploy a professional card application hosted in Docker containers. The application uses two Kubernetes manifests:
1. `apache-deployment.yml`: The deployment of Apache web servers running the professional card application.
2. `service-loadbalancer.yml`: The load balancer service that exposes the Apache containers to external traffic.

## Prerequisites

- A Kubernetes cluster (e.g., using [Amazon EKS](https://aws.amazon.com/eks/), [Google GKE](https://cloud.google.com/kubernetes-engine), or [Minikube](https://minikube.sigs.k8s.io/docs/)).
- kubectl installed and configured to interact with the Kubernetes cluster.
- Docker image `benahoure/apache-app` available on Docker Hub or locally built and pushed to your registry.

## Components

### 1. `apache-deployment.yml`
This manifest defines the Kubernetes deployment for the Apache web server running your professional card application. The deployment will create 4 replicas of the Apache web server, ensuring high availability and scalability.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: apache-deployment
  labels:
    app: apache
spec:
  replicas: 4
  selector:
    matchLabels:
      app: apache
  template:
    metadata:
      labels:
        app: apache
    spec:
      containers:
        - name: apache
          image: benahoure/apache-app
          ports:
            - containerPort: 80
```

### 2. `service-loadbalancer.yml`
This manifest defines the Kubernetes service that acts as a load balancer. It exposes port 80 to external traffic and routes requests to the Apache containers deployed in the `apache-deployment`.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: loadbalancer-service
spec:
  selector:
    app: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```

### Key Notes:
- The load balancer service is set to type `LoadBalancer`, meaning it will provision a public-facing load balancer (on supported cloud providers).
- The deployment ensures 4 replicas of your Apache web servers are running for high availability.
- `benahoure/apache-app` is the Docker image for the professional card application. You can replace this image with your custom image if needed.

## Steps to Deploy

### 1. Apply the Deployment Manifest
First, apply the `apache-deployment.yml` file to create the Apache deployment:

```bash
kubectl apply -f apache-deployment.yml
```

This will create the necessary deployment for your Apache containers.

### 2. Apply the Load Balancer Manifest
Next, apply the `service-loadbalancer.yml` file to create the load balancer service:

```bash
kubectl apply -f service-loadbalancer.yml
```

This will create the load balancer and expose the application to external traffic on port 80.

### 3. Check the Status
You can check the status of your deployment and service with the following commands:

- Check the pods:
  ```bash
  kubectl get pods
  ```

- Check the service:
  ```bash
  kubectl get svc
  ```

Once the load balancer is provisioned (this may take a few minutes), you will see the external IP or DNS name under the `EXTERNAL-IP` column in the service status.

### 4. Access the Professional Card
Once the load balancer's external IP is available, you can access your professional card by opening the URL in your web browser:
```
http://<EXTERNAL-IP>
```

## Troubleshooting

- If your load balancer doesn't show an external IP, ensure that your Kubernetes cluster is set up to support external load balancers. Some local environments may not provision external IPs automatically (e.g., Minikube).
- If you encounter issues with pods not starting, check the logs for any errors:
  ```bash
  kubectl logs <pod-name>
  ```

## Conclusion
This Kubernetes setup demonstrates how to deploy and expose your professional card application using Docker containers, Apache web servers, and a load balancer service. With this, you can make your professional card publicly accessible and scalable.
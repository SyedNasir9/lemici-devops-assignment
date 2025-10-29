# LeMiCi IQ — DevOps Internship Assignment
**Candidate:** Syed Nasir  
**Repo:** https://github.com/SyedNasir9/lemici-devops-assignment

---
## Part 1: Version Control (Git & SSH)

### Repository Setup
- Created a **public GitHub repository** and configured it for **SSH authentication**.
- Generated SSH key in VS Code:
  ```bash
  ssh-keygen -t ed25519 -C "nasirsyed652@gmail.com"
  ```
- Copied the key using:
 ```bash 
 clip < ~/.ssh/id_ed25519.pub
 ```

### Repository Setup
- Added the **public SSH key** to GitHub and cloned the repository using SSH:
  ```bash
  git@github.com:SyedNasir9/lemici-devops-assignment.git


## Git Fetch vs Git Pull

- **git fetch:** Downloads changes from remote but does not modify the local branch. Useful to review changes before merging.
- **git pull:** Fetches and automatically merges changes into the current branch (git fetch + git merge).

## Merge Conflict Example

- Created two branches: feature-A and feature-B.
- Modified the Dockerfile in each branch differently:
> - feature-A: Added step-by-step comments
> - feature-B: Added slightly different comments

**Merging Steps:**
  
- Merged feature-A into main → merged cleanly
- Merged feature-B into main → merge conflict appeared in Dockerfile
- Resolved the conflict manually in GitHub by editing the file and committing the resolved version
- Screenshots of the merge conflict and resolution are included in the repo.
---

## Part 2: Docker & Containerization

## App
- `app.py` (Flask) and `requirements.txt` are included in the repo.

## Key Terms
- **Dockerfile**: A text file that contains step-by-step instructions to build a Docker image.
- **Docker image**: A ready-to-use, read-only package that contains your app code, runtime, libraries, and dependencies.
- **Docker container**: A running instance of a Docker image that executes your application in isolation.


## *Reduce Docker Image Size*

If your first build is too large, you can optimize it by following these steps:

- **Use lightweight base images:** `python:slim` or `alpine`  
- **Use multi-stage builds** for temporary build artifacts  
- **Install Python packages** with `--no-cache-dir`  
- **Remove build-time or temporary files** after installation  
- **Minimize the number of layers** in the Dockerfile  
- **Add a `.dockerignore` file** to exclude unnecessary files

## Run Locally & Push to DockerHub

**Build and run the Docker image locally:**

```bash
docker build -t lemici-flask:1.0 .
docker run --rm -p 5000:5000 lemici-flask:1.0
```
**Push the image to DockerHub:**

```bash
docker tag lemici-flask:1.0 syednasir9/lemici-flask:1.0
docker login
docker push syednasir9/lemici-flask:1.0
```

- After pushing, the image can be accessed from DockerHub: `docker pull syednasir9/lemici-flask:1.0`

---

## Part 3: Kubernetes (EKS Basics)

### Concepts

- **Pod:** The smallest deployable unit in Kubernetes; contains one or more containers sharing network and storage.  
- **Deployment:** A controller that manages pods, replica sets, and rolling updates to ensure the desired state is maintained.  
- **Service:** An abstraction to expose pods to the network. Types include:
  - `ClusterIP` — internal access within the cluster
  - `NodePort` — external access on a node port
  - `LoadBalancer` — external access via a cloud provider load balancer

### Why Use EKS / Managed Kubernetes?

- AWS handles the control plane (master components)  
- Simplifies upgrades and maintenance  
- Easy integration with AWS services (IAM, VPC, CloudWatch)  
- Improved security, reliability, and operational efficiency  

### Kubernetes YAML

- `k8s-deployment.yaml` included in the repo  
- Deploys the app with:
  - **2 replicas**  
  - **LoadBalancer service** to expose the app externally  
- (See the YAML file in the repo for full configuration)

---

## Part 4: CI/CD Pipeline

### Workflow:

- GitHub Actions workflow: `.github/workflows/ci.yml`  
- Steps included:
  1. **Build Docker image** from the Dockerfile  
  2. **Run simple tests** (simulated with `echo "Tests passed"`)  
  3. **Push Docker image** to DockerHub  

### Steps that would change if we wanted to deploy to Kubernetes after building:

- **Authenticate to the Kubernetes cluster**  
  - Store the `kubeconfig` file as a GitHub Secret (e.g., `KUBE_CONFIG`)  
  - In the workflow, write it to a temporary file and set `KUBECONFIG` to point to it:
    ```bash
    echo "${{ secrets.KUBE_CONFIG }}" > kubeconfig
    export KUBECONFIG=$(pwd)/kubeconfig
    ```
- **Deploy or update the application**  
  - Apply manifests directly: `kubectl apply -f k8s-deployment.yaml`  
  - Or update the image in an existing deployment:  
    ```bash
    kubectl set image deployment/lemici-flask-deployment lemici-flask=syednasir9/lemici-flask:1.0
    ```
 ---

## Part 5: Monitoring & Logs

### Metrics vs Logs vs Traces
- **Metrics:** Numeric time-series data, useful for alerts and dashboards.  
- **Logs:** Textual records of events, helpful for debugging issues.  
- **Traces:** End-to-end tracking of requests across multiple services, useful for performance analysis.

### Debugging a Crashed Pod
Commands:
```bash
# List pods and check their status
kubectl get pods

# Describe the pod for detailed info
kubectl describe pod <pod-name>

# Check logs of the crashed container
kubectl logs <pod-name> --previous

# Access the pod shell for live debugging
kubectl exec -it <pod-name> -- /bin/sh

# Check cluster events for recent failures
kubectl get events
```
### Recommended Monitoring Tools
- **Prometheus + Grafana:** Collect metrics and visualize them on dashboards.  
- **CloudWatch:** AWS-native service for metrics, logs, and alerts.  
- **ELK / OpenSearch:** Centralized logging solution for searching and analyzing logs.  
- **Jaeger / AWS X-Ray:** Distributed tracing to monitor requests across microservices.

---

## Part 6: Problem-Solving Scenario

You are asked to set up a new microservice in AWS EKS: 

**High-Level Approach:**  
1. **Containerize the application**  
   - Write a Dockerfile for the microservice.  
   - Build and test the Docker image locally.  

2. **Push the image to a registry**  
   - Tag the image and push to DockerHub or AWS ECR.  

3. **Set up Kubernetes deployment**  
   - Write deployment and service YAMLs for the microservice.  
   - Configure replicas and service type (LoadBalancer or ClusterIP).  

4. **CI/CD pipeline**  
   - Create a GitHub Actions workflow to:  
     - Build and push Docker image on code push or PR merge.  
     - Apply Kubernetes manifests using `kubectl` to update the deployment.  
     - Monitor deployment status and run basic health checks.  

5. **Logging and monitoring**  
   - Configure centralized logging (CloudWatch, ELK, or OpenSearch).  
   - Use Prometheus + Grafana for metrics dashboards. 

---

## Screenshots / Logs
All screenshots and logs are present in the `screenshots/` folder

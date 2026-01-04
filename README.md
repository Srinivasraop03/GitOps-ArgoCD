# 🚀 GitOps & Argo CD Automation

Welcome! This repository demonstrates a complete, automated **GitOps** workflow. It allows you to deploy microservices to Kubernetes just by pushing code to GitHub.

## 🌟 What is this?
**GitOps** means using Git as the "single source of truth" for your infrastructure.
- **You** push code changes to GitHub.
- **Automation** (GitHub Actions) builds the software and updates the configuration.
- **Argo CD** sees the change and automatically syncs your Kubernetes cluster to match.

**No manual `kubectl` commands needed to deploy!**

---

## 🛠️ How It Works (The Flow)

Here is the lifecycle of a change in this project:

1.  **Code Change**: You modify code in `service-a` and push to `main`.
2.  **Continuous Integration (CI)**:
    - **GitHub Actions** triggers automatically.
    - It builds a new **Docker Image**.
    - It pushes the image to Docker Hub.
    - It **updates the deployment file** (`deployment.yaml`) in the repository with the new image tag.
3.  **Continuous Deployment (CD)**:
    - **Argo CD** (running in your cluster) constantly watches this repository.
    - It detects the change in `deployment.yaml`.
    - It **automatically updates** the pods in your cluster to the new version.

---

## 📂 Project Structure

This playground is organized to make learning easy:

| Folder | Description |
| :--- | :--- |
| **`bootstrap/`** | **Start Here**. detailed setup files to install Argo CD itself into your cluster. |
| **`projects/`** | Defines "Projects" in Argo CD. Think of these as folders to group your apps and set permissions. |
| **`applicationsets/`** | The **Automation Engine**. This tells Argo CD to automatically find and deploy *all* applications in the `microservices-scaffold` folder. |
| **`microservices-scaffold/`** | Contains your actual applications (`service-a`, `service-b`, `service-c`). |
| **`ci-examples/`** | Example templates for CI/CD pipelines (GitHub Actions). |

---

## ⚡ Getting Started (Step-by-Step)

Follow these steps to get everything running from scratch.

### 1️⃣ Prerequisites
*   A running Kubernetes Cluster (Docker Desktop, Minikube, or Kind).
*   `kubectl` installed and configured.
*   `git` installed.

### 2️⃣ Install Argo CD (Bootstrap)
First, we install the Argo CD control plane.
```bash
# Apply the bootstrap configuration
kubectl apply -k bootstrap/
```
*Wait 1-2 minutes for the Argo CD pods to start.*

### 3️⃣ Configure the GitOps Engine
Now, tell Argo CD about our projects and where to find the apps.
```bash
# Create the Project definition
kubectl apply -f projects/

# Create the ApplicationSet (Start the automation)
kubectl apply -f applicationsets/
```

### 4️⃣ Access the Argo CD Dashboard
To see what's happening visually:

1.  **Get the Admin Password**:
    ```bash
    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
    ```
2.  **Connect to the UI**:
    ```bash
    kubectl port-forward svc/argocd-server -n argocd 8080:443
    ```
3.  **Open in Browser**: Go to [https://localhost:8080](https://localhost:8080).
    - **Username**: `admin`
    - **Password**: *(The output from step 1)*

---

## 🧪 How to Test the Flow

1.  **Modify a Service**: Open `microservices-scaffold/service-a/server.js` and change the message.
    ```javascript
    res.end('Hello from GitOps! Version: v2\n');
    ```
2.  **Push Changes**:
    ```bash
    git add .
    git commit -m "Update service A to v2"
    git push
    ```
3.  **Watch the Magic**:
    - Go to the **Actions** tab in GitHub to see the build.
    - Go to the **Argo CD UI** to see the app sync locally.
    - Refresh your app endpoint to see the new message!

---

## ❓ Troubleshooting

- **App not syncing?** Check the Argo CD UI for error messages.
- **GitHub Action failed?** Check the Docker Hub credentials in your GitHub Repository Secrets (`DOCKERHUB_USERNAME`, `DOCKERHUB_TOKEN`).

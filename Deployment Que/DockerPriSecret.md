Here is a **complete working example for a private Docker registry (Docker Hub or any private registry)** with full YAML ✅

***

## ✅ **Step 1: Create Secret for Docker Registry**

You can create it via command:

```bash
kubectl create secret docker-registry my-docker-secret \
  --docker-server=https://index.docker.io/v1/ \
  --docker-username=<your-docker-username> \
  --docker-password=<your-docker-password> \
  --docker-email=<your-email>
```

***

## ✅ **Step 2: Deployment YAML (Full)**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-private
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      imagePullSecrets:
        - name: my-docker-secret   # 👈 used for private registry auth

      containers:
        - name: nginx
          image: <your-docker-username>/nginx-private:latest
          imagePullPolicy: Always
          ports:
            - containerPort: 80
```

***

## ✅ **How it works**

1. Kubernetes reads:
   ```yaml
   imagePullSecrets
   ```
2. It uses that secret to:
   * Login to Docker registry
   * Pull the private image
3. Then container starts normally ✅

***

## ✅ **Important Notes**

* ✅ No volume mounting needed
* ✅ Secret is not inside container
* ✅ Used only during image pull

***

## ✅ **If using private registry (not DockerHub)**

Just change:

```bash
--docker-server=<your-registry-url>
```

Example:

```bash
--docker-server=https://myregistry.com
```

***

## ✅ **Common Errors**

| Error            | Reason                  |
| ---------------- | ----------------------- |
| ImagePullBackOff | Wrong credentials       |
| Unauthorized     | Wrong registry/username |
| ErrImagePull     | Image name incorrect    |

***

## ✅ **Quick Verify**

```bash
kubectl get pods
kubectl describe pod <pod-name>
```

***

If you want, I can give:
✅ YAML-only secret (no command)  
✅ GitHub Actions / CI-CD setup  
✅ Multi-registry setup

Just tell 👍

Below is a **fully production‑grade Kubernetes Deployment YAML** that includes almost every commonly used configuration:  
(resource limits, probes, security, rollout strategy, SHA-based image, etc.)

***

# ✅ ✅ Full Deployment YAML (Production Ready)

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment
  namespace: default
  labels:
    app: nginx
    tier: frontend
    env: production

spec:
  replicas: 3

  revisionHistoryLimit: 5   # Keep previous ReplicaSets for rollback

  selector:
    matchLabels:
      app: nginx

  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1

  template:
    metadata:
      labels:
        app: nginx
        tier: frontend

    spec:
      serviceAccountName: default

      terminationGracePeriodSeconds: 30

      containers:
      - name: nginx

        # ✅ Using SHA for immutability
        image: nginx@sha256:xxxxxxxxxnumberxxx
        imagePullPolicy: IfNotPresent

        ports:
        - containerPort: 80
          name: http

        # ✅ Resource limits & requests
        resources:
          requests:
            cpu: "100m"
            memory: "128Mi"
          limits:
            cpu: "500m"
            memory: "256Mi"

        # ✅ Environment variables
        env:
        - name: ENVIRONMENT
          value: "production"

        # ✅ Readiness Probe (traffic acceptance)
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 10
          timeoutSeconds: 2
          failureThreshold: 3
          successThreshold: 1

        # ✅ Liveness Probe (health check)
        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 10
          periodSeconds: 20
          timeoutSeconds: 2
          failureThreshold: 3

        # ✅ Startup probe (for slow start apps)
        startupProbe:
          httpGet:
            path: /
            port: 80
          failureThreshold: 30
          periodSeconds: 5

        # ✅ Security context (best practice)
        securityContext:
          runAsUser: 101
          runAsNonRoot: true
          allowPrivilegeEscalation: false
          readOnlyRootFilesystem: false

        # ✅ Volume Mount example
        volumeMounts:
        - name: nginx-config
          mountPath: /etc/nginx/conf.d

      # ✅ Pod-level security
      securityContext:
        fsGroup: 2000

      # ✅ Volumes (ConfigMap example)
      volumes:
      - name: nginx-config
        configMap:
          name: nginx-config-map

      # ✅ Scheduling constraints
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 100
            podAffinityTerm:
              labelSelector:
                matchLabels:
                  app: nginx
              topologyKey: "kubernetes.io/hostname"

      # ✅ Node selector (optional)
      nodeSelector:
        kubernetes.io/os: linux

      # ✅ Tolerations (optional)
      tolerations:
      - key: "node-role.kubernetes.io/control-plane"
        operator: "Exists"
        effect: "NoSchedule"
```

***

# ✅ 🔍 Full Explanation (Important Sections)

***

# 1. ✅ Metadata

```yaml
metadata:
  name: nginx-deployment
  labels:
```

* Identifies the deployment
* Labels help in grouping and selection

***

# 2. ✅ Replicas

```yaml
replicas: 3
```

* Runs **3 pods**
* Ensures high availability

***

# 3. ✅ Strategy (Rolling Update)

```yaml
strategy:
  type: RollingUpdate
```

* Updates pods **gradually**
* Avoids downtime
* `maxSurge: 1` → 1 extra pod allowed
* `maxUnavailable: 1` → 1 pod can go down

***

# 4. ✅ Image with SHA (IMMUTABLE)

```yaml
image: nginx@sha256:xxxxxxxxxnumberxxx
```

✅ Ensures:

* Exact version
* No surprise updates

***

# 5. ✅ Resource Management (Question 4)

```yaml
resources:
  requests:
  limits:
```

| Type     | Meaning            |
| -------- | ------------------ |
| requests | Minimum guaranteed |
| limits   | Maximum allowed    |

✅ Prevents:

* Resource overuse
* Cluster instability

***

# 6. ✅ Probes (Question 5)

### 🔹 Readiness Probe

```yaml
readinessProbe:
```

* Determines if pod can receive traffic
* If fails → removed from Service

***

### 🔹 Liveness Probe

```yaml
livenessProbe:
```

* Detects broken container
* Kubernetes restarts it

***

### 🔹 Startup Probe

```yaml
startupProbe:
```

* For slow apps
* Prevents premature restarts

***

# 7. ✅ Security Context

```yaml
securityContext:
```

✅ Enhances security:

* Runs as non-root user
* Prevents privilege escalation

***

# 8. ✅ Volumes

```yaml
volumes:
```

* Inject configs or secrets
* Example: ConfigMap for nginx config

***

# 9. ✅ Affinity / Anti-Affinity

```yaml
affinity:
```

✅ Ensures:

* Pods are spread across nodes
* Improves resilience

***

# 10. ✅ Node Selector & Tolerations

```yaml
nodeSelector:
tolerations:
```

✅ Control scheduling:

* Run only on specific nodes
* Allow pods on tainted nodes

***

# ✅ ✅ Key Takeaways

✔ Use **SHA instead of tags**  
✔ Always add **resource limits**  
✔ Use **probes for traffic + health**  
✔ Apply **security context**  
✔ Use **rolling updates**  
✔ Spread pods using **affinity**

***

# ✅ If You Want Next Level (Optional)

I can also help you with:

* ✅ Service YAML (Expose deployment)
* ✅ Ingress (Domain routing)
* ✅ HPA (Auto scaling)
* ✅ Helm chart version
* ✅ CI/CD pipeline integration

Just tell me 👍

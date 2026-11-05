# CST8915 Lab 6: Deploy Algonquin Pet Store to Azure Kubernetes Service (AKS)

| Student Name | Student ID | Course | Semester |
|---|---|---|---|
| Peng Wang | 041107730 | CST8915 Full-stack Cloud-native Development | Fall 2026 |

🎥 [Watch Demo Video](VIDEO_LINK)

---

## 1. Demo Video

The demo shows the deployed app on the AKS cluster, in this order.

The cluster and its two node pools:

```bash
kubectl get nodes
```

The four Pods and the Services, including the external IP of `store-front`:

```bash
kubectl get pods -o wide
```

```bash
kubectl get services
```

Open the Store Front at `http://<EXTERNAL-IP>`, then open `http://<EXTERNAL-IP>/products`.

Place an order on the website. A browser GET on `/orders` returns 404 because the Order Service accepts POST there, so the order is checked in the queue:

```bash
kubectl exec deployment/rabbitmq -- rabbitmqctl list_queues name durable messages
```

Open `http://<EXTERNAL-IP>/rabbitmq/` and show `order_queue` under Queues and Streams.

## 2. Lab Tasks: Updating API Endpoints

All paths share one IP address because `store-front` is the only Service of type `LoadBalancer`, and its Pod runs NGINX as a reverse proxy. The `store-front-L6` Dockerfile copies [`nginx.conf`](https://github.com/ramymohamed10/store-front-L6/blob/main/nginx.conf) into the image.

| Path | NGINX sends the request to | Why |
|---|---|---|
| `/` | Vue files in `/usr/share/nginx/html` | The website itself is served by this Pod. |
| `/products` | `http://product-service:3030/products` | The internal Service name resolves inside the cluster. |
| `/orders` | `http://order-service:3000/orders` | The internal Service name resolves inside the cluster. |
| `/rabbitmq/` | `rabbitmq:15672`, with `/rabbitmq` removed from the path | The dashboard expects paths that start at `/`. |

The backend Services are `ClusterIP`, so they have no public address and are reached only through this proxy.

## 3. Screenshots

### 3.1 Create the AKS Cluster

#### 3.1.1 Resource Group

![Resource Group](screenshots/01_resource_group.png)

#### 3.1.2 Cluster Basics, Top

The region changed to East US after this capture.

![Cluster Basics, Top](screenshots/02_cluster_basics_top.png)

#### 3.1.3 Cluster Basics, Bottom

![Cluster Basics, Bottom](screenshots/03_cluster_basics_bottom.png)

#### 3.1.4 D2as_v4 Not Available in Canada Central

![D2as_v4 Not Available in Canada Central](screenshots/04_d2as_v4_not_available_canada_central.png)

#### 3.1.5 masterpool

![masterpool](screenshots/05_masterpool.png)

#### 3.1.6 workerspool

![workerspool](screenshots/06_workerspool.png)

#### 3.1.7 Review + Create

![Review + Create](screenshots/07_review_create.png)

#### 3.1.8 Deployment Complete

![Deployment Complete](screenshots/08_deployment_complete.png)

### 3.2 Deploy the Application

#### 3.2.1 `az aks get-credentials`

![az aks get-credentials](screenshots/09_get_credentials.png)

#### 3.2.2 `kubectl get nodes`

![kubectl get nodes](screenshots/10_get_nodes.png)

#### 3.2.3 `kubectl apply`

![kubectl apply](screenshots/11_apply.png)

#### 3.2.4 `kubectl get pods`

![kubectl get pods](screenshots/12_get_pods.png)

#### 3.2.5 `kubectl get services`

![kubectl get services](screenshots/13_get_services.png)

#### 3.2.6 Services in the Portal

![Services in the Portal](screenshots/14_portal_services.png)

#### 3.2.7 Store Front

![Store Front](screenshots/15_store_front.jpg)

#### 3.2.8 Products

![Products](screenshots/16_products.png)

#### 3.2.9 RabbitMQ Overview

![RabbitMQ Overview](screenshots/17_rabbitmq_overview.png)

#### 3.2.10 RabbitMQ Queue

![RabbitMQ Queue](screenshots/18_rabbitmq_queue.png)

### 3.3 Explore Kubernetes

#### 3.3.1 `kubectl get pods -o wide`

![kubectl get pods -o wide](screenshots/19_get_pods_wide.png)

#### 3.3.2 `kubectl get pods --show-labels`

![kubectl get pods --show-labels](screenshots/20_get_pods_labels.png)

#### 3.3.3 `kubectl scale --replicas=3`

![kubectl scale --replicas=3](screenshots/21_scale_up.png)

#### 3.3.4 `kubectl rollout status`

![kubectl rollout status](screenshots/22_rollout_up.png)

#### 3.3.5 `kubectl get pods -l app=store-front -o wide`

![kubectl get pods -l app=store-front -o wide](screenshots/23_store_front_pods_wide.png)

#### 3.3.6 `kubectl get pods --watch`

![kubectl get pods --watch](screenshots/24_watch.png)

#### 3.3.7 `kubectl logs`

![kubectl logs](screenshots/25_logs.png)

#### 3.3.8 `kubectl scale --replicas=1`

![kubectl scale --replicas=1](screenshots/26_scale_down.png)

#### 3.3.9 `kubectl get pods -l app=store-front`

![kubectl get pods -l app=store-front](screenshots/27_store_front_pods.png)

#### 3.3.10 `kubectl rollout status`, after scaling down

![kubectl rollout status, after scaling down](screenshots/28_rollout_down.png)

### 3.4 kubectl Cheat Sheet

#### 3.4.1 `kubectl get all`

![kubectl get all](screenshots/29_get_all.png)

#### 3.4.2 `kubectl describe svc`

![kubectl describe svc](screenshots/30_describe_svc.png)

#### 3.4.3 `kubectl top nodes`

![kubectl top nodes](screenshots/31_top_nodes.png)

---

## Submission Details

| Item | Value |
|---|---|
| Cluster region | East US, because Standard_D2as_v4 is not available to this subscription in Canada Central |
| Resource group region | Canada Central |
| Node pools | `masterpool` (System) and `workerspool` (User), each 1 node of Standard_D2as_v4 |
| Manifest | `algonquin-pet-store-all-in-one.yaml` |

GenAI declaration: As permitted for labs in this course, Claude (Anthropic) was used to explain the steps, check portal settings and terminal output, and draft this README. The portal steps, commands and demo recording were done by the student.

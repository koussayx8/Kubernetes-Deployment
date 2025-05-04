Deployment Instructions
1.Create the namespace first:
```bash
kubectl apply -f namespace.yaml
```
2.Create ConfigMaps and Secrets:
```bash
kubectl apply -f configmaps-secrets/
```
3.Deploy the database:
```bash
kubectl apply -f database/
```
4.Deploy Eureka discovery service:
```bash
kubectl apply -f discovery/
```
5.Deploy all backend microservices:
```bash
kubectl apply -f backend/
```
6.Deploy the frontend:
```bash
kubectl apply -f frontend/
```


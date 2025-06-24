# Kubernetes Deployment for Outdoors Application

This repository contains the Kubernetes configuration files and deployment scripts for the Outdoors application, which consists of:
- [Outdoors-SpringBoot-MicroService](https://github.com/koussayx8/Outdoors-SpringBoot-MicroService) - Backend microservices
- [Outdoors-Angular](https://github.com/koussayx8/Outdoors-Angular) - Frontend Angular application

## Project Overview

The Outdoors application is a full-stack solution designed to help users discover and participate in outdoor activities and adventures. This repository specifically handles the containerization and Kubernetes deployment aspects of both the frontend and backend components.

## Repository Structure

```
Kubernetes-Deployment/
├── k8s/
│   ├── backend/        # Backend microservices configurations
│   │   ├── deployments/
│   │   ├── services/
│   │   └── configmaps/
│   ├── frontend/       # Frontend Angular configurations
│   │   ├── deployments/
│   │   └── services/
│   ├── ingress/        # Ingress configurations
│   └── secrets/        # Secret configurations (gitignored)
├── scripts/            # Deployment and maintenance scripts
└── docs/               # Additional documentation
```

## Prerequisites

- Docker installed locally
- kubectl CLI installed
- Access to a Kubernetes cluster
- Helm (optional, for package management)
- Access to both repositories:
  - Outdoors-SpringBoot-MicroService
  - Outdoors-Angular

## Deployment Instructions

### 1. Clone the repositories

```bash
# Clone the backend repository
git clone https://github.com/koussayx8/Outdoors-SpringBoot-MicroService.git

# Clone the frontend repository
git clone https://github.com/koussayx8/Outdoors-Angular.git

# Clone the deployment repository
git clone https://github.com/koussayx8/Kubernetes-Deployment.git
```

### 2. Build and push Docker images

#### Backend

```bash
cd Outdoors-SpringBoot-MicroService
# Build each microservice
for service in $(find . -name "pom.xml" -not -path "*/target/*" | xargs -n1 dirname | sort -u); do
  cd $service
  mvn clean package -DskipTests
  docker build -t outdoors-$(basename $service):latest .
  docker tag outdoors-$(basename $service):latest [your-registry]/outdoors-$(basename $service):latest
  docker push [your-registry]/outdoors-$(basename $service):latest
  cd ..
done
```

#### Frontend

```bash
cd ../Outdoors-Angular
npm install
npm run build --prod
docker build -t outdoors-angular:latest .
docker tag outdoors-angular:latest [your-registry]/outdoors-angular:latest
docker push [your-registry]/outdoors-angular:latest
```

### 3. Configure your deployment

Update the image references in the Kubernetes manifests to point to your Docker images:

```bash
cd ../Kubernetes-Deployment
# Edit the deployment files with your image references
```

### 4. Deploy to Kubernetes

```bash
# Create namespace
kubectl apply -f k8s/namespace.yaml

# Deploy configuration
kubectl apply -f k8s/configmaps/
kubectl apply -f k8s/secrets/

# Deploy backend services
kubectl apply -f k8s/backend/deployments/
kubectl apply -f k8s/backend/services/

# Deploy frontend application
kubectl apply -f k8s/frontend/deployments/
kubectl apply -f k8s/frontend/services/

# Deploy ingress rules
kubectl apply -f k8s/ingress/
```

## Architecture Overview

The deployment architecture consists of:

1. **Backend Microservices**:
   - Multiple Spring Boot services each with their own database
   - Service-to-service communication via RESTful APIs
   - Configured with appropriate resource limits and health checks

2. **Frontend Application**:
   - Angular SPA served through an Nginx container
   - Configured to communicate with backend services

3. **Infrastructure Components**:
   - Ingress controller for routing external traffic
   - Persistent volumes for database storage
   - ConfigMaps for environment-specific configuration

## Monitoring and Maintenance

To check the status of your deployment:

```bash
kubectl get pods -n outdoors
kubectl get services -n outdoors
kubectl get ingress -n outdoors
```

For logs:

```bash
# Backend microservice logs
kubectl logs -f deployment/[service-name] -n outdoors

# Frontend logs
kubectl logs -f deployment/outdoors-angular -n outdoors
```

## Scaling

The microservices can be scaled horizontally:

```bash
kubectl scale deployment [service-name] --replicas=3 -n outdoors
```

## CI/CD Integration

This repository can be integrated with CI/CD pipelines (GitHub Actions, Jenkins, etc.) to automate the deployment process. Sample workflow files can be found in the `.github/workflows/` directory.

## Troubleshooting

Common issues and their solutions:

- **Service Connection Issues**: Check that service discovery is working properly and ports are correctly exposed
- **Database Connection Problems**: Verify persistent volume claims and database credentials
- **Frontend 404 Errors**: Check the ingress configuration and ensure the Angular app is properly built

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## Related Projects

- [Outdoors-SpringBoot-MicroService](https://github.com/koussayx8/Outdoors-SpringBoot-MicroService) - Backend microservices
- [Outdoors-Angular](https://github.com/koussayx8/Outdoors-Angular) - Frontend application
- [OUTDOORS-Full-project](https://github.com/koussayx8/OUTDOORS-Full-project) - The main application repository


"Umbrella Chart"
 
yapp-helm/
├── Chart.yaml               # Root chart metadata
├── values.yaml              # Root common values or empty
├── values-dev.yaml          # Dev environment overrides
├── values-prod.yaml         # Prod environment overrides
├── charts/
│   ├── frontend/            # Subchart for frontend service
│   │   ├── Chart.yaml
│   │   ├── values.yaml
│   │   └── templates/
│   │       ├── deployment.yaml
│   │       ├── service.yaml
│   │       └── ingress.yaml
│   ├── backend/             # Subchart for backend service
│   │   ├── Chart.yaml
│   │   ├── values.yaml
│   │   └── templates/
│   │       ├── deployment.yaml
│   │       ├── service.yaml
│   │       └── ingress.yaml
│   └── mongo/               # Subchart for MongoDB
│       ├── Chart.yaml
│       ├── values.yaml
│       └── templates/
│           ├── deployment.yaml
│           ├── service.yaml
│           └── pvc.yaml

----------------------------------------------------------------------
"Monolithic Chart"

helm-chart/
├── Chart.yaml
├── values.yaml                # default values (optional)
├── values-dev.yaml            # development environment config
├── values-prod.yaml           # production environment config
├── templates/
│   ├── _helpers.tpl
│   ├── frontend/
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   │   └── ingress.yaml       # optional
│   ├── backend/
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   │   └── ingress.yaml       # optional
│   ├── mongo/
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   │   └── pvc.yaml          
│   └── secrets/
│       └── mongo-secret.yaml  


---------------------------------------------------------------------------
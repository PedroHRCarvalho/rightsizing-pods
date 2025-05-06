# Rightsizing PODs

Aplicação web para análise de recursos de PODs em Kubernetes, usando a API da Dynatrace.

## Pré-requisitos
- Python 3.11
- Docker
- Conta Dynatrace SaaS com token de API (`metrics.read`, `entities.read`)
- Cluster EKS configurado

## Configuração
1. Clone o repositório:
   ```bash
   git clone <repo-url>
   cd rightsizing-pods
   ```

2. Configure as variáveis de ambiente em `backend/.env`:
   ```
   TENANT=your-tenant-id
   DT_API_TOKEN=dt0c01.your-token
   PORT=8000
   ```

3. Construa e rode os contêineres localmente:
   ```bash
   cd backend
   docker build -t rightsizing-pods-backend .
   cd ../frontend
   docker build -t rightsizing-pods-frontend .
   docker run -d -p 8000:8000 --env-file backend/.env rightsizing-pods-backend
   docker run -d -p 80:80 rightsizing-pods-frontend
   ```

4. Acesse `http://localhost` no navegador.

## Deploy no EKS
1. Faça push das imagens para um registro (ex.: Amazon ECR).
2. Crie o segredo do token:
   ```bash
   kubectl create secret generic dynatrace-secret --from-literal=api-token=dt0c01.your-token
   ```
3. Aplique os arquivos Kubernetes:
   ```bash
   kubectl apply -f kubernetes/deployment.yaml
   kubectl apply -f kubernetes/service.yaml
   ```
4. Obtenha a URL do LoadBalancer:
   ```bash
   kubectl get svc rightsizing-pods-service
   ```

## Estrutura
- `backend/`: API FastAPI (Python).
- `frontend/`: Interface web (HTML/JS/Tailwind).
- `kubernetes/`: Configurações para EKS.
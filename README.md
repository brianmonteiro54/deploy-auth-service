# deploy-togglemaster-auth

Repositório GitOps do microsserviço `auth-service` do projeto ToggleMaster.

## Estrutura

- `.github/workflows/cd.yml`: validação dos manifests Kubernetes
- `manifests/`: arquivos YAML consumidos pelo ArgoCD
- `README.md`: documentação do repositório

## Fluxo GitOps

1. O pipeline do repositório da aplicação gera uma nova imagem Docker.
2. A imagem é publicada no registry.
3. A tag da imagem é atualizada em `manifests/deployment.yaml`.
4. O ArgoCD detecta a alteração neste repositório.
5. O ArgoCD sincroniza automaticamente o ambiente.

## Configuração atual

- Namespace: `togglemaster-auth`
- Serviço: `auth-service`
- Porta: `8001`
- Healthcheck: `/health`
- Hostname: `toggle.pt`
- Path público: `/auth`
- Réplicas: `2`

## Variáveis obrigatórias da aplicação

- `DATABASE_URL`
- `MASTER_KEY`

## Observações

- O `DATABASE_URL` é montado no `initContainer`.
- O `MASTER_KEY` é consumido via Kubernetes Secret.
- As credenciais do banco são materializadas via `ExternalSecret`.
- O arquivo `deployment.yaml` é o principal ponto atualizado pela pipeline da aplicação quando uma nova imagem for publicada.

## Pré-requisitos no cluster

- NGINX Ingress Controller
- External Secrets Operator
- ArgoCD instalado e configurado
- Secret `aws-credentials` no namespace `togglemaster-auth`
- Secret do RDS disponível no AWS Secrets Manager
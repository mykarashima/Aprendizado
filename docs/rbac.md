# RBAC
# Criando um usuário no Kubernetes com RBAC e gerando um kubeconfig

Vou guiá-lo através do processo de criar um usuário chamado "marcio" com permissão de "view" no namespace "vivo" e extrair as credenciais para um arquivo kubeconfig.

## Passo 1: Criar certificados para o usuário

Primeiro, precisamos gerar um certificado para o usuário marcio:

```bash
# Criar chave privada
openssl genrsa -out marcio.key 2048

# Criar CSR (Certificate Signing Request)
openssl req -new -key marcio.key -out marcio.csr -subj "/CN=marcio/O=vivo"

# Assinar o CSR com a CA do cluster (ajuste os caminhos conforme seu cluster)
openssl x509 -req -in marcio.csr -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -out marcio.crt -days 365
```

## Passo 2: Criar as regras RBAC

Crie um arquivo chamado `rbac-marcio-view.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: vivo
  name: viewer-role
rules:
- apiGroups: ["*"]
  resources: ["*"]
  verbs: ["get", "list", "watch"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  namespace: vivo
  name: viewer-binding
subjects:
- kind: User
  name: marcio
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: viewer-role
  apiGroup: rbac.authorization.k8s.io
```

Aplique o RBAC:

```bash
kubectl apply -f rbac-marcio-view.yaml
```

## Passo 3: Criar o arquivo kubeconfig para o usuário marcio

```bash
# Defina variáveis (ajuste conforme seu cluster)
CLUSTER_NAME="seu-nome-do-cluster"  # Pegue com: kubectl config get-contexts
SERVER_URL="https://<endereço-do-kubernetes-api>:6443"  # Pegue com: kubectl cluster-info

# Criar o kubeconfig
kubectl config set-credentials marcio \
  --client-certificate=marcio.crt \
  --client-key=marcio.key \
  --embed-certs=true \
  --kubeconfig=marcio.kubeconfig

kubectl config set-cluster $CLUSTER_NAME \
  --server=$SERVER_URL \
  --certificate-authority=/etc/kubernetes/pki/ca.crt \
  --embed-certs=true \
  --kubeconfig=marcio.kubeconfig

kubectl config set-context marcio-context \
  --cluster=$CLUSTER_NAME \
  --namespace=vivo \
  --user=marcio \
  --kubeconfig=marcio.kubeconfig

kubectl config use-context marcio-context \
  --kubeconfig=marcio.kubeconfig
```

## Passo 4: Verificar as permissões

Você pode testar o novo kubeconfig com:

```bash
kubectl get pods --namespace=vivo --kubeconfig=marcio.kubeconfig
```

O usuário marcio agora tem permissões de visualização (view) no namespace vivo e pode usar o arquivo `marcio.kubeconfig` para acessar o cluster.

## Observações importantes:

1. Certifique-se de que o namespace "vivo" já existe no cluster. Se não existir, crie-o primeiro com `kubectl create namespace vivo`.

2. O caminho para os arquivos da CA pode variar dependendo da sua instalação do Kubernetes. Ajuste conforme necessário.

3. Para ambientes de produção, considere usar um sistema mais robusto como OpenID Connect para autenticação de usuários.

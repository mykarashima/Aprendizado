## 1. Criar o Role

Primeiro, vamos criar um Role com permissões de leitura:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: infraestrutura
  name: vendor-readonly
rules:
- apiGroups: ["*"]
  resources: ["*"]
  verbs: ["get", "list", "watch"]
```

## 2. Criar o RoleBinding

Agora, vamos vincular o Role ao usuário "vendor":

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: vendor-readonly-binding
  namespace: infraestrutura
subjects:
- kind: User
  name: vendor
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: vendor-readonly
  apiGroup: rbac.authorization.k8s.io
```

Aplique essas configurações usando:

```bash
kubectl apply -f vendor-role.yaml
kubectl apply -f vendor-rolebinding.yaml ```

## 3. Gerar o kubeconfig

Para gerar o kubeconfig para o usuário "vendor", siga estes passos:

1. Gere um certificado para o usuário:

```bash
openssl genrsa -out vendor.key 2048
openssl req -new -key vendor.key -out vendor.csr -subj "/CN=vendor/O=infraestrutura"
```

2. Assine o certificado com a CA do cluster (substitua os caminhos conforme necessário):

```bash
sudo openssl x509 -req -in vendor.csr -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -out vendor.crt -days 365 ```

3. Crie o kubeconfig:

```bash
KUBECONFIG_FILE=vendor-kubeconfig

kubectl config set-cluster meu-cluster --server=https://seu-servidor-api:6443 --certificate-authority=/etc/kubernetes/pki/ca.crt --kubeconfig=$KUBECONFIG_FILE

kubectl config set-credentials vendor --client-certificate=vendor.crt --client-key=vendor.key --kubeconfig=$KUBECONFIG_FILE

kubectl config set-context vendor-context --cluster=meu-cluster --namespace=infraestrutura --user=vendor --kubeconfig=$KUBECONFIG_FILE

kubectl config use-context vendor-context --kubeconfig=$KUBECONFIG_FILE ```

Substitua "meu-cluster" e "https://seu-servidor-api:6443" pelos valores corretos do seu cluster.

4. Ajuste as permissões do arquivo:

```bash
chmod 600 $KUBECONFIG_FILE
```

Agora você tem um arquivo kubeconfig (`vendor-kubeconfig`) que pode ser enviado ao usuário "vendor". Este arquivo permitirá que o usuário acesse o namespace "infraestrutura" com permissões somente de leitura.

## Observações importantes

1. Mantenha a chave privada (vendor.key) e o kubeconfig seguros.
2. O usuário terá acesso somente de leitura a todos os recursos no namespace "infraestrutura".
3. Certifique-se de que o namespace "infraestrutura" existe no cluster.
4. O processo de assinatura do certificado pode variar dependendo da configuração do seu cluster.

Lembre-se de testar o acesso do usuário para garantir que as permissões estejam corretas e limitadas conforme desejado.

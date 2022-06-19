# Day 04 - Configurando Istio via Helm

> Verifique antes se não existe nenhum componente já instalado do Istio para ser removido.

- Instalar todas os CRDs necessários:
```
for i in istio-1.1.9/install/kubernetes/helm/istio-init/files/crd*yaml; do kubectl apply -f $i; done
```
- Download do Helm e descompactação
```
wget https://get.helm.sh/helm-v3.9.0-linux-amd64.tar.gz
tar -zxvf helm-v3.9.0-linux-amd64.tar.gz 
```
- Mover os binários para o local/bin
```
sudo mv linux-amd64/helm /usr/local/bin
```
- Inicializa o Helm:
```
helm init
```

# Egress

> Usando o container Sleep como exemplo
Há duas formas de permitir as saídas das informações de meus containers:
- ALLOW_ANY
- REGISTRY_ONLY

Presente no configmap Istio:
```
kubectl get configmaps istio -n istio-system -o yaml | grep -o "mode: ALLOW_ANY" 
```
Testando uma conexão do container com algum link externo:
```
kubectl exec -ti sleep-698cfc4445-tnfxq -c sleep -- curl -I https://www.google.com
```
Editando manualmente o configmap - Exemplo 01:
```
kubectl edit configmaps istio -n=istio-system
```
Editando manualmente o configmap - Exemplo 02:
```
kubectl get configmaps istio -n istio-system -o yaml | sed 's/mode: ALLOW_ANY/mode: REGISTRY_ONLY/g' | kubectl replace -n istio-system -f -
```

## ServiceEntry
> Exemplo usando as aplicações Sleep e Http/bin. Arquivos: libera_httpbin.yaml e libera_httpbin_https.yaml
- Configuração necessária quando existe a necessidade de meu container se comunicar com alguma aplicação externa ao meu Service Mesh e nas configurações estiver definido como REGISTRY_ONLY. Configuração de exemplo para HTTP e HTTPS.
```
kubectl exec -ti sleep-698cfc4445-tnfxq -c sleep -- curl -I http://httpbin.org
```

# RetryPolicy
> Exemplo usando a aplicação HelloWorld, definindo 5 tentativas de conexão com 2 segundos de timeout. Arquivos: destination_rule_retry_policy.yaml e vs_retry_policy.yaml
# Day 01 - Configuração do ambiente

## Download e instalação da última versão do Istio

- Download:
```
curl -L https://istio.io/downloadIstio | sh - 
``` 
- Configurando o istioctl como variável de ambiente:
```
export PATH="$PATH:/home/felipe/Documentos/workspace/service-mesh-istio-linuxtips/istio-1.14.0/bin"
```
ou

```
sudo cp istio-1.14.0/bin/istioctl /usr/local/bin
```

## Configurando a Demo Profile

- Instalação:
```
istioctl install --set profile=demo -y
```
- Configurando a label default para o Istio, habilitando em um determinado namespace
```
kubectl label namespace default istio-injection=enabled
```
- Verificar os pods do Istio em execução:
```
kubectl get pods -n=istio-system
```

## Configurando a imagem de exemplo BookInfo:
- Executando arquivo Yaml do container:
```
kubectl apply -f istio-1.14.0/samples/bookinfo/platform/kube/bookinfo.yaml
```
- Verifica se todos o serviços da aplicação está em execução corretamente:
```
kubectl exec "$(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -c ratings -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"
```
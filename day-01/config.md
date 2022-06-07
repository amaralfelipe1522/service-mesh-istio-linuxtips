# Configuração do ambiente

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

## Configurando Gateway e Ingress Service
- Inicializa o Gateway e Virtual Service da aplicação:
```
kubectl apply -f istio-1.14.0/samples/bookinfo/networking/bookinfo-gateway.yaml 
```
- Usando cluster local (Kind) armazene algumas variáveis de ambiente necessárias para o Ingress do Gateway:
```
export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')
export SECURE_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="https")].nodePort}')
export INGRESS_HOST=$(kubectl get po -l istio=ingressgateway -n istio-system -o jsonpath='{.items[0].status.hostIP}')
export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT
```
- Verifica se as variáveis de ambiente foram instânciadas corretamente:
```
echo $GATEWAY_URL
echo "http://$GATEWAY_URL/productpage"
```

## AddOns - Dashboards
- Instalação:
```
kubectl apply -f istio-1.14.0/samples/addons
kubectl get pods -n=istio-system
```
- Acesso ao Kiali usando Istio:
```
istioctl dashboard (grafana, kiali, prometheus ou jaeger)
```
- Teste de acessos para acompanhar no Dashboard (bash):
```
for i in $(seq 1 100); do curl -s -o /dev/null "http://$GATEWAY_URL/productpage"; done
```
# Day 02 - Virtual Services, Gateway e Destination Rules

## Configurando Gateway e Virtual Service
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

## Instalando Virtual Service e Destination Rules
```
kubectl apply -f istio-1.14.0/samples/bookinfo/networking/virtual-service-all-v1.yaml

kubectl get virtualservice

kubectl apply -f istio-1.14.0/samples/bookinfo/networking/destination-rule-all.yaml

kubectl get destinationrule
```

> Foi necessário alterar o arquivo *virtual-service-all-v1.yaml* para que a Destination Rule acesse as outras versões do serviço de Reviews do exemplo do BookInfo e saiba qual o "peso" da distribuição de cargas para cada um.
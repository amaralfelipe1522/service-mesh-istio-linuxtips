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

> Foi necessário alterar o arquivo *virtual-service-all-v1.yaml* para que a Destination Rule acesse as outras versões do serviço de Reviews do exemplo do BookInfo e saiba qual o "peso" da distribuição de cargas para cada um

## Políticas e Segurança
Ambiente usado no exemplo:
- Criação de 3 namespaces
```
kubectl create ns ns-teste-1
kubectl create ns ns-teste-2
kubectl create ns ns-teste-3
```
- Aplicar apenas em 2 namespaces as labels para que os sidecars (envoys)
```
kubectl label namespace ns-teste-1 istio-injection=enabled
kubectl label namespace ns-teste-1 istio-injection=enabled
```
- Inicializa o exemplo HTTPBIN nas 3 namespaces
```
kubectl apply -f istio-1.14.0/samples/httpbin/httpbin.yaml -n ns-teste-1
kubectl apply -f istio-1.14.0/samples/httpbin/httpbin.yaml -n ns-teste-2
kubectl apply -f istio-1.14.0/samples/httpbin/httpbin.yaml -n ns-teste-3
```
- Inicializa o exemplo SLEEP nas 3 namespaces
```
kubectl apply -f istio-1.14.0/samples/sleep/sleep.yaml -n ns-teste-1
kubectl apply -f istio-1.14.0/samples/sleep/sleep.yaml -n ns-teste-2
kubectl apply -f istio-1.14.0/samples/sleep/sleep.yaml -n ns-teste-3
```
- Realiza um teste de conexão via cURL entre o SLEEP e o HTTPBIN de namespaces diferentes, para que traga a variável *http_code*
```
kubectl exec -ti -n ns-teste-2 sleep-698cfc4445-tvgpx -- curl http://httpbin.ns-teste-1:8000/ip -s -o /dev/null -w "%{http_code}\n"
```
- Verifica as políticas atuais
```
kubectl get policies.authentication.istio.io --all-namespaces
kubectl get meshpolicies.authentication.istio.io
```
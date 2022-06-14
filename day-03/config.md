# Day 03 - Políticas e Segurança

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
- Executa o arquivo Meshpolicy
```
kubectl apply -f day-03/meshpolicy.yaml
```
- Uma de minhas tentativas em fazer funcionar o MeshPolicy
```
istioctl manifest apply --set profile=demo \    
  --set values.global.mtls.auto=true --force \
  --set values.global.mtls.enabled=false --force
```

// 21:36
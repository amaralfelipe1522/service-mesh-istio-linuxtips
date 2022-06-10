# Cheatsheet Istio

- Analisa se tem algum problema nos serviços do Istio
```
istioctl analyze
```

kubectl rollout restart deployment istio-egressgateway -n=istio-system
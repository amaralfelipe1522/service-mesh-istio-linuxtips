# Cheatsheet Istio

- Analisa se tem algum problema nos serviços do Istio
```
istioctl analyze
```

- Reiniciar um deployment:
```
kubectl rollout restart deployment istio-egressgateway -n=istio-system
```

- Desinstalar o Istio do cluster:
```
istioctl x uninstall --purge
```
# Service Mesh - Istio

## O que é Service Mesh?
Uma service mesh é uma camada de infraestrutura incorporada a uma aplicação que documenta como os serviços interagem, facilitando a comunicação e eliminando o downtime. Responsável por gerenciar, observar e controlar sua malha de micro serviços.

## Istio
Conjunto de ferramentas Open Sources que cobre os pilares do Service Mesh.

## Envoy
Camada proxy presente no Pod da aplicação que irá interceptar as comunicações de entrada e saída para cada micro serviço e que são armazenadas no Control Plane.

## Control Plane - Istiod
Camada que armazena e gerencia as informações das comunicações ocorridas entre os micro serviços e a partir disso, disponibiliza-las para outras ferramentas, como por exemplo Grafana e Kiali. É composto por:
- Pilot;
- Citadel;
- Gallery.


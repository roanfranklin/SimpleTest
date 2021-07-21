### 🛠 Pré-requesitos

docker
minikube

### 🛠 Lista Comandos

1º Iniciar o minkube com o comando:

```bash
minikube start --driver=none
```

Depois de iniciado o minikube, verifique algumas coisas, como addons, informações do minikube e containers carregados por padrão:

```bash
minikube addons list

minikube ip

kubectl get all --all-namespaces

kubectl get namespaces
```

2º Ativa o modulo de metricas para funcionamento do HPA - HorizontalPodAutoescaler:

```bash
minikube addons enable metrics-server
```

3º Ativa o modulo ingress para uso de uma entrada como Load Balancer:

```bash
minikube addons enable ingress
```

4º O Ingress foi carregado, porém ao dar um describe para ver as configurações do mesmo, aparece um erro de redirecioanamento. Então para funcionar é só carregar os dois manifestos abaixo.
```bash
kubectl apply -f https://raw.githubusercontent.com/roelal/minikube/5093d8b21c0931a6c63fa448538761b4bf100ee0/deploy/addons/ingress/ingress-rc.yaml
kubectl apply -f https://raw.githubusercontent.com/roelal/minikube/5093d8b21c0931a6c63fa448538761b4bf100ee0/deploy/addons/ingress/ingress-svc.yaml
```

5º Aplicar os manifestos das aplicações:

```bash
kubectl apply -f SimpleWhale/namespace.yaml
kubectl apply -f SimpleWhale/deployment.yaml
kubectl apply -f SimpleWhale/hpa.yaml
kubectl apply -f SimpleWhale/service.yaml
```

```bash
kubectl apply -f RacingCarAnimation/namespace.yaml
kubectl apply -f RacingCarAnimation/deployment.yaml
kubectl apply -f RacingCarAnimation/hpa.yaml
kubectl apply -f RacingCarAnimation/service.yaml
```

6º Aplicar os manifestos para Traefik + Ingress Controller

```bash
kubectl apply -f Traefik/traefik-cr.yaml
kubectl apply -f Traefik/traefik-crb.yaml
kubectl apply -f Traefik/traefik-deployment.yaml
kubectl apply -f Traefik/traefik-ingress.yaml
kubectl apply -f Traefik/traefik-service-acc.yaml
kubectl apply -f Traefik/traefik-service.yaml
```

7º Adicionar todos as URLs de acesso no /etc/hosts.

```bash
echo "$(minikube ip) traefik.minikube.local whale.minikube.local car.minikube.local" | sudo tee -a /etc/hosts
```

OBS.: Caso voce esteja utilizando o Windows, acredito que esse processo de setar o hosts, terá que ser um pouco manual. Como, acessar pelo Windows Explorer o diretório "C:/Windows/System32/drivers/etc/" copie o arquivo "hosts" para a "Área de trabalho", abra o arquivo e adicione no final deste arquivo "IP_MINIKUBE traefik.minikube.local whale.minikube.local car.minikube.local". Salve, e depois copie esse arquivo para o diretório padrão "C:/Windows/System32/drivers/etc/".

Pronto, agora é só acessar pelo seu browser as 3 URLs.

8º Monitoramento - Alguns comandos kubectl:

```bash
kubectl get all --all-namespaces

kubectl get deploy,pods,svc,hpa,ing -n hml

kubectl get deploy,pods,svc,hpa,ing -n hml -o wide

watch -n1 "kubectl get deploy,pods,svc,hpa,ing -n hml -o wide"
```

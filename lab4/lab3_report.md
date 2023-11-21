University: [ITMO University](https://itmo.ru/ru/)
Faculty: [FICT](https://fict.itmo.ru)
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)
Year: 2023/2024
Group: K4110c
Author: Urbaev Maxim Gennadievich
Lab: Lab1
Date of create: 13.11.2023
Date of finished: 


# CNI, CoreDNS

minikube start --network-plugin=cni --cni=calico --nodes 2

minikube kubectl -- get pods -l k8s-app=calico-node -A -w

![Alt text](image.png)

minikube kubectl -- label node minikube zone=east

minikube kubectl -- label node minikube-m02 zone=west

Был найден существующий ippool из которого я нашел нужную crd
![Alt text](image-1.png)

minikube kubectl -- delete ippool default-ipv4-ippool

![Alt text](image-2.png)

![Alt text](image-3.png)

---

Пинги не проходят(

minikube kubectl -- apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.4/manifests/calico.yaml

Не в этом дело
---

kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.4/manifests/apiserver.yaml

![Alt text](image-4.png)

![Alt text](image-5.png)

openssl req -x509 -nodes -newkey rsa:4096 -keyout apiserver.key -out apiserver.crt -days 365 -subj "/" -addext "subjectAltName = DNS:calico-api.calico-apiserver.svc"

kubectl create secret -n calico-apiserver generic calico-apiserver-certs --from-file=certs/apiserver.key --from-file=certs/apiserver.crt

<!-- kubectl patch apiservice v3.projectcalico.org -p "{\"spec\": {\"caBundle\": \"$(kubectl get secret -n calico-apiserver calico-apiserver-certs -o go-template='{{ index .data \"apiserver.crt\" }}')\"}}" -->

minikube kubectl -- patch apiservice v3.projectcalico.org --patch-file .\Patch.yaml

api-server pod поднялся (потому что я стукнул его после патча)

![Alt text](image-6.png)

УРАААААА

![Alt text](image-7.png)

![Alt text](image-8.png)
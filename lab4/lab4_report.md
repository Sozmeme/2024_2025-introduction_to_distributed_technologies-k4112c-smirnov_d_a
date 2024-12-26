University: [ITMO University](https://itmo.ru/ru/)\
Faculty: [FICT](https://fict.itmo.ru)\
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)\
Year: 2024/2025\
Group: K4112c\
Author: Smirnov Daniil Aleksandrovich\
Lab: Lab4\
Date of create: 04.12.2023\
Date of finished: 26.12.2024

### Цель работы
Познакомиться с сертификатами и "секретами" в Minikube, правилами безопасного хранения данных в Minikube. 

### Ход работы

1. Запустил Minikube с использованием CNI Calico и режимом Multi-Node Clusters, чтобы создать кластер с двумя нодами. Для этого выполнил команду `minikube start --nodes=2 --network-plugin=cni --cni=calico `
2. Проверил поды и ноды с помощью `minikube kubectl -- get pods -n kube-system -l k8s-app=calico-node` и `minikube kubectl -- get nodes`
   
   ![image](https://github.com/user-attachments/assets/f3b26c6d-eeef-4caf-92bd-9c209ef65877)
   
3. Назначил метки нодам по географическому признаку `minikube kubectl -- label node minikube location=spb` `minikube kubectl -- label node minikube-m02 location=msc`
   ![image](https://github.com/user-attachments/assets/41913cc3-c6b5-4209-adf3-71371fa7290d)

4. Удалил установленные по умолчанию пулы `minikube kubectl -- delete ippools default-ipv4-ippool`
   ![image](https://github.com/user-attachments/assets/c805da58-7cf6-4add-8fed-b98aed2a4faf)
   
5. Cоздал и применил манифест для Calico, который назначает IP-адреса подам на основе меток нод:
```yaml
apiVersion: crd.projectcalico.org/v1
kind: IPPool
metadata:
  name: spb-pool
spec:
  cidr: 192.168.1.0/24
  nodeSelector: location == "spb"
  ipipMode: Always
  natOutgoing: true
---
apiVersion: crd.projectcalico.org/v1
kind: IPPool
metadata:
  name: msc-pool
spec:
  cidr: 192.168.2.0/24
  nodeSelector: location == "msc"
  ipipMode: Always
  natOutgoing: true

```
![image](https://github.com/user-attachments/assets/ae2d93cf-1313-44b7-889f-e15507629e14)

6. Cоздал и применил Deployment с двумя репликами контейнера ifilyaninitmo/itdt-contained-frontend:master и передал переменные окружения:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: lab4
spec:
  replicas: 2
  selector:
    matchLabels:
      app: lab4
  template:
    metadata:
      labels:
        app: lab4
    spec:
      containers:
        - name: frontend
          image: ifilyaninitmo/itdt-contained-frontend:master
          ports:
          - containerPort: 3000
          env:
            - name: REACT_APP_USERNAME
              value: "SDA"
            - name: REACT_APP_COMPANY_NAME
              value: "SDA enterprise"
```
![image](https://github.com/user-attachments/assets/71f777a1-b7b2-4005-ade6-a894e3bd3406)

7. создал сервис для доступа к подам с помощью `minikube kubectl -- expose deployment lab4 --port=3000 --name=lab4-service --type=ClusterIP`, пробросил порты `minikube kubectl -- port-forward service/lab4-service 3000:3000` и проверил результат:
   
![image](https://github.com/user-attachments/assets/36c09df4-e6fd-4346-b89e-27b120117d2d)

8. Посмотрел IP подов с помощью `minikube kubectl -- get pods -o wide`, пинганул их с помощью `minikube kubectl -- exec -it lab4-5cfc7799dd-2lng8 -- ping 192.168.2.4` и `minikube kubectl -- exec -it lab4-5cfc7799dd-tjwfw -- ping 192.168.1.64` соответственно:

![image](https://github.com/user-attachments/assets/9917d580-6e66-43f6-8f08-0344acf4e3ca)

9. Схема организации контейнеров

![image](https://github.com/user-attachments/assets/a2b46f81-32dd-4e61-9e55-879327b3d135)




 

University: [ITMO University](https://itmo.ru/ru/)\
Faculty: [FICT](https://fict.itmo.ru)\
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)\
Year: 2024/2025\
Group: K4112c\
Author: Smirnov Daniil Aleksandrovich\
Lab: Lab3\
Date of create: 04.12.2024\
Date of finished: ...\

### Цель работы
Познакомиться с сертификатами и "секретами" в Minikube, правилами безопасного хранения данных в Minikube. 

### Ход работы
1. ConfigMap используется для хранения переменных окружения. Создал и применил файл configmap.yaml со следующим содержимым:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: lab3-conf
data:
  REACT_APP_USERNAME: "SDA"
  REACT_APP_COMPANY_NAME: "SDA enterprise"
```
![image](https://github.com/user-attachments/assets/945f43cb-76b7-4f2a-8b65-d3c2c7eba08e)

2. ReplicaSet обеспечивает запуск и управление репликами Pod'ов. Создал и применил файл replicaset.yaml со следующим содержимым:
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: lab3-replicaset
spec:
  replicas: 2
  selector:
    matchLabels:
      app: lab3
  template:
    metadata:
      labels:
        app: lab3
    spec:
      containers:
      - name: frontend
        image: ifilyaninitmo/itdt-contained-frontend:master
        envFrom:
        - configMapRef:
            name: lab3-conf
        ports:
        - containerPort: 3000
```
![image](https://github.com/user-attachments/assets/69440c64-a3e8-4765-abdf-004823455142)

3. Ingress позволяет настроить доступ к сервисам через HTTP/HTTPS. Включил Ingress в Minikube `minikube addons enable ingress`:
![image](https://github.com/user-attachments/assets/3531c32e-7a50-41a4-821b-6dcf3a997b8b)

4. Для работы с HTTPS нужно сгенерировать TLS сертификат. Сделал его с помощью `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=sdacomp.com"`
![image](https://github.com/user-attachments/assets/8a00831b-de87-42ac-91d7-1f056ab2338d)

5. Secret хранит конфиденциальные данные, такие как пароли, токены, ключи и сертификаты. Он позволяет изолировать чувствительную информацию от кода и конфигураций. Создал Secret с помощью`minikube kubectl -- create secret tls tls-secret --key tls.key --cert tls.crt`:
![image](https://github.com/user-attachments/assets/1c3088b9-ac8f-467f-b452-7fddefd86b8f)

6. Создал сервис `minikube kubectl -- expose replicaset lab3-replicaset --port=3000 --name=lab3-service --type=ClusterIP`:
![image](https://github.com/user-attachments/assets/e6e369e5-9356-4954-a6bc-9ddaba91c59b)

7. Объект Ingress, который будет использовать сервис, описал манифестом и созадл его с помощью `minikube kubectl -- apply -f ingress.yaml`:
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: lab3-ingress
spec:
  tls:
  - hosts:
    - sdacomp.com
    secretName: tls-secret
  rules:
  - host: sdacomp.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: lab3-service
            port:
              number: 3000

```
8. Настроил hosts, чтобы сервис работал на заданном домене, для этого посмотрел IP, на котором работает minikube, и добавил запись в /etc/hosts:
![image](https://github.com/user-attachments/assets/4098ba6b-f91d-4a43-ae2d-c4d3d8b505e6)

9. В браузере обратился к сервису по домену sdacomp.com, получил страницу, на которой можно увидеть прописанные в ConfigMap переменные:
![image](https://github.com/user-attachments/assets/9dcf0468-ff65-45e4-82ad-410b8220c8f3)

10. Возможно посмотреть информацию о безопасном соединении и увидеть наличие сертификата. При этом браузер жалуется на небезопасное соединение:
![image](https://github.com/user-attachments/assets/750e9078-be41-4fba-ab81-400185ad4068)

11. Диаграмма организации:
![image](https://github.com/user-attachments/assets/111161dc-1ab2-4f38-b253-97cd0ecf82d5)





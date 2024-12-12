University: [ITMO University](https://itmo.ru/ru/) \
Faculty: [FICT](https://fict.itmo.ru) \
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies) \
Year: 2024/2025 \
Group: K4112c \
Author: Smirnov Daniil Aleksandrovich \
Lab: Lab2 \
Date of create: 04.12.2024 \
Date of finished: 12.12.2024

### Цель работы
Ознакомиться с типами "контроллеров" развертывания контейнеров, ознакомится с сетевыми сервисами и развернуть свое веб приложение.

### Ход работы

1. С помощью `docker pull ifilyaninitmo/itdt-contained-frontend` скачал образ контейнера
2. Написал манифест для Deployment. Deployment в Kubernetes — это ресурс, который отвечает за развертывание, обновление и масштабирование подов с контейнерами. Он обеспечивает контроль за состоянием приложения, поддерживая заданное количество подов и упрощая управление версиями через обновления или откаты \
Текст манифеста:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: lab2
spec:
  replicas: 2
  selector:
    matchLabels:
      app: lab2
  template:
    metadata:
      labels:
        app: lab2
    spec:
      containers:
        - name: lab2_frontend
          image: ifilyaninitmo/itdt-contained-frontend:master
          env:
            - name: REACT_APP_USERNAME
              value: "SDA"
            - name: REACT_APP_COMPANY_NAME
              value: "SDA enterprise"

```
`kind` указывает тип ресурса Kubernetes, `replicas` задаёт количество подов, которые нужно запустить, `env` определяет переменные окружения для контейнера - они передаются внутрь контейнера и могут использоваться приложением.

3. С помощью `minikube kubectl -- apply -f deployment.yaml` применил deployment:
   
![image](https://github.com/user-attachments/assets/0f061305-3342-43d7-8597-9f6bc280aff7)

4. проверил создание объектов с помощью `minikube kubectl get deployment` и `minikube kubectl get pods`:
   
![image](https://github.com/user-attachments/assets/7886a2db-1a83-4205-bee5-160bbfbcd83a)
![image](https://github.com/user-attachments/assets/08221bf1-4f07-478f-8f80-9d28745be6fe)

5. Поднял сервис `minicube kubectl -- expose deployment lab 2 --type=Node Port --port=3000`, пробросил порты `minicube kubectl -- port-forward service/lab 2 3000:3000`, получил доступ к сервису через браузер по localhost:3000:
   ![image](https://github.com/user-attachments/assets/b9bdb31a-fec8-4736-8b26-368f5d301525)
   Можно увидеть переменные окружения, заданные ранее в deployment.yaml - они не изменяются, название контейнера, в свою очередь, может меняться в зависимости от используемого контейнера.

6. Посмотрел логи для подов с помощью `minicube kubectl --
logs lab2-685b59d484-5qmmj` и `minicube kubectl --
logs lab2-685b59d484-vgvjk`:
![image](https://github.com/user-attachments/assets/320bfbeb-da80-4bd4-b232-df4ae20531bf)

7. Схема организации контейеров и сервисов:
   ![image](https://github.com/user-attachments/assets/3abaf554-75ab-47d5-832a-869dc6167c36)



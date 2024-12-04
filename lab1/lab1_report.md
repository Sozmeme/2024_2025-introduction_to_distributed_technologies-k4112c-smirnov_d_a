University: [ITMO University](https://itmo.ru/ru/) \
Faculty: [FICT](https://fict.itmo.ru) \
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies) \
Year: 2024/2025 \
Group: K4112c \
Author: Smirnov Daniil Aleksandrovich \
Lab: Lab1 \
Date of create: 04.12.2023 \
Date of finished: 04.12.2023

## Ход работы

1. Установил [Docker](https://docs.docker.com/engine/install/ubuntu/) и [Minikube](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download) на Ubuntu VM по инструкциям.
2. Развернул кластер minikube с помощью `minikube start`:
   
   ![image](https://github.com/user-attachments/assets/07ed18cc-ebf0-4e84-840e-34e9791d89a8)
   
5. Загрузил образ HashiCorp Vault с помощью `docker pull vault:1.13.3`:
   
   ![image](https://github.com/user-attachments/assets/3be002b6-1b15-4e0d-84bd-266051da2aad)
   
7. Чтобы развернуть под с образом, создал манифест vault.yaml:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: vault
  labels:
    name: vault
spec:
  containers:
  - name: vault
    image: vault:1.13.3
    ports:
    - containerPort: 8200
```
5. Применил манифест `minikube kubectl -- apply -f vault-pod.yaml`:
   
   ![image](https://github.com/user-attachments/assets/d7b0c929-c809-45aa-a23f-117676807cba)
   
7. Поднял сервис `minikube kubectl -- expose pod vault --type=NodePort --port=8200`:
   
   ![image](https://github.com/user-attachments/assets/7054960a-b3a7-4feb-a8cc-b6d47523043d)
  
9. Прокинул порт в контейнер с помощью `minikube kubectl -- port-forward service/vault 8200:8200`, сервис стал доступен на localhost:8200:
    
   ![image](https://github.com/user-attachments/assets/866543d5-c20b-4fc0-bf7a-fede27857eab)
   
11. Токен хранится в логах контейнера в поде - с помощью команды `minikube kubectl logs vault` нашел токен:
    
  ![image](https://github.com/user-attachments/assets/32ac01bc-4426-49e6-ae6f-fe822d723969)
  
  и вошел в сервис:
  
  ![image](https://github.com/user-attachments/assets/7554df84-b8ae-470c-bae0-6023189bbc7b)
  ## Диаграмма организации
  ![image](https://github.com/user-attachments/assets/2744893c-c295-413c-b72c-e7b4b6794fe8)


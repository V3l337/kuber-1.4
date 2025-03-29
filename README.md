# Заданеи - Создать Deployment и обеспечить доступ к контейнерам приложения по разным портам из другого Pod внутри кластера

## Создал Deployment 
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-multitool-deployment
  labels:
    app: web
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
      - name: multitool
        image: wbitt/network-multitool
        ports:
        - containerPort: 8080
        command: ["/bin/sh", "-c", "httpd -p 8080 -h / & sleep infinity"]
        # command: ["/bin/sh", "-c", "sed -i 's/listen 80/listen 8080/g' /etc/nginx/conf.d/default.conf && nginx -g 'daemon off;'"]
```

## Создал Service
```yml
apiVersion: v1
kind: Service
metadata:
  name: nginx-multitool-service
spec:
  selector:
    app: web 
  ports:
  - name: nginx-port
    protocol: TCP
    port: 9001          # Порт, на котором сервис принимает запросы
    targetPort: 80    # Порт, на который пересылает внутри пода (nginx)
  - name: multitool-port
    protocol: TCP
    port: 9002       # Порт сервиса для multitool
    targetPort: 8080  # Внутренний порт multitool
  type: ClusterIP
```

## Проверил с помощью отдельного пода multitool 

![Продемонстрировать доступ с помощью curl по доменному имени сервиса](https://github.com/user-attachments/assets/719c5c91-78d0-42fb-bc5b-c7b2c00b16ef)

# Задание - Создать Service и обеспечить доступ к приложениям снаружи кластера

## Создал Service 
```yml
apiVersion: v1
kind: Service
metadata:
  name: nginx-multitool-service222
spec:
  selector:
    app: web 
  ports:
  - name: nginx-port
    protocol: TCP
    port: 80         
    nodePort: 30007
  type: NodePort
```

## Проверил с хоста системы

![Продемонстрировать доступ с помощью браузера или curl с локального компьютера](https://github.com/user-attachments/assets/a0a09829-8a30-4dc9-9a58-5397cae10057)


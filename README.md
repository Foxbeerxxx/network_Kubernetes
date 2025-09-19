# Домашнее задание к занятию "`Сетевое взаимодействие в Kubernetes`" - ` Татаринцев Алексей`



---

### Задание 1


1. `Пишу манифест deployment-multi-container.yaml`

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: multi-container-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: multi-container-app
  template:
    metadata:
      labels:
        app: multi-container-app
    spec:
      containers:
        - name: nginx
          image: nginx:1.27-alpine
          ports:
            - containerPort: 80
        - name: multitool
          image: wbitt/network-multitool:latest
          env:
            - name: HTTP_PORT
              value: "8080"
          ports:
            - containerPort: 8080

```

2. `Пишу манифест service-clusterip.yaml`

```
apiVersion: v1
kind: Service
metadata:
  name: multiapp-clusterip
spec:
  selector:
    app: multi-container-app
  ports:
    - name: nginx
      port: 9001
      targetPort: 80
      protocol: TCP
    - name: multitool
      port: 9002
      targetPort: 8080
      protocol: TCP
  type: ClusterIP

```
3. `Пишу последний манифест service-nodeport.yaml`

```
apiVersion: v1
kind: Service
metadata:
  name: multiapp-nginx-nodeport
spec:
  selector:
    app: multi-container-app
  type: NodePort
  ports:
    - name: http
      port: 80
      targetPort: 80
      nodePort: 30080   # допустимый диапазон 30000–32767
      protocol: TCP

```
4. `Запускаем манифесты и проверяем`

 ![1](https://github.com/Foxbeerxxx/network_Kubernetes/blob/main/img/img1.png)

5. `Проверка доступа изнутри кластера:`

```
kubectl run test-pod --image=wbitt/network-multitool:latest --rm -it --restart=Never -- sh
# внутри test-pod:
curl -s multiapp-clusterip:9001   # должен вернуть html nginx
curl -s multiapp-clusterip:9002   # должен вернуть multitool
exit

```
 ![2](https://github.com/Foxbeerxxx/network_Kubernetes/blob/main/img/img2.png)

6. `Пробую доступ из вне `

```
Узнай IP ноды:
kubectl get nodes -o wide
Проверка доступа:
curl http://192.168.1.140:30080
```
 ![3](https://github.com/Foxbeerxxx/network_Kubernetes/blob/main/img/img3.png)


### Задание 2


1. `Пишу манифест deployment-frontend.yaml`

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
        - name: nginx
          image: nginx:1.27-alpine
          ports:
            - containerPort: 80

```
2. `Пишу манифест deployment-backend.yaml`

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
        - name: multitool
          image: wbitt/network-multitool:latest
          env:
            - name: HTTP_PORT
              value: "8080"
          ports:
            - containerPort: 8080

```

3. `Пишу манифест service-frontend.yaml`

```
apiVersion: v1
kind: Service
metadata:
  name: frontend-svc
spec:
  selector:
    app: frontend
  ports:
    - name: http
      port: 80
      targetPort: 80
  type: ClusterIP


```
4. `Еще Один манифест service-backend.yaml`
```
apiVersion: v1
kind: Service
metadata:
  name: backend-svc
spec:
  selector:
    app: backend
  ports:
    - name: http
      port: 80
      targetPort: 8080  # в поде multitool слушает 8080
  type: ClusterIP

```
5. `ingress.yaml манифест`

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: frontend-svc
                port:
                  number: 80
          - path: /api
            pathType: Prefix
            backend:
              service:
                name: backend-svc
                port:
                  number: 80


```
6. `Проверяю записанные манифесты  и взаимодествие сервисов`

```
Включаю контроллер 
microk8s enable ingress

Запускаю написанные манифесты
kubectl apply -f deployment-frontend.yaml
kubectl apply -f deployment-backend.yaml
kubectl apply -f service-frontend.yaml
kubectl apply -f service-backend.yaml
kubectl apply -f ingress.yaml

```

![4](https://github.com/Foxbeerxxx/network_Kubernetes/blob/main/img/img4.png)

7. `Проверяю`

```
kubectl get pods -o wide
kubectl get svc
kubectl get ingress
```

![5](https://github.com/Foxbeerxxx/network_Kubernetes/blob/main/img/img5.png)

---








### Задание 3

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6. 

```
Поле для вставки кода...
....
....
....
....
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`

### Задание 4

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6. 

```
Поле для вставки кода...
....
....
....
....
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`

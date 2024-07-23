# deploy mongodb with pvc kubernetes

buat file mongodb.yaml
```
sudo nano mongodb.yaml
```
```
apiVersion: v1
kind: Secret
metadata:
  name: mongodb-secret
  namespace: jeri
type: Opaque
data:
  MONGO_INITDB_ROOT_USERNAME: aXJ3YW4= #jeri 
  MONGO_INITDB_ROOT_PASSWORD: aXJ3YW4xMjM=   #jeri123
  MONGO_INITDB_DATABASE: aXJ3YW5kYg==   #jeri

---

apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mongodb
  namespace: jeri
spec:
  serviceName: "mongodb"
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
        - name: mongodb
          image: mongo:4.4
          envFrom:
             - secretRef:
                 name: mongodb-secret
                 optional: false
          volumeMounts:
            - name: mongodb-storage
              mountPath: /data/db
      volumes:
      - name: mongodb-storage
        persistentVolumeClaim:
          claimName: local-path-pvc

---

apiVersion: v1
kind: Service
metadata:
  name: mongodb
  namespace: jeri
spec:
  selector:
    app: mongodb
  type: NodePort
  ports:
    - name: port
      port: 27017
      targetPort: 27017


```

```
kubectl apply -f mongodb.yaml

```
akses pod mongodb dengan memasukkan perintah

```
kubectl exec -it pod/mongodb-0 /bin/bash -n jeri
```
lalu masuk ke db
```
mongo -u jeri -p Jeri123
```
```
show databases
```
```
use test

```
![1d](https://github.com/user-attachments/assets/a60e8f3c-b06e-42a7-b20c-2dcc62fbf700)


buat dummy database
-membuat collection baru
```
db.createCollection("dummydb")
```
-isi collection dengan data dummy
```
db.mycollection.insertMany([
{ name: "John Doe", age: 25, city: "New York" },
{ name: "Jane Smith", age: 30, city: "San Francisco" },
{ name: "Bob Johnson", age: 28, city: "Los Angeles" }
])
```
-perintah find() untuk menampilkan semua data dalam koleksi
```
db.dummydb.find()
```
![2d](https://github.com/user-attachments/assets/8c4f7082-b2c0-4bea-98d3-9e2597079857)

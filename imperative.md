create a simple pod
kk run --generator=run-pod/v1 nginx-pod --image:nginx:alpine

same as above but with label
kk run --generator=run-pod/v1 redis --image=redis:alpine -l tier=db

expose as service
kk expose pod redis --type ClusterIP --port 6379 --name redis-service

create deployment
kk create deployment webapp --image=kodekloud/webapp-color

replicase set by scaling the deployment
kk scale deployment webapp --replicas=3

create service for deployment with definit NodePort
So first create service
kk expose deployment webapp --port=8080 --name=webapp-service --type=NodePort --dry-run -o yaml > webapp-service.yaml

Then edit NodePort
vi webapp-service.yaml

apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: webapp
  name: webapp-service
spec:
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 8080
    nodePort: 30082
  selector:
    app: webapp
  type: NodePort
status:
  loadBalancer: {}

kk apply -f webapp-service.yaml

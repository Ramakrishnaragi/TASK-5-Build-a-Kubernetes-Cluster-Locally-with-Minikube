#  TASK-5-Build-a-Kubernetes-Cluster-Locally-with-Minikube


## Objective:
- Deploy and manage an app using Kubernetes on a local/ec2 instance Minikube cluster.
## Tools Needed
- Minikube
- Kubectl
- Docker
- Ec2/locally
## Step-by-Step Process 
- Launch the server first (or ) we can use the locally also
## Step 1: Install Tools: 
- For minikube install purpose required 2cpu and 2gib ram
## Install docker
```sh
sudo dnf install docker -y   -----Install Docker first

sudo systemctl start docker.service ---to start thr docker service

sudo systemctl enable docker.service  --- to enable docker

sudo usermod -a -G docker ec2-user  --- to add the user ec2-user to the docker group 

newgrp docker --to run new group make changes immediatly
```
## Install Minikube
```sh
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

sudo install minikube-linux-amd64 /usr/local/bin/minikube

to start minikube ---minikube start 
to check status ----minikube status
```
## Install kubectl
```sh
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

chmod +x ./kubectl  -----Make the kubectl binary executable

sudo mv ./kubectl /usr/local/bin/kubectl  ----sudo mv ./kubectl /usr/local/bin/kubectl
```
## Step 2: Create a deployment.yml and service.yml for sample App

- deployment.yml
```sh
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: nginx
        ports:
        - containerPort: 80
```

- service.yml
```sh
# service.yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  selector:
    app: myapp
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30007
```
## Then execute this cmds:
- kubectl apply -f .
- kubectl get pods

![image](https://github.com/user-attachments/assets/c4f9a674-9d37-4264-b8fe-b2717ca82135)

- kubectl get nodes

![image](https://github.com/user-attachments/assets/2d7aee7f-d4a1-43e3-ad3f-11eb50c5d57a)


- kubectl get svc # In ec2 the svc cannot show the external ip so it shows like, so we have to access the application from powershell , we choose for port forwarder.

  ![image](https://github.com/user-attachments/assets/587551d4-744e-4531-bbd6-854f1bbe6d9d)



- nohup kubectl port-forward svc/myapp-service 8080:80 > port-forward.log 2>&1 & # execute this cmd in ec2
- ssh -i "your-key.pem" -L 8080:localhost:8080 ec2-user@<EC2_PUBLIC_IP> # execute this cmd in local powershell or cmd

![image](https://github.com/user-attachments/assets/456ccf8d-fe14-4b07-a62a-afa0f259d04d)


- After connection in powershell only the application can access in locally otherwise it not possible
## Then open this in your local browser:
- http://localhost:8080  (a secure tunnel will form from ec2 and locally)


![image](https://github.com/user-attachments/assets/76752c02-c8c2-4786-bcbf-5ae27e37ef93)


- After change the replica set in deployment.yml do manually or execute the below cmd

```sh
kubectl scale deployment myapp-deployment --replicas=4
kubectl get pods
```

![image](https://github.com/user-attachments/assets/2e7542a3-a89d-4d3e-8096-16cef285d65b)


## Describe and Check Logs
- kubectl describe pod <pod-name>

![image](https://github.com/user-attachments/assets/8fd7d068-bb41-47a2-8333-e34fad3c3e28)


- kubectl logs <pod-name>


![image](https://github.com/user-attachments/assets/9c4ff091-650e-40a8-acfd-9d423b04b8de)


- kubectl describe node <node-name>

![image](https://github.com/user-attachments/assets/008f7816-12a0-4cf7-a229-379880a5e0bd)


## For minikube install purpose required 2cpu and 2gib ram

![image](https://github.com/user-attachments/assets/7dda2024-c905-4214-ad34-efadaeb4ebfc)

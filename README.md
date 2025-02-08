# docker-k8s-project

## Set up Project
- Create a new project directory.
- Inside the directory, create an HTML file `index.html` and a CSS file `styles.css`
>See the image below
![](/img/setup.png)

## Initialize Git

- Initialize Git using ```git init``` command
- Connect it to github
    - Login to your [Github](htttps://github.com) account
    - Create new repository
    - Copy the repository link
    - On your terminal, run ```git remote add origin git@github.com:jesmanto/docker-k8s-project.git```

## Git Commit
Commit the file changes by staging the files and adding a commit message

![](/img/commit.png)

## Dockerize the application
- Go to chat gpt and ask for a it to create a simple profile webpage using html and ccss
- Copy the codes and paste them in the `index.html` and `styles.css` files respectively.
- Create a dockerfile
    - Create a file named `dockerfile`
    - Edit the file with nano editor
    - Supply the instructions as in the image below.
    ![](/img/dockerfile.png)
    >From the above image 
    `FROM nginx:latest` specifies the base image
    `WORKDIR  /usr/share/nginx/html/` Sets the working directory
    `COPY index.html /usr/share/nginx/html/` Copies the html file to the working directory
    `COPY sytles.css /usr/share/nginx/html/` Copies the css file to the working directory
- Build the dockerfile by running ```docker build -t dockerfile .```

    ![](/img/docker_build.png)
## Push to Docker Hub
- Login to Docker Hub
- Login to docker on your terminal
    ![](/img/docker_login.png)
- Push your Docker Image to Docker Hub
- Go to docker hub and create a new repository
    ![](/img/docker_repo.png)
- Copy the full path of the repository `jesmanto/my-nginx-alpine`, as it will be used when puching from our terminal
- Head over to your terminal and create a tag for the image using the name you copied
    ```docker tag <image-name> <your-dockerhub-username>/<your-repository-name>:<tag>```

- Push to Docker hub with the tag you just created
    ```docker push <your-dockerhub-username>/<your-repository-name>:<tag>```

    ![](/img/push.png)

- Go to Docker Hub to verify your push
    ![](/img/confirm-push.png)

## Set up a Kind Kubernetes
- To install Kind, go to [Kind Docs](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)
- Follow the instructions for your enviroment. For me, I use a Mac Intel
- I will install from package manager, **Homebrew** by runnig `brew install kind`
- Create a Kind cluster
![](/img/create_clutter.png)
    >This will take a few minutes to create

## Deploy to Kubernetes
- Create a Kubernetes Deployment YAML file specifying the imageand desired replicas
```apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-nginx-alpine
  template:
    metadata:
      labels:
        app: my-nginx-alpine
    spec:
      containers:
      - name: my-nginx-alpine
        image: jesmanto/my-nginx-alpine:1.0
        ports:
        - containerPort: 80
```

- Apply the deployment to your cluster
    - Create a Kind cluster (if not already created)

    ```kind create cluster --name my-kind-cluster ```



    - Get the kubeconfig for the cluster

    ```kind get kubeconfig --name my-kind-cluster > my-kind-cluster.kubeconfig ```


    - Set the kubectl context to the Kind cluster

    ```kubectl config use-context kind-my-kind-cluster ```


    - Apply your deployment 

    ```kubectl apply -f my-deployment.yaml ```

## Create a Service (ClusterIP)
- Create a Kubernetes Service YAML file specifying the type as ClusterIP
```
apiVersion: v1
kind: Service
metadata:
  name: my-clusterip-service
  labels:
    app: my-nginx-alpine
spec:
  type: ClusterIP
  selector:
    app: my-nginx-alpine
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```
- Apply the service to your clutter
```
kubectl apply -f my-service.yaml
```

## Access the application
- Port forward to the service to access the application locally
- Open your browser and visit the specified port to view your frontend application.
![](/img/result.png)


## Challenges Faced
- Installing and working with Kind
- Applying Deployment and Service YAML files with my Kind cluster
- Port forwading to the service (My Pod was listen to port 80 while I was targeting port 8080 in my Service YAML)

## Troubleshooting
- Use of online resources from medium
- I also got help from my previous projects on Kubernetes
- I got to find out the my Pod was listening to a different port by running 
```
kubectl exec -it my-nginx-deployment-5bf77f77b4-nlslt -- netstat -tuln
```

## Best Practices
It is a best practice to always read documentation when working with Kubernetes and Docker.

## Push to Github
- Add all file to staging
```
git add .
```
- Commit changes 
```
git commit -m <COMMIT_MESSAGE>
```
- Push to Github
```
git push --set-upstream origin master
```
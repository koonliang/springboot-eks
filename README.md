# Getting Started
A small project to demo how to deploy springboot application onto AWS EKS.

### Prerequisite
1. AWS account
2. eksctl cli tool (https://eksctl.io/)
3. AWS cli
4. Powershell AWS tool
```
$ Install-Module -Name AWS.Tools.Installer -Force
$ Install-AWSToolsModule AWS.Tools.ECR -CleanUp
```

###
1. Create a repository in AWS ECR named springboot-eks

2. Login to your AWS ECR (replace the last portion accordingly based on your ECR URI, you can check it via your ECR repository 'View push commands')
```
$ (Get-ECRLoginCommand).Password | docker login --username AWS --password-stdin 309779120361.dkr.ecr.ap-southeast-1.amazonaws.com
```

3. Build the docker image for this project
```
$ docker build -t springboot-eks .
```

4. After the build is completed, tag your image so that you can push the image to the repository
```
$ docker tag springboot-eks:latest 309779120361.dkr.ecr.ap-southeast-1.amazonaws.com/springboot-eks:latest
```

5. Run the following command to push the image to the ECR repository
```
$ docker push 309779120361.dkr.ecr.ap-southeast-1.amazonaws.com/springboot-eks:latest
```

6. Create the EKS cluster via eksctl cli. This will take 15-20 minutes
```
$ eksctl create cluster --name my-cluster --version 1.31 --nodes=1 --node-type=t2.small --region ap-southeast-1
```

7. Configure kubectl to use the EKS cluster
```
$ aws eks --region ap-southeast-1 update-kubeconfig --name my-cluster
```

8. Apply manifest file to start deployment and service
```
$ kubectl apply -f src/main/resources/eks/k8s.yaml
```

9. You can check the status of your deployed services
```
$ kubectl get svc
$ kubectl get deployment
$ kubectl get pods
```

10. You will find your ‘newapp-service’ service listed, along with an external IP.

11. Open your browser and go to that IP; you should see the message ‘Welcome! This app is deployed in AWS EKS’

### Clean up
It is recommended to tear down EKS clusters after use, as there is cost associated with running EKS clusters.
```
$ eksctl delete cluster my-cluster
```

### Reference Documentation
For further reference, please consider the following sections:

* [Official Apache Maven documentation](https://maven.apache.org/guides/index.html)
* [Spring Boot Maven Plugin Reference Guide](https://docs.spring.io/spring-boot/3.4.3/maven-plugin)
* [Create an OCI image](https://docs.spring.io/spring-boot/3.4.3/maven-plugin/build-image.html)
* [Spring Web](https://docs.spring.io/spring-boot/3.4.3/reference/web/servlet.html)

### Guides
The following guides illustrate how to use some features concretely:

* [Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/)
* [Serving Web Content with Spring MVC](https://spring.io/guides/gs/serving-web-content/)
* [Building REST services with Spring](https://spring.io/guides/tutorials/rest/)

### Maven Parent overrides

Due to Maven's design, elements are inherited from the parent POM to the project POM.
While most of the inheritance is fine, it also inherits unwanted elements like `<license>` and `<developers>` from the parent.
To prevent this, the project POM contains empty overrides for these elements.
If you manually switch to a different parent and actually want the inheritance, you need to remove those overrides.


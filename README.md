# Create 2-Tiered Web Application using Persistent Volumes, Persistent Volume Claims and Storage Classes

# Step 1: Disable AWS Temp. Credentials and Create EKS Cluster
```
vi ~/.aws/credentials
eksctl create cluster -f <cluster file name.yaml>
```

# Step 2: Build and Push App and SQL images to ECR Repo's
```
docker build -t mysql -f <file name> .
aws ecr create-repository --repository-name <give name>  --image-scanning-configuration scanOnPush=true
docker tag <image name> <repo URI>
aws ecr get-login-password --region <region name> | docker login -u AWS <repo URI> --password-stdin
docker push <repo URI>
```

# Step 3: Create Namespace, PVC, SC, SQL Deployment and Bound PVC 
```
k create ns <give it a name>
k create -f <storageclass name>
k create -f <pvc name>
k create -f <sql deployment name>
k create -f <clusterIP service name>
k get all -n lab3
```

# Step 4: Check Provisioned Node of SQL Pod
```
k get pods -n <namespace>
k describe pod <pod name that you get above>
```

# My Deployment Sequence
```
1. Storage Class
2. PVC sql
3. Deployment SQL
4. Service SQL
5. Deployment APP
6. Load Balancer
7. Host PV
8. Host PVC
9. Deployment SQL (configured for HostPath)
10. Deployment APP (again for HostPath)
```

# Common Errors
```
1. Data Not Persisting 
   Solution: Make sure there is no "DROP DATABASE IF" Line in your Original SQL Database file.
2. PVC Not getting Bounded
   Solution: kubectl patch storageclass <storageclass name> -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
   This will make your StorageClass as Default
3. LoadBalancer in OutofService State
   Solution: Wait for 5-10 minutes it automatically goes into inService state.
4. TemplateError
   Solution: Make sure you have your original Templates Folder in your environment while testing and provisioning.
```

# Thanks & Regards - Dhruv

Remote Cluster Setup on Linode VM:

installed docker

installed kubelet kubeadm kubectl kubernetes-cni

created /home/.kube/config -> kubectl didn't work as searches config in user directory

exporting KUBECONFIG env variable doesn't work

Err: The connection to the server localhost:8080 was refused - did you specify the right host or port?

Res: created home/privatecircle/.kube/config file

      export KUBECONFIG=$HOME/.kube/config
      
      echo 'export KUBECONFIG=$HOME/.kube/config' >> $HOME/.bashrc //for permanent variable export
      
  KUBECTL WORKS 
  
installed helm through binary

Err: WARNING: Kubernetes configuration file is group-readable. This is insecure. Location: /home/privatecircle/.kube/config
WARNING: Kubernetes configuration file is world-readable. This is insecure. Location: /home/privatecircle/.kube/config

Res: chmod 600 home/privatecircle/.kube/config
      
**RUNNING HELM CHARTS**

DOC: https://wkrzywiec.medium.com/how-to-deploy-application-on-kubernetes-with-helm-39f545ad33b8
GitHub: https://github.com/wkrzywiec/k8s-helm-helmfile/tree/master/helm

![image](https://user-images.githubusercontent.com/53597532/173527107-942bb2ae-af09-4f48-8474-be59480d6d8e.png)

HELM POSTGRES DEPLOYMENT NOT RUNNING : Pod Failed due to CrashLoopBackOff

kubectl delete deployment postgres

helm install -f kanban-postgres.yaml postgres ./postgres

Err: cannot re-use a name that is still in use

Res: delete helm release as well

ERROR "Pod Failed due to CrashLoopBackOff" PERSISTENT

![image](https://user-images.githubusercontent.com/53597532/173530473-2a95e102-b1ac-4209-9cb0-732c65b924a8.png)

DEBUGS:

1. CHANGED storage in values.yaml from 4Gi to 1Gi, image from postgres:9.6-alpine to postgres (didnt work)

2. ADDED volumeMount subPath to PersistentVolume for Postgres container (didnt work)

REFERENCE https://stackoverflow.com/questions/51168558/how-to-mount-a-postgresql-volume-using-aws-ebs-in-kubernete

3. ADDED EnvFromSource env vars in deployment.yaml by removing ConfigMapRef (deployment failed directly) 

REFRENCE https://serverfault.com/questions/1018377/postgres-mount-volume-error-in-k8s

WORKING SOLUTION: ADDED KEY-VALUE PAIR IN ConfigMap (kanban-postgres.yaml)

      - key: PGDATA

        value: /var/lib/postgresql/data/pgdata
        
RCA ---> earlier we had set the mount point path and the data path to be the same dir; data directory should be set to subdir -> set values in configmap for the same

DEPLOYED Postgres

DEPLOYED kanban-app

DEPLOYED kanban-ui

DEPLOYED adminer

![image](https://user-images.githubusercontent.com/53597532/173891387-6ee48ac6-1710-4e7b-92a4-e9e6c5a2f825.png)


ERROR: INGRESS DEPLOYMENT FAILED

![image](https://user-images.githubusercontent.com/53597532/173891640-fcb8bd34-ecb6-485b-9c6b-dd7ac8c13e18.png)


ASSUMPTION: Didn't add any hosts in /etc/hosts file, ingress routing will fail as localhost


**HELMFILE DEPLOYMENTS**

Deploying same kanban app-stack using helmfile 

https://medium.com/swlh/how-to-declaratively-run-helm-charts-using-helmfile-ac78572e6088

ERROR: 

![image](https://user-images.githubusercontent.com/53597532/174021833-846129b6-1321-4bd8-a804-7b20fa3c2703.png)

ASSUMPTION: Ingress deployment fails with both helm and helmfile; don't have access from my namespace to fetch RBAC roles

-------PROMETHEUS STACK USING HELM------------

ADDED helm charts to helm repo

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

Created namespace "prom" for prometheus-monitoring stack

Installed helm chart

      helm install prometheus prometheus-community/kube-prometheus-stack -n prom
      
<img width="784" alt="image" src="https://user-images.githubusercontent.com/53597532/174551265-3aa9d9af-1988-40e9-afce-f9dc03fb701d.png">


kube-prometheus-stack deployed

ERROR: No response while port-forwarding
ASSUMPTION: Inbound, ooutbound ports not opened from VM; since getting curl response

      kubectl port-forward -n prom pod/prometheus-prometheus-kube-prometheus-prometheus-0 9090
      
<img width="352" alt="image" src="https://user-images.githubusercontent.com/53597532/174552482-58675870-6b95-44e1-8505-aa1fcdbb057b.png">

**USING FIREWALLS TO MANAGE PORTS IN HHOST VM**

LEARNING FIREWALLS, PROXIES AND IPTABLES - UFW

<img width="478" alt="image" src="https://user-images.githubusercontent.com/53597532/175221093-874935bc-1782-4058-ad67-43cc850600aa.png">

DEBUG: 

1. Allowed connections from my IPv4  (didnt work) -- UFW and any firewall proxy requires IPv6
      
        sudo ufw allow from 182.64.181.47

2. SSH TUNNEL (didn't work)
      
            ssh -N -L 9090:localhost:9090 privatecircle@172.105.63.45 

3. Allowed specific ports using UFW

            sudo ufw allow 9090/tcp

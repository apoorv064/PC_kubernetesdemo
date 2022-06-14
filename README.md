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
      
RUNNING HELM CHARTS

![image](https://user-images.githubusercontent.com/53597532/173527107-942bb2ae-af09-4f48-8474-be59480d6d8e.png)

HELM DEPLOYMENT NOT RUNNING : Pod Failed due to CrashLoopBackOff

kubectl delete deployment postgres

helm install -f kanban-postgres.yaml postgres ./postgres

Err: cannot re-use a name that is still in use

Res: delete helm release as well

ERROR "Pod Failed due to CrashLoopBackOff" PERSISTENT

![image](https://user-images.githubusercontent.com/53597532/173530473-2a95e102-b1ac-4209-9cb0-732c65b924a8.png)

CHANGED storage in values.yaml from 4Gi to 1Gi, image from postgres:9.6-alpine to postgres


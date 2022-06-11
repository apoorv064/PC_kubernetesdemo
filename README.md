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
      

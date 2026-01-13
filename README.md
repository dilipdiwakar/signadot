1. Create a Cluster :

      navigate to Clusters > Connect Cluster, and then supply a name that identifies the cluster you are connecting.
      https://app.signadot.com/cluster/ddcluster

2. Connecting Your Cluster :

      A cluster token has been created for you. Be sure to save it as you will not be able to view it again if you leave or refresh this page.
    
         stockborn@vqWT0uk9vy-Hz0NnuVQlu9PLp4dTfFd9a2s0_Ds4Y04




3. Run the following commands to install Signadot on the cluster you are connecting:
   
      kubectl create ns signadot       // create a namespace signadot in the Kubernetes cluster in baseline env
   
      helm repo add signadot https://charts.signadot.com      // Adds Signadot’s Helm chart repository to your local Helm configuration, Helm knows where to find Signadot packages.

      helm install signadot-operator signadot/operator --set controlPlane.clusterToken='stockborn@vqWT0uk9vy-Hz0NnuVQlu9PLp4dTfFd9a2s0_Ds4Y04' // it installs the signadot components


    

5. Install the HotROD Application: Install the demo app using the appropriate overlay

      kubectl create ns hotrod --dry-run=client -o yaml | kubectl apply -f -
   
      kubectl -n hotrod apply -k 'https://github.com/signadot/hotrod/k8s/overlays/prod/istio'


7. Accessing the App's Frontend :
      First, authenticate with your Signadot account using the CLI:

           signadot auth login

8. Once authentication is completed, you can connect to your cluster as shown below:

    Create a config.yaml file

      $HOME/.signadot/config.yaml:
local:
  connections:
    - cluster: ddcluster
      type: ControlPlaneProxy


9. Connect to the cluster:

    signadot local connect

10. Apply the Kubernetes YAML file frontend-highlight.yaml , driver-plates.yaml in Sandbox env

Frontend app :

name: frontend-highlight
spec:
  description: Add plate number highlighting in UI
  cluster: "ddcluster"
  labels:
    feature: hotrod-plates
  forks:
    - forkOf:
        kind: Deployment
        namespace: hotrod
        name: frontend
      customizations:
        images:
          - image: signadot/hotrod:97de62bf5a6d91482f62db23de565282ff97e60d-linux-amd64
            container: hotrod

<img width="738" height="474" alt="Screenshot 2026-01-12 at 7 59 34 PM" src="https://github.com/user-attachments/assets/21c72bda-a7ef-49de-9266-f0fb758ec6b0" />


Backend app :

name: driver-plates
spec:
  description: Adding SD- prefix to driver plates
  cluster: "ddcluster"
  labels:
    feature: hotrod-plates
  forks:
    - forkOf:
        kind: Deployment
        namespace: hotrod
        name: driver
      customizations:
        images:
          - image: signadot/hotrod:f788b05bca80429425b814da62b2f17c3c564089-linux-amd64
            container: hotrod


<img width="717" height="387" alt="Screenshot 2026-01-12 at 8 00 13 PM" src="https://github.com/user-attachments/assets/1aedfa43-1c63-4725-856a-9c12492d7c84" />


9. signadot % kubectl -n hotrod patch deployment frontend \
  -p '{"spec":{"template":{"metadata":{"annotations":{"sidecar.signadot.com/inject":"true"}}}}}'

  


Front End :
===========


   <img width="637" height="716" alt="Screenshot 2026-01-12 at 8 19 13 PM" src="https://github.com/user-attachments/assets/b3d8a262-4afb-4ae6-ba1a-306d00145adb" />



Back End :
===========

   <img width="551" height="714" alt="Screenshot 2026-01-12 at 8 31 29 PM" src="https://github.com/user-attachments/assets/f3a0ac1e-85d4-4b21-812b-b4ab8367c7c8" />


SandBoxes :
===========

 <img width="1706" height="324" alt="Screenshot 2026-01-12 at 9 28 19 PM" src="https://github.com/user-attachments/assets/7ba910ed-b419-4771-95f2-2a262af16c35" />

Url : http://frontend.hotrod.svc:8080/
======================================

<img width="1704" height="1009" alt="Screenshot 2026-01-12 at 8 35 01 PM" src="https://github.com/user-attachments/assets/953c6d34-a222-435d-baf3-2fe6e80bd7d4" />





<img width="1681" height="970" alt="Screenshot 2026-01-12 at 8 43 19 PM" src="https://github.com/user-attachments/assets/a4f577c8-8887-4518-be68-f3a528b6b15e" />


plates-routegroup.yaml
========================

name: hotrod-plates-feature
spec:
  cluster: "@{cluster}"
  description: "route group for testing multiple sandboxes together"
  match:
    any:
    - label:
        key: feature
        value: hotrod-plates


Save the route group spec as plates-routegroup.yaml and apply it:
       signadot routegroup apply -f ./plates-routegroup.yaml --set cluster=ddcluster


 
Others :

Upgrading the Operator:
You upgrade an existing installation by running the following Helm commands.

          Operator Upgrade
  
             helm repo update
             helm upgrade signadot-operator signadot/operator


    Uninstalling the Operator:
            helm uninstall signadot-operator
            release "signadot-operator" uninstalled
            kubectl delete ns signadot

    Delete a namespace :
      kubectl delete namespace signadot

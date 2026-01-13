1. Create a Cluster :

      navigate to Clusters > Connect Cluster, and then supply a name that identifies the cluster you are connecting.
      https://app.signadot.com/cluster/ddcluster

2. Connecting Your Cluster :

      A cluster token has been created for you. Be sure to save it as you will not be able to view it again if you leave or refresh this page.
    
         stockborn@vqWT0uk9vy-Hz0NnuVQlu9PLp4dTfFd9a2s0_Ds4Y04




3. Run the following commands to install Signadot on the cluster you are connecting:
      kubectl create ns signadot
      helm repo add signadot https://charts.signadot.com
      helm install signadot-operator signadot/operator --set controlPlane.clusterToken='stockborn@vqWT0uk9vy-Hz0NnuVQlu9PLp4dTfFd9a2s0_Ds4Y04'


    

4. Install the HotROD Application: Install the demo app using the appropriate overlay

      kubectl create ns hotrod --dry-run=client -o yaml | kubectl apply -f -
      kubectl -n hotrod apply -k 'https://github.com/signadot/hotrod/k8s/overlays/prod/istio'


5. Accessing the App's Frontend :
      First, authenticate with your Signadot account using the CLI:

           signadot auth login

6. Once authentication is completed, you can connect to your cluster as shown below:

    Create a config.yaml file

      $HOME/.signadot/config.yaml:
local:
  connections:
    - cluster: ddcluster
      type: ControlPlaneProxy


7. Connect to the cluster:

    signadot local connect

8. Apply the Kubernetes YAML file frontend-highlight.yaml , driver-plates.yaml in Sandbox env



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
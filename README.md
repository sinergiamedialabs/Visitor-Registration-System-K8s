# Visitor Registration System Deployment in EKS

#### Prerequisites

1. Create an EKS Cluster using the AWS CLI or Management Console.
1. Update your kubeconfig to use the new cluster. For this to be done, you need to configure the AWS CLI in your local machine and also make sure that you have `kubectl` installed.

    ```bash
    aws eks update-kubeconfig --name <NAME_OF_EKS_CLUSTER>
    ```
1. Create a Node Group
    - Go to the EKS console.
    - Select your cluster.
    - Navigate to the “Node Groups” tab.
    - Click “Add Node Group” and follow the wizard to configure the node group.
    - Choose all the addons in default selection.

1. Now got to Addons Tab > Get More Addons and install **Amazon EFS CSI Driver**. This is needed for using EFS volume for persistent storage in PostgresDB service.
1. Create an EFS volume in AWS and make a note of the EFS Volume ID. Also modify the security group to allow connection from the node groups to EFS volume on port 2049.
1. Create a PostgresDB service with Persistent Volume Claim (PVC) using EFS volume.
1. 

#### Deployment

Clone the repo to your local machine where you have configured AWS CLI and `kubectl`. Note that, you may need to modify the credentials as per your requirement. This changes must be made in the following files

    - efs-pv.yaml
    - java-app-deployment.yaml
    - postgres-statefulset.yaml 

Once thsi is done, we can go ahead provisioning EKS pods. Run the following commands one by one.

1. Provision the EFS Persistent Volume,

    ```bash
    kubectl apply -f efs-pv.yaml
    ```
1. Provision the Persistent volume claim,

    ```bash
    kubectl apply -f efs-pvc.yaml
    ```
1. Now, deploy the postgres statefulset.

    ```bash
    kubectl apply -f postgres-statefulset.yaml
    ```
1. Go ahead and create the postgresql service

    ```bash
    kubectl apply -f postgres-service.yaml
    ```
1. Deploy the java backend app.

    ```bash
    kubectl apply -f java-app-deployment.yaml
    ```
1. Deploy a service for java app

    ```bash
    kubectl apply -f java-app-service.yaml
    ```
1. Deploy the frontend app
    
    ```bash
    kubectl apply -f frontend-deployment.yaml
    ```
1. Deploy a service for the frontend app.

    ```bash
    kubectl apply -f frontend-service.yaml
    ```

After the completion of the above commands you can verify the status by following commands

    ```bash
    kubectl get deployments
    kubectl get po
    kubectl get svc
    ```

Once this is complete, you should have the service up and running. To access this application you can either call the frontend app Load balancer endpoint directly. You can also point your domain to this loadbalancer to get the service.

# Assigning a static IP address to the Knative gateway

If you are running Knative on Google Kubernetes Engine and want to deploy 
apps on a [custom domain](./using-a-custom-domain.md), you need to configure a 
static IP address to ensure your custom domain mapping doesn't break.

Knative uses a shared gateway to serve all incoming traffic within Knative 
service mesh, which is the "knative-shared-gateway" gateway under the 
"knative-serving" namespace. 

The IP address to access the gateway is the external IP address of the 
"knative-ingressgateway" service under the "istio-system" namespace. So, 
in order to set a static IP for the Knative shared  gateway, you need to 
set the external IP address of the "knative-ingressgateway" service to 
the static IP you need.

## Step 1: Reserve a static IP address

If you are running a Knative cluster on GKE, you can follow the [instructions](https://cloud.google.com/compute/docs/ip-addresses/reserve-static-external-ip-address#reserve_new_static) to reserve a REGIONAL 
IP address. The region of the IP address should be the region that your Knative
cluster is running in (such as us-east1, us-central1, etc.).

### Step 2: Update the external IP of the "knative-ingressgateway" service

Run the following command to reset the external IP for the  "knative-ingressgateway" 
service to the static IP that you reserved:
```shell
kubectl patch svc knative-ingressgateway -n istio-system --patch '{"spec": { "loadBalancerIP": "<your-reserved-static-ip>" }}'
```

### Step 3: Verify the static IP address of knative-ingressgateway service

You can check the external IP of the "knative-ingressgateway" service with:
```shell
kubectl get svc knative-ingressgateway -n istio-system
```
The result should look similar to the following output:
```
NAME                     TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                      AGE
knative-ingressgateway   LoadBalancer   10.50.250.120   35.210.48.100   80:32380/TCP,443:32390/TCP,32400:32400/TCP   5h
```
The process to set the external IP to the static IP address can take several minutes.
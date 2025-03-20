---  
title: Explore Observability Features  
weight: 40
---

This article will guide you through how to use Istio to inject Sidecars into the Bookinfo application and explore observability features.

**Before you begin, make sure:**  

- The [Bookinfo sample application has been deployed].  
- A [service mesh has been created].  
- You have [accessed Bookinfo].  

## Step 1: Manage the Namespace  

Go to **Platform Management**, navigate to **Service Mesh** > **Service Meshes**, and click on the service mesh name to enter the details page. In the **Namespaces** section, click **Managed Namespace**. Select `demo-dev` and click **Confirm**.

## Step 2: Inject Sidecar  

1. **Deploy using kubectl**  
   Open the container platform or platform management page, and click the `kubectl` tool in the bottom right corner.

2. **Apply the YAML file**  
   Paste the following script into the terminal and execute it:
   ```bash
   #!/bin/bash
   cat <<EOF > sidecar.yaml
   apiVersion: asm.alauda.io/v1beta3
   kind: MicroService
   metadata:
     labels:
       app.cpaas.io/microservice-type: service-mesh
       asm.cpaas.io/isolatepod: enabled
     name: details
   spec:
     deployments:
     - name: details-v1
     services:
     - name: details
     sidecar:
       enabled: true
       resources:
         limits:
           cpu: 500m
           memory: 512Mi
         requests:
           cpu: 100m
           memory: 128Mi
   ---
   apiVersion: asm.alauda.io/v1beta3
   kind: MicroService
   metadata:
     labels:
       app.cpaas.io/microservice-type: service-mesh
       asm.cpaas.io/isolatepod: enabled
     name: ratings
   spec:
     deployments:
     - name: ratings-v1
     services:
     - name: ratings
     sidecar:
       enabled: true
       resources:
         limits:
           cpu: 500m
           memory: 512Mi
         requests:
           cpu: 100m
           memory: 128Mi
   ---
   apiVersion: asm.alauda.io/v1beta3
   kind: MicroService
   metadata:
     labels:
       app.cpaas.io/microservice-type: service-mesh
       asm.cpaas.io/isolatepod: enabled
     name: reviews
   spec:
     deployments:
     - name: reviews-v1
     services:
     - name: reviews
     sidecar:
       enabled: true
       resources:
         limits:
           cpu: 500m
           memory: 512Mi
         requests:
           cpu: 100m
           memory: 128Mi
   ---
   apiVersion: asm.alauda.io/v1beta3
   kind: MicroService
   metadata:
     labels:
       app.cpaas.io/microservice-type: service-mesh
       asm.cpaas.io/isolatepod: enabled
     name: productpage
   spec:
     deployments:
     - name: productpage-v1
     services:
     - name: productpage
     sidecar:
       enabled: true
       resources:
         limits:
           cpu: 500m
           memory: 512Mi
         requests:
           cpu: 100m
           memory: 128Mi
   EOF
   kubectl apply -f sidecar.yaml -n demo-dev
   rm sidecar.yaml
   ```

3. **Confirm successful injection**  
   Navigate to **Service Mesh**, and in the **Services**, you should see that the Sidecar injection is `Enabled` for all four Bookinfo services.

## Step 3: Explore Observability Features  

1. **Generate traffic**  
   To explore the observability features, the Bookinfo application needs traffic so that Istio can collect metrics and traces from the services. Use the following script to generate traffic.  
   **Note: ** Replace `GATEWAY_IP_PORT` with the actual IP of your ingress gateway. We have previously obtained the `GATEWAY_IP_PORT` in the [Accessing Bookinfo]({{< relref "mesh-gettingstart/access-bookinfo.md" >}}) section.

   Save the following script as `send-requests.sh`
   ```bash
   #!/bin/sh
   export GATEWAY_IP_PORT=192.168.130.0:30665
   while true; do
       result=$(curl -m 5 -s -o /dev/null -I -w "%{http_code}" "http://$GATEWAY_IP_PORT/productpage")
       echo date: $(date), status code: "$result"
       sleep 1
   done
   ```

   Make the script executable and run it:
   ```bash
   chmod +x send-requests.sh
   ./send-requests.sh
   ```

   This will send a request to the Bookinfo product page every 1 second. After a while, you can explore the observability features in the UI.

2. **Service Topology**  
   - After running the traffic generation script for 1–5 minutes, navigate to **Service Mesh** > **Topologies**. Here, you can see the topology showing the dependencies between the ingress gateway and the four Bookinfo services. You can also view metrics related to traffic and error rates on the topology graph.

   - Click on the link between the `public-ingressgw` and `productpage` nodes in the topology. **Traffic Info** between them will appear on the right side of the topology.

3. **Trace Details**  
   - On the **Traffic Info** page, click **View Tracing** to open the trace details page. The query time and parameters will be autopopulated. The Span list on the left side will display Span data that matches the query conditions.

   - Click on any Span to view the complete Trace data on the right side.

   - Click **View Log** in the Trace data to check the persistent log data of the Pods involved in the trace.

<h2 id="a-few-things-you-should-know">
    <strong>Step 1: Install Kubernetes Dashboard</strong>
</h2>

<h3 id="a-few-things-you-should-know">
    <strong>Kubernetes provides a YAML file to install the Dashboard:</strong>
</h3>
<blockquote>
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>Verify the deployment:</strong>
</h3>

<blockquote>
kubectl -n kubernetes-dashboard get pods
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>If all Pods are in the Running state, the installation is successful:</strong>
</h3>
<pre>
NAME                                        READY   STATUS    RESTARTS   AGE
dashboard-metrics-scraper-7b64584c5c-hfzdx   1/1     Running   0          2m
kubernetes-dashboard-789ff87696-7bvq9        1/1     Running   0          2m
</pre>


<h2 id="a-few-things-you-should-know">
    <strong>Step 2: Change Service Type to NodePort</strong>
</h2>

<h3 id="a-few-things-you-should-know">
    <strong>Edit the Service:</strong>
</h3>
<blockquote>
kubectl -n kubernetes-dashboard edit svc kubernetes-dashboard
</blockquote>


<h3 id="a-few-things-you-should-know">
    <strong>Locate this section:</strong>
</h3>
<pre>
spec:
  type: ClusterIP
</pre>

<h3 id="a-few-things-you-should-know">
    <strong>Change it to:</strong>
</h3>
<pre>
spec:
  type: NodePort
  ports:
    - port: 443
      targetPort: 8443
      nodePort: 32000  # Choose a port between 30000-32767
</pre>

<h3 id="a-few-things-you-should-know">
    <strong>Verify the Service:</strong>
</h3>
<blockquote>
kubectl -n kubernetes-dashboard get svc kubernetes-dashboard
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>Expected output:</strong>
</h3>
<pre>
NAME                   TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)         AGE
kubernetes-dashboard   NodePort   10.108.183.34   <none>        443:32000/TCP   2m
</pre>

<h2 id="a-few-things-you-should-know">
    <strong>Step 3: Configure Nginx Reverse Proxy</strong>
</h2>

<h3 id="a-few-things-you-should-know">
    <strong>Create a new configuration file:</strong>
</h3>

<blockquote>
sudo nano /etc/nginx/sites-available/k8s-dashboard.your-web.com
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>Add the following configuration:</strong>
</h3>

<pre>
server {
    listen 443 ssl;
    server_name dashboard.example.com;

    ssl_certificate /etc/nginx/ssl/dashboard.crt;
    ssl_certificate_key /etc/nginx/ssl/dashboard.key;

    location / {
        proxy_pass https://192.168.1.100:32000;  # Kubernetes Node IP
        proxy_ssl_verify off;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
</pre>

<h3 id="a-few-things-you-should-know">
    <strong>Enable the Configuration & Restart Nginx:</strong>
</h3>

<blockquote>
sudo ln -s /etc/nginx/sites-available/k8s-dashboard.your-web.com /etc/nginx/sites-enabled/
sudo systemctl restart nginx
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>Now, you can access the Dashboard at:</strong>
</h3>

<blockquote>
https://k8s-dashboard.your-web.com
</blockquote>

<h2 id="a-few-things-you-should-know">
    <strong>Step 5: Create Login Token
</strong>
</h2>

<h3 id="a-few-things-you-should-know">
    <strong>Create a ServiceAccount and ClusterRoleBinding:</strong>
</h3>

<pre>
<code>apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard</code>
</pre>

<h3 id="a-few-things-you-should-know">
    <strong>Generate Service Account:</strong>
</h3>

<blockquote>
kubectl create serviceaccount dashboard-admin -n kubernetes-dashboard
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>Grant Cluster Admin permissions:</strong>
</h3>

<blockquote>
kubectl create clusterrolebinding dashboard-admin --clusterrole=cluster-admin --serviceaccount=kubernetes-dashboard:dashboard-admin
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>Create token:</strong>
</h3>

<blockquote>
kubectl -n kubernetes-dashboard create token dashboard-admin
</blockquote>

<h4 id="a-few-things-you-should-know">
    Copy this token, go to the Dashboard login page, select Token, and paste it to log in.
</h4>


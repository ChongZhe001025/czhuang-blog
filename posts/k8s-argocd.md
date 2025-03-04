<h2 id="a-few-things-you-should-know">
    <strong>1. Install ArgoCD</strong>
</h2>

<h3 id="a-few-things-you-should-know">
    <strong>Deploy ArgoCD</strong>
</h3>

<blockquote>
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>Verify Installation</strong>
</h3>

<blockquote>
kubectl get pods -n argocd
</blockquote>

<h2 id="a-few-things-you-should-know">
    <strong>2. Expose ArgoCD Server</strong>
</h2>

<h3 id="a-few-things-you-should-know">
    <strong>Change to NodePort</strong>
</h3>

<blockquote>
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>Get the external port:</strong>
</h3>

<blockquote>
kubectl get svc argocd-server -n argocd -o=jsonpath='{.spec.ports[?(@.port==443)].nodePort}'
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>Access ArgoCD via:</strong>
</h3>

<blockquote>
    <code>https://&lt;NODE_IP&gt;:&lt;PORT&gt;</code>
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>Retrieve Admin Password</strong>
</h3>

<blockquote>
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
</blockquote>
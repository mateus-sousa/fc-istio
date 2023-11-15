k3d cluster create -p "8000:30000@loadbalancer" --agents 2
istioctl install
kubectl apply -f deployment.yml 
kubectl label namespace default istio-injection=enabled


kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.19/samples/addons/prometheus.yaml
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.19/samples/addons/kiali.yaml
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.19/samples/addons/jaeger.yaml
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.19/samples/addons/grafana.yaml
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.19/samples/addons/loki.yaml

while true; do curl http://localhost:8000; echo; sleep 0.5; done;

kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.19/samples/httpbin/sample-client/fortio-deploy.yaml

export FORTIO_POD=$(kubectl get pods -l app=fortio -o 'jsonpath={.items[0].metadata.name}')

kubectl exec "$FORTIO_POD" -c fortio -- fortio load -c 2 -qps 0 -t 20 -loglevel Warning http://servicex-service


kubectl edit svc istio-ingressgateway -n istio-system
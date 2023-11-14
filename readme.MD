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

kubectl exec "$FORTIO_POD" -c fortio -- fortio load -c 2 -qps 0 -t 200s -loglevel Warning http://svc:8000
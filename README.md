# Install k3d and yq
```bash
# k3d
curl -s https://raw.githubusercontent.com/k3d-io/k3d/main/install.sh | bash
k3d completion zsh > "${fpath[1]}/k3d" # zsh completion not working yet

# yq
wget https://github.com/mikefarah/yq/releases/download/v4.27.2/yq_linux_amd64 && chmod +x yq_linux_amd64 && sudo mv yq_linux_amd64 /usr/local/bin/yq
```

# Create k8s Cluster

```bash
k3d cluster create strimzi
```

# Install Strimzi Kafka
```bash
kubectl create namespace kafka
kubectl create -f 'https://strimzi.io/install/latest?namespace=kafka' -n kafka

# use 1Gi volumes instead of 100Gi and apply the manifest
curl https://strimzi.io/examples/latest/kafka/kafka-persistent-single.yaml | \
  yq '.spec.kafka.storage.volumes[0].size = "1Gi"' - | \
  yq '.spec.zookeeper.storage.size = "1Gi"' - | \
  kubectl apply -n kafka -f -
```

# Port forward the service
```bash
kubectl port-forward service/my-cluster-kafka-brokers -n kafka 9092:9092
echo 127.0.0.1 my-cluster-kafka-0.my-cluster-kafka-brokers.kafka.svc >> /etc/hosts
```

# Run the main
```bash
go run main.go
```


# TODO:
- authentication
- TLS
- logging
- metrics

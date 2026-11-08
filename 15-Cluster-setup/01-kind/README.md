sudo apt update
sudo apt install -y golang-go podman

go version
podman --version

go install sigs.k8s.io/kind@v0.33.0

export PATH="$(go env GOPATH)/bin:$PATH"

grep -qxF 'export PATH="$(go env GOPATH)/bin:$PATH"' ~/.bashrc ||
  echo 'export PATH="$(go env GOPATH)/bin:$PATH"' >> ~/.bashrc

source ~/.bashrc

kind version

KIND_EXPERIMENTAL_PROVIDER=podman \
  systemd-run --scope --user -p Delegate=yes \
  kind create cluster --name kind-first-cluster --wait 3m

kind get clusters
kind get nodes --name kind-first-cluster

podman exec kind-first-cluster-control-plane which kubectl

podman cp kind-first-cluster-control-plane:/usr/bin/kubectl ./kubectl

sudo install -o root -g root -m 0755 ./kubectl /usr/local/bin/kubectl

kind export kubeconfig --name kind-first-cluster

kubectl version --client
kubectl --context kind-kind-first-cluster get nodes
kubectl --context kind-kind-first-cluster get pods -A

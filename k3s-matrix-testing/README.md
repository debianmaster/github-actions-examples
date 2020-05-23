## Action
```sh
name: Matrix Testing
on: push
jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        k8s_version: [v0.8.1,v0.9.0,v1.16.9-k3s1,v1.17.4-k3s1,v1.18.2-k3s1]
    steps:
    - name: Test on k3s
      run: |
        export KUBECONFIG=/tmp/output/kubeconfig-${{ matrix.k8s_version }}.yaml
        export K3S_VERSION=${{ matrix.k8s_version }}
        
        echo "K3S_TOKEN=mytoken" > .env
        echo "K3S_KUBECONFIG_OUTPUT=${KUBECONFIG}" >> .env
        echo "K3S_KUBECONFIG_MODE=666" >> .env 
        
        docker run -d --privileged --name k3s-${K3S_VERSION} \
        --env-file .env -v /tmp/output:/tmp/output -p 6443:6443 \
        rancher/k3s:${K3S_VERSION} server 
        
        until [ -f "$KUBECONFIG" ]
        do
             sleep 2
             echo "waiting for kubeconfig file.."
        done
        sleep 2
        
        kubectl get nodes
        kubectl get po -A
 ```

## K3S to K8s Mapping


| k3s                       | k8s      |
|---------------------------|----------|
| v1.18.2-k3s1              | v1.18.2  |
| v1.17.4-k3s1              | v1.17.4  |
| v1.17.3-k3s1              | v1.17.3  |
| v1.17.2-k3s1              | v1.17.2  |
| v1.17.0-k3s.1             | v1.17.0. |
| v1.16.9-k3s1              | v1.16.9  |
| v0.10.0, 0.10.1, 0.10.2   | v1.16.2. |
| v0.9.0, 0.9.1             | v1.15.4  |
| v0.8.1                    | v1.14.6. |
| v0.8.0                    | v1.14.5. |
| v0.7.0                    | v1.14.4  |
| 0.6.0, 0.6.1              | v1.14.3. |


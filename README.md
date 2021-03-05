- Kind Documentation
    - source: [官方文档](https://kind.sigs.k8s.io/)
    - author: [[kind]]
    - 快速开始
        - 如何安装 kind 命令行工具?
            - 通过 **arkade** 安装, `arkade get kind`
            - 通过 **brew** 安装, `brew install kind`
            - 通过 **golang** 安装， `GO111MODULE="on" go get sigs.k8s.io/kind@v0.10.0`
        - 如何快速创建一个集群？
            - `kind create cluster`
        - 如何创建一个指定版本的集群?
            - `kind create cluster --image=...`
        - 如何查看集群?
            - `kind get clusters`
        - 如何删除集群?
            - `kind delete cluster`
        - 如何加载镜像到集群中?
            - `kind load docker-image my-custom-image`
        - 如何查看集群中的镜像?
            - `docker exec -it kind-control-plane crictl images`
        - 如何创建一个多节点集群?
            - 创建一个 kind 配置文件
                - `touch kind-multi-node-config.yaml`
            - 配置文件中指定一个 control-plane 节点，两个 worker 节点
                - ```yaml

# three node (two workers) cluster config
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker```
            - 创建集群
                - `kind create cluster --config kind-multi-node-config.yaml`
        - 如何创建一个控制平面高可用的集群？
            - 创建一个 kind 配置文件
                - `touch kind-control-plane-ha.yaml`
            - 配置文件中指定三个 control-plane 节点，三个 worker 节点
                - ```yaml

# a cluster with 3 control-plane nodes and 3 workers
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: control-plane
- role: control-plane
- role: worker
- role: worker
- role: worker```
            - 创建集群
                - `kind create cluster --config kind-control-plane-ha.yaml`
        - 如何指定集群的 kubernetes 版本？
            - ```yaml

kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  image: kindest/node:v1.16.4@sha256:b91a2c2317a000f3a783489dfb755064177dbc3a0b2f4147d50f04825d016f55
- role: worker
  image: kindest/node:v1.16.4@sha256:b91a2c2317a000f3a783489dfb755064177dbc3a0b2f4147d50f04825d016f55```
        - 如何导出集群日志?
            - `kind export logs`
        - 如何导出集群日志到指定的目录?
            - `kind export logs ./logs`
    - 配置
        - 最小的有效配置内容是什么？
            - ```yaml

kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4```
        - kind 配置分为集群级别和节点级别
            - Cluster-Wide Options
            - Per-Node Options
        - Cluster-Wide Options 包含哪些可配置项?
            - Feature Gates
            - Runtime Config
            - Networking
            - Nodes
        - Per-Node Options 包含哪些可配置项?
            - Extra Mounts
            - Extra Port Mappings
            - Kubeadm Config Patches
        - kubernetes 的 feature gates 是什么？
            - feature gates 指的是 kubernetes 组件上各种特性的功能开关
    - Ingress
        - 如何在集群中部署 ingress controller ？
            - 第一步，创建集群，并指定 extraPortMappings 和 node-labels
            - 第二步，在集群上部署 ingress controller
        - 如何在集群上部署 Ambassador 作为 ingress controller ？
            - 第一步，安装 CRDS
                - `kubectl apply -f https://github.com/datawire/ambassador-operator/releases/latest/download/ambassador-operator-crds.yaml`
            - 第二步，部署 Ambassador
                - `kubectl apply -n ambassador -f https://github.com/datawire/ambassador-operator/releases/latest/download/ambassador-operator-kind.yaml`
                - `kubectl wait --timeout=180s -n ambassador --for=condition=deployed ambassadorinstallations/ambassador`
            - 第三步，部署测试应用
                - `kubectl apply -f https://kind.sigs.k8s.io/examples/ingress/usage.yaml`
            - 第四步，指定 ingress class
                - `kubectl annotate ingress example-ingress kubernetes.io/ingress.class=ambassador`
            - 第五步，测试 ingress 是否正常工作
                - ```shell

# should output "foo"
curl localhost/foo
# should output "bar"
curl localhost/bar```
        - 

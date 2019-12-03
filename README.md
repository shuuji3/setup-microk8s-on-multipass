# setup-microk8s-on-multipass

Bootstrap [Microk8s](https://microk8s.io/) cluster on [multipass](https://multipass.run/) VMs.

## How to bootstrap

1.  Install multipass.

1.  Create a microk8s master node.

    ```bash
    $ multipass launch -n microk8s-master --cloud-init cloud-config.yaml
    ```
    `cloud-config.yaml` enables multipass to install the snap package of microk8s 
    and add the default user `muitipass` to `microk8s` group
    on the launch phase automatically.

1.  Create microk8s worker nodes as many as you want.

    ```bash
    $ for i in 1 2 3 do
        multipass launch -n microk8s-worker-$i --cloud-init cloud-config.yaml
    done
    ```

1. Issues join token on the master node by `microk8s.add-node` command.

    ```bash
    multipass@microk8s-master:~$ microk8s.add-node
    Join node with: microk8s.join 192.168.64.7:25000/vThtnbGvNzhERWuJQEPsjeFZPWfAAHkf
    ```

1. Join the master node on each worker nodes by `microk8s.join` command.

    ```bash
    multipass@microk8s-worker-1:~$ microk8s.join 192.168.64.7:25000/vThtnbGvNzhERWuJQEPsjeFZPWfAAHkf
    ```

1. Confirm the worker nodes has successed to join the microk8s cluster.

    ```bash
    multipass@microk8s-master:~$ microk8s.kubectl get no
    NAME              STATUS   ROLES    AGE   VERSION
    192.168.64.8      Ready    <none>   30s   v1.16.0
    microk8s-master   Ready    <none>   20m   v1.16.0
    ```

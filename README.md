# weather-api-deploy
This project is a 1st homework of MTS SRE course.

## Project description
![image](https://github.com/duke0x/weather-api-deploy/assets/4352997/f4196447-822e-4b0e-bb34-9e920f69435b)

Project consists of:
- weather-application (runs in k8s)
- postgresql cluster (runs on VMs) 2 nodes, primary-secondary replication 
- etcd (runs on VMs) for postgresql cluster state
- haproxy (runs on VM) as balancer

postgresql, etcd, haproxy can be deployed via ansible playbooks (https://github.com/vitabaks/postgresql_cluster, branch: _master_, commit: _e65abf1_).

## Deployment steps

0. Requirements.

    Use terminal to deploy, ansible & helm required.

    This deployment should be running from your pc, so please fill your ~/.ssh/config with Jump host settings like this:

    ```shell
    Host <your-public-haproxy-host>
       User <username>
       IdentityFile <your-vm-admin-private-key-file>
       Hostname <your-public-haproxy-host>
   
    Host 10.0.10.*
       ProxyJump <your-public-haproxy-host>
    ```

0. Clone this git repo.

    ```shell
    git clone --recurse-submodules git@github.com:duke0x/weather-api-deploy.git
    ```  
    This repo contains submodule, so do not forget _--recurse-submodules_ flag.

0. Enter clonned directory.

0. Apply patch.

     ```shell
     git apply --directory=postgresql_cluster pg_cluster.patch
     ```

0. Deploy etcd, postgresql and haproxy.

     ```shell
     ansible-playbook deploy_pgcluster.yml
     ```

0. Create weather db tables (cities, forecast)

    ```shell
    ansible-playbook -i postgresql_cluster/inventory create_weather_tables.yaml
    ```

0. Deploy weather-api helm-chart to k8s

   ```shell
   helm --kubeconfig <path-to-your-kubeconfig-file> install -n sre-cource-student-104 weather-api weather-api
   ```

0. Add data to weather databases (optional)

    ```shell
    ansible-playbook -i postgresql_cluster/inventory import_dummy_data.yaml
    ```

1. Check it is working

    ```shell
    curl -H "Host: weather-api" -v http://91.185.85.213/cities/1
    ```

That's it.

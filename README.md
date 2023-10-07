# weather-api-deploy
This project is a 1st homework of MTS SRE course.

## Project description
![image](https://github.com/duke0x/weather-api-deploy/assets/4352997/f4196447-822e-4b0e-bb34-9e920f69435b)

Project consists of:
- weather-application (runs in k8s)
- postgresql cluster (runs on VMs) 2 nodes, primary-secondary replication 
- etcd (runs on VMs) for postgresql cluster state
- haproxy (runs on VM) as balancer

postgresql, etcd, haproxy can be deployed via ansible playbooks (https://github.com/vitabaks/postgresql_cluster/tree/master, commit: _e65abf1_).

## Deployment steps
1. clone git repo
```shell
git clone git@github.com:vitabaks/postgresql_cluster.git
```
3. apply patch
4. deploy postgresql, etcd, haproxy
5. create weather db tables (cities, forecast)
6. add data to db
7. deploy weather-api helm-chart

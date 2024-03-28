# Kubernetes Monitoring with Prometheus

### Benefits of setting up the Prometheus within the cluster than as a server

- Deploy the prometheus as close to targets as possible.
- Make use of pre-existing k8’s infrastructure.

### Prometheus can be used to monitor for 2 purposes

- Monitor applications running on kubernetes infrastructure.
- Monitor Kubernetes cluster itself.
    - Control plane components ⇒ api-server, coredns, kube-scheduler
    - Kubelet (cAdivsor) ⇒ Exposing container level metrics.
    - Kube-state-metrics ⇒ Cluster level metric (deployments, pod metrics)
        
        ![Untitled](./Images/Screenshot%202024-03-21%20183102.png)
        
        The Kube-state-metric must be deployed as a container to access cluster level metrics
        
    - Node-exporter - Run on all nodes for host related metrics (cpu, memory, network)
        
        ![Untitled](./Images/Screenshot%202024-03-21%20183324.png)
        
        By utilizing the feature of `deamonSet` we can set to pod which contain the node-exporter will be deployed automatically when new nodes are created.
        

### Service Discovery

This is one of the feature of Prometheus. To dynamically discovery the target nodes Prometheus will reach out to `Kubernetes API` and request for all kube components, Node Exporters, Kube-state-metrics

![Untitled](./Images/Screenshot%202024-03-21%20183939.png)

which will remove the side hassle of manually entering the targetting the nodes IP and its Port. 

### How to Deploy Prometheus

Best way to deploy it is using `Helm chart` to deploy prometheus operator. In this way we can make use of helm to deploy all of the different components of pormetheus.

![Untitled](./Images/Screenshot%202024-03-22%20144540.png)

### What is Helm?

- Helm is a package manager for Kubernetes
- All application and kubernetes configs necessary for an application can be bundled (all `*.yml` files) into a package and easily deployed.

### Helm Chart

It is a collection of template and `yaml` files that convert into kubenetes manifest files.

It can be shared with others by uploading a chart to a repository. 

Helm Charts for Prometheus: 

https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack

This chart by default has these dependencies

- kube-state-metrics
- prometheus-node-exporter
- grafana

The Kube-Prometheus-Stack chart makes use of the prometheus `operator` 

### Operator

A Kubernetes `operator` is an application-specific controller that extends the K8s API to create/configure/manage instances of complex application.

It is going to manage the entire life cycle of the application its going to handle the initialization, configuration and customization of any of the application. 

It will automatically restart the application when there is modification made to the configuration files 

https://github.com/prometheus-operator/prometheus-operator

With Pormetheus Operator we create the resource of prometheus like 

- Alert Manager
- Prometheus Rule
- Alertmanager Config
- ServiceMonitor
- PodMonitor

using the `prometheus.yml` fill with the resource as `kind: Prometheus` in the yaml file.

### What is Helm?

- Helm is a package manager for Kubernetes
- All application and kubernetes configs necessary for an application can be bundled (all `.yml` files) into a package and easily deployed.

### Helm Chart

It is a collection of template and `yaml` files that convert into kubenetes manifest files.

It can be shared with others by uploading a chart to a repository.

Helm Charts for Prometheus:

https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack

This chart by default has these dependencies

- kube-state-metrics
- prometheus-node-exporter
- grafana

The Kube-Prometheus-Stack chart makes use of the prometheus `operator`

### Operator

A Kubernetes `operator` is an application-specific controller that extends the K8s API to create/configure/manage instances of complex application.

It is going to manage the entire life cycle of the application its going to handle the initialization, configuration and customization of any of the application.

It will automatically restart the application when there is modification made to the configuration files

https://github.com/prometheus-operator/prometheus-operator

With Pormetheus Operator we create the resource of prometheus like

- Alert Manager
- Prometheus Rule
- Alertmanager Config
- ServiceMonitor
- PodMonitor

using the `prometheus.yml` fill with the resource as `kind: Prometheus` in the yaml file.

## Step to Achieve

1. Install Helm
2. Once installed add prometheus-community repo using `helm repo add prometheus-community <github link>`
3. `helm repo update`
4. `helm install prometheus <name of the repo>`  Here prometheus is called ⇒ release name
5. To show the different values that we can customize `helm show value prometheus-commuity/kube-prometheus-stack`
6. To run the custom configuration file for the Premetheus u can use `helm install prometheus <name of the repo> -f <configfile.yml>`     

### Ways to Monitor the deployed application:

Configure the prometheus config file so that prometheus could scrap new targets. 

1. Additional Scrape config - Less Ideal way
    1. In the config file of pometheus which you can get it from `helm show value <repo/chart>` > value.yml
    2. In this file look of additional scrape config and un-command the part with all the jobs in it to scape added application in the cluster.
    3. Once the config file is modified use `helm upgrade <release name> <repo/cheat> -f <file.yml>` which will apply to modification to the Prometheus. 
2. Service Monitor - More optimal way.
    
    ### CDM
    
    The Pronetheus operator comes with serveral `custom resouces definitions` - **CRD** that provide a high level abstraction for deploying prometheus as well as configuring it. 
    
    `kubectl get crd` 
    
    ### Service Monitors
    
    `Service Monitors` define a set of targets for prometheus to monitor ans scrape.
    
    They allow you to avoid touching prometheus config directly and give you a declarative kubernetes syntax to defin targets.
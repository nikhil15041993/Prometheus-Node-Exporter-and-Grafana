# Prometheus-Node-Exporter-and-Grafana

Performance monitoring and alerting are very crucial to measure the performance metrics of an application running in a production environment.

## 1. Installation of Prometheus
Prometheus has a various ways of installation for example - pre-compiled binaries, from source, Docker. But to keep the installation simple we are going to use the pre-compiled binaries for installing Prometheus onto my Ubuntu Linux machine.

Goto Download Page of Prometheus and select the prometheus-x.xx.x.linux-amd64.tar.gz file for

Extract the download binary file. (Note- Replace x.xx.x with the downloaded version of Prometheus)
```
tar xvfz prometheus-x.xx.x.linux-amd64.tar.gz
```

Go into the extracted directory ``` cd prometheus-x.xx.x.linux-amd64.tar.gz ```

Start the prometheus server with the command ``` ./prometheus ```


The Prometheus server should start on port 9090

You can access the Prometheus graph UI by visint ```http://localhost:9090/graph```

You can access the Prometheus metrics UI by visint ``` http://localhost:9090/metrics ```


## 2. How to create and Install Node exporter

Node exporter is responsible for fetching the statistics from various hardware and virtual resources in the format which Prometheus can understand and with the help of the prometheus server those statistics can be exposed on port 9100.

There are many third-party Node exporters which can be used by SREs as well as DevOps based on their application needs. But primarily we look for the following metrics -

CPU usage
Memory usage
Disk usage
Network usage


### 2.1 How to Install Node exporter

After installing the Prometheus in the previous step the next package we are going to install is **Node Exporter **. Node exported is used for collecting various hardware and kernel-level metrics of your machine.

Here are the download and installation steps -

Download the binary of Node exporter based on the operating system.

Extract the download node exporter binary file. (Note- Replace x.xx.x with the downloaded version of node exporter)

```
tar xvfz node_exporter-*.*-amd64.tar.gz
```

Go into the extracted directory ``` cd xvfz node_exporter-*.*-amd64.tar.gz```

Start the node exported with the command ```./node_exporter```

Access the Node exporter metrics on the browser with URL - ```http://localhost:9100```


### 2.2 Adding node exporter scrap_configs to prometheus as a YAML Configuration

In the previous node export installation, we had Prometheus and node exporter running on the same server. But if you want to add node exporter for any remote server then you need to define scrap_configs with target hosts inside the YAML configuration.

```

global:
   scrape_interval: 15s

scrape_configs:
   - job_name: node
     static_configs:
        - targets: ['localhost:9100','100.0.0.3:9100'] 
        
```

As you can see I have added two hosts localhost, 100.0.0.3 inside the scrap_config.
Save the above configuration as YAML(ex. exporter.yaml)
Start the Prometheus server bypassing scrap_config with the help of --config.file flag

```
./prometheus --config.file=exporter.yml
```

Verify the Host status-

After adding the remote host into the node exporter configuration verify it by accessing the Prometheus target URL ```localhost:9090/targets```



## 3. Installing the Grafana

After installing the Prometheus server and node exporter let's install the Grafana for setting up visualization using Grafana dashboard. Here is the official download link of Grafana, choose the correct Grafana binary based on your operating system. For this blog post, I am going to use ubuntu Linux binary.

Steps for Installing Grafana-

In this installation, we are going to install the latest enterprise edition Grafana.

Update the package info -
```
sudo apt-get install -y apt-transport-https
sudo apt-get install -y software-properties-common wget
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
```

Add stable repository of Grafana -

```
echo "deb https://packages.grafana.com/enterprise/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```

Update repository and Install Grafana

```
sudo apt-get update
sudo apt-get install grafana-enterprise
```

Start the Grafana Server -
```
sudo systemctl daemon-reload
sudo systemctl start grafana-server
sudo systemctl status grafana-server
```

Configure Grafana to run at boot time-
```
sudo systemctl enable grafana-server.service
```

Access the Grafana Dashboard using ``` http://localhost:3000/login```

The default login Username: Password for accessing the Grafana dashboard is admin: admin
Change the default admin password after the login


## 4. Setting up the Grafana Dashboard

The next step after installing the Grafana Server would be to set up the Grafana Dashboard. Till now, we have installed the Prometheus Server, Node Exporter as well as Grafana Server but there is a connection between Prometheus and Grafana.

As I mentioned earlier Grafana is an analytics and visualization dashboard, Grafana does not store data, but instead it relies on Datasource connection towards the Prometheus server because Prometheus server is responsible for scraping and storing the data.

### 4.1 Let's create a Prometheus Datasource inside Grafana Dashboard

Goto your Grafana Dashboard and click on Gear(Settings Icon)->Data Sources

Click on Add Data Source

Select the Prometheus as preferred data source -

Enter the hostname or IP address of the prometheus server

Save and test data source

### 4.2 Import Grafana Dashboard from Grafana Labs
Now after settings the data source we can import pre-existing opensource dashboard from Grafana Labs using the Dashboard ID.

Goto Grafana Dashboard and search for Linux Memory

Click on the result and then copy the board ID .e.g 2747

Goto Import and enter the board ID .e.g. 2747

Click load and then select the data source as Prometheus

Finally Click on Import

You should be able to see the memory dashboard

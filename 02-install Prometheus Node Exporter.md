## 1. Download Node Exporter
As first step, you need to download the Node Exporter binary which is available for Linux in the official Prometheus website here.

```
wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz
```

## 2. Extract Node Exporter and move binary 
After downloading the latest version of Node Exporter, proceed to extract the content of the downloaded tar using the following command:

```
tar xvf node_exporter-1.3.1.linux-amd64.tar.gz
```
The content of the zip will be extracted in the current directory, the extracted directory will contain 3 files:

LICENSE (license text file)
node_exporter (binary)
NOTICE (license text file)

You only need to move the binary file node_exporter to the /usr/local/bin directory of your system. Switch to the node_exporter directory:

```
cd node_exporter-1.3.1.linux-amd64
```
And then copy the binary file with the following command:

```
sudo cp node_exporter /usr/local/bin
```
Then you can remove the directory that we created after extracting the zip file content:

## Exit current directory
cd ..

## Remove the extracted directory
```
rm -rf ./node_exporter-1.3.1.linux-amd64
```

## 3. Create Node Exporter User
As a good practice, create an user in the system for Node Exporter:

```
sudo useradd --no-create-home --shell /bin/false node_exporter
```
And set the owner of the binary node_exporter to the recently created user:

```
sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
```


## 4. Create and start the Node Exporter service
The Node Exporter service should always start when the server boots so it will always be available to be scrapped for information. Create the node_exporter.service file with nano:

```
sudo nano /etc/systemd/system/node_exporter.service
```
And paste the following content in the file:
```
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```
Close nano and save the changes to the file. Proceed to reload the daemon with:

```
sudo systemctl daemon-reload
```
And finally start the node_exporter service with the following command:

```
sudo systemctl start node_exporter
```
## 5. Test the Node Exporter service
As last step, access your server through the web browser at port 9100 and browse the metrics (http://your_server_ip:9100/metrics).



## 6. Configure the Server as Target on Prometheus Server

Now that we have the node exporter up and running on the server, we have to add this server a target on the Prometheus server configuration.

Note: This configuration should be done on the Prometheus server.

### Step 1: Login to the Prometheus server and open the prometheus.yml file.

```
sudo vi /etc/prometheus/prometheus.yml
```
### Step 2: Under the scrape config section add the node exporter target as shown below. Change 10.142.0.3 with your server IP where you have setup node exporter. Job name can be your server hostname or IP for identification purposes.

```
- job_name: 'node_exporter_metrics'
  scrape_interval: 5s
  static_configs:
    - targets: ['10.142.0.3:9100']
```
### Step 3: Restart the prometheus service for the configuration changes to take place.

```
sudo systemctl restart prometheus
```
Now, if you check the target in prometheus web UI (http://<prometheus-IP>:9090/targets) , you will be able to see the status as shown below.


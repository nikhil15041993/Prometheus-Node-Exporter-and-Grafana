# What is Prometheus?
Prometheus monitoring solution is a free and open-source solution for monitoring metrics, events, and alerts. It collects and records metrics from servers, containers, and applications. In addition to providing a flexible query language (PromQL), and powerful visualization tools, it also provides an alerting mechanism that sends notifications when needed.

## install Prometheus on Ubuntu 22.04

In this section, you'll go through the steps on how to install Prometheus on Ubuntu 22.04.

### Step 1 - Update System Packages
You should first update your system's package list to ensure that you are using the most recent packages. To accomplish this, issue the following command:

```
sudo apt update
```
### Step 2 - Create a System User for Prometheus
Now create a group and a system user for Prometheus. To create a group and then add a user to the group, run the following command:

```
sudo groupadd --system prometheus
sudo useradd -s /sbin/nologin --system -g prometheus prometheus
add group and user
```
This will create a system user and group named "prometheus" for Prometheus with limited privileges, reducing the risk of unauthorized access.

### Step 3 - Create Directories for Prometheus
To store configuration files and libraries for Prometheus, you need to create a few directories. The directories will be located in the /etc and the /var/lib directory respectively. Use the commands below to create the directories:
```
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
create directories for Prometheus
```
### Step 4 - Download Prometheus and Extract Files
To download the latest update, go to the Prometheus official downloads site and copy the download link for Linux Operating System. Download using wget and the link you copied like so:
```
wget https://github.com/prometheus/prometheus/releases/download/v2.45.0-rc.0/prometheus-2.45.0-rc.0.linux-amd64.tar.gz
```
You should see it being downloaded.

download Prometheus

After the download has been completed, run the following command to extract the contents of the downloaded file:

```
tar vxf prometheus*.tar.gz
```
extract Prometheus file

### Step 5- Navigate to the Prometheus Directory
After extracting the files, navigate to the newly extracted Prometheus directory using the following command:

```
cd prometheus*/
```
## Configuring Prometes 

### Step 1 - Move the Binary Files & Set Owner
First, you need to move some binary files (prometheus and promtool) and change the ownership of the files to the "prometheus" user and group. You can do this with the following commands:

```
sudo mv prometheus /usr/local/bin
sudo mv promtool /usr/local/bin
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool
move binary files and set owner
```

### Step 2 - Move the Configuration Files & Set Owner
Next, move the configuration files and set their ownership so that Prometheus can access them. To do this, run the following commands:
```
sudo mv consoles /etc/prometheus
sudo mv console_libraries /etc/prometheus
sudo mv prometheus.yml /etc/prometheus
sudo chown prometheus:prometheus /etc/prometheus
sudo chown -R prometheus:prometheus /etc/prometheus/consoles
sudo chown -R prometheus:prometheus /etc/prometheus/console_libraries
sudo chown -R prometheus:prometheus /var/lib/prometheus
move configuration files and set owner
```

The prometheus.yml file is the main Prometheus configuration file. It includes settings for targets to be monitored, data scraping frequency, data processing, and storage. You can set alerting rules and notification conditions in the file. You don't need to modify this file for this demonstration but feel free to open it in an editor to take a closer look at its contents.
```
sudo nano /etc/prometheus/prometheus.yml
```
## Create Prometheus Systemd Service

Now, you need to create a system service file for Prometheus. Create and open a prometheus.service file with the Nano text editor using:
```
sudo nano /etc/systemd/system/prometheus.service
```
Include these settings to the file, save, and exit:
```
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```
### Reload Systemd
You need to reload the system configuration files after saving the prometheus.service file so that changes made are recognized by the system. Reload the system configuration files using the following:
```
sudo systemctl daemon-reload
```
Reload systemd

### Step 4 - Start Prometheus Service
Next, you want to enable and start your Prometheus service. Do this using the following commands:
```
sudo systemctl enable prometheus
sudo systemctl start prometheus
```

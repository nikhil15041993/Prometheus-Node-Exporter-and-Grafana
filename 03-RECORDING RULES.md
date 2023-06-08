### Configuring rules
Prometheus supports two types of rules which may be configured and then evaluated at regular intervals: recording rules and alerting rules. To include rules in Prometheus, create a file containing the necessary rule statements and have Prometheus load the file via the rule_files field in the Prometheus configuration. Rule files use YAML.

### Syntax-checking rules
To quickly check whether a rule file is syntactically correct without starting a Prometheus server, you can use Prometheus's promtool command-line utility tool:

```
promtool check rules /path/to/example.rules.yml
```
### Recording rules
Recording rules allow you to precompute frequently needed or computationally expensive expressions and save their result as a new set of time series.

Types of rules in Prometheus?

Prometheus supports two types of rules which may be configured and then evaluated at regular intervals:

* Recording rules are for pre-calculating frequently used or computationally expensive queries. The results of those rules are saved into their own time series.
* Alerting rules on the other hand enable you to specify the conditions that an alert should be fired to an external service like Slack. These are based on PromQL queries.

## How to add Recording rules and Alerting rules in Prometheus?

* Step 1 – You can create prometheus.rules.yml file in the same directory where prometheus.yml is stored, e.g.
```
/etc/prometheus/prometheus.rules.yml.
```

prometheus.rules.yml
```
groups:
  - name: custom_rules
    rules:
      - record: node_memory_MemFree_percent
        expr: 100 - (100 * node_memory_MemFree_bytes / node_memory_MemTotal_bytes)
``` 
* Step 2 – Now lets add the prometheus_rules.yml reference to the prometheus.yml rule_files section.
* Step 3 – and restart the prometheus service.
* Step 4 – Refresh the Prometheus user interface and check the drop down.

How to check rules config file?
```
promtool check rules /etc/prometheus/prometheus.rules.yml
```
 
## AlertManager
First, we need to download the latest binary of AlertManager.

```

sudo su
cd /opt/
wget https://github.com/prometheus/alertmanager/releases/download/v0.11.0/alertmanager-0.11.0.linux-amd64.tar.gz
tar -xvzf alertmanager-0.11.0.linux-amd64.tar.gz
mv alertmanager-0.11.0.linux-amd64/alertmanager /usr/local/bin/
```

AlertManager Configuration

The AlertManager uses a configuration file named alertmanager.ymlThis file is contained in the extracted directory. However, it is not of our use. That’s why we need to create our own alertmanager.yml

```

mkdir /etc/alertmanager/
sudo nano /etc/alertmanager/alertmanager.yml
```
```
global:
  resolve_timeout: 5m

route:
  group_by: ['alertname']
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 10s
  receiver: 'email'
receivers:
- name: 'email'
  email_configs:
  - to: 'receiver_mail_id@gmail.com'
    from: 'mail_id@gmail.com'
    smarthost: smtp.gmail.com:587
    auth_username: 'mail_id@gmail.com'
    auth_identity: 'mail_id@gmail.com'
    auth_password: 'password'
inhibit_rules:
  - source_match:
      severity: 'critical'
    target_match:
      severity: 'warning'
    equal: ['alertname', 'dev', 'instance']
 ```
 
 Still, the Alertmanager isn’t added to our Prometheus, we need to update our prometheus.yml file by adding the location of our Alertmanager.
 
 ```
 alerting:
  alertmanagers:
  - static_configs:
    - targets:
      - localhost:9093
```      
 



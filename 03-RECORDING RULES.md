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
- name: Hardware alerts
  rules:
  - alert: Node down
    expr: up{job="node_exporter"} == 0
    for: 3m
    labels:
      severity: warning
    annotations:
      title: Node {{ $labels.instance }} is down
      description: Failed to scrape {{ $labels.job }} on {{ $labels.instance }} for more than 3 minutes. Node seems down.
``` 
* Step 2 – Now lets add the prometheus_rules.yml reference to the prometheus.yml rule_files section.
* Step 3 – and restart the prometheus service.
* Step 4 – Refresh the Prometheus user interface and check the drop down.

How to check rules config file?
```
promtool check rules /etc/prometheus/prometheus.rules.yml
```
     



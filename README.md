# Tor node Grafana dashboard

Grafana dashboard to display stats from Tor node stored in prometheus DB  
![Dashboard view](Dashboard_full.jpg "Dashboard view") 


## Getting started

Based on [this guide on torproject.org](https://support.torproject.org/id/relay-operators/relay-bridge-overloaded/)
Prometheus exporter was only supported in tor version 0.4.7.1-alpha on time of writing

1. Enable prometheuth exporter on tor process
    "It's important to understand that exposing tor metrics publicly is dangerous to the Tor network users. Please take extra precaution and care when opening this port. Set a very strict access policy with MetricsPortPolicy and consider using your operating systems firewall features for defense in depth."
    Add or uncoment folowing two tines to *torrc* file 
    ```text
    ## Prometheus exporter
    MetricsPort x.x.x.x:9035 prometheus
    MetricsPortPolicy accept y.y.y.y

    ```
    x.x.x.x is ip address where Tor process will listen for connection from prometheus.<br> 
    **DO NOT USE PUBLIC IP THERE**. Use 127.0.0.1 if TOR and prometheuts are running at same host
    y.y.y.y is ip from where TOR will accept connectios. Keep it as restrictive as possible. Use 127.0.0.1 if TOR and prometheuts are running at same host
1. Restart TOR proccess
1. Confugure job for prometheuts to scrap data from tor
   Add following to *prometheus.yml* configuration file
   ```yaml
    scrape_configs:  # this line should be already in config
        - job_name: tor-exit
            metrics_path: /metrics
            scheme: http  # https is not yet supported by tor
            static_configs:
            - targets:
                - x.x.x.x:9035
    ```
   
1. Restart prometheus process
1. Check prometheuth console */targets* that prometheus can scrap data from TOR
1. Go to your grafana and import new dashboard tor_stats.json. Point it to prometheus datasource

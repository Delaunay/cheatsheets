
* https://prometheus.io/docs/prometheus/latest/federation/

# Setup Prometheus Node to listen to Node Exporter
    
    
    > wget https://github.com/prometheus/prometheus/releases/download/v2.8.1/prometheus-2.8.1.linux-amd64.tar.gz
    > tar xvfz prometheus-*.tar.gz
    > mv Prometheus-2.8.1.linux-amd64/ prometheus
    > rm Prometheus-2.8.1.linux-amd64.tar.gz
    > cd prometheus
    > sudo ln -s ./prometheus /usr/bin
    > vi prometheus.yml 
    global:
      scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
      evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.

    scrape_configs:
      # node_explorer config
      - job_name: 'node'
        scrape_interval: 5s
        static_configs:
        - targets: ['localhost:9100']
        
    > nohup ./prometheus > prometheus.log 2>&1 &
    prometheus will start on port 9090
        
# Setup Node Exporter to get Node usage


    > mkdir -p prometheus/collectors
    > cd prometheus/collectors
    > wget https://github.com/prometheus/node_exporter/releases/download/v0.17.0/node_exporter-0.17.0.linux-amd64.tar.gz
    > tar xvfz node_exporter-0.17.0.linux-amd64.tar.gz 
    > mv node_exporter-0.17.0.linux-amd64/ node_exporter
    > rm node_exporter-0.17.0.linux-amd64.tar.gz 
    > sudo ln -s node_exporter/node_exporter /usr/bin
    > sudo vi /etc/systemd/system/node_exporter.service
    [Unit]
    Description=Node Stat Exporter
    After=network.target
    StartLimitIntervalSec=0

    [Service]
    Type=simple
    Restart=always
    RestartSec=1
    User=root
    ExecStart=/usr/bin/node_exporter

    [Install]
    WantedBy=multi-user.target
    
    > sudo systemctl enable node_exporter
    > sudo systemctl start node_exporter

    Node exporter use port 9100
    
    
# Setup Prometheus Federation

    > vi prometheus.yml 
    - job_name: 'federate'
      scrape_interval: 15s

      honor_labels: true
      metrics_path: '/federate'

      params:
        'match[]':
          - '{job="node"}'
          - '{__name__=~"job:.*"}'
  
      #  federates any series with the label job="prometheus" or 
      # a metric name starting with job: from the Prometheus servers at source-prometheus-{1,2,3}:9090
      static_configs:
        - targets:
          #- 'source-prometheus-1:9090'
          #- 'source-prometheus-2:9090'
          #- 'source-prometheus-3:9090'


![prometheus](https://github.com/Delaunay/cheatsheets/blob/master/assets/prometheus.png)
![prometheus_overall](https://github.com/Delaunay/cheatsheets/blob/master/assets/prometheus_overall.png)

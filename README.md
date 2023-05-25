Install Prometheus and Grafana and collect metrics from all ec2 on AWS. Which have a special tag.
- Prometheus: https://prometheus.io/

Step 1: Create a IAM role with the following policy
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt1488499227000",
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeInstances",
                "ec2:DescribeTags"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```
Step 2: Create a ec2 instance with the above IAM role and install Prometheus and Grafana on it.
Step 3: Create a IAM user with the following policy
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt1488499227000",
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeInstances",
                "ec2:DescribeTags"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```
Step 3: Assume role to EC2 with role created before
Step 4: ssh to EC2
- Install Prometheus by docker-compose (ensure the instance was install docker and docker-compose before if not install it with command: 
```
 sudo wget -O - https://gist.githubusercontent.com/shawnsavour/2722086405f7670471908294eed84e76/raw/734f666c7c9bdabe2d09b769f5c7cf48ee756394/debian-docker.sh | sudo bash 
 ```
- create a directory to store prometheus config file 
mkdir prometheus
cd prometheus
- create a file prometheus.yml with content:
```
global:
  scrape_interval: 30s
  scrape_timeout: 10s
scrape_configs:
  - job_name: prometheus
    scrape_interval: 30s 
    static_configs:
    - targets: ["localhost:9090"]

  - job_name: ec2-discovery-node-exporter
    honor_timestamps: true
    scrape_interval: 30s
    scrape_timeout: 10s
    metrics_path: /metrics
    scheme: http
    follow_redirects: true
    relabel_configs:
    - source_labels: [__meta_ec2_tag_Name]
      separator: ;
      regex: (.*)
      target_label: name
      replacement: $1
      action: replace
    - source_labels: [__meta_ec2_public_ip]
      separator: ;
      regex: (.*)
      target_label: instance
      replacement: $1
      action: replace
    - source_labels: [__meta_ec2_public_ip]
      replacement: ${1}:9100
      target_label: __address__
    # - source_labels: [__meta_ec2_tag_Service]
    #   separator: ;
    #   regex: (.*)
    #   target_label: group
    #   replacement: $1
    #   action: replace
    # - source_labels: [__meta_ec2_tag_Prometheus]
    #   separator: ;
    #   regex: (.*)
    #   target_label: Prometheus
    #   replacement: $1
    #   action: replace
    # - source_labels: [__meta_ec2_tag_Project]
    #   separator: ;
    #   regex: (.*)
    #   target_label: Project
    #   replacement: $1
    #   action: replace
    ec2_sd_configs:
    - endpoint: ""
      region: ap-southeast-1
      refresh_interval: 30s
      port: 9100
      filters:
      - name: tag:Prometheus
        values:
        - Enable


```
-   Install prometheus + grafana: docker-compose up -d



# prometheus config
Update the cAdvisor address

On Docker host create prometheus.yml in /home/docker/prom/ with the following content:

### prometheus.yml ###
global:
  scrape_interval: 15s
  evaluation_interval: 15s
rule_files:
  - '/etc/prometheus/alert.rules'

# A scrape configuration for cadvisor:
scrape_configs:
  - job_name: 'cadvisor'
    scrape_interval: 5s
    scrape_timeout: 10s
    target_groups:
      - targets: ['192.168.99.102:8080']

===============================
Install cAdvisor:
docker run --detach=true --volume=/:/rootfs:ro --volume=/var/run:/var/run:rw --volume=/sys:/sys:ro --volume=/var/lib/docker/:/var/lib/docker:ro --publish=8080:8080 --privileged=true --name=cadvisor google/cadvisor:latest

Install Prometheus:
docker run -d -p 9090:9090 -v /home/docker/prom/prometheus.yml:/etc/prometheus/prometheus.yml --name=prometheus prom/prometheus

Create PromDash DB volume:
docker run   --volume=/tmp/prom:/tmp/prom   -e DATABASE_URL=sqlite3:/tmp/prom/file.sqlite3 prom/promdash ./bin/rake db:migrate

Install PromDash:
docker run   --detach=true   --volume=/tmp/prom:/tmp/prom   -e DATABASE_URL=sqlite3:/tmp/prom/file.sqlite3   --publish=3000:3000    --name=promdash prom/promdash


Some expressions:
container_cpu_usage_seconds_total{job="cadvisor",name="cadvisor"}
container_cpu_usage_seconds_total{name=~"^webdockercompose_web"}
container_memory_usage_bytes{name=~"^webdockercompose_web"}
.

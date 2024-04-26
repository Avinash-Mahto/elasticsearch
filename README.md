## elasticsearch

# Install Docker

# Create docker network
$ docker network create elastic-demo
# Pull the Elasticsearch Docker image
$ docker pull docker.elastic.co/elasticsearch/elasticsearch:8.13.2
# Install Cosign
$ curl -O -L "https://github.com/sigstore/cosign/releases/latest/download/cosign-linux-amd64"
$ sudo mv cosign-linux-amd64 /usr/local/bin/cosign
$ sudo chmod +x /usr/local/bin/cosign

Cosign: It is command line utility that can sign and verify software artifact

# Now use cosign to verify Elasticsearch image's signature.
$ wget https://artifacts.elastic.co/cosign.pub
$ cosign verify --key cosign.pub docker.elastic.co/elasticsearch/elasticsearch:8.13.2
# Start an Elasticsearch container
$ docker run --name es-demo --net elastic-demo -p 9200:9200 -it -m 1GB docker.elastic.co/elasticsearch/elasticsearch:8.13.2

Note: Copy the generated elastic password and enrollment token. These credentials are only shown when you start Elasticsearch for the first time

In baove command if you receive error, just increase the size of vm.max_map_count. The default value is 65530

Replace 65530 with by following below steps

$ sysctl vm.max_map_count
$ sudo nano /etc/sysctl.conf
vm.max_map_count=262144
$ sudo sysctl -p

If you want to regenerate the credentials use the following commands.

$ docker exec -it es-demo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic
$ docker exec -it es-demo /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana

# Export password as an environment variable in your shell.
$ export ELASTIC_PASSWORD="your_password"

# Copy the http_ca.crt SSL certificate from the container to your local machine
$ docker cp es-demo:/usr/share/elasticsearch/config/certs/http_ca.crt 

# Make a REST API call to Elasticsearch to ensure the Elasticsearch container is running
$ curl --cacert http_ca.crt -u elastic:$ELASTIC_PASSWORD https://localhost:9200

## Kibana installation & Running

# Pull the Kibana Docker image
$ docker pull docker.elastic.co/kibana/kibana:8.13.2
# Verify the Kibana image’s signature (Optional)
$ wget https://artifacts.elastic.co/cosign.pub
$ cosign verify --key cosign.pub docker.elastic.co/kibana/kibana:8.13.2
# Start a Kibana container
$ docker run --name kib01 --net elastic -p 5601:5601 docker.elastic.co/kibana/kibana:8.13.2

Note: Just in case of you missed previously generated token the regenrate it using below command
$ docker exec -it es-demo /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana











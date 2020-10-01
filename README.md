<p align="center">
<img width="680px" src="https://user-images.githubusercontent.com/16992394/65840473-f70ca780-e319-11e9-9245-29ec0a8948d6.png">
</p>
<h2 align="center">Elastic Stack on Docker, with preconfigured security, tools, self-monitoring, and Prometheus Metrics Exporters</h2>
<h4 align="center">With tools like Curator, Rubban, ElastAlert for Alerting.</h4>
<p align="center">
   <a>
      <img src="https://img.shields.io/badge/Elastic%20Stack-7.9.1-blue?style=flat&logo=elasticsearch" alt="Elastic Stack Version 7^^">
   </a>
   <a>
      <img src="https://img.shields.io/github/v/tag/sherifabdlnaby/elastdocker?label=release&amp;sort=semver">
    </a>
   <a>
      <img src="https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat" alt="contributions welcome">
   </a>
   <a href="https://github.com/sherifabdlnaby/elastdocker/network">
      <img src="https://img.shields.io/github/forks/sherifabdlnaby/elastdocker.svg" alt="GitHub forks">
   </a>
   <a href="https://github.com/sherifabdlnaby/elastdocker/issues">
        <img src="https://img.shields.io/github/issues/sherifabdlnaby/elastdocker.svg" alt="GitHub issues">
   </a>
   <a href="https://raw.githubusercontent.com/sherifabdlnaby/elastdocker/blob/master/LICENSE">
      <img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="GitHub license">
   </a>
</p>

# Introduction
Elastic Stack (AKA **ELK**) Docker Composition, preconfigured with **Security**, **Monitoring**, and Tools Up with a Single Command.

Based on [Official Elastic Docker Images](https://www.docker.elastic.co/)

Stack Version: [7.9.1](https://www.elastic.co/blog/elastic-stack-7-9-1-released).
> You can change Elastic Stack version by setting `ELK_VERSION` in `.env` file and rebuild your images. Any version >= 7.0.0 is compatible with this template.

### Main Features 📜

- Configured as Production Single Node Cluster. (With a multi-node cluster option for experimenting).
- Deployed on a Single Docker Host or a Docker Swarm Cluster.
- Security Enabled (under basic license).
- SSL Enabled for Transport Layer and Kibana.
- Use Docker-Compose and `.env` to configure your entire stack parameters.
- Persist Elasticsearch's Keystore and SSL Certifications.
- Self-Monitoring Metrics Enabled.
- Prometheus Exporters for Stack Metrics.
- Embedded Container Healthchecks for Stack Images.
- [ElastAlert](https://github.com/Yelp/elastalert) preconfigured for Alerting.
- [Curator](https://github.com/elastic/curator) with Crond preconfigured for Automated Scheduled tasks (e.g Snapshots to S3).
- [Rubban](https://github.com/sherifabdlnaby/rubban) for Kibana curating tasks.

-----

# Requirements

- [Docker 17.05 or higher](https://docs.docker.com/install/)
- [Docker-Compose 3 or higher](https://docs.docker.com/compose/install/)

# Setup

1. Clone the Repository
     ```bash
     git clone https://github.com/eamonfoy/elastic-stack-docker.git
     ```
    
2. Initialize Elasticsearch Keystore and SSL Certificates
    ```shell
    $ make setup
    ```
3. Start Elastic Stack
    ```shell
    $ make elk
    ---- OR ----
    $ docker-compose up -d
    ```
4. Visit Kibana at [https://localhost:5601](https://localhost:5601)

    Notice that Kibana is configured to use HTTPS, so you'll need to write `https://` before `localhost:5601` in the browser.

    Username: `elastic` Password: `changeme`

    > Modify `.env` file for your needs, most importantly `ELASTIC_PASSWORD` that setup your superuser `elastic`'s password, `ELASTICSEARCH_HEAP` & `LOGSTASH_HEAP` for Elasticsearch & Logstash Heap Size.

Whatever your Host (e.g AWS EC2, Azure, On-premise server), once you expose your host to the network ELK component will be accessible on their respective ports.

#### Reset everything, Remove all containers, and delete **DATA**!
```shell
$ make prune
```

</p>
</details>

# Configuration
* Some Configuration are parameterized in the `.env` file.
  * `ELASTIC_PASSWORD`, user `elastic`'s password (default: `changeme` _pls_).
  * `ELK_VERSION` Elastic Stack Version (default: `7.9.1`)
  * `ELASTICSEARCH_HEAP`, how much Elasticsearch allocate from memory (default: 1GB -good for development only-)
  * `LOGSTASH_HEAP`, how much Logstash allocate from memory.
  * Other configurations which their such as cluster name, and node name, etc.
* Elasticsearch Configuration in `elasticsearch.yml` at `./elasticsearch/config`.
* Logstash Configuration in `logstash.yml` at `./elasticsearch/config/logstash.yml`.
* Logstash Pipeline in `main.conf` at `./elasticsearch/pipeline/main.conf`.
* Kibana Configuration in `kibana.yml` at `./kibana/config`.
* ElastAlert Configuration in `./tools/elastalert/config`.
* ElastAlert Alert rules in `./tools/elastalert/rules`, [head to ElastAlert docs to lookup how to create alerts.](https://elastalert.readthedocs.io/en/latest/elastalert.html)
* Curator Actions at `./tools/curator/actions` and `./tools/curator/crontab`.
* Rubban Configuration using Docker-Compose passed Environment Variables.

### Setting Up Keystore
You can extend the Keystore generation script by adding keys to `./setup/keystore.sh` script. (e.g Add S3 Snapshot Repository Credentials)

To Re-generate Keystore:
```
make keystore
```
### Notes

- Makefile is a wrapper around `Docker-Compose` commands, use `make help` to know every command.

- Elasticsearch will save its data to a volume named `elasticsearch-data`

- Elasticsearch Keystore (that contains passwords and credentials) and SSL Certificate are generated in the `./secrets` directory by the setup command.

- Make sure to run `make setup` if you changed `ELASTIC_PASSWORD` and to restart the stack afterwards.

- For Linux Users it's recommended to set the following configuration (run as `root`)
    ```
    sysctl -w vm.max_map_count=262144
    ```
    By default, Virtual Memory [is not enough](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html).

---------------------------


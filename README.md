# docker-dnsmasq

dnsmasq in a docker container, configurable via a [simple web UI](https://github.com/jpillora/webproc)

[![Docker Pulls](https://img.shields.io/docker/pulls/jpillora/dnsmasq.svg)][dockerhub]
[![Image Size](https://images.microbadger.com/badges/image/jpillora/dnsmasq.svg)][dockerhub]

### Usage

1. Create a [`/opt/dnsmasq.conf`](http://oss.segetech.com/intra/srv/dnsmasq.conf) file on the Docker host

   ```ini
   #dnsmasq config, for a complete example, see:
   #  http://oss.segetech.com/intra/srv/dnsmasq.conf
   #log all dns queries
   log-queries
   #dont use hosts nameservers
   no-resolv
   #use cloudflare as default nameservers, prefer 1^4
   server=1.0.0.1
   server=1.1.1.1
   strict-order
   #serve all .company queries using a specific nameserver
   server=/company/10.0.0.1
   #explicitly define host-ip mappings
   address=/myhost.company/10.0.0.2
   ```

1. Run the container

   ```
   $ docker run \
   	--name dnsmasq \
   	-d \
   	-p 53:53/udp \
   	-p 5380:8080 \
   	-v /opt/dnsmasq.conf:/etc/dnsmasq.conf \
   	--log-opt "max-size=100m" \
   	-e "HTTP_USER=foo" \
   	-e "HTTP_PASS=bar" \
   	--restart unless-stopped \
   	joweisberg/dnsmasq
   ```

1. Run the container vie docker-compose
   ```
   dnsmasq:
    container_name: dnsmasq
    image: joweisberg/dnsmasq:latest
    volumes:
      - /opt/dnsmasq.conf:/etc/dnsmasq.conf
    restart: unless-stopped
    ports:
      - 53:53/udp
      - 5380:8080
   ```
   
1. Visit `http://<docker-host>:5380`, authenticate with `foo/bar` and you should see

   <img width="833" alt="screen shot 2017-10-15 at 1 41 21 am" src="https://user-images.githubusercontent.com/633843/31580966-baacba62-b1a9-11e7-8439-ca1ddfe828dd.png">

1. Test it out with

   ```
   $ host myhost.company <docker-host>
   Using domain server:
   Name: <docker-host>
   Address: <docker-host>#53
   Aliases:

   myhost.company has address 10.0.0.2
   ```

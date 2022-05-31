
FROM ubuntu:20.04

RUN apt-get update
RUN apt-get -y install cmake make gcc g++ flex bison libpcap-dev libssl-dev python3 python3-dev swig zlib1g-dev gpg curl nano apt-utils

RUN apt update && apt upgrade -y
RUN apt install -y software-properties-common wget lsb-release 
RUN wget https://packages.ntop.org/apt-stable/20.04/all/apt-ntop-stable.deb
RUN apt install ./apt-ntop-stable.deb
RUN apt update
RUN apt install -y pfring
RUN apt install -y curl gnupg2



RUN echo 'deb http://download.opensuse.org/repositories/security:/zeek/xUbuntu_20.04/ /' | tee /etc/apt/sources.list.d/security:zeek.list
RUN curl -fsSL https://download.opensuse.org/repositories/security:zeek/xUbuntu_20.04/Release.key | gpg --dearmor | tee /etc/apt/trusted.gpg.d/security_zeek.gpg

RUN apt update
RUN apt-cache policy zeek
RUN DEBIAN_FRONTEND=noninteractive apt-get -yq install zeek
ENV PATH=$PATH:/opt/zeek/bin

COPY /configs/zeek/node.cfg /opt/zeek/etc/
COPY /configs/zeek/networks.cfg /opt/zeek/etc/
COPY /configs/zeek/zeekctl.cfg.in /opt/zeek/etc/
COPY /configs/zeek/zeekctl.cfg /opt/zeek/etc/
COPY /configs/zeek/local.zeek /opt/zeek/share/zeek/site/


#Elastic 
RUN wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | apt-key add -
RUN apt-get install apt-transport-https
RUN echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | tee -a /etc/apt/sources.list.d/elastic-7.x.list
RUN apt-get update && apt-get install elasticsearch
COPY /configs/elastic/elasticsearch.yml /etc/elasticsearch/
COPY /configs/elastic/jvm.options /etc/elasticsearch/


#Kibana
RUN apt-get update && apt-get install kibana

#Filebeat
RUN apt-get update && apt-get install filebeat
COPY /configs/filebeat/zeek.yml /etc/filebeat/modules.d/

#Elastalert
RUN apt-get install -y pip python3-dev libffi-dev libssl-dev
RUN apt-get -y install python3-setuptools
RUN pip install pyOpenSSL
RUN git clone https://github.com/Yelp/elastalert.git
WORKDIR "/elastalert"
RUN python3 setup.py install

WORKDIR "/"
RUN pip install "elasticsearch>=5.0.0"
COPY /configs/elastalert/config.yaml /elastalert/
COPY /configs/elastalert/example_frequency.yaml /elastalert/example_rules/
#RUN elastalert-create-index




#RUN zeekctl deploy
#RUN zeekctl start


FROM elasticsearch:5.6.9-alpine

RUN apk update && apk upgrade

RUN sed -i "s|LimitFSIZE=infinity|a LimitMEMLOCK=infinity|" /usr/lib/systemd/system/elasticsearch.service && \
		sed -i "s|#bootstrap.memory_lock: true|bootstrap.memory_lock: true|" /etc/elasticsearch/elasticsearch.yml && \
		sed -i "s|#network.host: 192.168.0.1|network.host: 0.0.0.0|" /etc/elasticsearch/elasticsearch.yml && \
		sed -i "s|#MAX_LOCKED_MEMORY=unlimited|MAX_LOCKED_MEMORY=unlimited|" /etc/default/elasticsearch && \
		sed -i "s|-Xms2g|-Xms1g|" /etc/elasticsearch/jvm.options && \
		sed -i "s|-Xmx2g|-Xmx1g|" /etc/elasticsearch/jvm.options
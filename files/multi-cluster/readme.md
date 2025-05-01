# Elastic
### 1st create certs step
1. you will create a ca, from ca you will create crt and key using pem format on node-1 in /etc/elasticsearch/certs 
  using command: /usr/share/elasticsearch/bin/elasticsearch-certutil
root@ubuntu-jammy:/usr/share/elasticsearch/bin# /usr/share/elasticsearch/bin/elasticsearch-certutil ca --pem --out /etc/elasticsearch/certs/ca_elastic.zip
cd /etc/elasticsearch/certs/
root@kibana:/etc/elasticsearch/certs# unzip ca_elastic.zip

root@ubuntu-jammy:/usr/share/elasticsearch/bin# /usr/share/elasticsearch/bin/elasticsearch-certutil cert --out /etc/elasticsearch/certs/certificate.zip --name elastic --ca-cert /etc/elasticsearch/certs/ca/ca.crt --ca-key /etc/elasticsearch/certs/ca/ca.key --dns sys.fcc --pem
cd /etc/elasticsearch/certs/
root@kibana:/etc/elasticsearch/certs# unzip certificate.zip


### 2nd copy certs to node-2 and node-3 step
you will copy ca and elastic certs from /etc/elasticsearch/certs in remote server node-2 and node-3
curl cheat.sh/scp
scp <file> <user>@<host>:<dest>

### 3rd change permissions for files on each node step
you will give the permissions
   75  chown -R elasticsearch:elasticsearch *
   79  chmod 755 *
  104  sed -E '/^\.*(#|$)/d' /etc/elasticsearch/elasticsearch.yml
  
### 4th monitor logs
start elasticsearch service on node-1
then sequentially on node-2 and node-3

  107  telnet 10.211.55.11 9200
  111  tail -f /var/log/elasticsearch/future.log

### 5th verify 
  45  curl -u elastic:N=kyzqR1JWHwFUj=frQo https://10.211.55.11:9200/_cat/master -k
root@elk:~# curl -u elastic:N=kyzqR1JWHwFUj=frQo https://10.211.55.11:9200/_cat/nodes -k
10.211.55.11 26 66 2 0.03 0.06 0.01 cdfhilmrstw - node-1
10.211.55.12 30 89 2 0.02 0.02 0.00 cdfhilmrstw * node-2
10.211.55.13 20 88 2 0.07 0.04 0.01 cdfhilmrstw - node-3
root@elk:~# curl -u elastic:N=kyzqR1JWHwFUj=frQo https://10.211.55.11:9200/_cat/master -k
H9_-E2DnQCWx0dU8LK1v6w 10.211.55.12 10.211.55.12 node-2

root@elk:~# curl -u elastic:N=kyzqR1JWHwFUj=frQo https://10.211.55.11:9200/_cluster/health -k
{"cluster_name":"future","status":"green","timed_out":false,"number_of_nodes":3,"number_of_data_nodes":3,"active_primary_shards":3,"active_shards":6,"relocating_shards":0,"initializing_shards":0,"unassigned_shards":0,"unassigned_primary_shards":0,"delayed_unassigned_shards":0,"number_of_pending_tasks":0,"number_of_in_flight_fetch":0,"task_max_waiting_in_queue_millis":0,"active_shards_percent_as_number":100.0}root@elk:~#


# Kibana
create the kibana user
Copy elasticsearch certs to the kibana

we are going to copy the certs from elasticsearch to kibana

root@elk:/etc/elasticsearch# cd /etc/kibana/
root@elk:/etc/kibana# ls
kibana.keystore  kibana.yml  node.options
root@elk:/etc/kibana# mkdir certs
root@elk:/etc/kibana# ls
certs  kibana.keystore  kibana.yml  node.options
root@elk:/etc/kibana# cd certs/


root@elk:/etc/kibana/certs# cp -r /etc/elasticsearch/certs/ca .
root@elk:/etc/kibana/certs# ls ca/
ca.crt  ca.key
root@elk:/etc/kibana/certs# cp -r /etc/elasticsearch/certs/elastic .
root@elk:/etc/kibana/certs# ls
ca  elastic
root@elk:/etc/kibana/certs#


drwxr-s---   3 root kibana 4096 May  1 17:23 ./
drwxr-xr-x 114 root root   4096 Apr 30 23:53 ../
drwxr-sr-x   4 root kibana 4096 Apr 30 23:57 certs/
-rw-rw----   1 root kibana  130 Apr 30 23:53 kibana.keystore
-rw-rw----   1 root kibana 7879 May  1 17:23 kibana.yml
-rw-rw-r--   1 root kibana  447 Jun 11  2024 node.options
root@elk:/etc/kibana# chown -R kibana:kibana certs/
root@elk:/etc/kibana# chown -R kibana:kibana *
root@elk:/etc/kibana# chmod -R 777 *
root@elk:/etc/kibana# ll
total 28
drwxr-s---   3 root   kibana 4096 May  1 17:23 ./
drwxr-xr-x 114 root   root   4096 Apr 30 23:53 ../
drwxrwsrwx   4 kibana kibana 4096 Apr 30 23:57 certs/
-rwxrwxrwx   1 kibana kibana  130 Apr 30 23:53 kibana.keystore*
-rwxrwxrwx   1 kibana kibana 7879 May  1 17:23 kibana.yml*
-rwxrwxrwx   1 kibana kibana  447 Jun 11  2024 node.options*
root@elk:/etc/kibana#


### getting logs
### this logs shows that you cannot use the elastic user to start kibana
root@elk:~# cd /usr/share/kibana/
root@elk:/usr/share/kibana# ls
bin  LICENSE.txt  node  node_modules  NOTICE.txt  package.json  packages  plugins  README.txt  src  x-pack
root@elk:/usr/share/kibana# cd bin
root@elk:/usr/share/kibana/bin# ls
kibana  kibana-encryption-keys  kibana-health-gateway  kibana-keystore  kibana-plugin  kibana-setup  kibana-verification-code
root@elk:/usr/share/kibana/bin# sudo -u kibana ./kibana
{"log.level":"info","@timestamp":"2025-05-01T17:42:48.924Z","log.logger":"elastic-apm-node","ecs.version":"8.10.0","agentVersion":"4.5.0","env":{"pid":1669,"proctitle":"./../node/bin/node","os":"linux 6.8.0-58-generic","arch":"arm64","host":"elk","timezone":"UTC+00","runtime":"Node.js v20.13.1"},"config":{"active":{"source":"start","value":true},"breakdownMetrics":{"source":"start","value":false},"captureBody":{"source":"start","value":"off","commonName":"capture_body"},"captureHeaders":{"source":"start","value":false},"centralConfig":{"source":"start","value":false},"contextPropagationOnly":{"source":"start","value":true},"environment":{"source":"start","value":"production"},"globalLabels":{"source":"start","value":[["kibana_uuid","29dc673b-eacc-497c-8fbc-7e1607af61df"],["git_rev","afbd904e868f2a48a2bbeb8ff20baee8d4aeb468"]],"sourceValue":{"kibana_uuid":"29dc673b-eacc-497c-8fbc-7e1607af61df","git_rev":"afbd904e868f2a48a2bbeb8ff20baee8d4aeb468"}},"logLevel":{"source":"default","value":"info","commonName":"log_level"},"metricsInterval":{"source":"start","value":120,"sourceValue":"120s"},"serverUrl":{"source":"start","value":"https://kibana-cloud-apm.apm.us-east-1.aws.found.io/","commonName":"server_url"},"transactionSampleRate":{"source":"start","value":0.1,"commonName":"transaction_sample_rate"},"captureSpanStackTraces":{"source":"start","sourceValue":false},"secretToken":{"source":"start","value":"[REDACTED]","commonName":"secret_token"},"serviceName":{"source":"start","value":"kibana","commonName":"service_name"},"serviceVersion":{"source":"start","value":"8.14.1","commonName":"service_version"}},"activationMethod":"require","message":"Elastic APM Node.js Agent v4.5.0"}
Native global console methods have been overridden in production environment.
[2025-05-01T17:42:49.191+00:00][INFO ][root] Kibana is starting
[2025-05-01T17:42:49.222+00:00][INFO ][node] Kibana process configured with roles: [background_tasks, ui]

\u001b[37m\u001b[41m FATAL \u001b[49m\u001b[39m Error: [config validation of [elasticsearch].username]: value of "elastic" is forbidden. This is a superuser account that cannot write to system indices that Kibana needs to function. Use a service account token instead. Learn more: https://www.elastic.co/guide/en/elasticsearch/reference/8.0/service-accounts.html

root@elk:/usr/share/kibana/bin# timedatectl status
### solution
I will have to create a kibana user 
curl -X POST "https://localhost:9200/_security/user/kibana_system/_password" -u elastic:BMV=YdeAn-QtVm5xageh -H "Content-Type: application/json" -d '{"password": "123456789"}' -k
----------------------------------------------------------------------------

root@elk:/usr/share/kibana/bin# curl -X POST "https://localhost:9200/_security/user/kibana_system/_password" -u elastic:N=kyzqR1JWHwFUj=frQo -H "Content-Type: application/json" -d '{"password": "123456789"}' -k
{}
root@elk:/usr/share/kibana/bin#

# =================== System: Elasticsearch ===================
\# The URLs of the Elasticsearch instances to use for all your queries.
elasticsearch.hosts: ["https://sys.fcc:9200"]

# If your Elasticsearch is protected with basic authentication, these settings provide
# the username and password that the Kibana server uses to perform maintenance on the Kibana
# index at startup. Your Kibana users still need to authenticate with Elasticsearch, which
# is proxied through the Kibana server.
elasticsearch.username: "kibana_system" <--------------------
elasticsearch.password: "123456789"  <--------------------

# Kibana can also authenticate to Elasticsearch via "service account tokens".
# Service account tokens are Bearer style tokens that replace the traditional username/password based configuration.
# Use this token instead of a username/password.
# elasticsearch.serviceAccountToken: "my_token"

### now start 
systemctl start kibana
systemctl status kibana
systemctl enable kibana


### important kibana command for debugging
root@elk:/etc/kibana# sudo -u kibana /usr/share/kibana/bin/kibana
{"log.level":"info","@timestamp":"2025-05-01T20:07:29.445Z","log.logger":"elastic-apm-node","ecs.version":"8.10.0","agentVersion":"4.5.0","env":{"pid":2168,"proctitle":"/usr/share/kibana/bin/../node/bin/node","os":"linux 6.8.0-58-generic","arch":"arm64","host":"elk","timezone":"UTC+00","runtime":"Node.js v20.13.1"},"config":{"active":{"source":"start","value":true},"breakdownMetrics":{"source":"start","value":false},"captureBody":{"source":"start","value":"off","commonName":"capture_body"},"captureHeaders":{"source":"start","value":false},"centralConfig":{"source":"start","value":false},"contextPropagationOnly":{"source":"start","value":true},"environment":{"source":"start","value":"production"},"globalLabels":{"source":"start","value":[["kibana_uuid","29dc673b-eacc-497c-8fbc-7e1607af61df"],["git_rev","afbd904e868f2a48a2bbeb8ff20baee8d4aeb468"]],"sourceValue":{"kibana_uuid":"29dc673b-eacc-497c-8fbc-7e1607af61df","git_rev":"afbd904e868f2a48a2bbeb8ff20baee8d4aeb468"}},"logLevel":{"source":"default","value":"info","commonName":"log_level"},"metricsInterval":{"source":"start","value":120,"sourceValue":"120s"},"serverUrl":{"source":"start","value":"https://kibana-cloud-apm.apm.us-east-1.aws.found.io/","commonName":"server_url"},"transactionSampleRate":{"source":"start","value":0.1,"commonName":"transaction_sample_rate"},"captureSpanStackTraces":{"source":"start","sourceValue":false},"secretToken":{"source":"start","value":"[REDACTED]","commonName":"secret_token"},"serviceName":{"source":"start","value":"kibana","commonName":"service_name"},"serviceVersion":{"source":"start","value":"8.14.1","commonName":"service_version"}},"activationMethod":"require","message":"Elastic APM Node.js Agent v4.5.0"}
Native global console methods have been overridden in production environment.
[2025-05-01T20:07:29.733+00:00][INFO ][root] Kibana is starting
[2025-05-01T20:07:29.760+00:00][WARN ][environment] pid file already exists at /run/kibana/kibana.pid
[2025-05-01T20:07:29.762+00:00][INFO ][node] Kibana process configured with roles: [background_tasks, ui]
[2025-05-01T20:07:31.379+00:00][INFO ][plugins-service] The following plugins are disabled: "cloudChat,cloudExperiments,cloudFullStory,profilingDataAccess,profiling,securitySolutionServerless,serverless,serverlessObservability,serverlessSearch".

\u001b[37m\u001b[41m FATAL \u001b[49m\u001b[39m Error: Port 5601 is already in use. Another instance of Kibana may be running!

root@elk:/etc/kibana#

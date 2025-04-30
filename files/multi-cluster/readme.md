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

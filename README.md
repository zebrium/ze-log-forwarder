# LOG FORWARDER DETAILS
Zebrium's Log Forwarder accepts syslog and sends logs to Zebrium for automated Anomaly detection.
Our github repository is located [here](https://github.com/zebrium/ze-log-forwarder).

# ze-log-forwarder

## Getting Started
### Preparation
1. By default ze-log-forwarder container runs syslog server on TCP and UDP port 5514. Please make sure syslog clients can reach host IP on port 5514.
2. Make sure host firewall does not block port 5514 for both TCP and UDP.
3. Install docker software if it is not installed.

### Installation

1. To support syslog over TCP and UDP, run the following command as root:
```
docker run --name="zlog-forwarder" --restart=always \
    -p 5514:5514/tcp \
    -p 5514:5514/udp \
    -e ZE_LOG_COLLECTOR_URL="<ZE_LOG_COLLECTOR_URL>" \
    -e ZE_LOG_COLLECTOR_TOKEN="<ZE_LOG_COLLECTOR_TOKEN>" \
    -e ZE_DEPLOYMENT_NAME="<DEPLOYMENT_NAME>" \
    zebrium/log-forwarder:latest
```
2. To support syslog over TLS and UDP:
   1. Create/copy root certificate, host certificate and host private key files to a directory on the host which will be running log-forwarder container.
   2. Run the following command as root:
```
docker run --name="zlog-forwarder" --restart=always \
    -p 5514:5514/tcp \
    -p 5514:5514/udp \
    -v <USER_SERVER_CERTS_KEY_DIR>:/fluentd/tls
    -e ZE_SYSLOG_PROTOCOL="tls" \
    -e ZE_LOG_COLLECTOR_URL="<ZE_LOG_COLLECTOR_URL>" \
    -e ZE_LOG_COLLECTOR_TOKEN="<ZE_LOG_COLLECTOR_TOKEN>" \
    -e ZE_DEPLOYMENT_NAME="<DEPLOYMENT_NAME>" \
    zebrium/log-forwarder:latest
```

## Client Configuration

1. Use host IP as syslog server IP address, and port 5514 for syslog port.

2. Configure rsyslog
   1. To use UDP, add the following to the end of rsyslog configuration file
      `*.*    @<LOG_FORWARDER_HOST_IP>:5514`
   2. To use TCP, add the following to the end of rsyslog configuration file
      `*.*    @@<LOG_FORWARDER_HOST_IP>:5514`
  3. To use TLS:
      1. copy client_configs/rsyslog/25-zebrium.conf to /etc/rsyslog.d/,
      2. Open the file, replace `CLIENT_SSL_CERT_PATH` with real client SSL certificate path, change `SERVER_HOST` to the hostname running log-forwarder container, and `SERVER_DOMAIN_NAME` to the domain of the host running log-forwarder container.
      3. Restart rsyslog service.

### Setup
No additional setup is required

## Testing your installation
Once the log forwarder software has been deployed in your environment, your syslogs and anomaly detection will be available in the Zebrium UI.

## Contributors
* Brady Zuo (Zebrium)

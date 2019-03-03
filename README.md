RandomELK
==========
Random things for pushing local network traffic into ELK. 

Data -> Syslog (from remote) -> Filebeat -> Logstash -> ES 

GROK for HOSTAPD
==================
```
if [fields.filename] == "hostapd.log" {
    grok {
        match => {
        "message" => [
        #Most specific grok:
            "%{TIMESTAMP_ISO8601:log_timestamp} %{SYSLOGHOST:host} %{DATA:program}: (?<interface>\b[\w\-]+\b): %{WORD:Station} %{MAC:mac} %{GREEDYDATA:hostapd_message}",
        #Less specific:
            "%{TIMESTAMP_ISO8601:log_timestamp} %{SYSLOGHOST:host} %{DATA:program}: (?<interface>\b[\w\-]+\b): (?<Status>\b[\w\-]+\b) %{MAC:mac}"
        ]
    }
    }
}
```

T1: 2019-02-25T20:18:36+02:00 HOSTNAME hostapd: wlan0: STA 94:a1:a2:00:00:00 IEEE 802.11: disassociated
`%{TIMESTAMP_ISO8601:log_timestamp} %{SYSLOGHOST:host} %{DATA:program}: (?<interface>\b[\w\-]+\b): %{WORD:Station} %{MAC:mac} %{GREEDYDATA:message}`

T2: 2019-02-25T20:18:36+02:00 HOSTNAME hostapd: wlan0: AP-STA-DISCONNECTED 94:a1:a2:00:00:00
`%{TIMESTAMP_ISO8601:log_timestamp} %{SYSLOGHOST:host} %{DATA:program}: (?<interface>\b[\w\-]+\b): (?<Status>\b[\w\-]+\b) %{MAC:mac}`

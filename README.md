# Docker service with Telegraf (StatsD), InfluxDB, Grafana and MQTT

:facepunch: Battle-tested

[![CircleCI](https://circleci.com/gh/samuelebistoletti/docker-statsd-influxdb-grafana.svg?style=svg)](https://circleci.com/gh/samuelebistoletti/docker-statsd-influxdb-grafana)

## Versions

### Warning: UPGRADE FROM OLDER VERSIONS TO VERSION 3.0.0 IS NOT POSSIBLE, SEE CHANGELOG.MD

* Main version:      3.0.0
* InfluxDB:          2.1.1
* Telegraf (StatsD): 1.21
* Postgres:          14.2.0
* Grafana:           8.4.4
* Mosquitto:        2.0.15


## Quick Start

First download and install the latest available version of Docker Compose <https://docs.docker.com/compose/install/>

In order to start the service the first time launch:

```sh
COMPOSE_PROFILES=grafana,telegraf,mosquitto docker-compose up -d
```

You can replace `COMPOSE_PROFILES=grafana,telegraf,mosquitto` with the desired profiles to launch, you can launch only InfluxDB (default with no profiles).

To stop the service launch:

```sh
COMPOSE_PROFILES=grafana,telegraf,mosquitto docker-compose down
```

## Mapped Ports

```
Host		Container		Service

3003		3003			grafana
8086		8086		  influxdb
8125		8125			statsd
1883		1883			mqtt
9001		9001			mqtt websockets
```

## MQTT (Mosquitto)

MQTT broker is configured with authentication enabled.

### Create MQTT user
```bash
# Create first user
docker-compose exec mosquitto mosquitto_passwd -c /mosquitto/config/passwd mqttuser

# Add more users (if needed)
docker-compose exec mosquitto mosquitto_passwd /mosquitto/config/passwd anotheruser
```

### Test MQTT Connection
```bash
# Subscribe to topic
mosquitto_sub -h localhost -p 1883 -u mqttuser -P your_password -t "sensors/#"

# Publish message
mosquitto_pub -h localhost -p 1883 -u mqttuser -P your_password -t "sensors/temperature" -m '{"value":25.5}'
```

### Configuration
MQTT configuration file is located at:
```
mosquitto/config/mosquitto.conf
```

Default configuration includes:
- Authentication required (no anonymous access)
- Persistence enabled
- WebSocket support (port 9001)
- Logging to both file and stdout

## Grafana

Open <http://localhost:3003>

```
Username: root
Password: root
```

### Data source on Grafana

InfluxDB data source is automatically provisioned with new Flux language support flag.

## InfluxDB

### Web Interface

Open <http://localhost:8086>

```
Username: admin
Password: admin123456
Port: 8086
```

## Customizations

You can customize all settings in the attached config files, then you can stop and start the service in order to reload the new configurations.


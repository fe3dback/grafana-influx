# What is it?

This custom monitoring environment based on docker.

What inside:
- influxdb (main metrics db)
- grafana (ui frontend for influxdb)
- postgres (grafana storage)
- nginx (reverse proxy for grafana)

This env support any of clients for influxdb.
https://docs.influxdata.com/influxdb/v1.5/tools/api_client_libraries/

# How to use?

Setup client library from your favorite language: (go/php/java/etc..)

For example php:
```php
// all variables from .env
// $db = INFLUX_DB
// $host = your ip/domain
// $port = INFLUX_PORT_API
// $user = INFLUX_USER
// $pass = INFLUX_PASS
$client = new InfluxDB\Client($host, $port, $user, $pass);
$database = $client->selectDB($dbName);

$points = array(
	new Point(
		'test_metric', // name of the measurement
		0.64, // the measurement value
		['host' => 'server01', 'region' => 'us-west'], // optional tags
		['cpucount' => 10], // optional additional fields
		1435255849 // Time precision has to be set to seconds!
	),
    new Point(
    	'test_metric', // name of the measurement
		0.84, // the measurement value
		['host' => 'server01', 'region' => 'us-west'], // optional tags
		['cpucount' => 10], // optional additional fields
		1435255849 // Time precision has to be set to seconds!
	)
);

$result = $database->writePoints($points, Database::PRECISION_SECONDS);
```

now setup grafana dashboard and you should see this metrics.

# install

example for ubuntu:

### Install docker:
```bash
apt update
apt-get install docker.io
apt-get install docker-compose
docker run hello-world
```

### set up
Copy `.env.dist` to `.env`

```bash
cp .env.dist .env
docker-compose up
```

Change owner of .data/grafana to user:group `472:472`

```bash
chown 472:472 -R .data/grafana
docker-compose up -d
```

# Check

open ip/domain in browser. You should see grafana ui
also open url at :9001 port (or another from .env),
you should see page with 404 not found error. (this is ok)

# Config

Create new datasource in grafana admin panel

- type: influxDB
- url: http://influxdb:8086
- database, user, password same as .env values
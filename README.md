Epic Games Embedded DevOps Exercise
==========

This Synthesize branch has the scripts created for Question 1. After finishing
up the questions 2-4, I decided to go back and run the script as a service in
systemd. This is working and it sends off to Graphite every 2 seconds.

## Scripts created

### memory_reporter
A simple script that provides the metric server ip and loops

### output_used_memory_to_statsd

This shell script calls ps and then parallelizes the statsd calls

### process_mem_to_statsd

This shell script looks up used memory and sends the info to statsd

### Usage:

Download or clone the repo on the eg-script fork
```
$ cd synthesize
$ vagrant up
```
Then you can go to [Graphite](localhost:8443) and see the stats roll in under
Metrics.statsite.counts.numStats

# Synthesize readme below
## Provides

* Graphite 1.1.x ([graphite-web](https://github.com/graphite-project/graphite-web), [carbon](https://github.com/graphite-project/carbon), [whisper](https://github.com/graphite-project/whisper))
* StatsD ([statsite](https://github.com/armon/statsite))
* [Collectd](http://collectd.org/)
* [Grafana](https://grafana.org/)

## Dependencies

* Vagrant, an Ubuntu 18.04 VM or a non-production server
* Some mechanism for downloading Synthesize

## Installation

:warning: **WARNING:** Windows users may encounter provisioning [issues](https://github.com/obfuscurity/synthesize/issues/21). An apparent workaround is to run `dos2unix` on the `install` file before re-attempting provisioning.

### Manual

```
$ cd synthesize
$ sudo ./install
```

### Vagrant

Synthesize configures the following host ports to forward to the private vagrant box:

```
config.vm.network :forwarded_port, guest: 443, host: 8443
config.vm.network :forwarded_port, guest: 8125, host: 8125, protocol: 'tcp'
config.vm.network :forwarded_port, guest: 8125, host: 8125, protocol: 'udp'
config.vm.network :forwarded_port, guest: 2003, host: 22003
config.vm.network :forwarded_port, guest: 2004, host: 22004
config.vm.network :forwarded_port, guest: 3000, host: 3030
```

```
$ cd synthesize
$ vagrant plugin install vagrant-vbguest
$ vagrant up
```

## Administration

### Graphite-Web

There is a superuser (Django) account that grants access to the administrative features in the backend Django database. The default credentials are:

* username `admin`
* password `graphite_me_synthesize`

These credentials can be changed with the following commands:

```
$ cd /opt/graphite/webapp/graphite
$ sudo python manage.py changepassword admin
```

### Grafana

Grafana includes a default user to start:

* username `admin`
* password `admin`

## Upgrade

:warning: **WARNING:** The following information is outdated for this experimental branch. If you attempt to run the upgrade script it will display a warning with further instructions to acknowledge the current experimental status and override the warning.

It's now possible to upgrade an existing Synthesize (e.g. Graphite 0.9.15) to the newest Graphite `HEAD`. Besides upgrading the Graphite components, it will also migrate the webapp database (`graphite.db`) to the newest fixtures version.

```
$ cd synthesize
$ sudo ./upgrade
```

## Removal

### Manual

```
$ cd synthesize
$ ./uninstall
```

### Vagrant

```
$ cd synthesize
$ vagrant destroy
```

## License

Synthesize is distributed under the MIT license.

# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

METRIC_SERVER_IP = "10.0.1.110"
NODES_TO_BUILD = 4

$cronjobs = <<CRONTAB
tee "/tmp/metrics_monitoring" > "/dev/null" <<EOF
* * * * * /bin/bash -l -c  'STATSD_SERVER=#{METRIC_SERVER_IP} /vagrant/output_used_memory_to_statsd >> /tmp/output_memory.log 2>&1'
EOF
CRONTAB

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define :metric do |metric|
    metric.vm.box = "ubuntu/bionic64"
    metric.vm.network :forwarded_port, guest: 443, host: 8443
    metric.vm.network :forwarded_port, guest: 8125, host: 8125, protocol: 'tcp'
    metric.vm.network :forwarded_port, guest: 8125, host: 8125, protocol: 'udp'
    metric.vm.network :forwarded_port, guest: 2003, host: 22003
    metric.vm.network :forwarded_port, guest: 2004, host: 22004
    metric.vm.network :forwarded_port, guest: 3000, host: 3030
    graphite_version = ENV['GRAPHITE_RELEASE'].nil? ? '1.1.7' : ENV['GRAPHITE_RELEASE']
    metric.vm.provision "shell", inline: "cd /vagrant; GRAPHITE_RELEASE=#{graphite_version} ./install"
    metric.vm.provision "shell", inline: "cp /vagrant/templates/scripts/statsite-sink-graphite.py /usr/local/sbin/statsite-sink-graphite.py"
    metric.vm.network :private_network, ip: METRIC_SERVER_IP
    metric.vm.hostname = "metric"
  end

  (1..NODES_TO_BUILD).each do |i|
    config.vm.define "node#{i}" do |nodeconfig|
      nodeconfig.vm.box = "ubuntu/trusty64"
      nodeconfig.vm.network :private_network, ip: "10.0.1.#{110 + i}"
      nodeconfig.vm.hostname = "node#{i}"
    end
  end

  config.vm.provision "shell", inline: $cronjobs, run: "always"
  config.vm.provision "shell", inline: "crontab -u vagrant /tmp/metrics_monitoring"
  #config.vm.provision "shell", inline: "cp /vagrant/output_used_memory_to_statsd /usr/local/bin/output_used_memory_to_statsd"
  #config.vm.provision "shell", inline: "cp /vagrant/process_mem_to_statsd /usr/local/bin/process_mem_to_statsd"

end

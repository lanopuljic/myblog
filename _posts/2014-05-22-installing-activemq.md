---
layout: post
title: "Installing ACTIVEMQ"
description: "How to install ActiveMQ"
category: articles
modified: 2014-05-22 16:07:48 +1000
tags: [puppet,activemq]
image:
  feature: 
  credit: 
  creditlink: 
comments: true
share: true
---

yum install java-1.6.0-openjdk activemq

cd /etc/activemq
mv activemq.xml activemq.xml-org
wget https://raw.github.com/puppetlabs/marionette-collective/master/ext/activemq/examples/single-broker/activemq.xml

service activemq start
chkconfig activemq on
 
 Install Plugins
yum install mcollective mcollective-client mcollective-common rubygem-stomp 
wget https://github.com/blewa/puppetlabs-mcollective-plugins/tree/master/agent/package.rb
wget https://github.com/puppetlabs/mcollective-plugins/blob/master/agent/puppetd/application/puppetd.rb
wget https://github.com/puppetlabs/mcollective-plugins/blob/master/agent/puppetd/application/puppetd.rb
wget https://github.com/blewa/puppetlabs-mcollective-plugins/blob/master/agent/service/application/service.rb
wget https://github.com/puppetlabs/mcollective-filemgr-agent/blob/master/agent/filemgr.rb
wget https://github.com/blewa/puppetlabs-mcollective-plugins/blob/master/agent/filemgr/agent/filemgr.ddl
wget https://github.com/puppetlabs-operations/puppet-mcollective/blob/master/files/plugins/agent/puppetral.rb
wget https://github.com/example42/puppet-mcollective/blob/master/files/plugins/agent/puppetral.ddl
wget https://github.com/puppetlabs/mcollective-package-agent/blob/master/agent/package.ddl
wget https://github.com/blewa/puppetlabs-mcollective-plugins/blob/master/agent/service/agent/puppet-service.rb
wget https://github.com/blewa/puppetlabs-mcollective-plugins/blob/master/agent/service/agent/service.ddl
wget https://github.com/phobos182/mcollective-plugins/blob/master/agent/etcfacts/etc_facts.rb
wget https://raw.github.com/phobos182/mcollective-plugins/master/agent/etcfacts/etc_facts.ddl
wget https://raw.github.com/phobos182/mcollective-plugins/master/agent/shell/shell.rb
wget https://raw.github.com/phobos182/mcollective-plugins/master/agent/shell/shell.ddl
wget https://raw.github.com/phobos182/mcollective-plugins/master/agent/yum/yum.rb
wget https://raw.github.com/phobos182/mcollective-plugins/master/agent/yum/yum.ddl

cd /usr/libexec/mcollective/mcollective/facts/
wget https://github.com/puppetlabs/mcollective-facter-facts/blob/master/facts/facter_facts.rb


vim /etc/mcollective/server.cfg
main_collective = mcollective
collectives = mcollective
libdir = /usr/libexec/mcollective
logfile = /var/log/mcollective.log
loglevel = info
daemonize = 1

# Plugins
securityprovider = psk
plugin.psk = unset

connector = activemq
plugin.activemq.pool.size = 1
plugin.activemq.pool.1.host = puppet01.xonal.com ( CHANGE TO YOUR PUPPET SERVER)
plugin.activemq.pool.1.port = 61613
plugin.activemq.pool.1.user = mcollective
plugin.activemq.pool.1.password = marionette
plugin.activemq.heartbeat_interval= 30

# Facts
factsource = yaml
plugin.yaml = /etc/mcollective/facts.yaml

If this will be your server where you will be executing mco commend you will need to install the mcollective-client as well and configuire the client.cfg file.
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box     = 'raring'
  config.vm.box_url = 'http://cloud-images.ubuntu.com/raring/current/raring-server-cloudimg-vagrant-amd64-disk1.box'
  config.vm.provision :shell, inline: <<-BASH
apt-get update
apt-get -y install linux-image-extra-`uname -r`
apt-get -y install software-properties-common
add-apt-repository ppa:dotcloud/lxc-docker
apt-get update
apt-get -y install lxc-docker
mkdir railsapp
cp -R /vagrant railsapp/app

cat > railsapp/Dockerfile <<EOF
FROM ubuntu

RUN apt-get update
RUN apt-get install -y curl git libc6-dev libssl-dev make build-essential libssl-dev libreadline6-dev zlib1g-dev libyaml-dev

RUN echo "gem: --no-ri --no-rdoc" > /.gemrc
RUN mkdir -p /opt/ruby
RUN git clone https://github.com/sstephenson/ruby-build.git
RUN cd ruby-build && ./install.sh
RUN ruby-build 2.0.0-p247 /opt/ruby
RUN /opt/ruby/bin/gem install bundler

ADD app /opt/app
RUN cd /opt/app && /opt/ruby/bin/bundle install

CMD ruby /opt/testapp/script/rails s
EXPOSE 3000:8080
EOF

docker build -t mje113/testapp railsapp
BASH

  config.vm.provider :virtualbox do |vb|
    vb.customize ['modifyvm', :id, '--memory', '512']
  end

end

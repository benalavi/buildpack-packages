Vagrant.configure("2") do |config|
  config.vm.box = "lucid64"
  config.vm.box_url = "http://files.vagrantup.com/lucid64.box"
  
  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--name", "buildpack-packages", "--memory", "512"]
  end
  
  config.vm.provision "shell", inline: <<-EOS
if [ ! -x "/usr/bin/git" ];then
  echo "Installing git"
  
  apt-get update -qq
  apt-get install -y -qq git-core
fi

version=2.0.0-p247
alt=`echo $version | sed -e "s/-.*//g" -e "s/\\.//g"`

if [ ! -x "/usr/bin/ruby$version" ]; then
  echo "Installing ruby $version"
  
  echo '  Installing build requirements...'
	apt-get update -qq
	apt-get install -y build-essential autoconf libxslt1.1 libssl-dev \
    libxslt1-dev libxml2 libffi-dev libyaml-dev libxslt-dev libc6-dev \
    libreadline6-dev zlib1g-dev libcurl4-openssl-dev ncurses-dev > /dev/null

  cd /tmp
  wget -q ftp://ftp.ruby-lang.org/pub/ruby/2.0/ruby-$version.tar.gz -O ruby-$version.tar.gz
  tar xvf ruby-$version.tar.gz > /dev/null
  cd ruby-$version
  
  echo '  Building ruby...'
  autoconf > /dev/null
  ./configure --with-ruby-version=$version --prefix=/usr --program-suffix=$version --disable-install-doc --with-opt-dir=/usr/include > /dev/null
  make > /dev/null
  
  echo '  Installing ruby...'
  make install-nodoc > /dev/null
  update-alternatives \
    --install /usr/bin/ruby ruby /usr/bin/ruby$version $alt \
    --slave /usr/bin/erb erb /usr/bin/erb$version \
    --slave /usr/bin/irb irb /usr/bin/irb$version \
    --slave /usr/bin/rdoc rdoc /usr/bin/rdoc$version \
    --slave /usr/bin/ri ri /usr/bin/ri$version \
    --slave /usr/bin/rake rake /usr/bin/rake$version \
    --slave /usr/bin/testrb testrb /usr/bin/testrb$version > /dev/null
  update-alternatives --install /usr/bin/gem gem /usr/bin/gem$version $alt > /dev/null
  update-alternatives --config ruby > /dev/null
  update-alternatives --config gem > /dev/null
fi

gem list bundler | grep bundler ||
  gem install bundler --no-rdoc --no-ri

cd /vagrant && bundle install

echo 'Done.'
EOS
end

# jenkins-slave-angularjs-shellscript
shell script for setting up jenkins slave
<pre>
Welcome to the angularjs-jenkins-slave wiki!
#!/bin/bash

#This is a script setting up Webtwo slave from scratch
#This script expects the following installables alongside. 
# git-1.9.5.tar.gz, instantclient-basic-linux.x64-12.1.0.2.0.zip, Python-2.7.9.tgz, ruby-2.2.3.tar.gz, groovy-binary-2.4.0.zip, instantclient-sqlplus-linux.x64-12.1.0.2.0.zip
# rainbow-2.0.0.gem, scss-lint-0.38.0.gem, scss_lint_reporter_checkstyle-0.2.0.gem and sass-3.4.19.gem 


#set homedir
homedir=/c9/setup/

#set script home
scripthome=$(pwd)

#set userhome
userhome=/home/wgadmin

#create required directories
mkdir -p $homedir/scripts/
mkdir -p $homedir/tools/
mkdir -p $homedir/data/
mkdir -p $homedir/tools/instantclient_12_1


#create symbolic links so that home directory do not get filled up
cd $userhome

mkdir -p /c9/setup/tools/.cache
ln -s /c9/setup/tools/.cache .cache
mkdir -p /c9/setup/tools/.config
ln -s /c9/setup/tools/.config .config
mkdir -p /c9/setup/tools/.jenkins
ln -s /c9/setup/tools/.jenkins .jenkins
mkdir -p /c9/setup/tools/.local
ln -s /c9/setup/tools/.local .local
mkdir -p /c9/setup/tools/.node-gyp
ln -s /c9/setup/tools/.node-gyp .node-gyp

#create required configuration files
echo '{
   "proxy":"",
   "https-proxy":"",
   "registry":"http://c9prdap17:5678",
   "tmp": "/c9/setup/data/bower-tmp"
}' >  ~/.bowerrc

echo 'http_proxy: http://baproxy-dmz.baplc.com:8080/
https_proxy: http://baproxy-dmz.baplc.com:8080/'  >  ~/.gemrc

echo '[url "https://github"]
        insteadOf = git://github
[http]
    proxy = http://baproxy-dmz.baplc.com:8080/
[https]
    proxy = http://baproxy-dmz.baplc.com:8080/
[http "https://git.baplc.com"]
    proxy =' > ~/.gitconfig

echo 'https-proxy=http://baproxy-dmz.baplc.com:8080
tmp=/c9/setup/data/npm
cache = "/c9/setup/data/.npmcache"
#proxy=http://baproxy-dmz.baplc.com:8080/
proxy=null
registry="http://cap-nexus.baplc.com:8081/nexus/content/groups/npm-all/"' > ~/.npmrc

#setup key based authentication - public key of jenkins master in authorized_keys

#mkdir $userhome/.ssh

echo 'ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAgEAy6gqtYvj1yknVgRAk2gUcJwQuoaWn6jN6DSyk2Lwvq5vBXXoDaGxexo8rgPRU+vQyvlSDpPZakhI5xheR+hy8KixwhwX9LuxS+dn5MajrNySjflM1HFyQ3cpXs7K3VAlwhTzWs8DmELM1KMB+zcOJgFO+rrSirab5FLIO+5EdBXJiGPDHe1gYvulQgrTZvskGnezGBGqr9qhrQeSTc+WXMBQmSFW2b19XZB0woBEIKyQ3OPY+UBm1gfa5q50gxhVpVZjxMqZ9a/SP49GZvbFAz/BZ3WC+KHalMXBb1sj/QSTO/nWp9pm5X1wtuHivTvP70Tq2QxTpTU4kr6ZZEnWBtBYW6mHOpWg4jj5BZFwNUVD1draQ5obDUyP5FMttcFml+hDkeKERiX65IzP6gQWNotS/MIX8iHVYmSJvyq8qplagXhPr1QetDZSxOCaP8KA/JCEEQSYf659HLzRHgHzAD26gZAdiQQxYvPHdhOrI6zBqHo03cV+E0jsnrGbrJxV7vLFJ7xLpBQUqt0ub1rY1JhOdA8hE+eLxzww6RGTk3RbZbgYwz786qYNG6lA9pZNJuvtawht0ek0+lt/jW00gjMfK62WPY/PAIBwyAcervvDIp+J/u2Cqmr8NOZYQJW1pgwcHhVCPk3cVnIVQO67kyqelrejyqLNoxMVzEwjdms= wgadmin@c9prdtc07' > $userhome/.ssh/authorized_keys

chmod 700 -R $userhome/.ssh
chmod 600 $userhome/.ssh/authorized_keys

#install python. expects installation file inside homedir
#wget https://www.python.org/ftp/python/2.7.9/Python-2.7.9.tgz

cd $scripthome

cp Python-2.7.9.tgz $homedir
cd $homedir
tar -zxvf Python-2.7.9.tgz
cd $homedir/Python-2.7.9
./configure --prefix=$homedir/tools/Python-2.7.9
make install
cd ..

#clean up
rm Python-2.7.9.tgz
rm -rf Python-2.7.9

cd $scripthome

#install git
#wget https://www.kernel.org/pub/software/scm/git/git-1.9.5.tar.gz

cp git-1.9.5.tar.gz $homedir
cd $homedir
tar -zxvf git-1.9.5.tar.gz
cd $homedir/git-1.9.5
./configure --prefix=$homedir/tools/git-1.9.5
make
make install
cd ..

#cleanup 
rm git-1.9.5.tar.gz
rm -rf git-1.9.5
cd $scripthome

#install Instant Client
cp instantclient-basic-linux.x64-12.1.0.2.0.zip $homedir/tools/
cp instantclient-sqlplus-linux.x64-12.1.0.2.0.zip $homedir/tools/
cd $homedir/tools/
unzip instantclient-basic-linux.x64-12.1.0.2.0.zip -d $homedir/tools/instantclient_12_1
unzip instantclient-sqlplus-linux.x64-12.1.0.2.0.zip -d $homedir/tools/instantclient_12_1

cd /c9/setup/tools/instantclient_12_1/
ln -s libclntsh.so.12.1 libclntsh.so
ln -s libocci.so.12.1 libocci.so

cd ..

rm instantclient-basic-linux.x64-12.1.0.2.0.zip
rm instantclient-sqlplus-linux.x64-12.1.0.2.0.zip
cd $scripthome

#export PYTHON_HOME and GIT_HOME
export GIT_HOME=/c9/setup/tools/git-1.9.5
export PATH=${GIT_HOME}/bin:$HOME/.rbenv/bin:$PATH

#install rbenv

cd $userhome
mkdir -p /c9/setup/tools/.rbenv
ln -s /c9/setup/tools/.rbenv .rbenv
cd
git clone git://github.com/sstephenson/rbenv.git .rbenv
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)"
git clone git://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build

cd $scripthome
#source ~/.profile

export PATH=${GIT_HOME}/bin::$HOME/.rbenv/bin:$PATH
eval "$(rbenv init -)"

#rbenv install 2.2.3 #fails because of curl failure

#install ruby-2.2.3
cp ruby-2.2.3.tar.gz $homedir

cd $homedir
tar -xvzf ruby-2.2.3.tar.gz
cd ruby-2.2.3
./configure --prefix=$HOME/.rbenv/versions/2.2.3
make
make install
cd ..

rbenv global 2.2.3

#install ruby gems
gem install rainbow-2.0.0.gem --local
gem install scss-lint-0.38.0.gem --local
gem install scss_lint_reporter_checkstyle-0.2.0.gem --local
gem install sass-3.4.19.gem --local

#clean up
rm ruby-2.2.3.tar.gz
rm -rf ruby-2.2.3
cd $scripthome

#Install Groovy-2.4.0
cp groovy-binary-2.4.0.zip $homedir/tools/
cd $homedir/tools/
unzip groovy-binary-2.4.0.zip
rm groovy-binary-2.4.0.zip

#setup ~/.profile file
echo 'export PYTHON_HOME=/c9/setup/tools/Python-2.7.9
export GIT_HOME=/c9/setup/tools/git-1.9.5
export GROOVY_HOME=/c9/setup/tools/groovy-2.4.0
export INSTANT_CLIENT_PATH=/c9/setup/tools/instantclient_12_1
export PATH=${INSTANT_CLIENT_PATH}:${PYTHON_HOME}/bin:${GIT_HOME}/bin:${GROOVY_HOME}/bin:$HOME/.rbenv/bin:$PATH
eval "$(rbenv init -)"
export LD_LIBRARY_PATH=${INSTANT_CLIENT_PATH}:$LD_LIBRARY_PATH' >  ~/.profile
</pre>

VMWare Environment

1. Test was conducted on VMWare esXI 5.5
 1. CPUS = 1 Core
 1. RAM = 2GB
HDD = 100GB
HostName = openmbee.db8tnet
1. Download CentOS 6.6 for x64
1. Install Desktop Environment
1. Add a User to sudo
 1. become root
 ```
su - root
```

 1. <enter root pw>
 1. Open Terminal
 1. Switch to root user # su (enter password)

```
# visudo (it is vi editor editing file mentioned in the post, so basic vi skills required here)
```
 . in visudo find lines:
```
## Allows people in group wheel to run all commands
%wheel ALL=(ALL) ALL
```
remove # from 2nd line
save changes and quit (:wq)
now add the user to the wheel group:
usermod -aG wheel USERNAME 
(“a” is important so you will not remove user from existing groups…)
logout your user and log back in. The sudo command will work now.
Setup Network
If using static IP
Configure eth0
vim /etc/sysconfig/network-scripts/ifcfg-eth0
Add the following
IPADDR=<your ip>
NETMASK=255.255.255.0
Configure Default gateway
vim /etc/sysconfig/network
Run DHClient
sudo dhclient
Restart machine or in desktop click on network status to ensure the connection is active
Enable SSH
Open terminal
sudo chckconfig sshd on
Restart
sudo shutdown -r now
Setup VNC
sudo yum install vnc-server
create your desktop (NOT AS ROOT)
vncserver -geometry <Width>x<Height>
Enter a display password (not your system pw just something so you can share screens)
Note the Desktop number
Enable VNC at startup
sudo chkconfig vncserver on
Amend /etc/sysconfig/iptables to allow VNC ports
-A INPUT -m state --state NEW -m tcp -p tcp -m multiport --dports 5901:5903,6001:6003 -j ACCEPT
Test using your favorite VNC viewer (RealVNC, chicken of the VNC etc)
Install Development Environment
Install JAVA
sudo yum install java-1.7.0-openjdk
sudo yum install java-1.7.0-openjdk-devel
Configure JAVA_HOME
$ sudo vi /etc/profile.d/java.sh
export JAVA_HOME=/usr/lib/jvm/jre-1.7.0-openjdk.x86_64
export PATH=$PATH:$JAVA_HOME
Install Git
sudo yum install git
Install Maven
get maven installer
wget http://mirror.nexcess.net/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.tar.gz
$ sudo tar xzf apache-maven-3.0.5-bin.tar.gz -C /usr/local
$ cd /usr/local
$ sudo ln -s apache-maven-3.0.5 maven

Next, set up Maven path system-wide:
$ sudo vi /etc/profile.d/maven.sh
export M2_HOME=/usr/local/maven
export PATH=${M2_HOME}/bin:${PATH}
Finally, log out and log in again to activate the above environment variables.
To verify successful installation of maven, check the version of maven:
$ mvn -version 
Install Ant
Setup
wget http://www.gtlib.gatech.edu/pub/apache//ant/binaries/apache-ant-1.9.4-bin.tar.gz
sudo tar xzf apache…bin.tar.gz -C /usr/local
ln -s apache-ant-1.9.4 ant
Next setup ant path system-wide:
sudo vim /etc/profile.d/ant.sh
export ANT_HOME=/usr/local/ant
export ANT_OPTS="-Xmx256M"
export PATH=${ANT_HOME}/bin:${JAVA_HOME}/bin:${PATH}.
Install Eclipse
Setup
wget http://carroll.aset.psu.edu/pub/eclipse/technology/epp/downloads/release/luna/SR2/eclipse-java-luna-SR2-linux-gtk-x86_64.tar.gz
sudo tar xzf eclipse-java…_64.tar.gz -C /opt/local
cd /opt/local
sudo chmod -R +r /opt/local/eclipse
sudo touch /usr/bin/eclipse
sudo chmod 755 /usr/bin/eclipse
Open eclipse file with your favorite editor
vim /usr/bin/eclipse
##Paste the following content into the file ##
export ECLIPSE_HOME=“/opt/local/eclipse”
$ECLIPSE_HOME/eclipse $*
Create the following file 
vim usr/share/applications/eclipse.desktop
Paste in the content
[Desktop Entry]

Encoding=UTF-8

Name=Eclipse

Comment=Eclipse Luna SR2 

Exec=eclipse

Icon=/opt/local/eclipse/icon.xpm

Terminal=false

Type=Application

Categories=GNOME;Application;Development;

StartupNotify=true

Logout of all accounts
Start Eclipse
eclipse
Install Alfresco 4.2e/f
wget http://dl.alfresco.com/release/community/4.2.f-build-00012/alfresco-community-4.2.f-installer-linux-x64.bin
Make installer executable
cd /tmp/
chmod +x alfresco-…-x64.bin
Execute installer as sudo
./alfresco-…-x64.bin
Select 1 for english
Select 1 for Easy
Choose folder (default is /opt/)
Choose admin Pw
PW: *Yf…
Install as a service Y
Launch Community Share
Go to iptables and open the tomcat port
-A INPUT -p tcp -m tcp —dport 8080 -j ACCEPT
Test using internal and external browsers!
Install git repos
Open VNC
open eclipse in git perspective
open browser and get git links
ems-repo : https://github.com/Open-MBEE/EMS-Repo.git
util : https://github.com/Open-MBEE/util.git 
bae : https://github.com/Open-MBEE/bae.git
sysml : https://github.com/Open-MBEE/sysml.git
docbook : https://github.com/Open-MBEE/docbook.git
uncheck build automatically
download these repos using eclipse
Allow maven dependencies to update (may take some time)
Build .Jar files
For each repo bae/sysml/util:
open each build.xml and replace gitDir definition with
<property name="gitDir" location="..”/>
rename view_repo_lib property to view_repo_amp_lib
add a new property
<property name="view_repo_lib" value="${view_repo}/lib”/>
after <copy todir=“${view_repo_lib}…. add
<copy todir="${view_repo_amp_lib}" file="mbee_util.jar" failonerror="false”/>
in terminal
navigate to git directory for repo
ant
repeat
Next build docbook
navigate to the docbook git directory
mvn package
Copy the file to EMS-Repo/lib
cp docbook.jar

Build the EMS-Repo AMP
In terminal
Navigate to EMS-Repo in git directory
Build the amp
mvn package -P mbee-dev
If you want to run a local instance in an embedded jetty container (Repo on port  8080)
mvn integration-test -Pamp-to-war -Dmaven.test.skip=true -P mms-dev

if you want to clean a failed attempt
mvn clean -Ppurge
Build the EMS-Share AMP
Build the AMP
mvn package -P mbee-dev
If you want to run a local instance in an embedded jetty container (Share on port  8081)
mvn integration-test -Pamp-to-war -Dmaven.test.skip=true -P mms-dev
Build the EVM.zip
Test locally Grunt server:localhost (port 9000)
Installing MMS-Repo
Uninstall any existing, Navigate to /{alfresco.dir}/bin/
cd $TOMCAT/webapps
java -jar $ALFRESCO/bin/alfresco-mmt.jar uninstall mms-repo alfresco.war
java -jar $ALFRESCO/bin/alfresco-mmt.jar uninstall mms-share share.war
Install the amps
cd $TOMCAT/webapps
java -jar $ALFRESCO/bin/alfresco-mmt.jar install $PATH_TO_AMP/mms-repo.amp $TOMCAT/alfresco.war -force
java -jar $ALFRESCO/bin/alfresco-mmt.jar install $PATH_TO_AMP/mms-share.amp $TOMCAT/share.war -force
Explode the WAR
rm -rf alfresco
mkdir alfresco
cd alfresco
jar xvf ../alfresco.war
Unzip EVM
unzip evm.zip .
mv build mmsapp
cd $TOMCAT/webapps
chown -R tomcat:tomcat webapps
MDK
there’s a class called qvt script runner or something similar, if you delete that and delete any broken calls to it it should compile with md without nick’s stuff

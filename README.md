#These instructions are now up to date for EMS Version 2.1 (Commits afrer to May 4th 2015)

# Install OpenMBEE Environment from Source

1. Test was conducted on VMWare esXI 5.0 (CentOS 6) and Amazon AWS (CentOS 7)
HostName = openmbee.db8tnet

## Optional Setup Procedure

### Install OS
1. Download CentOS 6.6 for x64
1. Install w/ Desktop Environment

### Configure OS

#### Add a User to sudo
1. become root

    ```
    su - root
    ```
2. enter root pw
3. Open Terminal
1. Switch to root user # su (enter password)
1. Edit sudoers file using visudo

    ```
    # visudo (it is vi editor editing file mentioned in the post, so basic vi skills required here)
    ```
 1. in visudo find lines:
 
    ```
    ## Allows people in group wheel to run all commands
    # %wheel ALL=(ALL) ALL
    ```
 1. remove # from 2nd line
 1. save changes and quit (:wq)
 1. now add the user to the wheel group:
    ```
    usermod -aG wheel USERNAME 
    ```
    
    (“a” is important so you will not remove user from existing groups…)
 1. logout your user and log back in. The sudo command will work now.

#### Setup Network
2. If using static IP
 3. Configure eth0
     ```
    vim /etc/sysconfig/network-scripts/ifcfg-eth0
    ```
    
 4. Add the following
 
    ```
    DEVICE=eth0
    HWADDR=<mac number>
    TYPE=Ethernet
    NM_CONTROLLED=no
    ONBOOT=yes
    IPADDR=<your ip>
    NETMASK=255.255.255.0
    ```
    
 6. Configure Default gateway and hostname
 
    ```
    vim /etc/sysconfig/network
    ```
    
 6. OPTIONAL: Configure local dns lookups if desired ```sudo vim /etc/resolv.conf```
 
    ```
    search <yourdomain>
    nameserver <your dns/dhcp server>
    ```

#### Enable SSH
2. Open terminal
    ```
    sudo chkconfig sshd on
    ```
3. Restart
    ```
    sudo shutdown -r now
    ```

#### Setup VNC (Optional only if you plan to use eclipse)
1. Install TigerVNC
    ```
    sudo yum install vnc-server
    ```
1. create your desktop (NOT AS ROOT)
    ```vncserver -geometry <Width>x<Height>```
 1. Enter a display password (not your system pw just something so you can share screens)
 2. Note the Desktop number
3. Enable VNC at startup
    ```sudo chkconfig vncserver on```
4. Amend /etc/sysconfig/iptables to allow VNC ports
 
    ```
    -A INPUT -m state --state NEW -m tcp -p tcp -m multiport --dports 5901:5903,6001:6003 -j ACCEPT
    ```
5. Test using your favorite VNC viewer (RealVNC, chicken of the VNC etc)

### Install Development Environment

#### Install Support tools

1. Install vim (or your favorite text editor) ```sudo yum install vim```
2. Install wget ```sudo yum install wget```

#### Install JAVA
1. Download and Install

    ```
    sudo yum install java-1.7.0-openjdk
    sudo yum install java-1.7.0-openjdk-devel
    ```
1. Configure JAVA_HOME
 1. Create path file
 
    ```$ sudo vim /etc/profile.d/java.sh```
 1. Insert the following:
    ```
    export JAVA_HOME=/usr/lib/jvm/jre-1.7.0-openjdk
    export PATH=$PATH:$JAVA_HOME
    ```

#### Install Git
1. Install
    ```sudo yum install git```

#### Install Maven
1. get maven installer

    ```
    wget http://mirror.nexcess.net/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.tar.gz
    ```
2. Expand Archive

    ```
    $ sudo tar xzf apache-maven-3.0.5-bin.tar.gz -C /usr/local
    ```
3. Change directory ```$ cd /usr/local```
4. Create simlink ```$ sudo ln -s apache-maven-3.0.5 maven```
5. Next, set up Maven path system-wide:
 1. Create file ```$ sudo vi /etc/profile.d/maven.sh```
 2. Paste the following
 
    ```
    export M2_HOME=/usr/local/maven
    export PATH=${M2_HOME}/bin:${PATH}
    ```
6. Finally, log out and log in again to activate the above environment variables.
7. To verify successful installation of maven, check the version of maven: ```$ mvn -version ```

#### Install Ant (Not required)
1. Setup
    ```
    wget http://www.gtlib.gatech.edu/pub/apache//ant/binaries/apache-ant-1.9.4-bin.tar.gz
    sudo tar xzf apache…bin.tar.gz -C /usr/local
    ln -s apache-ant-1.9.4 ant
    ```
2. Next setup ant path system-wide:
 1. Create file ```sudo vim /etc/profile.d/ant.sh```
 2. Paste
     ```
    export ANT_HOME=/usr/local/ant
    export ANT_OPTS="-Xmx256M"
    export PATH=${ANT_HOME}/bin:${JAVA_HOME}/bin:${PATH}.
    ```

#### Install Eclipse (Optional only if you prefer a gui git/coding environment)
1. Download Installer

    ```
    wget http://mirror.cc.columbia.edu/pub/software/eclipse/technology/epp/downloads/release/luna/SR2/eclipse-java-luna-SR2-linux-gtk-x86_64.tar.gz
    ```
2. Expand Archive and copy to opt

    ```
    sudo tar xzf eclipse-java…_64.tar.gz -C /opt
    ```
3. Change Directories ```cd /opt```
4. Fix permissions
    ```
sudo chmod -R +r /opt/eclipse
    ```
5. Create a config file and set its permissions
    ```
    sudo touch /usr/bin/eclipse
    sudo chmod 755 /usr/bin/eclipse
    ```
6.Open usr/bin/eclipse file with your favorite editor
 
    ```
    vim /usr/bin/eclipse
    ```
 1. Paste the following content into the file
    ```
    export ECLIPSE_HOME=/opt/eclipse
    $ECLIPSE_HOME/eclipse $*
    ```
7. Create the following file 

    ```
    vim usr/share/applications/eclipse.desktop 
    ```
 1. Paste the following
 
    ``` 
    [Desktop Entry]
    Encoding=UTF-8
    Name=Eclipse
    Comment=Eclipse Luna SR2 
    Exec=eclipse
    Icon=/opt/eclipse/icon.xpm
    Terminal=false
    Type=Application
    Categories=GNOME;Application;Development;
    StartupNotify=true
    ```
8. Logout of all accounts
9. Start Eclipse
 1. Terminal ```eclipse```
 2. GUI, click Applications > Programming > Eclipse

###Installing EMS Webapp Applications development environment
 
#### Install node.js
1. Install centos development tools for building and running EMS-Webapp

    ```
    sudo yum -y groupinstall "Development Tools"
    ```

2. Become root ```sudo -i```
3. As root, install rpm repository for Node.js

    ```
    curl -sL https://rpm.nodesource.com/setup | bash -
    ```

4. Again as root, install Node.js

    ```
    yum install -y nodejs
    ```

5. logout root ```logout```

#### Install Ruby
1. Install package

    ```
    sudo yum install -y ruby
    ```
    
2. Install ruby gems

    ```
    sudo yum install -y rubygems
    ```

#### Install grunt
1. install grunt-cli ```npm install -g grunt-cli```

#### Install bower
1. ```npm install -g bower```

#### Install sass
1. ```gem install sass```


### Install Alfresco
1. Download Alfresco 4.2e/f
    ```
    wget http://dl.alfresco.com/release/community/4.2.f-build-00012/alfresco-community-4.2.f-installer-linux-x64.bin
    ```
2. Make installer executable
 1. ```cd /tmp/```
 2. ```chmod +x alfresco-…-x64.bin```
3. Execute installer as sudo ```sudo ./alfresco-…-x64.bin```
 1. Select 1 for english
 2. Select 1 for Easy
 3. Choose folder (default is /opt/)
 4. Choose admin Pw
 6. Install as a service ```Y```
 7. Launch Community Share
 8. Allow external access to alfresco services
  1. Go to iptables and open the tomcat dev port and tomcat ssl port
  2. 
    (WARNING You may potentially be exposing information to the internet be sure to understand your firewall and network infrastructure before performing these steps)

    ```
    -A INPUT -p tcp -m tcp —dport 8080 -j ACCEPT
    -A INPUT -p tcp -m tcp —dport 8443 -j ACCEPT
    ```
11. Test using internal and external browsers!

### Configure httpd
1. Install httpd ``sudo yum install httpd```
2. Open httpd ports
    ```
    -A INPUT -p tcp -m tcp —dport 80 -j ACCEPT
    -A INPUT -p tcp -m tcp —dport 443 -j ACCEPT
    ```
3. Configure httpd for virtual host and enable mod_proxy services ```sudo vim /etc/httpd/conf/httpd.conf```
4. Append to the end of the file:

    ```
    #
    # Use name-based virtual hosting
    #
    NameVirtualHost *:80
    #
    # NOTE: NameVirtualHost cannot be used without a port specifier
    # (e.g. :80) if mod_ssl is being used, due to the nature of the
    # SSL protocol.
    #
    LoadModule proxy_ajp_module modules/mod_proxy_ajp.so
    ```
5. Clean up apache and configure the virtual host

    ```
    cd /etc/httpd/conf.d/
    sudo rm welcome.conf
    sudo vim tomcat.conf
    ```
6. Insert into tomcat.conf

    ```
    <VirtualHost *:80>
       ServerName <yourdomain openmbee.domain>
       ServerAlias <yourfulldomain i.e. www.openmbee.com>

       ProxyRequests Off
       ProxyPreserveHost On

       ErrorLog /var/log/httpd/tomcat.error.log
       CustomLog /var/log/httpd/tomcat.log combined

       <Proxy *>
               Order deny,allow
               Allow from all
       </Proxy>

       ProxyPass / ajp://localhost:8009/
       ProxyPassReverse / ajp://localhost:8009/
    </VirtualHost>
    ```
7. Restart httpd ```sudo service httpd restart```
8. Test using browser

### Build OpenMBEE Source Code
Follow these instructions to update or install EMS from source

#### Get Repositories
1. Open GUI interface
2. open eclipse in git perspective
3. open browser and get git links from (http://openmbee.org)
4. Core Components
 1. ems-repo : https://github.com/Open-MBEE/EMS-Repo.git
 2. ems-share : https://github.com/Open-MBEE/EMS-Share.git
 3. ems-webapp : https://github.com/Open-MBEE/EMS-Webapp.git
5. Libraries :
 2. util : https://github.com/Open-MBEE/util.git 
 3. bae : https://github.com/Open-MBEE/bae.git
 4. sysml : https://github.com/Open-MBEE/sysml.git
 5. docbook : https://github.com/Open-MBEE/docbook.git
5. uncheck build automatically
5. download these repos using eclipse
6. make sure to checkout the '2.1' Branch
6. Allow maven dependencies to update (may take some time)

#### Stop Alfresco
1. If you have an alfresco instance running you can stop it now
2. ```cd /opt/alfresco-{alf.version}```
3. ```sudo ./alfresco.sh stop```
4. If you installed alfresco as a service and dont have port 8005 open internally you may get an error message and fail to stop the server if you attempt ```sudo service alfresco stop```

#### Build .Jar files
1. For each repo *in order* mbee_util/sysml/docbook/bae:
 1. Open Terminal
 2. Change to git directory ```cd /home/${user.name}``` changing ```${user.name}``` to your username
 3. Build the JAR ```mvn package```
 4. Next, add the newly created JAR to your local maven repo replacing ```${repo.name}``` with the repository you are working with
 
     ``` 
    mvn install:install-file -Dfile=target/${repo.name}-2.1.0-SNAPSHOT.jar -DgroupId=gov.nasa.jpl.mbee.${repo.name} -DartifactId=${repo.name} -Dversion=2.1.0-SNAPSHOT -Dpackaging=jar
    ```
    Note: For docbook chagne ```2.1.0-SNAPSHOT``` to ```0.0.5```
    Note: For util use ```mbee_util``` for Dfile and DartifactId but only ```util``` for DgroupId
    
 5. Repeat for other repos before building bae
    

#### Build EMS
1. There are three parts to EMS
 2. EMS-Repo the Alfresco Explorer extension for the model repository
 3. EMS-Share the extensions for Alfresco's Share CMS
 4. EVM the Engineering View Applications built using ractive and javascript
1. Each must first be built and deployed on a local environment before testing locally
    If you are deploying locally you can skip the "mvn package step" and go straight to integration-test
2. To deploy to a production environment after building see the next section

##### Build the EMS-Repo AMP
1. In terminal navigate to EMS-Repo in git directory
2. Build the amp ```mvn package -P mbee-dev```
3. If you want to run a local instance in an embedded jetty container (Repo on port  localhost:8080)

    ```
    mvn integration-test -Pamp-to-war -Dmaven.test.skip=true -P mms-dev
    ```
4. if you want to clean a failed attempt ```mvn clean -Ppurge```

##### Build the EMS-Share AMP
1. In terminal navigate to EMS-Share repository
2. Build the AMP ```mvn package -P mbee-dev```
3. If you want to run a local instance in an embedded jetty container (Share on port  localhost:8081)

    ```
    mvn integration-test -Pamp-to-war -Dmaven.test.skip=true -P mms-dev
    ```

##### Build the EVM.zip
1. You must first have installed all the dependencies in the EMS-Webapp Applications development environment above
2. Navigate to the correct directory ```cd ${git.dir}/EMS-Webapp```
3. Install additional dependencies ```npm install``` which will look at the directory's package.json file
4. Execute the build ```grunt```
5. If you get an error like this:
    ```
    Unable to find a suitable version for angular, please choose one:
    1) angular#1.2.28 which resolved to 1.2.28 and is required by angular-animate#1.2.28 
    2) angular#~1.2.x which resolved to 1.2.28 and is required by angular-ui-sortable#0.12.11 
    3) angular#~1.3.8 which resolved to 1.3.15 and is required by mms 
    4) angular#>= 1.0.8 which resolved to 1.3.15 and is required by angular-ui-router#0.2.14 
    5) angular#>=1 which resolved to 1.3.15 and is required by angular-ui-bootstrap-bower#0.11.2 
    6) angular#>= 1.2.0 which resolved to 1.3.15 and is required by angular-ui-tree#2.1.5 
    7) angular#>=1.2.1 which resolved to 1.3.15 and is required by angular-growl-v2#0.6.1 
    8) angular#>=1.2.x which resolved to 1.3.15 and is required by angular-ui-sortable#0.13.3 
    9) angular#1.3.15 which resolved to 1.3.15 and is required by angular-animate#1.3.15Prefix the choice with ! to persist it to bower.json
    ```
    Select an option that resolves to 1.3.15 (3-9)

6. If you want to test locally Grunt server:localhost (port 9000)

#### Deploying to production Alfresco

1. Uninstall any existing, Navigate to /{alfresco.dir}/bin/
 2. cd /opt/alfresco-${alfresco.version}

    ```
    sudo java -jar ./bin/alfresco-mmt.jar uninstall mms-repo ./tomcat/webapps/alfresco.war
    sudo java -jar ./bin/alfresco-mmt.jar uninstall mms-share ./tomcat/webapps/share.war
    ```
3. Install the amps
 4. cd /opt/alfresco-${alfresco.version}
    ```
    sudo java -jar ./bin/alfresco-mmt.jar install ${git.dir}/EMS-Repo/target/mms-repo.amp ./tomcat/webapps/alfresco.war -force
    sudo java -jar ./bin/alfresco-mmt.jar install ${git.dir}/EMS-Share/target/mms-share.amp ./tomcat/webapps/share.war -force
    ```
4. Explode the Alfresco WAR
 4. ```cd $TOMCAT/webapps```
 5. ```sudo rm -rf alfresco```
 6. ```sudo mkdir alfresco```
 7. ```cd ./alfresco```
 8. ```sudo jar xvf ../alfresco.war```
5. Explode the Share WAR
 4. ```cd $TOMCAT/webapps```
 5. ```sudo rm -rf share```
 6. ```sudo mkdir share```
 7. ```cd ./share```
 8. ```sudo jar xvf ../share.war```
5. Unzip EVM ```unzip evm.zip```
 1. ```cp -r ${git.dir}/EMS-Webapp/build mmsapp```
2. ```cd $TOMCAT/webapps```
3. ```chown -R tomcat:tomcat webapps```
6. Remove Extra Solr files
 1. ```sudo rm -rf ${tomcat.dir}/conf/Catalina/localhost/solr.xml```
 2. ```sudo rm -rf ${tomcat.dir}/work```
7. Copy Docbookgen
 1. ```sudo cp -r {$git.dir}/docbookgen docbookgen```
 2. ```cd docbookgen```
 3. 

### Server Configuration
1. Modify the global properties file to support lucine searching
2. ```sudo vim ${tomcat.home}/shared/classes/alfresco-global.properties```
3. Edit the file to include

    ```
    ### Lucene Settings ###
    index.subsystem.name=lucene
    index.recovery.mode = AUTO
    index.tracking.disableInTransactionIndexing = false
    ```

4. If you see a section called ```### Solr Settings ###``` comment each setting out by placing a ```#``` in front of each line
#### Basics
1. Check your ports depending on your configuration you might want (80,443,
2. Ensure alfresco and httpd are running at startup ```sudo chkconfig alfresco on```; ```sudo chkconfig httpd on```
3. 



### MDK
1. TBD
2. there’s a class called qvt script runner or something similar, if you delete that and delete any broken calls to it it should compile with md without nick’s stuff

#Warning these instructions are only tested for EMS Version 2.0 (Commits prior to May 4th 2015)

# Install OpenMBEE Environment from Source

1. Test was conducted on VMWare esXI 5.5
 1. CPUS = 1 Core
 1. RAM = 2GB
HDD = 100GB
HostName = openmbee.db8tnet

## Procedure

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
    IPADDR=<your ip>
    NETMASK=255.255.255.0
    ```
 5. Configure Default gateway
    ```
    vim /etc/sysconfig/network
    ```
    
 6. Run DHClient
    ```
    sudo dhclient
    ```
    
 7. Restart machine
1. in desktop click on network manager to ensure the connection is active

#### Enable SSH
2. Open terminal
    ```
    sudo chkconfig sshd on
    ```
3. Restart
    ```
    sudo shutdown -r now
    ```

#### Setup VNC (OPTIONAL)
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
#### Install Support Pgms
1. These may already be installed depending on the version and deployment of CentOS used
2. Install vim (or your favorite text editor) ```sudo yum install vim```
3. Install wget ```sudo yum install wget```
4. 

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
    export JAVA_HOME=/usr/lib/jvm/jre-1.7.0-openjdk.x86_64
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

#### Install Ant (Not Required)
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

#### Install Eclipse (Optional only if you prefer a gui git/coding interface)
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
 2 . Other useful ports
 
     HTTP 80 and 443 (redirect these to /share/page)
     Postgresql 5432
     
10. Test using internal and external browsers!

### Build OpenMBEE Source Code

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
4. uncheck build automatically
5. download these repos using eclipse
6. Allow maven dependencies to update (may take some time)

#### Build .Jar files
1. For each repo in order util/sysml/docbook:
 1. Open Terminal
 2. Change to git directory ```cd /home/${user.name}``` changing ```${user.name}``` to your username
 3. Build the JAR ```mvn package```
 4. Next, add the newly created JAR to your local maven repo replacing ```${repo.name}``` with the repository you are working with
 
     ``` 
    mvn install:install-file -Dfile=target/${repo.name}-2.1.0-SNAPSHOT.jar -DgroupId=gov.nasa.jpl.mbee.${repo.name} -DartifactId=${repo.name} -Dversion=2.1.0-SNAPSHOT -Dpackaging=jar
    ```
    Note: For docbook chagne ```2.1.0-SNAPSHOT``` to ```0.0.1```
    
 5. Repeat for other repos
2. Next build BAE
 1. Open Eclipse
 2. In JAVA Perspective open the BAE project
 3. Open pom.xml
 4. After Line 20 ```<repositories>``` delete all content within the tag
 5. Insert the following between the newly empty tags
     ```
     <repository>
      <id>local-repository</id>
      <name>local</name>
      <url>file://${user.home}/.m2/repository</url>
      </repository>
      ```
      This tells maven to look only at your local repository rather than the JPL internal maven repo
  6. Look for the ```<dependencies>``` tag
  7. Insert the following new dependency
  
    ```
      <dependency>
    	<groupID>junit</groupID>
    	<artifactID>junit</artifactID>
    	<version>4.12</version>
    </dependency>
    ```
 
  7. Save and close the file
  7. Open the terminal again
  8. Change directories to the BAE repository ```${user.home}/git/bae```
  9. Build the JAR ```mvn package```
  10. Install the JAR to the local repository
    ```
    mvn install:install-file -Dfile=target/bae-2.1.0-SNAPSHOT.jar -DgroupId=gov.nasa.jpl.mbee.bae -DartifactId=bae -Dversion=2.1.0-SNAPSHOT -Dpackaging=jar
    ```
    

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
4. 
2. Test locally Grunt server:localhost (port 9000)

#### Deploying to production Alfresco

1. Uninstall any existing, Navigate to /{alfresco.dir}/bin/
 2. cd $TOMCAT/webapps

    ```
    java -jar $ALFRESCO/bin/alfresco-mmt.jar uninstall mms-repo alfresco.war
    java -jar $ALFRESCO/bin/alfresco-mmt.jar uninstall mms-share share.war
    ```
3. Install the amps
 4. cd /opt/alfresco-${alfresco.version}
    ```
    sudo java -jar ./bin/alfresco-mmt.jar install ${git.dir} /EMS-Repo/target/mms-repo.amp ./tomcat/webapps/alfresco.war -force
    sudo java -jar $ALFRESCO/bin/alfresco-mmt.jar install $PATH_TO_AMP/mms-share.amp $TOMCAT/share.war -force
    ```
4. Explode the WAR
 4. ```cd $TOMCAT/webapps```
 5. ```sudo rm -rf alfresco```
 6. ```sudo mkdir alfresco```
 7. ```cd ./alfresco```
 8. ```sudo jar xvf ../alfresco.war```
5. Unzip EVM ```unzip evm.zip```
 1. ```cp ${git.dir}/EMS-Webapp/build mmsapp```
2. ```cd $TOMCAT/webapps```
3. ```chown -R tomcat:tomcat webapps```

### Server Configuration

#### Basics
1. Check your ports depending on your configuration you might want (80,443,
2. Ensure alfresco and httpd are running at startup ```sudo chkconfig alfresco on```; ```sudo chkconfig httpd on```
3. 



### MDK
1. TBD
2. there’s a class called qvt script runner or something similar, if you delete that and delete any broken calls to it it should compile with md without nick’s stuff

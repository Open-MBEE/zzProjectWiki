#Warning these instructions are still in testing and may not be complete

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

#### Setup VNC
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

#### Install Ant
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

#### Install Eclipse
1. Download Installer

    ```
    wget http://www.eclipse.org/downloads/download.php?file=/technology/epp/downloads/release/luna/SR2/eclipse-java-luna-SR2-linux-gtk-x86_64.tar.gz&r=1
    ```
2. Expand Archive and copy to opt

    ```
    sudo tar xzf eclipse-java…_64.tar.gz -C /opt
    ```
3. Change Directories ```cd /opt```
4. Fix permissions
    ```
sudo chmod -R +r /opt/local/eclipse
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
    export ECLIPSE_HOME=“/opt/eclipse”
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
1. For each repo bae/sysml/util:
 1. open each build.xml and replace gitDir definition with
 
    ```
    <property name="gitDir" location="..”/>
    ```
 2. rename view_repo_lib property to view_repo_amp_lib
 3. add a new property

     ```
    <property name="view_repo_lib" value="${view_repo}/lib”/>
    ```
 4. after ```<copy todir=“${view_repo_lib}….``` add

    ```
    <copy todir="${view_repo_amp_lib}" file="mbee_util.jar" failonerror="false”/>
    ```
5. in terminal navigate to git directory for repo

    ```
    ant
    ```
 
 6. repeat
2. Next build docbook
 1. navigate to the docbook git directory ```mvn package```
 2. Copy the file to EMS-Repo/lib ```cp docbook.jar```

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
1. See instructions in the EMS-Webapp Repo
2. Test locally Grunt server:localhost (port 9000)

#### Deploying to production Alfresco

1. Uninstall any existing, Navigate to /{alfresco.dir}/bin/
 2. cd $TOMCAT/webapps

    ```
    java -jar $ALFRESCO/bin/alfresco-mmt.jar uninstall mms-repo alfresco.war
    java -jar $ALFRESCO/bin/alfresco-mmt.jar uninstall mms-share share.war
    ```
3. Install the amps
 4. cd $TOMCAT/webapps
    ```
    java -jar $ALFRESCO/bin/alfresco-mmt.jar install $PATH_TO_AMP/mms-repo.amp $TOMCAT/alfresco.war -force
    java -jar $ALFRESCO/bin/alfresco-mmt.jar install $PATH_TO_AMP/mms-share.amp $TOMCAT/share.war -force
    ```
4. Explode the WAR
 4. ```cd $TOMCAT/webapps```
 5. ```rm -rf alfresco```
 6. ```mkdir alfresco```
 7. ```cd alfresco```
 8. ```jar xvf ../alfresco.war```
5. Unzip EVM ```unzip evm.zip```
 1. ```mv build mmsapp```
2. ```cd $TOMCAT/webapps```
3. ```chown -R tomcat:tomcat webapps```

#### MDK
1. TBD
2. there’s a class called qvt script runner or something similar, if you delete that and delete any broken calls to it it should compile with md without nick’s stuff

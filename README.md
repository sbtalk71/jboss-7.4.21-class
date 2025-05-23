# jboss-7.4.21-class

1. Exploded Deployment
   ```sh
   cd /home/alchemy/<your folder for apps>
   unzip Test.war -d Test1.war/
   cp -r Test1.war /opt/production/EAP74-1/standalone/deployment/
   touch  /opt/production/EAP74-1/standalone/deployment/Test1.war.dodeploy
   ```

3. Open browser at http://<your_vm_ip>:8080/Test1/Hello.jsp


## Deployment with CLI
1. Open a terminal
2. Issue the commands as given
```sh
 cd /opt/production/EAP74-1/bin
 ./jboss-cli.sh
```
connect <your vm ip>

deploy path/to/archive/file

## Start Server in domain mode
```sh
cd
cd /opt/produntion/EAP74-1/bin
./domain.sh -b192.168.231.128 -bmanagement=192.168.231.12
```
## Deploy apps in domain mode
```sh
cd /opt/produntion/EAP74-1/bin
./jboss-cli.sh
connect
 deploy path/to/Test.war --server-groups=main-server-group
```

## add a module for Datasource jdbc Driver
```sh
   cd /opt/production/EAP74-1/bin
./jboss-cli.sh
module add --name=my.mariadb.app --module-root-dir=/opt/production/EAP74-1/modules/system/layers/base/ --resources=/path/to/mariadb-java-client-3.1.4.jar --dependencies=javax.api,javax.transaction.api
```

## Datasource Configuration
1. Add a jdbc Driver to full profile using jboss cli online
   ```sh
   /profile=full/subsystem=datasources/jdbc-driver=mariadb:add(driver-name="mariadb",driver-module-name="my.mariadb.app",driver-class-name="org.mariadb.jdbc.Driver")
   ```
2. Add the datasource
   ```sh
   /profile=full/subsystem=datasources/data-source=MariadbDS:add(jndi-name="java:/jdbc/mariadbDS",connection-url="jdbc:mariadb://192.168.231.1:3306/empdb",user-name="root",password="welcome",min-pool-size=2,max-pool-size=5,use-ccm=true, driver-name=mariadb)
   ```
## Deploy application using the above Datasource
```sh
cd /home/alchemy/<your examples directory>
unzip TestDatasource.war -d TestDS/
cp jboss-web.xml TestDS/WEB-INF/
vi TestDS/WEB-INF/jboss-web.xml

update <jndi-name>java:/jdbc/mariadbDS</jndi-name>
cd TestDS
jar -cvf TestDS.war . 
```
 Use jboss-cli to deploy the created TestDS.war
1. Test the application with url : http://192.168.231.128:8080/TestDS/EmpInfo.html

## SSL commands
```sh
keytool -genkey -alias mykey -keystore mykeys.pkcs12 -storepass welcome1 -storetype PKCS12 -keyalg RSA -validity 365

/host=master/subsystem=elytron/key-store=exampleKeyStore:add(path=mykeys.pkcs12,credential-reference={clear-text=welcome1},type=PKCS12)
/host=master/subsystem=elytron/key-manager=exampleKeyManager:add(key-store=exampleKeyStore,credential-reference={clear-text=welcome1})

/host=master/subsystem=elytron/server-ssl-context=examplehttpsSSC:add(key-manager=exampleKeyManager, protocols=["TLSv1.2"])

/host=master/core-service=management/management-interface=http-interface:write-attribute(name=ssl-context, value=examplehttpsSSC)

/host=master/core-service=management/management-interface=http-interface:write-attribute(name=secure-port,value=9993)
```
## For Domain Controller (undertow Server)
```sh
/profile=full/subsystem=elytron/key-store=exampleKeyStore:add(path=mykeys.pkcs12,credential-reference={clear-text=welcome1},type=PKCS12)
/profile=full/subsystem=elytron/key-manager=exampleKeyManager:add(key-store=exampleKeyStore,credential-reference={clear-text=welcome1})

/profile=full/subsystem=elytron/server-ssl-context=examplehttpsSSC:add(key-manager=exampleKeyManager, protocols=["TLSv1.2"])

/profile=full/subsystem=undertow/server=default-server/https-listener=https:write-attribute(name=ssl-context, value=examplehttpsSSC)
```
## Using a Security domain for Application Security (File System realm)
```sh
cd
cd /opt/production/EAP74-1/bin
./jboss-cli.sh
connect 192.168.231.128
cd /host=master

./subsystem=elytron/filesystem-realm=exampleFfsRealm:add(path=fs-realm-users,relative-to=jboss.domain.config.dir)

./subsystem=elytron/filesystem-realm=exampleFfsRealm:add-identity(identity=user1)

./subsystem=elytron/filesystem-realm=exampleFfsRealm:set-password(identity=user1, clear={password="passwordUser1"})

./subsystem=elytron/filesystem-realm=exampleFfsRealm:add-identity-attribute(identity=user1, name=Roles, value=["Admin","Guest"])
```
**As a result of the above commands, the following entry is made in host.xml**
```xml
	<filesystem-realm name="exampleFfsRealm">
      <file path="fs-realm" relative-to="jboss.domain.config.dir"/>
    </filesystem-realm>
```
**copy the block and paste it bewteen `<security-relams>` tag in full profile of domain.xml file**
1. now execute the following in CLI terminal to apply the scurity domain to undertow subsystem:
 ```sh
cd /profile=full
./subsystem=elytron/security-domain=exampleSecurityDomain:add(default-realm=exampleFfsRealm,permission-mapper=default-permission-mapper,realms=[{realm=exampleFfsRealm}])

./subsystem=elytron/http-authentication-factory=myHttpAuthFactory:add(http-server-mechanism-factory=global, security-domain=exampleSecurityDomain, mechanism-configurations=[{mechanism-name="BASIC", realm-name="exampleFfsRealm"}])

./subsystem=undertow/application-security-domain=myWebAppDomain:add(http-authentication-factory=myHttpAuthFactory)
```
## Deploy a secured app and test
user: user1
password: welcome1

1. go to your application repository folder
2. `unzip SecuredApp.zip`
3. `cd SecuredApp`
4. `vi WEB-INF/web.xml and change <role-name>manager</role-name> to <role-name>Admin<role-name>, save and exit`
5. `vi WEB-INF/jboss-web.xml replace java:jass/.... to myWebAppDomain`, save and exit
6. `jar -cvf securedApp.war .`
7. use jboss-cli to deploy the securedApp.war 
8. check deployment-info
9. `use http://192.168.231.128:8080/securedApp/`
10. Authenticate the user
    
##  Create JMS Resources in full profile
```sh
/profile=full/subsystem=messaging-activemq/server=default/jms-queue=MyQueue:add(entries=["java:/jms/queue/MyQueue"])
/profile=full/subsystem=messaging-activemq/server=default/jms-topic=MyTopic:add(entries=["java:/jms/topic/MyTopic"])
/profile=full/subsystem=messaging-activemq/server=default/connection-factory=MyConnectionFactory:add(entries=["java:/jms/MyConnectionFactory"],connectors=["in-vm"])
```
## Deploy JMS Sample App
1. `unzip JMSSampleApplication_Web.war -d JmsWebApp/`
2. copy jboss-web.xml to JmsWebApp/WEB-INF/
3. edit jboss-web.xml
4. overwrite the existing content between `<jboss-web> </jboss-web>` tag with
```xml
<resource-ref>
        <res-ref-name>jms/qcf</res-ref-name>
        <jndi-name>java:/jms/MyConnectionFactory</jndi-name>
    </resource-ref>
	<resource-ref>
        <res-ref-name>jms/queue_sender</res-ref-name>
        <jndi-name>java:/jms/queue/MyQueue</jndi-name>
    </resource-ref>
	<resource-ref>
        <res-ref-name>jms/queue_receiver</res-ref-name>
        <jndi-name>java:/jms/queue/MyQueue</jndi-name>
    </resource-ref>
```
Create a .war file from the JmsWebApp's content
`cd JmsWebApp/`
`java -jar JmsWebApp.war .`

in another terminal use jboss-cli.sh to deploy the application:
```sh
./jboss-cli.sh
connect <ip>
deploy <the location of JmsWebApp.war> --server-groups=main-server-group
```
## Apache Server commands
1. download apache server for windows 64bit from https://www.apachelounge.com/download/VS17/binaries/httpd-2.4.63-250207-win64-VS17.zip
2. extract Apache24 from the Zip on c:\
3. Open a cmd terminal as Administrator
4. cd c:\Apache24\bin
5. httpd -k install (installe Windows Service)
6. httpd -k start
7. httpd -k restart
8. httpd -k stop
9. httpd -k uninstall

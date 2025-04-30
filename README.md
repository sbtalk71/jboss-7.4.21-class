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
## Deploy application using The above Datasource
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

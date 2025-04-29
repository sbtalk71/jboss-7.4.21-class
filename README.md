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
./jboss-cli.sh deploy path/to/Test.war --server-groups=main-server-group
```


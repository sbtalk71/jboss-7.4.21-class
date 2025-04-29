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

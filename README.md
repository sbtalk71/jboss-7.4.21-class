# jboss-7.4.21-class

1. Exploded Deployment
   ```sh
   cd /home/alchemy/<your folder for apps>
   unzip Test.war -d Test1.war/
   cp -r Test1.war /opt/production/EAP74-1/standalone/deployment/
   touch  /opt/production/EAP74-1/standalone/deployment/Test1.war.deploy
   ```

3. Open browser at http://<your_vm_ip>:8080/Test1/Hello.jsp

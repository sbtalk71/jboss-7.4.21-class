# Update Jboss EAP 7.4 to EAP 7.4.21 on Windows 11

**Refer to document https://access.redhat.com/articles/6956863**

1. **Download the jboss-eap-7.4.zip from Redhat**
2. **Download the patch file jboss-eap-7.4.21-patch.zip  from redhat and keep it in local file system (it is in D:\)**
3. **Exract jboss-eap-7.4.zip file in a suitable folder (e.g. d:\jboss-eap-7.4 -->JBOSS_HOME)**
4. **Run the following command:**

```cli
BOSS_HOME\bin\jboss-cli.bat "patch apply d:\jboss-aep-7.4.21-patch.zip"

```
5. **wait for the update to finish**

6. **Once finished, update the elytron security configurations**

    **Note**: *after running the update, if you start the server(s), it will fail to start raising execptions due to non supported security system consfiguration. You need to update the elytron subsystem configuration.*

7. Standalone Mode
To use the script, after backing up your configuration directory, open a terminal in the 

$JBOSS_HOME directory and run the CLI as follows:
```sh
bin/jboss-cli.sh --file=docs/examples/enable-elytron-se17.cli
```
That command will update the $JBOSS_HOME/standalone/configuration/standalone.xml configuration. To modify a different configuration, set the config system property to specify the name of the desired configuration file. (The file must be located in the configuration directory.)
```sh
bin/jboss-cli.sh --file=docs/examples/enable-elytron-se17.cli -Dconfig=standalone-full.xml
```
8. Domain Mode
Similarly to standalone mode, there is an example domain mode script in EAP 7.4 Update 8+

To use the script, after backing up your configuration directory, open a terminal in the $JBOSS_HOME directory and run the CLI as follows:
```sh
bin/jboss-cli.sh --file=docs/examples/enable-elytron-se17-domain.cli

```
**The above script will update the elytron subsystem configurations**

7. **Start Jboss EAP**
```sh
BOSS_HOME\bin\standalone.bat

``` 
8. **Log into admin console to verify the applied patch.**

## Same approach can be followed for Linux OS. Please change the command syntax as per the OS you use


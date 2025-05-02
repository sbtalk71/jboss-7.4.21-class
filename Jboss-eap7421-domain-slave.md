## Domain mode setup in Jboss EAP 7.4.21 (patched and upgraded with patch .21 on EAP 7.4)
1. JBoss EAP 7.4 is patched with upadte .21
2. Elytron Security updated to use JDk 17 
3. ELytron upgrade CLI files avilable after patch in $JBOSS_HOME/docs/examples

### Setup Domain side (machine1)

1. Create an admin user in Management Realm
```sh
$JBOSS_HOME/bin/add-user.sh -u jboss -p welcome1 -g admin
```

2. Start the domain 
```sh
$JBOSS_HOME/bin domain.sh -b <IP_Address> -bmanagement=<IP_ADDRESS>
```

### Setup Host side (Machine2)
1. 1. Create an admin user in Management Realm
```sh
$JBOSS_HOME/bin/add-user.sh -u jboss -p welcome1 -g admin
```
2. We are changing `host.xml` in the domain directory
3. Open `host.xml` in an editor
4. find the element :
```xml
<domain-controller>
        <local/>
</domain-controller>
```
replace the above `xml` fragment with the given one below:
```xml
<domain-controller>
    <remote authentication-context="secondary-hc-auth-context">
        <discovery-options>
             <static-discovery name="primary" protocol="${jboss.domain.master.protocol:remote+http}" host="${jboss.domain.master.address}" port="${jboss.domain.master.port:9990}"/>
        </discovery-options>
    </remote>
</domain-controller>
```
4. Find the eleytron subsystem as given
```xml
<subsystem xmlns="urn:wildfly:elytron:13.0" final-providers="combined-providers" disallowed-providers="OracleUcrypto">
....
</subsystem>
```
add the following xml fragment after `</sasl>` element:
```xml
<authentication-client>
    <authentication-configuration name="secondary-hc-auth" authentication-name="jboss">
      <credential-reference clear-text="welcome1"/>
    </authentication-configuration>
    <authentication-context name="secondary-hc-auth-context">
      <match-rule authentication-configuration="secondary-hc-auth"/>
    </authentication-context>
</authentication-client>

```
5. Open a terminal and start the domain with the follwong command:
```sh
$JBOSS_HOME/bin/domain.sh --host-config host.xml -b<IP_ADDRESS_OF_THIS_MACHINE> -Djboss.domain.master.address=<master node IP> -Djboss.domain.master.port=<master node port>
```
6. Go to Machine1 and verify the domain logs that a remote host has joined

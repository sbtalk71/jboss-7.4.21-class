## Add a custom module to Jboss EAP

1. To add a custom mocule start jboss-cli.(sh/bat) and use the follwing command
```sh
	module add --name=com.mariadb.app --resources=D:\jboss-eap-7.4.21\mariadb-java-client-3.5.1.jar --dependencies=javax.api,javax.transaction.api
```

2. Verify that the module is loaded. Start the server(any profile) and connect with Jboss CLI and then  issue the command, it should return success:

```sh
 /core-service=module-loading:list-resource-loader-paths(module=com.mariadb.app)
```

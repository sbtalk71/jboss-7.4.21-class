
# JBoss EAP 8.0 CLI Commands to Create JMS Resources

This document provides CLI commands to create JMS Queue, Topic, and Connection Factory in JBoss EAP 8.0 using the messaging-activemq subsystem.

---

## 1. Create a JMS Queue

```
/subsystem=messaging-activemq/server=default/jms-queue=MyQueue:add(entries=["java:/jms/queue/MyQueue"])
```

---

## 2. Create a JMS Topic

```
/subsystem=messaging-activemq/server=default/jms-topic=MyTopic:add(entries=["java:/jms/topic/MyTopic"])
```

---

## 3. Create a Connection Factory

```
/subsystem=messaging-activemq/server=default/connection-factory=MyConnectionFactory:add(
    entries=["java:/jms/MyConnectionFactory"],
    connectors=["in-vm"],
    transaction=xa
)
```

- You can change `connectors=["in-vm"]` to `["http-connector"]` or others based on your transport.
- `transaction=xa` is optional; use `none` for non-XA.

---

## 4. Reload the Server (if required)

```
:reload
```

---

## Optional: Check Configuration

```
/subsystem=messaging-activemq/server=default:read-resource(recursive=true)
```

---

## Notes

- Adjust names and entries as per your application needs.
- For remote access, configure appropriate connectors and security realms.

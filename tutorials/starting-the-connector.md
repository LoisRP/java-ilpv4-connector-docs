---
description: Covers everything you need to start up the Connector in "developer" mode.
---

# Start the Connector

## Dev Mode Configuration

Next, start up the Connector with this command:

```text
cd /srv/connector-java
java -Dspring.profiles.active=dev -jar ./connector-server-HEAD-SNAPSHOT-exec.jar
```

{% hint style="success" %}
Success! Your Connector is now running.
{% endhint %}

This tutorial helps you start a minimally configured Connector. However, for actual usage, you should configure persistent data storage, balance tracking, and more. For more details, see the options defined in [Connector Configuration Properties](../operating-a-connector/configuration.md).

{% hint style="danger" %}
The above command will start up the Connector in "dev" mode, which uses in-memory persistence for all services that the Connector relies upon. This means that every time you restart the Connector in this mode, all account and balance data will be lost.
{% endhint %}

---
layout: default
title: "PE 2015.2 » Configuration"
subtitle: "Configuring & Tuning the Console & Databases"
canonical: "/pe/latest/console_config.html"
---

## Configuring Console Access Control

### Changing Session Duration

There are a few options that can be configured for access control:

- `password-reset-expiration`  When a user doesn't remember their current password, an administrator can generate a token for them to change their password. The duration, in hours, that this generated token is valid can be changed with this config parameter. The default value is 24.
- `session-timeout`  This parameter is a positive integer that specifies how long a user's session should last, in minutes. This session is the same across node classification, RBAC, and the console. The default value is 60.
- `failed-attempts-lockout`  This parameter is a positive integer that specifies how many failed login attempts are allowed on an account before that account is revoked. The default value is 10.

To change these defaults, add a new file to the `/etc/puppetlabs/console-services/conf.d` directory. The file name is arbitrary, but the file format is [HOCON](https://github.com/typesafehub/config#using-hocon-the-json-superset).

    rbac: {
     password-reset-expiration: 24
     session-timeout: 60
     failed-attempts-lockout: 10
    }

Then restart pe-console-services (`sudo service pe-console-services restart`).

### Tuning Java Args for the PE Console

You can increase the JVM (Java Virtual Machine) memory that is allocated to Java services running on the PE console. This memory allocation is known as the Java heap size.

Instructions for using the PE console to increase the Jave heap size are detailed on on the [Configuring Java Arguments for PE](./config_java_args.html#pe-console-services) page.

### Tuning the Classifier Synchronization Period

This synchronization period controls how long it takes the node classifier (NC) to retrieve classes from the Puppet master, which essentially dictates how soon you can see your changes appear in the NC. By default this value is set to 600 seconds (10 minutes). Depending on the size of your environment(s), this may or may not be a suitable amount of time.

You can change this value in the PE console.

1. In the PE console, navigate to the **Classification** page.
2. Click the **PE Console** group.
3. In the **PE Console** group page, click the **Classes** tab.
4. Locate the **puppet_enterprise::profile::console** class, and from the **Parameter** drop-down list, select **classifier_synchronization_period**.
5. In the **Value** field, adjust the period, in seconds.
6. Click **Add parameter**, and then the **Commit change** button.

You can also force the NC to retrieve classes from the master immediately, by clicking the **Refresh** button.

If you don't ever want to synchronize automatically because you'd like to [trigger synchronization from the endpoint](./nc_update_classes.html#update-classes-endpoint), you can set the value to `0` at which point it will never synchronize.

## Tuning the PostgreSQL Buffer Pool Size

If you are experiencing performance issues or instability with the console, you may need to adjust the buffer memory settings for PostgreSQL. The most important PostgreSQL memory settings for PE are `shared_buffers` and `work_mem`.  Generally speaking, you should allocate about 25% of your hardware's RAM to `shared_buffers`. If you have a large and/or complex deployment you will probably need to increase `work_mem` from the default of 1mb. For more detail, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.2/static/runtime-config-resource.html).

The PE PostgreSQL configuration file is located at `/opt/puppetlabs/server/data/postgresql/9.4/data/postgresql.conf`.

After changing any of these settings, restart the PostgreSQL server:

    $ sudo /etc/init.d/pe-postgresql restart

## Changing the Console's Port

By default, a new installation of PE will serve the console on the default SSL port, 443. If you wish to change the port the console is available on:

1. Visit *Classification*
2. Select the PE Console node group.
3. In the Classes tab, find the `puppet_enterprise::profile::console Class`.
4. Add a new value for the `console_ssl_listen_port` parameter.
5. Trigger a Puppet run.

The console should now be available on the port you provided.

## Disabling Update Checking

[Disabling updated checking](./puppet_config.html#disabling-update-checking) is handled via the `pe-puppetserver` service.


* * *

- [Next: Configuring the PE Console to Use a Custom Certificate ](./custom_console_cert.html)

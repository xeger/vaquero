Overview
--------

Vaquero is a shell script that uses SSH tunnels to provide connectivity to
databases, REST services and other dependencies of your application, allowing
you to write code on your laptop and run it against a production-like
system.

The program itself is a cross-platform bash script; it relies on a text file
to define the mapping between local ports and remote hosts/ports.

After opening an SSH session to your host, vaquero invokes your application
and waits for one or the other to exit. An optional `.env.vaquero` file allows
you to set environment variables before invoking your program, e.g. to override
default inputs to your application and tell it how to reach the tunneled
services that vaquero provides.

Usage
-----

### Install

Download the vaquero script and install it somewhere in your path.

```bash
curl -o vaquero https://raw.githubusercontent.com/xeger/vaquero/master/vaquero 
install vaquero /usr/local/bin
```

### Configure for Your Application

Create a `.vaquero` file in your project's root directory to define the tunnels
that your application needs in order to run.

```text
3306:mysql.service.consul:3306
10080:localhost:8080
10081:foobar.service.consul:8080
```

If you need to tunnel to multiple services that listen on the same port,
simply choose a different local port (left-hand number) for each tunnel.

When specifying tunnel endpoints by hostname, DNS records are resolved _by the
SSH host_ and not the client; if the host is connected to a DNS service
discovery mechanism such as consul or etcd, this allows you to define tunnels
without knowing specific IP addresses in the remote network.

To tunnel to a service running _on_ your SSH host, use `localhost` as the
destination hostname.

### Override Environment Variables

If the `.env.vaquero` file exists in your project's root directory,
vaquero will export each key=value pair as an environment variable, allowing
you to provide vaquero-specific inputs to your application:

```text
MYSQL_HOST=127.0.0.1:3306
FOOBAR_URL=http://127.0.0.1:10081
BAZ_URL=http://127.0.0.1:10080
```

### Run Vaquero

`cd` into the directory where you created your `.vaquero` file. Invoke
vaquero, telling it which SSH host to connect to and which command to run:

```bash
vaquero joe@some-host.some-cloud.com ./my-app
```

When you're done experimenting with your app, press Ctrl+C to exit and close
the SSH tunnels.

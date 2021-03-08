# Install by .msi Installer \(Windows\)

This article explains how to install `td-agent`, the stable Fluentd distribution package maintained by [Treasure Data, Inc](http://www.treasuredata.com/), on Windows.

## What is `td-agent`?

Fluentd is written in Ruby for flexibility, with performance-sensitive parts in C. However, some users may have difficulty installing and operating a Ruby daemon.

That is why [Treasure Data, Inc](http://www.treasuredata.com/) provides **the stable distribution of Fluentd**, called `td-agent`. The differences between Fluentd and `td-agent` can be found [here](https://www.fluentd.org/faqs).

For Windows, `td-agent` is distributed as `.msi` installer.

* [`td-agent` v4 \(fluentd 1.11.x\)](install-by-msi.md#td-agent-v4)
* [`td-agent` v3 \(fluentd 1.10.x\)](install-by-msi.md#td-agent-v3)

## `td-agent` v4

### Step 1: Install `td-agent`

Download and install the `.msi` installer:

* [Download](https://td-agent-package-browser.herokuapp.com/4/windows)

Or, install with [winget](https://www.microsoft.com/en-us/p/app-installer/9nblggh4nns1):

```text
> winget install td-agent
```

### Step 2: Run `td-agent` from Command Prompt

First, prepare your config file located at `C:/opt/td-agent/etc/td-agent/td-agent.conf`. The following simple configuration is to dump any incoming records to `td-agent`'s log file:

```text
<source>
  @type forward
</source>
<match test.**>
  @type stdout
</match>
```

A new program `Td-agent Command Prompt` is installed as part of `td-agent`. Open this Command Prompt from the Windows Start menu.

Its icon looks like this on Windows Server 2012:

![Td-agent Command Prompt](../.gitbook/assets/msi-td-agent-command-prompt%20%281%29.png)

Now, launch `td-agent` with the following command:

```text
> fluentd -c etc\td-agent\td-agent.conf
```

Then, open another `Td-agent Command Prompt` instance and type the following command to send a record to `td-agent`:

```text
> echo {"message":"hello"} | fluent-cat test.event
```

It is working properly if you see the following in the logs:

```text
test.event: {"k", "v"}
```

[![Td-agent Windows Prompt](../.gitbook/assets/td-agent-windows-prompt.png)](https://github.com/fluent/fluentd-docs-gitbook/tree/da81ba70252eaa863cc28fc888584c59d6fc14d3/images/td-agent-windows-prompt.png)

### Step 3: Run `td-agent` as Windows service

Since version 4.0.0, `td-agent` is registered as a Windows service permanently by the msi installer. You can start `td-agent` service manually.

#### Using GUI

Please guide yourself to `Control Panel -> System and Security -> Administrative Tools -> Services`, and you'll see `Fluentd Windows Service` is listed.

Please double click `Fluentd Window Service`, and click `Start` button. Then the process will be executed as Windows Service.

#### Using `net.exe`

```text
> net start fluentdwinsvc
The Fluentd Windows Service service is starting..
The Fluentd Windows Service service was started successfully.
```

#### Using Powershell Cmdlet

```text
PS> Start-Service fluentdwinsvc
```

Note that using `fluentdwinsvc` is needed to start Fluentd service from the command-line. `fluentdwinsvc` is the service name and it should be passed to `net.exe` or `Start-Service` Cmdlet.

The log file will be located at `C:/opt/td-agent/td-agent.log` as we specified in Step 3.

### Step 4: Install Plugins

Open `Td-agent Command Prompt` and use `fluent-gem` command:

```text
> fluent-gem install fluent-plugin-xyz --version=1.2.3
```

## `td-agent` v3

### Step 1: Install `td-agent`

Please download and install the `.msi` file from here:

* [Download](https://td-agent-package-browser.herokuapp.com/3/windows)

### Step 2: Run `td-agent` from Command Prompt

First, please prepare your config file located at `C:/opt/td-agent/etc/td-agent/td-agent.conf`. The config below is the simplest example to output any incoming records to `td-agent`'s log file:

```text
<source>
  @type forward
</source>
<match test.**>
  @type stdout
</match>
```

After you have installed the .msi package, you will see the program called `Td-agent Command Prompt` installed. Please double click this icon in the Windows menu \(below is how it looks like on Windows Server 2012\).

![Td-agent Command Prompt](../.gitbook/assets/msi-td-agent-command-prompt%20%281%29%20%283%29.png)

In the prompt, please execute the command below to launch `td-agent` process:

```text
> fluentd -c etc\td-agent\td-agent.conf
```

Then, please launch another `Td-agent Command Prompt` and type the command below to send a record to `td-agent` process:

```text
> echo {"message":"hello"} | fluent-cat test.event
```

It's working properly if td-agent process outputs:

```text
test.event: {"k", "v"}
```

[![Td-agent Windows Prompt](../.gitbook/assets/td-agent-windows-prompt.png)](https://github.com/fluent/fluentd-docs-gitbook/tree/da81ba70252eaa863cc28fc888584c59d6fc14d3/images/td-agent-windows-prompt.png)

### Step 3: Register `td-agent` to Windows Service

Next, register `td-agent` as a Windows Service to permanently run as a server process. Open `Td-agent Command Prompt` with administrative privileges, and type these commands:

```text
> fluentd --reg-winsvc i
> fluentd --reg-winsvc-fluentdopt '-c C:/opt/td-agent/etc/td-agent/td-agent.conf -o C:/opt/td-agent/td-agent.log'
```

**NOTE**: Making `td-agent` service start automatically requires additional command-line parameters:

```text
> fluentd --reg-winsvc i --reg-winsvc-auto-start --reg-winsvc-delay-start
> fluentd --reg-winsvc-fluentdopt '-c C:/opt/td-agent/etc/td-agent/td-agent.conf -o C:/opt/td-agent/td-agent.log'
```

### Step 4: Run `td-agent` as a Windows Service

#### Using GUI

Go to `Control Panel > System and Security > Administrative Tools > Services`, and you should see `Fluentd Windows Service` listed there.

Double click on `Fluentd Window Service` and click `Start` to execute it as a Windows Service.

#### Using `net.exe`

```text
> net start fluentdwinsvc
The Fluentd Windows Service service is starting..
The Fluentd Windows Service service was started successfully.
```

#### Using Powershell Cmdlet

```text
PS> Start-Service fluentdwinsvc
```

Note that `fluentdwinsvc` is the Fluentd service name and it should be passed to `net.exe` or `Start-Service` Cmdlet to start.

The log file will be located at `C:/opt/td-agent/td-agent.log` as specified in Step 3 earlier.

### Step 4: Install Plugins

Open `Td-agent Command Prompt` and use `fluent-gem` command:

```text
> fluent-gem install fluent-plugin-xyz --version=1.2.3
```

## Tips

### Manage privileges in td-agent 3.8.1 or later/td-agent 4.1.0 or later

You need admin privilege to execute `td-agent-gem` command. For upgrade users since 3.8.0 or earlier/td-agent 4.0.1 or earlier, explicitly remove privileges for `NT AUTHORITY\Authenticated Users` from `c:\opt\td-agent`.

This change is for fixing [CVE-2020-28169](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-28169).

## Next Steps

You are now ready to collect real logs with Fluentd. Refer to the following tutorials on how to collect data from various sources:

* Basic Configuration
  * [Config File](../configuration/config-file.md)
* Application Logs
  * [Ruby](../language-bindings/ruby.md)
  * [Java](../language-bindings/java.md)
  * [Python](../language-bindings/python.md)
  * [PHP](../language-bindings/php.md)
  * [Perl](../language-bindings/perl.md)
  * [Node.js](../language-bindings/nodejs.md)
  * [Scala](../language-bindings/scala.md)
* Examples
  * [Store Apache Log into Amazon S3](../how-to-guides/apache-to-s3.md)
  * [Store Apache Log into MongoDB](../how-to-guides/apache-to-mongodb.md)
  * [Data Collection into HDFS](../how-to-guides/http-to-hdfs.md)

For further steps, follow these:

* [ChangeLog of td-agent](http://docs.treasuredata.com/articles/td-agent-changelog)
* [Chef Cookbook](https://github.com/treasure-data/chef-td-agent/)

If this article is incorrect or outdated, or omits critical information, please [let us know](https://github.com/fluent/fluentd-docs-gitbook/issues?state=open). [Fluentd](http://www.fluentd.org/) is an open-source project under [Cloud Native Computing Foundation \(CNCF\)](https://cncf.io/). All components are available under the Apache 2 License.

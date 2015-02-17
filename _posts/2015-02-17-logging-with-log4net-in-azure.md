---
layout: post
title: "Logging with log4net in Azure"
description: ""
category: 
tags: ["programming", "azure"]
---
{% include JB/setup %}

Though you can configure basic logging for Azure using built in 
[diagnostic tools](http://azure.microsoft.com/en-us/documentation/articles/cloud-services-dotnet-diagnostics/)
sometimes it's necessary to have more control on logging. So it might be a good idea
to use log4net on top of the azure diagnostics and configure it to fit all your needs.

<!-- more -->

In my case we already had the application with configured log4net and needed to adapt it
to the cloud environment. But if you're starting developing from scratch, you might
want to configure some advanced logging scenarios, which are not possible for now 
in default azure diagnostics.

So assuming you have sample cloud service and two roles configured - one worker and
one web role, lets first add log4net configuration to the role settings. We want 
our logs by default are placed to the same place as azure diagnostics log, so we have 
to use `TraceAppender`. Sample configuration looks as below

```xml
<log4net>   
    <appender name="TraceAppender" type="log4net.Appender.TraceAppender">
        <layout type="log4net.Layout.PatternLayout">
            <conversionPattern value="%date [%thread] %-5level %logger [%property{NDC}] - %message%newline" />
        </layout>
    </appender>
    <root>
        <level value="ALL" />
        <appender-ref ref="TraceAppender" />
    </root>
</log4net>
```

The only minor problem with storing configuration in role settings is that it's not 
possible to have multiline value there. So we have to have all configuration in a 
single line. It's not a big deal as for me. Line breaks are easy to replace.

So it's time to use this configuration in our roles. For worker role we place 
logging initialization to `WorkerRole.OnStart` method. But for web roles approach
is a little tricky. Since our web application (I'm talking about sample MVC project)
is running is ASP.NET process, which is completely unrelated to Azure roles, we
have to initialize logging there in the `Global.asax.Application_Start()` method.

```csharp
if (RoleEnvironment.IsAvailable)
{
    try
    {
        var log4netConfig = CloudConfigurationManager.GetSetting("log4net");
        var xmlDocument = new XmlDocument();
        xmlDocument.LoadXml(log4netConfig);

        XmlConfigurator.Configure(xmlDocument.DocumentElement);
    }
    catch (Exception ex)
    {
        Trace.TraceError("Error configuring log4net. Details {0}", ex);
    }
    
}
```

If for some reason log4net configuration fails, we just logging error using standard
Azure diagnostics and it will be logged to the place we expect to have all our log messages.
So we won't miss that configuration error.

Handling changes. You might be familiar with the `RoleEnvironment.Changed` event we will
use to observe and react on configuration changes. If no, please read info 
[here](https://msdn.microsoft.com/en-us/library/microsoft.windowsazure.serviceruntime.roleenvironment.changed.aspx).
And here is the event handler.

```csharp
private void RoleEnvironmentOnChanged(object sender, RoleEnvironmentChangedEventArgs roleEnvironmentChangedEventArgs)
{
    var log4netChange = roleEnvironmentChangedEventArgs.Changes
        .OfType<RoleEnvironmentConfigurationSettingChange>()
        .FirstOrDefault(c => string.Equals(c.ConfigurationSettingName, "log4net", StringComparison.OrdinalIgnoreCase));
    if (log4netChange == null)
    {
        return;
    }

    ConfigureLogging();
}
```

At the moment our code is executed at event handler we already have new configuration
values applied, and all we have to do is to reconfigure log4net.

If you deploy that sample app to the cloud, you should see all your log messages
in the `WADLogsTable` table of the table storage created for the cloud service.

Since log4net is in charge for writing messages and dealing with the logging level,
to see information/debug/warning messages you have to configure `Log Level` to `All` in 
the role Diagnostis Configuration.

But you might wonder why we need all this stuff at all, if we do no more than
just redirecting messages from log4net to standard trace appender. And here is the 
answer. Certainly it's cool to have as detailed logs as possible, but `level=ALL` is
not a setting you would want in production - it's too easy to drown in all those megabytes
of log messages. It would be nice to have some kind of smart logging, where certain amount
of info or debug messages were logged before the error/fatal happened. Using log4net it's easy
to support such scenario. All you need to do is to use `BufferingForwardingAppender`. It aggregates 
N last messages in the buffer and when threshold is reached all of them are flushed to the 
configured appender, `TraceAppender` in our case.

In sample configuration below 10 last messages is aggregated and if error is happened,
all of them are flushed to the configured `TraceAppender` which in turn writes messages
to the Azure table storage.

```xml
<log4net>
    <appender name="ErrorAppender" type="log4net.Appender.BufferingForwardingAppender">
        <bufferSize value="10" />
        <lossy value="true" />
        <evaluator type="log4net.Core.LevelEvaluator">
            <threshold value="ERROR" />
        </evaluator>
        <appender-ref ref="TraceAppender" />
    </appender>
    <appender name="TraceAppender" type="log4net.Appender.TraceAppender">
        <layout type="log4net.Layout.PatternLayout">
            <conversionPattern value="%date [%thread] %-5level %logger [%property{NDC}] - %message%newline" />
        </layout>
    </appender>
    <root>
        <level value="ALL" />
        <appender-ref ref="ErrorAppender" />
    </root>
</log4net>
```

You can find sample code for the article [here](https://github.com/raol/articles-code/tree/master/azure-logging)

And that's it.
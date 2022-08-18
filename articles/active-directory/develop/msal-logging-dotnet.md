---
title: Logging errors and exceptions in MSAL.NET
description: Learn how to log errors and exceptions in MSAL.NET
services: active-directory
author: mmacy
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
---

# Logging in MSAL.NET

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## Configure logging in MSAL.NET

In MSAL logging is set at application creation using the `.WithLogging` builder modifier. This method takes optional parameters:

- `IIdentityLogger` The logging implementation MSAL will send its logs too after chacking if logging is enabled.
- `PiiLoggingEnabled` enables you to log personal and organizational data (PII) if set to true. By default this is set to false, so that your application does not log personal data.


#### IIdentityLogger Interface
```CSharp
    public interface IIdentityLogger
    {
        //
        // Summary:
        //     Checks to see if logging is enabled at given eventLogLevel.
        //
        // Parameters:
        //   eventLogLevel:
        //     Log level of a message.
        bool IsEnabled(EventLogLevel eventLogLevel);

        //
        // Summary:
        //     Writes a log entry.
        //
        // Parameters:
        //   entry:
        //     Defines a structured message to be logged at the provided Microsoft.IdentityModel.Abstractions.LogEntry.EventLogLevel.
        void Log(LogEntry entry);
    }
```

#### IIdentityLogger Implementation

###### Log Level as Environment Variable

It is highly recommended to configure your code to use an environment variable on the machine to set the log level as it will enable your code to change the MSAL logging level without needing to rebuild the application. This is critical for diagnostic purposes, enabling us to quickly gather the required logs from the application that is currently deployed and in production.

See [EventLogLevel](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/dev/src/Microsoft.IdentityModel.Abstractions/EventLogLevel.cs) for details on the available log levels.

Example: 

```CSharp
    class MyIdentityLogger : IIdentityLogger
    {
        public EventLogLevel MinLogLevel { get; }

        public TestIdentityLogger()
        {
            //Try to pull the log level from an environment variable
            var msalEnvLogLevel = Environment.GetEnvironmentVariable("MSAL_LOG_LEVEL");

            if (Enum.TryParse(msalEnvLogLevel, out EventLogLevel msalLogLevel))
            {
                MinLogLevel = msalLogLevel;
            }
            else
            {
                //Recommended default log level
                MinLogLevel = EventLogLevel.Informational;
            }
        }

        public bool IsEnabled(EventLogLevel eventLogLevel)
        {
            return eventLogLevel <= MinLogLevel;
        }

        public void Log(LogEntry entry)
        {
            //Log Message here:
            Console.WriteLine(entry.message);
        }
    }
```

Using IdentityLogger:
```CSharp
    MyIdentityLogger myLogger = new MyIdentityLogger(logLevel);

    var app = ConfidentialClientApplicationBuilder
        .Create(TestConstants.ClientId)
        .WithClientSecret("secret")
        .WithExperimentalFeatures() //Currently an experimental feature, will be removed soon
        .WithLogging(myLogger, piiLogging)
        .Build();
```

> [!TIP]
 > See the [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) for samples of MSAL.NET logging and more.

## Next steps

For more code samples, refer to [Microsoft identity platform code samples](sample-v2-code.md).

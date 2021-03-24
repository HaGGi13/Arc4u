February 2020.

# Guidance 2021.2.0

This version adds the following features:
</br>

- Reorganisation of the micro-service projects.
- AspNet 5.0
- Http/2
- gRPC
- Ef Core Migration tool.

## Backend micro-service file structure organisation.

To be more concentrated on the core business of the micro-services, the Sdks, unit test and migration tools are moved to dedicated folders.
- Sdks for facade and interface sdk.
- Tests for testing projects. Today only integration one is created by default.
- Tools for any tools like the migraiton one for EF Core.

## AspNet 5.0?

This version of the Guidance is using the .net5.0 Arc4u dlls.
Those dlls are versioned like this:
  netStandardVersion: '5.0.2'
  iOSVersion: '11.1.5000.0'
  AndroidVersion: '9.0.5000.0'
  UwpVersion: '10.18362.5000.0'
  PrismVersion: '7.2.5000.0'

The migration from .netCore 3.1 to .net5.0 is not complex.
There is more subtleties related to the introduction of http/2 and gRPC than the use of ASP.Net 5.0.

The first thing to do is to change the target framework from .netcore3.1 to .net5.0.
As in the feature we will have one .net (.net 6.0) the use of .net standard 2.1 from the different projects are replaced by the .net 5.0 one.
Only the Sdks dlls are differents and will be explained later.

This is an example from a Solution6 generated code.
```csharp

The project structure is now like this:

├───Solution6.Yarp.Business
│   └───net50
├───Solution6.Yarp.Domain
│   └───net50
├───Solution6.Yarp.Facade
│   └───net50
├───Solution6.Yarp.IBusiness
│   └───net50
├───Solution6.Yarp.IISHost
│   └───net50
├───Sdks
│   └───Solution6.Yarp.Facade.Sdk
│       ├───net50
│       └───netstandard2.0
└───Tests
    └───Solution6.Yarp.IntegrationTest
        └───net50

```

As the sdks are consumed by clients using different technologies (.Net, Uwp, Xamarin.Forms) it is important to keep .netStandard 2.0.

The fact that we use .net50 for the proxy is to force the usage of http2 when we do a call from a micro-service to another one.

The complexity of the kestrel configuration  regarding the usage of http/2 will be explained later.

After, you have to update the program.cs and startup.cs code to integrate the gRPC capabilities.

### Http/2.

Http/2 is now the standard protocol to use for our communication between clients and services but also from services to services.

We have 2 behaviors to define:
- Rest Api.
- gRpc.

And those 2 behaviors are dependent of the context: UI or backend.

#### gRPC.

gRPC is natively communicating via http/2. The .Net implementation force the request to be protected via TLS 1.2 (using https) but this is possible to use http.
In the front end applications the communication are always protected and use TLS 1.2. For the non Kubernetes backends, we will also deploy and communicate
between the services via TLS 1.2. But in K8s, we will 









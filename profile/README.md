**Transactional communications for .NET, organized around intent rather than channels and providers.**

Transmitly lets your application describe what it needs to communicate while keeping how that communication is composed and delivered in one place.

Instead of spreading SMTP, SendGrid, Twilio, Firebase, templates, delivery callbacks, and channel-specific behavior throughout your application, your business code can stay focused on domain actions such as:

```csharp
await _communicationsClient.DispatchAsync(
    "WelcomeKit",
    newAccount.EmailAddress,
    new { firstName = newAccount.FirstName });
```

The `WelcomeKit` pipeline decides what that means.

```text
Application
    |
    | Dispatch("WelcomeKit")
    v
Transmitly Core
    |
    +-- Pipeline: WelcomeKit
          |
          +-- Email ----> SMTP / SendGrid / Mailgun / Infobip
          +-- SMS ------> Twilio / Infobip
          +-- Push -----> Firebase
          +-- Voice ----> Twilio / Infobip
```

Change providers, templates, channels, or delivery handling without pushing those decisions back into your domain code.

## Why Transmitly?

Transactional communication tends to start small and grow sideways:

- one email becomes many message types
- one provider becomes several providers or channels
- templates and credentials spread across services
- webhook and delivery-report handling becomes provider-specific
- application services accumulate `IEmailClient`, `ISmsClient`, push clients, and provider SDKs

Transmitly gives those concerns a common model:

- **Pipeline** — a domain-oriented communication intent such as `WelcomeKit`, `PasswordReset`, or `OrderProcessing`
- **Channel** — how a recipient can receive the communication: Email, SMS, Voice, or Push
- **Channel Provider** — the infrastructure that delivers a channel, such as SMTP, SendGrid, Twilio, Infobip, Mailgun, or Firebase

### A good fit when

- communication behavior is becoming infrastructure rather than a single `SendEmail(...)` call
- you want business logic to express intent instead of provider mechanics
- you have, or expect, multiple channels or providers
- composition, templates, delivery reporting, or provider webhooks need a consistent home
- you want the freedom to change delivery infrastructure without rewriting application workflows

### Probably more abstraction than you need when

If your application sends one kind of email through one provider and that is unlikely to change, a small email abstraction may be all you need.

Transmitly becomes useful when the abstraction you need is no longer "send an email", but "perform this communication intent."

## Extensibility built-in

Transmitly is intentionally extensible, so providers and integrations live in focused packages and repositories. You won't need to know the details unless you want to extend the library.

Start with the core library, then add only the integrations your application actually uses.

```text
                         +----------------------+
                         |      Transmitly      |
                         |        Core          |
                         |----------------------|
                         | Pipelines            |
                         | Channels             |
                         | Dispatch             |
                         | Delivery reports     |
                         | Extension points     |
                         | Simulation support   |
                         +----------+-----------+
                                    |
            +-----------------------+-----------------------+
            |                       |                       |
            v                       v                       v
   Channel Providers         Template Engines        Host Integrations
   -----------------         ----------------        -----------------
   SMTP                      Fluid                   Microsoft DI
   SendGrid                  Scriban                 ASP.NET Core MVC
   Mailgun                                           ASP.NET MVC
   Twilio
   Infobip
   Firebase
```

### Start here

- [Transmitly core library](https://github.com/transmitly/transmitly)
- [Samples](https://github.com/transmitly/transmitly/tree/main/samples)
- [Wiki](https://github.com/transmitly/transmitly/wiki)
- [GitHub Discussions](https://github.com/transmitly/transmitly/discussions)

### Add a channel provider when you need real delivery

- [SMTP](https://github.com/transmitly/transmitly-channel-provider-smtp)
- [SendGrid](https://github.com/transmitly/transmitly-channel-provider-sendgrid)
- [Mailgun](https://github.com/transmitly/transmitly-channel-provider-mailgun)
- [Twilio](https://github.com/transmitly/transmitly-channel-provider-twilio)
- [Infobip](https://github.com/transmitly/transmitly-channel-provider-infobip)
- [Firebase](https://github.com/transmitly/transmitly-channel-provider-firebase)

### Add application integrations

- [Microsoft.Extensions.DependencyInjection](https://github.com/transmitly/transmitly-microsoft-extensions-dependencyinjection)
- [ASP.NET Core MVC](https://github.com/transmitly/transmitly-microsoft-aspnetcore-mvc)
- [Fluid templates](https://github.com/transmitly/transmitly-template-engine-fluid)
- [Scriban templates](https://github.com/transmitly/transmitly-template-engine-scriban)

## Try the core library without a provider account

The core package includes simulation support, so you can understand the model and exercise a pipeline before configuring SMTP or creating an account with a third-party provider.

```shell
dotnet add package Transmitly
```

```csharp
using Transmitly;

var communicationsClient = new CommunicationsClientBuilder()
    .AddSimulationSupport()
    .AddPipeline("WelcomeKit", pipeline =>
    {
        pipeline.AddEmail(
            "welcome@my.app".AsIdentityAddress("Welcome"),
            email =>
            {
                email.Subject.AddStringTemplate("Thanks for signing up!");
                email.TextBody.AddStringTemplate("Welcome to the app.");
            });
    })
    .BuildClient();

var result = await communicationsClient.DispatchAsync(
    "WelcomeKit",
    "developer@example.com",
    new { });

Console.WriteLine(result.IsSuccessful);
```

No SMTP server, SendGrid account, Twilio account, or API key is required for this simulated dispatch.

When you're ready to send real communications, add the provider package you want and configure it in the same composition root.

## Project status

Transmitly `0.4.0` is **production-ready and in production use**.

There are additional capabilities and refinements planned before the project declares `1.0`, and that work _may_ include API evolution.

## Feedback wanted

Transmitly is actively looking for feedback from developers.

If you try it and something is confusing, overly abstract, missing, or harder than it should be, please start a [GitHub Discussion](https://github.com/transmitly/transmitly/discussions). Questions, API criticism, onboarding troubles and different use cases are all useful feedback.

A report that says "I stopped here because this didn't make sense" is valuable!

## Learn more

- [Transactional Emails: A long term view](https://dev.to/jeremy_tly/transactional-emails-a-long-term-view-13l6)
- [Sending Transactional Emails with Transmitly (C#)](https://dev.to/jeremy_tly/sending-transactional-emails-with-transmitly-c-ede)
- [Sending SMS with Transmitly (C#)](https://dev.to/jeremy_tly/sending-sms-with-transmitly-c-13m)

# Transmitly

**Open-source transactional communications for .NET**

Transmitly helps teams manage outgoing transactional communications from one place instead of scattering email, SMS, push, and provider-specific code throughout an application.

Model communications as domain intents, define the channels and content once, and change providers as your requirements evolve.

- Start with SMTP and move to API-based providers later
- Keep business logic focused on domain actions instead of delivery details
- Centralize message composition, provider configuration, and delivery handling
- Stay vendor-agnostic across email, SMS, voice, and push notifications

## Why teams use Transmitly

As applications grow, communications usually get more complicated:

- New providers get introduced for deliverability, cost, or feature reasons
- Different channels get added for the same workflow
- Templates and credentials end up spread across multiple services
- Delivery tracking and webhook handling become inconsistent

Transmitly gives you a consistent model built around:

- **Channels**: Email, SMS, Voice, and Push Notifications
- **Channel Providers**: SMTP, SendGrid, Mailgun, Twilio, Infobip, Firebase, and more
- **Pipelines**: Domain-oriented communication flows such as `WelcomeKit`, `PasswordReset`, or `OrderProcessing`

## What Transmitly offers

| Area | What you get |
| --- | --- |
| Core library | [Transmitly](https://github.com/transmitly/transmitly) for building and dispatching transactional communications |
| Email providers | SMTP, SendGrid, Mailgun, Azure Communication Services, Infobip, and related provider/configuration packages |
| SMS and Voice providers | Twilio and Infobip integrations |
| Push notifications | Firebase support |
| ASP.NET integrations | MVC and ASP.NET Core webhook and delivery-report handling |
| Template engines | Scriban and Fluid integrations |
| Content and webhook tooling | Contentful, webhook client/configuration, and related ecosystem packages |

## Ecosystem highlights

- [Core library](https://github.com/transmitly/transmitly)
- [Samples](https://github.com/transmitly/transmitly/tree/main/samples)
- [Wiki](https://github.com/transmitly/transmitly/wiki)
- [Website](https://transmit.ly)
- [SMTP provider](https://github.com/transmitly/transmitly-channel-provider-smtp)
- [SendGrid provider](https://github.com/transmitly/transmitly-channel-provider-sendgrid)
- [Mailgun provider](https://github.com/transmitly/transmitly-channel-provider-mailgun)
- [Twilio provider](https://github.com/transmitly/transmitly-channel-provider-twilio)
- [Infobip provider](https://github.com/transmitly/transmitly-channel-provider-infobip)
- [Firebase provider](https://github.com/transmitly/transmitly-channel-provider-firebase)
- [Dependency Injection extensions](https://github.com/transmitly/transmitly-microsoft-extensions-dependencyinjection)
- [ASP.NET Core MVC integration](https://github.com/transmitly/transmitly-microsoft-aspnetcore-mvc)

## Quick look

```csharp
builder.Services.AddTransmitly(tly => tly
    .AddSmtpSupport(options =>
    {
        options.Host = "smtp.example.com";
        options.Port = 587;
    })
    .AddPipeline("WelcomeKit", pipeline =>
    {
        pipeline.AddEmail("welcome@my.app".AsIdentityAddress("Welcome"), email =>
        {
            email.Subject.AddStringTemplate("Thanks for signing up!");
        });
    }));
```

Your application code can then dispatch `WelcomeKit` without knowing whether the message goes out through SMTP today, SendGrid later, or additional channels in the future.

## Learn more

- [Transactional Emails: A long term view](https://dev.to/jeremy_tly/transactional-emails-a-long-term-view-13l6)
- [Sending Transactional Emails with Transmitly (C#)](https://dev.to/jeremy_tly/sending-transactional-emails-with-transmitly-c-ede)
- [Sending SMS with Transmitly (C#)](https://dev.to/jeremy_tly/sending-sms-with-transmitly-c-13m)

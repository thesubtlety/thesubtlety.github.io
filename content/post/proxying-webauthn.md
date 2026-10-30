---
title: "Ask a Remote Browser to Tap Their YubiKey"
date: 2026-10-30
draft: false
summary: "A browser-extension bridge forwards a site's WebAuthn request to a YubiKey on another computer, and a look at what Okta logged when it did."
description: "A proof-of-concept bridge forwards a login's WebAuthn ceremony to a remote browser and authenticator, as a way to test whether a sign-in policy requires a trusted device or only a successful key tap."
tags: ["webauthn", "red team", "research"]
featured: true
blurb: "Two browser extensions forward a site's WebAuthn request to a YubiKey on another computer. No cookies copied. A way to test whether sign-in policy checks the device or just the key."
---

All bets are off post-exploitation. I was curious how phishing-resistant authenticators could be abused from the browser, and since everything is JavaScript I had GPT build a bridge that lets you sign in to a browser using a remote YubiKey on another computer. Two extensions forward the site's WebAuthn request to the remote browser for tapping and return the signed assertion for authentication.

The local user starts the session creation in the browser, and another user completes authentication on the remote computer. No existing session or cookies need to be copied between them.

This is useful for remote development. And red teams. It also provides a way to test a sign-in policy: does access require a trusted browser or device to receive the session, or only successful authentication with the user's key?

{{< figure src="/img/webauthn/yubitunnel.gif" alt="A requesting browser completing login using a YubiKey connected to the signing computer." caption="The requesting browser starts the login and receives the session. The signing browser handles the YubiKey prompt. The policy event discussed below is separate from this demonstration." >}}

The extension is at [github.com/thesubtlety/webauthn-bridge](https://github.com/thesubtlety/webauthn-bridge).

## The authentication flow

The requesting browser starts the website login and receives the resulting session. The signing browser runs WebAuthn using the available authenticator. The relay transfers encrypted messages between their extensions. These names describe what each component does, regardless of which computer is physically local.

{{< figure src="/img/webauthn/architecture.svg" alt="The requesting computer, a Cloudflare relay, and the signing computer with its YubiKey." caption="The requesting browser and the signing browser, connected through an encrypted relay. The private key never leaves the authenticator." >}}

Chrome provides `webAuthenticationProxy` for remote-desktop software to forward authentication requests. Attaching an extension suspends ordinary WebAuthn handling in that browser until it detaches. The bridge uses this documented capability; request forwarding itself is not an undisclosed browser vulnerability. See [Chrome's API documentation](https://developer.chrome.com/docs/extensions/reference/api/webAuthenticationProxy).

{{< figure src="/img/webauthn/ceremony.svg" alt="A WebAuthn request forwarded from the login tab to a remote authenticator and back." caption="A sign-in request, end to end. The signing extension matches the relying party to a saved rule, opens its configured page, and returns the assertion for the website to verify." >}}

The local requesting extension receives the website's serialized options. The remote signing extension matches the relying-party ID to a profile, opens its configured HTTPS page, and calls `navigator.credentials.get()` from there. The remote browser natively handles credential selection, touch, PIN, and any other required verification.

This implementation checks the page origin and the returned client-data origin against the configured profile. The website legitimately verifies the cryptographic signature using its registered public key and decides whether login succeeds.

The Cloudflare Worker only tunnels the encrypted WebAuthn and management messages over WSS. It does not proxy the website's HTTP session; see [CursedChrome](https://github.com/mandatoryprogrammer/CursedChrome) for more on that. This does not use the Cloudflare Tunnel product.

## Security assumptions

For the attack scenario, we assume the attacker controls both browsers. As mentioned, all bets are off post-exploitation. An attacker able to run privileged extension code may have other ways to act in a user's browser.

This proof of concept demonstrates a specific flow that administrators can use to test whether device, network, and extension controls prevent an unapproved requesting browser from obtaining a session. It does not demonstrate initial compromise or a general bypass of device-bound sessions.

The session starts and completes in the requesting browser, which avoids a device transition caused by copying an authenticated session to another browser. The requesting browser still has an observable IP, location, ASN, and device context. Its initial sign-in can trigger risk signals, and later changes can trigger further evaluation.

## What I observed in Okta

I went through a successful login flow using the forwarded YubiKey authentication through a remote VPS. A contrived version of the sign-on policy evaluation event is below.

```json
{
  "eventType": "policy.evaluate_sign_on",
  "outcome": {"result": "CHALLENGE"},
  "risk": {"level": "MEDIUM", "reasons": ["Anomalous Location","Anomalous Device"]},
  "threatSuspected": "false"
}
```

The system logs are otherwise entirely benign.

{{< figure src="/img/webauthn/okta-logs.png" alt="Okta system log for the forwarded sign-in showing ordinary successful authentication events." caption="The Okta system log for the forwarded sign-in. Nothing is flagged as a threat." >}}

Okta surfaces quite a few data points. See Okta's [event reference](https://developer.okta.com/docs/reference/api/event-types/) and [behavior-log documentation](https://help.okta.com/en-us/Content/Topics/Security/behavior-detection/logs-behavior-detection.htm).

## Defenses

The extension is ordinary but with broad host permissions and URL access, attacker-configurable though. Powerful extensions that can modify all sites or inject scripts should be managed rather than left to the user to install. Chrome enterprise policy can force an allowlist so sideloaded extensions don't allow this scenario easily.

Inspecting existing installations across an environment is a bit trickier. osquery is the main general-purpose option; its `chrome_extensions` table enumerates installed extensions and their permissions. [geiger](https://github.com/puck-security/geiger)'s `--browser` mode is built for this specific risk: it scores each installed extension's permission union, flags the sideloaded and unsigned ones, and for the risky ones grabs the extension's on-disk code for hardcoded remote hosts you can check against egress and DNS logs.

## Further reading

- [CursedChrome](https://github.com/mandatoryprogrammer/CursedChrome) is an extension that turns browsers into HTTP proxies.
- [Unit 42's browser-based RDP implementation](https://unit42.paloaltonetworks.com/webauthn-added-to-browser-based-rdp/) on browser-based RDP and the precomputed client-data hash.
- [Catalano and colleagues' BitM+ paper](https://link.springer.com/article/10.1007/s11416-025-00556-2) combines browser-in-the-middle interaction with reflected XSS on a legitimate origin.

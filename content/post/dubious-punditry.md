---
title: "Dubious Punditry: Red Teams, Threat Actors, and Making It Up As We Go"
date: 2021-12-02T19:50:21-07:00
draft: false
---

**NB**: This is armchair punditry of the highest order, the words of whose ilk are typically dubious. But this is the internet, and here we are.

**"Simulate Real Adversaries"**

There's a persistent idea that red teams should simulate specific threat actors. Replicate their TTPs, mirror their toolchains, basically LARP as APT28 or whichever group is trendy this quarter. The [Diamond Model](https://apps.dtic.mil/sti/pdfs/ADA586960.pdf) gave us a nice framework for analyzing intrusions, and somewhere along the way everyone decided they needed to pretend to be a nation-state.

This is limiting. Some organizations genuinely want to know if they're vulnerable to a specific group's documented tactics. Fair enough. Most don't. And even if they did, threat groups aren't static. Their tactics evolve. You're defending against last year's attacks.

[sixdub asked this back in the day](https://web.archive.org/web/20200422214919/http://sixdub.net/?p=762): are your providers actually "replicating the multifaceted aspects" of an adversary, or checking boxes? Maybe the industry has matured past this in the last five years. Maybe not. Your mileage may vary.

**What These Groups Actually Are**

Let's get real about what we're dealing with.

An APT is, as [the grugq put it](https://medium.com/@thegrugq/cyber-ignore-the-penetration-testers-900e76a49500), "literally the instantiation of a nation state's will." Not a toolchain. Not a malware family. A nation's will, with resources to match.

A red team, consultant or internal, is the instantiation of an organization's desire to answer business risk questions. You get what you pay for. More money and time buys more assurance about your actual risks.

A cybercrime group is the instantiation of some people wanting to get paid, cause harm, or occasionally satisfy a misguided curiosity about what happens when you click the Big Red Button.

**But They're All Just People**

Here's the thing. These are all humans sitting at keyboards. Their resources vary. Their sophistication varies. Their capabilities vary, within each group as much as between them. Yes, elite groups use techniques the lawful side rarely practices. Look at Turla. But compromise happens because people are incentivized to achieve a goal against a victim. The means vary. The underlying mechanism is the same everywhere: abusing the shared technology stacks we all rely on.

**How Work Surfaces (Or Doesn't)**

How we learn about any of this depends entirely on who did it.

Red teams have career incentives to publish. Research leads to visibility, which leads to better jobs and higher earnings, plus visibility for the employer. Side effect: the same vuln gets "independently discovered" by multiple teams. There are multiple cases of this.

Cybercriminals don't publish quarterly reports. We see their work through AV and EDR telemetry, honeypots, VirusTotal uploads, malware analysis, threat intel reports, the occasional leak.

APT groups don't publish either, but we've had some spectacular leaks: Shadow Brokers dumping NSA toolkits, Vault7 revealing CIA capabilities. Curiously, no equivalent public release of Russian, Chinese, or Israeli toolkits. Make of that what you will.

**The Dual-Use Conversation We Keep Having**

Every time one of these toolkits leaks, observers clutch their pearls about overstep and the dangers of dual-use technology. Cue Wassenaar, arms control, policy, politics, and whether we should regulate vulnerability research. Inevitably it drifts toward censorship.

But these capabilities are inevitable. You can't un-invent knowledge. The cat's out of the bag and it's never going back in.

**So What's a Red Team Actually For?**

Strip away the threat actor simulation theater and this is just another definition, but here's mine. A red team exists to:

- Challenge assumptions
- Test systems and controls
- Test blue team response
- Find systemic risk so you know where to focus defense spending

You're not an APT, no matter how cool your custom C2 framework is. Stop pretending red teams are perfect threat actor simulators. Treat them as business risk assessment tools.

---
title: "Owning the Attack Path"
slug: owning-the-attack-path
aliases: ["/post/after-red-teaming/"]
date: 2026-10-02
draft: false
summary: "Every finding is closed, and the attack still works. Closing tickets fixes the pieces of an attack path, but someone has to own the path itself."
description: "Why closed findings don't prove an attack is harder, and what it takes to build evidence that your most important security assumptions are still true."
tags: ["red team", "adversarial engineering", "security engineering"]
featured: true
blurb: "Five findings, five tickets, all closed. Can a compromised employee still reach customer data? The tickets can't say. Adversarial engineering is the work of finding out, and keeping the answer current."
---

*This essay picks up a thread from* [The Effective Red Team](https://nostarch.com/effective-red-team)*, forthcoming in January 2027, and from* [Beyond the Security Organization](/post/beyond-the-security-organization/)*. The first is largely about building a red-team function that turns successful attacks into better security. The second asks how security organizations turn what their specialists learn into lasting change. This is a narrower question: what happens after we get good at proving ourselves wrong?*

The red team didn't _hack_ the super user admin portal. It didn't need to.

## One attack path

Imagine a SaaS company that has spent years making sure one customer cannot access another customer's data. Its authentication and authorization boundaries have been tested repeatedly. The engineering is sound.

Then a red team obtains access to customer data across tenants without exploiting any of those protections.

That attack probably begins with an employee account. The team manipulates an account recovery process, establishes access to the employee's environment, and works its way to a support platform.

That platform has legitimate reasons to access customer data. Support engineers investigate problems, inspect customer environments, and sometimes need privileges ordinary users don't have. But those privileges reach much further than any single support case requires. With the right internal identity, the attacker can retrieve data across customers.

The product's carefully engineered security boundaries aren't relevant since the attacker walks around them using a system the company built to help customers.

## What the tickets say

The red team achieves its goals, and the report produces a familiar set of findings: weak account recovery, excessive internal access, overly broad support permissions, insufficient monitoring.

Now each finding goes to the appropriate team: Account recovery is hardened. Network paths are restricted. Permissions are narrowed. Detection improves. Eventually, every ticket is closed.

{{< figure src="/img/adversarial-engineering/findings-to-tickets.svg" alt="One attack path from an employee account to every customer's data, split into four findings, each fixed and closed separately. The question of whether one compromised employee can still reach every customer's data is left open." caption="The usual flow. One attack path becomes four findings and four closed tickets. No one is assigned to check whether the outcome is still possible." >}}

The company knows the specific weaknesses were addressed, and that the original attack probably no longer works. But can a compromised employee still get broad customer-data access through the company's internal admin systems? That's a different question, and the closed tickets don't answer it.

The individual fixes may all be correct. The company can still lose the same data through another internal path, such as the next support tool, another privileged identity, or a different route to the same authority.

## The findings share one attack path

When we turn an attack into findings, we stop looking at the path as a whole. The attacker followed one path through several systems to one consequence. The organization splits that path into problems for different owners. That's necessary for engineering work, but each team then owns a part and no one owns the path.

Consider what had to be true for the attack to work. An employee identity was compromised. It gave useful access to the internal environment. From there, the attacker reached a support capability, got enough authority to use it, and used it across customers with no legitimate support need.

At no point did the attacker need to abuse some appsec vulnerability. Each system did what it was built to do. Account recovery restored access. Internal connectivity let employees reach internal tools. The support platform gave customer data to authorized support staff. The problem was how those functions combined.

### Start from a security requirement

One response is to fix the account recovery weakness, restrict the network route, and remove the excess permission. Those are sensible changes, and they may eliminate the demonstrated path.

The other response starts by writing down a security requirement that must stay true even when some defenses fail:

**Compromising an ordinary employee should not be sufficient to obtain broad customer-data access.**

That leads to different engineering questions. Should workforce access automatically reach privileged support tools? Should support access be scoped to one customer and one case, with explicit elevation for exceptions? Should bulk exports need a separate authority that the same compromised identity can't obtain?

And if some support authority will eventually be compromised, how much can one session do, how much data can it collect, and how quickly can suspicious use be detected and stopped?

Some of these controls stop the attacker from reaching a capability. Some limit what the capability can do. Some create a chance to intervene before the damage. No single step has to become impossible. The company needs enough independent boundaries that a plausible compromise can't easily escalate into an unacceptable outcome.

{{< figure src="/img/adversarial-engineering/path-to-boundaries.svg" alt="The same attack path, now leading to one security requirement, three independent boundaries that support it (prevent, contain, detect), and continuous tests that check it." caption="Starting from the outcome. The path leads to one security requirement, several independent boundaries that support it, and continuous tests that check it." >}}

This changes how remediation is judged. Instead of asking whether the excessive-permissions ticket is closed, ask whether someone with a compromised support identity can still retrieve data unrelated to a legitimate support case. That can be tested.

## The red team can succeed while security improves

Keeping the whole attack path matters for another reason: a red team can keep succeeding while security measurably improves.

Suppose that a year later the red team gets the same objective. This time, phishing-resistant authentication blunts common identity attacks, endpoint controls catch public tooling, segmentation removes the convenient routes, and support access reaches far fewer customers. The operators adapt with custom tooling, a less obvious identity path, and a particular administrative workflow, and eventually get in. The report still says the objective was achieved.

It's tempting to read that as failure. But the second operation needed a much more capable attacker, took longer, depended on more fragile assumptions, and gave defenders several chances to contain it. An attacker who could easily repeat the first operation may be unable to repeat the second.

The more useful question is what the defenses required the attacker to do differently. Longer paths aren't automatically safer. An extra step that takes seconds adds little, and a detection that fires after the export doesn't prevent the loss. What matters is whether a control changed the capability, access, time, or chances to intervene that the attack required. If the report only records the outcome, both operations look the same, even though the second company is much harder to attack.

## A fix doesn't mean you're now safe

Return to the support platform. Engineering has changed the authorization model. Support staff now get narrower privileges, and access to customer data is tied to an approved support case. The work is deployed. The ticket is closed.

We have good reason to believe the implementation changed. We don't yet know whether the security requirement is actually met.

### Test the boundary directly

Now we start with a support identity. Can it retrieve data for a customer with no associated support case? Can it extend its access beyond the approved task? Switch customers without new authorization? Turn access to individual records into bulk collection? What happens if a legitimate support engineer's session is controlled by an attacker?

These tests don't need weeks of phishing, endpoint compromise, and internal movement. Those stages were already explored. The open question is the support platform's authorization boundary, so test it there.

Perhaps the direct access attempt fails. That's good to know!

But suppose another internal application uses the same privileged service account. Or an export workflow runs through a separate API that doesn't enforce the new restrictions. The original route is gone, but the ability to reach the same data still exists.

### Look for the same condition elsewhere

Checking whether a particular exploit still works is only part of remediation. We also need to examine the conditions that made the exploit consequential. Here, the dangerous condition wasn't one overly broad permission. It was internal capabilities that could exercise cross-customer authority without constraining the reason, scope, and duration of that access. The same condition might exist in impersonation tooling, debugging services, administrative consoles, internal reporting systems, or emergency-access mechanisms.

Finding those variants tells the company whether it fixed the boundary or only one route through it.

### Keep the tests

If the support authorization boundary matters, some tests should survive the engagement: access without a valid case, attempts to exceed a case's scope, broad exports from privileged sessions. Some questions will still need human judgment or more elaborate adversarial testing. But the organization shouldn't need another full red-team engagement to rediscover a condition it already understands.

A passing test doesn't prove that no attack path exists. It shows that one defined property held under defined conditions.

## The same pattern in CI

The same reasoning applies well beyond support tooling.

A developer can submit queries to a CI system. A query-processing feature embeds developer-controlled input into a script without handling it safely, so crafted input can change what the script executes. That's a straightforward injection vulnerability. But the worker running the script can also read a long-lived, broadly authorized credential used by the build infrastructure. An input-handling bug has become a path from ordinary developer access to control of the build environment, and possibly to a software release.

The team fixes the script, adds a regression test, and closes the finding. This is good work. It eliminates a real vulnerability. But if another flaw gives an attacker control of the worker, the credential is still there. The injection is fixed, but an untrusted workload still has access to privileged build authority.

Following the attack past the initial bug raises different engineering questions. Why could the worker read unrelated sensitive files? Why did the build environment need a broadly privileged credential? Could it be replaced by short-lived authority scoped to the job? Why was one secret enough to affect release integrity?

Each answer supports a different test at a different boundary. Repeating the original injection after the fix establishes something narrow and useful. Starting from a compromised worker and mapping what it can reach establishes something else. Following its authority all the way to release integrity establishes whether the original business consequence is still plausible. Then ask where else the condition exists: other workers with similar credentials, adjacent build services with comparable authority, release systems that accept credentials not bound to a particular job or approval.

A red-team operation can find a surprising path through several systems. Most of its value comes from learning which connections between those systems made the path possible.

## Someone needs to own the attack path

The support-platform attack crosses identity, endpoint, internal infrastructure, product engineering, and detection. The CI attack crosses application security, developer infrastructure, identity, secrets management, and release engineering. Each team owns a legitimate piece of the problem.

An identity team can correctly report that account recovery has been hardened. A product team can correctly report that support permissions work as designed. An infrastructure team can correctly report that internal connectivity follows approved policy. All three statements may be true while a compromised identity still provides a practical route to broad customer-data access.

Someone needs to remain responsible for the larger question. Not for implementing every fix, which would weaken the existing engineering ownership model, but for understanding the path across teams, identifying the conditions worth changing, coordinating the evidence needed after changes, and revisiting the conclusion when circumstances change.

**This is the work of adversarial engineering.**

A red team explores whether a consequential outcome is achievable. An adversarial engineering function takes what that exploration reveals and asks what the organization can now reasonably depend on. Sometimes that means another red-team operation. Sometimes it's a focused test that starts halfway through a known attack path, a repeatable authorization test, a review of privileged identities, or an exercise that checks whether defenders can interrupt a known sequence. And sometimes the most valuable work is with engineering, changing an architecture so an entire class of paths no longer depends on a fragile configuration or an easily misused credential.

That takes enough offensive depth to separate the exploit from the conditions that made it important, challenge a claimed fix, and recognize equivalent paths. The function keeps track of important questions after individual engagements, incidents, and remediation projects end. For the customer-data example, the question is whether compromise of an ordinary workforce identity can lead to unauthorized access across customers. For the CI example, it's whether compromise of a developer workload can result in an unauthorized production release.

Those questions stay stable while products, architectures, and vulnerabilities change. They also expose uncertainty that individual findings conceal. Engineering may have fixed several weaknesses while the honest answer is still that no one has demonstrated the larger outcome is materially harder. That's a useful thing to report, and it shouldn't embarrass anyone.

## Changes can make old test results out of date

Suppose the company finally establishes that its support access is sufficiently constrained. Ordinary employee credentials can't obtain broad customer-data access. Support sessions enforce customer and case boundaries. Bulk exports require separate authorization. The company has earned some confidence.

Then the support organization adopts a new platform with a powerful search capability across systems, backed by an internal service identity. The platform passes security review. Its authentication works. Nothing resembles the original findings. But the service identity can retrieve data across customers, and an administrative feature makes that authority available to more people than the original design allowed.

The company has recreated a consequential part of the old attack path without reintroducing any of the original vulnerabilities. Security knowledge can go out of date even when nothing has been broken. The earlier testing wasn't worthless. It was good evidence for the system that existed at the time.

So this function needs to know what each conclusion depends on, and re-test when those things change. That doesn't mean a full red-team operation for every change. A precise requirement tells you where to look.

A conclusion should be able to move from *we have good reason to believe this boundary works* to *the conditions have changed, and we don't know yet* without an incident forcing it. Until more testing is done, "unknown" is often the most accurate answer.

## What better looks like

It's tempting to formalize all of this. Every system gets assertions, every assertion gets controls, and every control gets an owner, a status, and a dashboard. That gives the organization a detailed way to express confidence without better evidence. A more useful start is a few outcomes the business can't afford to misunderstand. For a SaaS company, these might be broad unauthorized access to customer data, unauthorized production releases, and destruction of production along with the systems needed to recover. For each, write down the security requirement, what it depends on, the current evidence, which changes should trigger a re-test, and whether it's verified today. For the two examples in this post:

<div style="overflow-x:auto">

| Security requirement | Depends on | Current evidence | Re-test when | Status |
|---|---|---|---|---|
| A compromised employee can't reach broad customer data | How support access is granted; service identities that can read across customers | Case-scoped access tests pass; bulk export needs a separate grant | New support tool, new service identity, vendor gets admin access | **Unknown.** New search platform uses a cross-customer service identity |
| A compromised build worker can't change a production release | Job-scoped credentials; release authority kept separate from build | Worker-reach test; release needs an approval-bound credential | New CI platform, new features, material changes | Verified |

</div>

AI agents make this table much cheaper to keep current. An agent can watch the changes a requirement depends on, such as new service identities, IAM and identity provider changes, OAuth grants, and vendor access. When one lands, it can set the row to unknown, attach the change, and rerun the targeted test: start from a support identity and try to read another customer's data. The change that flips a row is also increasingly an agent. A department builds its own assistant on an older model, gives it a service account that can search customer data, and never sends it through review. No red-team finding will mention it, but it changes the answer for the first row.

Incentives need to reward honest answers. Teams rewarded for closing findings optimize for closure. An adversarial engineering function rewarded for the percentage of claims marked satisfactory drifts toward easy claims and weak evidence. Red teams rewarded for vulnerability counts have little reason to investigate the causes of a few serious failures. Risk acceptance used to hide hard questions produces a record of decisions without a picture of what the company can withstand.

A mature adversarial program may still compromise the company. That's part of the reason to have a red team. The question is what the organization can say afterward.

After the customer-data operation, can it explain which access paths were removed, which boundaries were strengthened, and how much authority a compromised support identity can now exercise? Has it examined adjacent support and administrative tools for the same condition? Does it know which system changes would invalidate that evidence? After the CI operation, can it explain why a compromised worker no longer provides a route to release authority? And if the red team returns to those objectives next year, will it have to discover a genuinely different route, or just another instance of the same underlying problem?

Answering these questions is harder than assigning findings and closing tickets. It takes cooperation across teams, specialized testing, and engineering effort, and some questions will stay open for months. That's still more useful than treating completed remediation as established security.

The alternative is familiar. A red team demonstrates a dangerous path. The organization fixes the visible weaknesses. A year or two later, another red team, researcher, or incident responder finds a different route through the same underlying conditions. Everyone did their assigned work. The organization learned the same lesson twice.

Adversarial engineering won't produce certainty. It can give the organization specific reasons to believe the failures it cares about have become harder to cause, with evidence that can be challenged and updated.

The point isn't to make the red team stop winning. It's to make sure the company stops losing for the same reasons.

<aside class="author-note"><span class="note-label">Author’s note on AI assistance</span><p>An LLM helped structure, pressure-test, and revise this. The opinions, and any errors, are mine.</p></aside>

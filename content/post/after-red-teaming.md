---
title: "After Red Teaming: What an Adversarial Assurance Function Actually Buys You"
date: 2026-10-02
draft: false
summary: "Red teams are good at proving security assumptions wrong. The next step is building the evidence that tells you which important assumptions are holding, and why."
description: "What happens after an organization gets good at proving itself wrong: following attack paths across teams, proving fixes mattered, and keeping the important questions current."
tags: ["red team", "assurance", "security engineering"]
featured: true
blurb: "Red teams prove assumptions wrong. Adversarial assurance is the harder work after: what had to be true for the path to work, prove the fix mattered, and reopen the question when the company changes."
---

*This essay picks up a thread from* [The Effective Red Team](https://nostarch.com/effective-red-team)*, forthcoming in January 2027, and from* [Beyond the Security Organization](/post/beyond-the-security-organization/)*. The first is largely about building a red-team function that turns successful attacks into better security. The second asks how security organizations turn what their specialists learn into lasting change. This is a narrower question: what happens after we get good at proving ourselves wrong?*

Red teams have spent decades proving security organizations wrong. That's the job, after all.

Give a capable team an important objective and enough freedom and it will work around endpoint controls, abuse identity, cross cloud and network boundaries, build custom capabilities when ordinary tooling stops working, and find combinations of legitimate systems nobody expected to connect.

Good red teams have become very good at proving that something the organization believed was safe was, in fact, not.

It is time to get equally serious about proving the things we depend on are *right*.

Not "proving that the company is secure". A failed attack cannot establish that.

But building enough current evidence that the organization can reasonably rely on its most important security assumptions, understand why it relies on them, and notice when that evidence stops being good enough.

That's what I mean by adversarial assurance.

## Start with the bad day

Security people naturally start with systems. But leadership usually experiences security through consequences.

Customer data leaving the company means explaining what happened to customers, answering uncomfortable questions from regulators and analysts, watching sales teams manage trust problems, dealing with public scrutiny, and sitting through meetings nobody wants to attend.

A destructive cloud compromise means the business may stop operating, at least temporarily.

At a hardware product company, compromise of some digital system may have life-safety impacts, trucks and equipment may not move, or operators lose confidence in the systems used to manage a physical fleet.

A malicious software release means customers may run code the company did not intend to ship.

Those are things that really matter.

Experienced red teams usually already have a rough map of them, and that comes from years of scoping conversations with leaders, engineers, product teams, incident responders, and business owners. We call them crown jewels and of course the name matters less than knowing which outcomes would cause real pain.

You might call those outcomes "security claims."

A SaaS company might ask:

> **Can compromise of a standard workforce identity turn into broad access to customer data through our internal support or administrative systems?**

A software company might ask:

> **Can compromise of a normal developer workstation turn into an unauthorized production release?**

A company with a large physical operation might ask:

> **Can compromise of an ordinary employee, contractor, or vendor path turn into broad authority over the systems required to operate the fleet safely?**

A company worried about destructive attacks might ask:

> **Can one compromised administrative path destroy both production and our ability to recover it?**

These questions come from the business, the product, the threat, and what the company has already learned.

## The attack paths get longer

While red teams are so often successful at achieving their goals, mature security programs do actually improve things.

Years of offensive testing, incidents, vulnerability research, secure development work, and architecture changes remove the easy paths.

- Developers lose direct production access
- Phishing-resistant authentication closes common identity routes
- Endpoint controls burn commodity tradecraft quickly
- Cloud permissions get narrower
- Network paths disappear

But attackers still have their objectives, so the routes change and the interesting paths begin to contain more nodes across identity, endpoint, internal access, cloud, developer infrastructure, support tooling, administrative capabilities, and eventually the impact the attacker actually wanted.

That is absolutely progress, even when the operation still succeeds.

Consider customer data. The red team social engineers an account recovery process and takes over an employee identity. That identity gets them onto a usable endpoint. From there they reach internal systems and eventually a support platform.

Nothing is wrong with the product's tenant isolation architecture or code, exactly. Instead, the support platform legitimately has powerful customer access because it was designed to help support engineers solve customer problems quickly.

And the attacker discovers that the access is much broader than a single customer or case. So a compromised employee provides broad customer-data access without ever breaking the security boundary everyone spent years hardening in the product.

Individual systems are behaving exactly as designed. But the overall path is still unacceptable and leadership will be experiencing security through those consequences.

This is increasingly what mature security work looks like.

## Don't stop at the finding

The obvious response is to turn the path into findings.

- "Weak account recovery"
- "Too much internal reach"
- "Overly broad support permissions"
- "A credential problem"
- "Lack of monitoring"

Sure, each of those may deserve a ticket. But once the path is broken into tickets, it becomes easy to lose what the attacker or red team just taught you.

I think there's a simpler way to reason about it.

> **What had to be true for this attack to work, and which of those things should we change?**

For the customer-data path, the attacker had to obtain a workforce identity, that identity had to be useful internally, the attacker had to reach the support environment, they had to obtain meaningful support authority, that authority had to provide access outside a legitimate support need.

And nothing along the path made the attack hard enough, narrow enough, or short-lived enough to prevent the bad outcome.

Now, not every step needs to become impossible; support teams still need to support the customer with powerful access.

But the company may deliberately assume that workforce identities will eventually be compromised. And so the useful question is which parts of the path should be different.

Maybe ordinary workforce compromise should not be sufficient to obtain privileged support authority. Maybe customer access should be tied much more tightly to the customer and task being worked. Maybe unusually broad access should require stronger approval or search should be restricted. Maybe a single support session should not be able to turn troubleshooting into broader collection.

Those are much more useful engineering goals than "fix excessive support permissions."

## Ask what the control did to the attack

Security programs often reduce controls to pass or fail.

- EDR blocked the red team or it did not
- Segmentation stopped lateral movement or it did not
- MFA prevented takeover or it did not

But real attacks are more useful than that. Suppose a year ago a red team could operate on employee endpoints using ordinary public tools and frameworks. After a year of detection and response engineering, those approaches are now blocked or quickly produce indicators that lead defenders to contain the host.

So the red team responds by building a custom capability that avoids those known signals and that custom capability works, allowing them to maliciously operate on endpoints to achieve their goals.

It's easy to look only at the final result and say the endpoint controls failed again. But the path now requires a more capable attacker, more engineering, better tooling, and more effort. An actor without that capability is more likely to expose known indicators and get contained before reaching the next stage. The cost has increased. That is valuable evidence.

And the same is true elsewhere. Segmentation may not make the target completely unreachable. It may add two pivots and force the attacker into a credential path defenders understand well.

Support access may not disappear. It may become narrow enough that one account cannot turn targeted support work into bulk customer-data access.

A recovery mechanism may not stop production destruction. It may keep the recovery environment independent enough that the attacker cannot destroy both.

So don't just ask:

> Did the control work?

Ask:

> **What did the control actually do to the attack?**

- Did it stop the path?
- Force another route?
- Require a stronger attacker?
- Add meaningful time or effort?
- Reduce the blast radius?
- Make common approaches easier to spot?
- Give defenders a realistic chance to contain the attack before the next important step?
- Or make almost no difference?

This is especially useful when the same objective is tested over time.

If the operator once moved from an employee account to customer data in a few simple steps, and several years later the same objective requires custom capabilities, multiple pivots, stronger access, and a much narrower set of viable paths, the company learned something even if both operations ultimately succeeded.

Security improvements rarely make outcomes impossible, but they often make the bad outcome materially harder.

## Follow the path far enough

Consider another example. Say a developer is allowed to submit query jobs through a CI system. The tool takes part of the developer's query and inserts it into a script without safely handling quotes.

That lets the developer break out of the intended command and read sensitive files from the worker. One of those files contains a highly privileged build credential. A relatively small bug in a query feature has now become a path to much greater authority.

It is easy to write:

> Script injection in CI query job.

Or:

> Privileged build token exposed on worker.

Sure, both of those things are true. But neither gets you very far. Instead, ask what had to be true for the whole path to work.

1. Developer-controlled input reached script execution
2. The worker could read files unrelated to the job
3. Sensitive credentials were present there
4. One credential had extraordinary authority
5. Simple possession of the credential was sufficient to exercise that authority

Now ask:

> **Which of those things should not have been possible?**

The unsafe quoting obviously needs to be fixed. But perhaps the job should never have been able to read unrelated secrets. Perhaps a super-admin credential should never have existed on that worker. Maybe the worker should have received short-lived authority for one task instead of a reusable token. Perhaps one CI worker should never have been able to cross from running a developer job into administration of the build system.

There doesn't have to be one perfect answer: complex systems rarely fail for one clean reason, and the point is not to produce a flawless theory of the incident.

The point is to understand the path well enough to choose changes that remove more than the exact trick the operator happened to use.

## Then prove the changes mattered

This is where a surprising amount of security work stops too early. Say the script interpolation bug is fixed, the ticket is closed.

Now you test again. Can developer-controlled input still escape the intended query? If another execution path exists, can the job still read sensitive files? If it can read files, is there still a powerful reusable credential to steal? If the worker itself is compromised, what authority does its identity have? Can that authority alter a release?

Maybe the exact injection is gone. Excellent.

Maybe another execution trick remains, but worker isolation makes the useful secrets unreachable. That's meaningful.

Maybe sensitive files are still reachable, but the reusable super-admin token has been removed and the worker now has narrowly scoped, short-lived authority. The important path has changed.

So a useful closure question is:

> **Did we make the important outcome materially harder or make this path disappear, and can we demonstrate that?**

Without the demonstration, "fixed" usually means someone changed something, but with it, you have evidence.

## Assurance begins when you widen the view

Showing that the original path changed is not the end of the work. It is where (adversarial) assurance begins. For example:

- If one CI worker contained a powerful reusable token, where else do build workers hold credentials like it?
- Where else can developer-controlled input reach privileged execution?
- Which shared build components can affect artifacts after the point where everyone thinks code review is complete?
- How did credentials with that much authority become normal in this environment?
- What would keep the next CI platform from recreating the same situation?
- And which of these things can the organization keep checking so a red team does not need to rediscover the same condition two years from now?

The same applies to the support example.

- If one support platform allowed broad customer access, where else do employees have similar capabilities?
- Which emergency, debugging, impersonation, export, or administrative tools have equivalent reach?
- How are those capabilities granted?
- How is their use bounded?
- What happens when a new support platform is introduced?

This is where a number of security practices that are difficult to sustain in isolation suddenly have a reason to exist. Variant analysis. Security architecture work. Attack-path analysis. Focused adversarial testing. Security engineering. Regression tests. Detection tests when detection is relevant. Retesting. Continuous checks.

A specific attack teaches the organization something important. But trying to understand where else the same condition exists, why it exists, how to change it, and how to know whether it comes back gives you adversarial assurance that you can rely on your security assumptions.

## Adversarial work and assurance work are different

A quick note on definitions. They overlap technically, but they are trying to answer different questions.

Adversarial work asks:

> **Can I achieve the outcome?**

Its value is exploration. A red team models attackers, develops capabilities, works around controls, combines systems in unexpected ways, and discovers paths the organization did not know existed. That may require weeks of work and deep tradecraft.

Assurance work asks:

> **Given what we have learned, what do we have good reason to believe now?**

And when the answer is not good enough, it goes and gets better evidence. That might mean asking the red team for another operation. It might mean performing a short focused test inside the assurance function. It might mean having product security test an authorization boundary. It might mean replaying one piece of an attack with the system owner. It might mean examining a permission graph or proving that a privileged identity can no longer reach a target. It might mean generating the actions necessary to see what telemetry exists. It might mean asking architecture whether a whole class of paths is still possible.

A useful way to divide the technical work is:

- **Exploration:** Find a path we do not know about.
- **Focused testing:** We know exactly what we are uncertain about. Let's go find out.
- **Repeatable testing:** We understand how this failed. Let's make it cheap to know if it happens again.

A nascent assurance function may depend heavily on the red team for the first two. Over time, it can build enough technical capability to handle much of the focused and repeatable work itself.

## Test the question you actually have

And not every assurance test should recreate the entire attack. If the helpdesk path has already been proven and the unanswered question is whether support access is properly scoped, start with a support identity.

If the question is whether a build worker can cross into release authority, there is very little reason to spend a week phishing a developer first.

If the question is authorization, stealth may not matter. If the question is containment, it may matter a lot. Detection and response are important when they are part of the path you are trying to understand. They are not mandatory boxes every test has to check.

The test should answer the uncertainty relevant to the question. Yes, that sounds obvious. But in large security programs, it is surprisingly easy to forget as we collectively lose the forest for the trees.

## Someone has to keep the whole path in view

Attack paths routinely cross organizational boundaries: identity owns one piece, endpoint owns another, cloud owns another, product engineering owns the support platform, developer infrastructure owns CI, detection may own some of the telemetry. No individual team has the whole picture.

And every team can be locally correct while the overall outcome remains unacceptable, since identity may accurately say that its system behaved as designed. Support may accurately say the operator had a legitimate permission. The product team may accurately say authentication and authorization controls worked perfectly. Together, those facts can still describe broad customer-data loss as proved out by an adversarial operation.

Someone has to keep the larger question alive and that is not passive knowledge management. It means talking with the teams involved, understanding what the systems really do, and following the important parts of incidents and red team operations beyond the report. It means breaking long attack paths into questions that can actually be tested, doing some of that testing, finding the right specialists for the parts that require deeper work, working with engineers to decide what would count as convincing proof after a fix.

And sometimes saying:

> We changed several things, but we still have not shown that the outcome is meaningfully harder.

The person or function responsible for that question should not be rewarded for keeping everything green. Its job is to keep the picture honest.

## Big changes should reopen old questions

Security knowledge goes stale when the company changes. Things are going stale constantly in a complex environment:

- An acquisition joins
- A major vendor receives privileged access
- A new product launches
- Support moves to a different platform
- The company changes identity providers
- A cloud environment is reorganized
- A vehicle or logistics platform gets a new remote-management capability
- An AI agent receives authority that used to belong only to a person

Any of these can make old evidence less useful without introducing a traditional vulnerability. Suppose a company has spent years making it difficult for a compromised developer to affect a release. Then it acquires a business whose engineers use a separate CI system, keep long-lived signing credentials, and deploy through a different process.

The useful question is not:

> Did we complete the acquisition security assessment?

It's:

> **Does what we believe about release integrity still stand now that this company is inside the release system?**

Or suppose a logistics company brings in a vendor that can remotely administer a large part of its fleet. The vendor may have passed every required security review.

But the important question is:

> **Did we just create a new path from one vendor identity to an operational outcome we previously believed required several independent compromises?**

A new product, vendor, acquisition, or architecture should be able to change the answer from:

> We have good reason to believe this holds.

to:

> We do not know anymore.

That's not *failure*. It is what an honest adversarial assurance function is supposed to notice, test, and support.

## Keep the important questions small in number

This should not become another enterprise catalog with hundreds of statements and thousands of controls underneath them. Simply start with a handful of outcomes that really matter. Usually the red team already knows what many of them are.

For each one, the organization should be able to answer roughly:

- What bad outcome are we trying to prevent?
- What attackers or starting positions actually matter?
- What paths have we already seen?
- What made those paths work?
- What did our controls actually do to them?
- What changed afterward?
- Can we show that the change mattered?
- Where else might the same problem exist?
- What are we still unsure about?
- What has changed since we last looked?
- And what should cause us to look again?

That's more than enough structure. The purpose is not to build the perfect model of the company. The purpose is to make better decisions about *where uncertainty matters*.

## What the function actually does

Week to week, the work is more active than maintaining that register.

Say a product team is building a new support capability. Someone sits with them and works out what authority it creates and what existing assumptions it might weaken.

An incident exposes an unexpected identity path. Someone traces how far that starting position could have gone and whether an important question should be reopened.

A red team proves a route to some crown jewel. Someone follows the route across teams, helps identify what made it possible, and works out which parts deserve broader engineering.

A fix is deployed. Someone proves that the relevant part of the attack changed.

A new acquisition is considered. Someone asks which existing security assumptions should now be treated as unknown or challenged.

Threat intelligence shows that a realistic attacker has acquired a capability that used to require far more skill. Someone asks whether an old conclusion still means what it used to mean.

The function is constantly taking in business priorities, product and architecture changes, threat information, incidents, red-team operations, vulnerability research, new vendors and acquisitions, and the evidence already produced by engineering and defensive teams.

Its outputs are not primarily reports. They are better questions, targeted tests, engineering work, proof after remediation, broader searches for the same condition, reusable checks, and an honest view of what the organization still does not know.

## Be careful what you reward

This kind of function is easy to ruin with incentives.

If system owners are rewarded for keeping their area green, they will naturally argue that findings are narrow exceptions. They should instead have reason to make their part of important attack paths demonstrably stronger.

If the people keeping the larger questions are rewarded for the percentage that look "good," the questions will slowly become easier and less useful. They should be rewarded for keeping them current, appropriately scoped, and honest about uncertainty.

If risk owners can make uncomfortable problems disappear by accepting them, acceptance will become the garbage chute. Accepting risk should mean somebody understands the consequence and consciously owns the decision, regularly.

If red teams are rewarded for the number of findings or successful compromises, they will optimize for finding and compromise counts. Their value is in reducing important uncertainty and creating changes that matter.

The goal is an accurate picture that leads to useful action, not a green dashboard.

## What better looks like

At first, this can be very small. A red team runs an important operation. Somebody follows the consequential parts beyond ticket closure and the teams involved ask what made the path possible. Related cases get investigated, the important fixes get tested, and the same objective gets revisited later.

The first sign of progress is:

> **The learning survives beyond the engagement.**

A more developed function starts turning important uncertainty into targeted work.

- A support platform changes, so somebody tests the customer-access boundary
- A build migration changes the release path, so somebody challenges it
- An acquisition introduces a new identity system, so somebody traces what that identity can reach
- A known attack path becomes harder, so the organization can explain what capabilities an attacker now needs and which actors are likely to fail earlier

The next sign of progress is:

> **The organization goes looking for answers before the next full red-team operation happens to find them.**

At the more advanced end, offensive discoveries routinely become lasting engineering: the exact bug is fixed, related instances are found, the system that allowed the class is improved where practical. That fix is demonstrated, useful tests remain behind, and major changes cause old conclusions to be revisited.

And red-team time shifts toward the questions the organization genuinely does not know how to answer.

The sign of this progress is:

> **Confidence changes when reality changes.**

## What it buys you

This takes more time than simply writing findings.

Following one attack path across six teams takes more effort than handing each team a ticket. Looking for related cases takes time. Proving the fix takes technical time and work. Repeatable tests need maintenance. Some problems cannot be eliminated and will instead become explicit tradeoffs.

Sometimes months of engineering end with the uncomfortable conclusion that the company still cannot confidently answer an important question.

But the alternative is familiar: a red team finds a path, the organization fixes the visible pieces, the report is filed. And two years later another red team, researcher, or incident responder discovers the same underlying condition through a different system.

Adversarial assurance is an attempt to break that cycle.

Use the red team's ability to prove the organization wrong. Then do the harder work. Understand what made the path possible. Make the important outcome materially harder or remove the path. Demonstrate that the changes actually mattered. Look for the same condition elsewhere. Understand how it became common. Change the broader system where that is practical. Keep checking the pieces that are cheap enough to check. And revisit the larger question when the company, the product, or the threat changes.

Red teams have spent decades getting better at proving that security assumptions are wrong.

Yes, they should keep doing it.

But the next step is to get equally serious about building the evidence that tells us which important assumptions are holding now, what it took to make them hold, and why we still have reason to believe them.

<aside class="author-note"><span class="note-label">Author’s note on AI assistance</span><p>An LLM helped structure, pressure-test, and revise this. The opinions, and any errors, are mine.</p></aside>

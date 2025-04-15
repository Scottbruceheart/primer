# Writing Software For Two Billion People 

## Human Population Scale Software

There are over 7 billion living humans, and there are over 9 billion user-focused computing devices.  Many, possibly most, software engineers are engaged in writing software that is useful to a material fraction (500 million or more) of those users and computing devices. Writing software at this scale has a very large number of very non-obvious challenges and implications.  We're are going to discuss the ones related to the concept that 'outlier has a very different meaning with a two billion sized user base'.

## Outliers Dominate User Experience

Intuitively, an 'outlier' is something we don't encounter very often. One person's experience of 'not very often' has very little to do with the 'infrequent' when there are millions to billions of users of some software.  Using a high capability device might result in software execution with nearly no glitches. Using a low capability device might result in software that breaks constantly. Any individual engineer, tester, or client is unlikely to directly experience a bad problem.[^1]

## Measuring p99 Is Barely Enough

The intuitive concept of outliers turns out to be wildly insufficient for planet scale software. It is completely possible to write software that you and your team use for months that appears perfectly performant, but after launch catastrohpically fails on a regular basis, capping how fast you can gain users and how many users you can gain overall.  Measuring the 99th percentile of bug break and latency per application action is barely enough to ensure your product is baseline usable, as heavy users will interact with your product hundreds of times a day.[^2]

## The Grim Mathematics of Planet Scale

With two billion active users of some piece of software, making the two billionth user even nominally happy is very difficult. Two billion users means two billion computing devices. Rank all computing devices by 'capability', from 1 to ~15 billion.  In the best case, our software must make a user happy using the device ranked two billionth in capability.  Having that device work well is difficult enough for the manufacturers of that device: Apple has no two billionth device, nor do larger computers.  The Android ecosystem has an entire branch devoted to running better on low RAM devices, without which they might not have a useful two billionth device.

Even worse, the capability of a computing device changes and worsens over time.  First: the hardware devices performing the computations get worse due to wear, thermal stress, and breakage[^3]. Second: temporary environment changes cause problems.  The second is mostly network changes, thermal throttling, low device energy throttling, and SSD device performance properties when they are near full.  Applications running in an environment exposed to real users (as opposed to people that use software for a job) result in exercising parts of the running code/configuration system in ways no one could have anticpated.  With enough users, some devices are always in a near zero power state, a near zero network state, a near zero storage state, or some novel failure state.  Our applications must still perform adequately in those situations!

## Users Expect Simple Things To Work

After 25 years of the Internet existing and 15 years of high capability smartphones, users expect simple things to work, work quickly, and never error.  Login, logout, messaging, file selection, profiles, backgrounds, timers, clocks, displays, never freezing, etc, etc.  Users have been trained by apps that are functional that they can demand functional apps.  A frustrated user is a user that encountered unacceptable slowness or bugginess when using your software. A frustrated user will very, very quickly become an ex user.  They will do this over the simple things that they expect to work and to be fast.

## Users Expect Complex Things To Work

Turns out users do not know the difference between a simple and a complex thing! They expect it all to work, and will aggressively abandon you if they perceive your software isn't working. In the case of heavy multimedia processing some users will tolerate some slowness, but that is at most 1% of users. With a potential 2 billion users, that means 20 million at most will use the slow parts of your software.

## Systems which cannot be guaranteed to have provable accuracy have bounded utility

Solving problems with algorithms takes time. We know how to solve some problems in an optimal sense with small consumption of time and space.  We need to actually solve those problems to that level. Many NP-hard problems are very valuable to solve to some approximation level. The provable approximation bound of a given NP-hard problem might not be enough to make users happy! So we resort to heuristic methods, machine learning, and other techniques.  When using the approximately correct systems to provide users useful output, it is _critical_ to not show them broken garbage. This especially applies in the age of LLM chatbots, as the string->string mapping of those chatbots does not and cannot have a provably correct output. A high level of post-processing must evolve alongside any user visible outputs of LLMs to avoid failures so notable they irritate users.

## Fix It By Focusing On It

### Benchmark Engineering Hits Constraints

Doing benchmark engineering is necessary to have correct and performant software, but it is not sufficient.  If you simply chase bottlenecks and reduce them, then eventually you hit the 'no perceptible bottlenecks' stage. Unfortunately, the only way to improve from that point is to do real engineering around how to structure a more effective system.

### Efficiency Focus Hits Constraints

Simply focusing on efficiency of execution is also insufficient.  Attempting to make everything hyper effecient at all points usually results in enormous amounts of spooky action at a distance. It is critical to focus on the efficiency of resource usage in your systems, but you must still structure your overall designs so that as they scale up and as usage changes, they can be swiftly improved or repaired.

### Abstraction Layers Allow Progress

The way to make progress is put cuts in the codebase frequently that can be reduced in cost or extracted to run on their own computing system/hardware when they become a limiting factor.  Keeping abstraction boundaries tight makes it tractable to do most large system engineering tasks that would otherwise be impossible. Generally, when designing large distributed systems (which all software systems eventualy become, as utilizing the network is critical for solving all non-trivial problems), defined hard RPC boundaries with simple request/response semantics in all cases.  Use those as the abstraction walls between boxes of software.  When one box is too full of slop or trash, you can safely replace it as all of its interfaces are precisely specified.

## Seems a Bit Rigid!

The requirements for writing software that services a billion people are different than the requirements to serve a million people. If you have two people and 8 days, you can hack something together for ten million people just fine.  If you want to have a credible chance at growing to a billion users, then knowing what the right path is is a requirement.  You can choose when and where to step off the correct path for the purpose of expediency; doing so simply has a severe cost.

[^1]: All this implies that anytime a engineer or tester does see a problem in an app, it is a HUGE DEAL, because it is probably hitting at a p95 or more frequent level.
[^2]: This also implies that large fractions of development and testing _must_ be done in low capability environments with low capability devices: a problem any engineer encounters directly is much more likely to be swiftly fixed.
[^3]: See the concept of a knockout transistor.

Appendix I:
Additional Reading
The Tail At Scale
Google SRE book

# Technical Debt Is Better Named Technical Inflation

_There is no organizaion that punishes you for your sins, your sins punish you directly._

  Whenever we add features to a software system, complexity grows. Adding complexity increases the software maintenance cost exponentially.
  People in the software industry have an instinct that 'cutting corners' lets us ship software faster, but with some sort of looming and unclear future price to be paid.

  Let's call the idea that 'corner-cutting code makes it harder to do useful work' the 'special cost'.  Most people in the software industry call this 'special cost' 'technical __debt__'.

  __It is not.__

  A more correct term is 'technical __inflation__'.

  In financial systems, debt is an obligation from a borrower to a lender with a debt service payment and (often) a duration.  Taking out debt is often seen as a very positive action, providing access to future money early at computable and bounded cost. This early access to capital allows useful work to be done sooner with a quantified cost. The lender that provides the capital also receives compensation in excess of the risk it incurs by lending.

  Inflation, however, is loss in purchasing power of a unit of currency over time. It isn't well controlled, it has persistent negative effects, and reversing its impact is prohibitively expensive.  It has no clear 'issuer' or 'lender'.  It is an observed and emergent characteristic of complex economic systems.  The people in those economic systems spend an enormous amounts of thought and effort trying to understand and ameliorate the impact of inflation.  They do this because inflation is in no sense optional.  It increases over time in almost all cases, reducing what today's money can buy tomorrow.  Attempts to control, reduce or predict the growth of inflation are extremely expensive and not guaranteed to work.

The 'special cost' in software we described has none of the debt-like qualities. 'Technical __debt__' fails as a metaphor, resulting in bad cost-benefit analysis being made by engineering organizations. The metaphor (wrongfully) implies:
  * the 'special cost' has consistent and stable debt service (or debt maintenance) cost.
  * the 'special cost' is local in scope, and has a small blast area.
  * the 'special cost' can be slowly and incrementally reduced or controlled with something like an interest payment.
  * every unit of engineering work applied to reducing the 'special cost' gets exactly one unit (less interest) of reduction!
  * that the 'technical debt' has a lender and borrower: who or what exactly is doing the lending of 'special cost', and how?
  * that you can choose not to take on the 'special cost'!

  None of these or any of the other implications of the metaphor are true. They are actively misleading items to base cost-benefit analysis on! Increasing the 'special cost' results in higher defect rates, inconsistent and increasing software maintenance burden, pervasive damage to most or all parts of a codebase, and hastening the phase change to a 'complex system'[^1].  In complex systems even simple bug fixes are dangerous and rolling out new features is extremely costly! Worse, the only optionality involved is how much attention you pay to slowing the complexity creep!

  However, the 'special cost' does have the inflation-like qualities! Each unit increase of 'special cost' makes each future unit of work put into the codebase less effective, forever[^2]. It is prohibitively expensive to reduce that increase.  Why is reducing the 'special cost' difficult? Refactors (even tool assisted) quickly exceed the complexity that can be handled by a single person or small team.  We also speculate that once your refactor gets too large, the 'special cost' associated with the refactor becomes larger than what was being repaired.  Using tooling helps shift this threshold, but all tooling causes complexity increases of its own, so you have to be careful that your tooling is a net positive. As engineering organizations grow, making 'special cost' reducing changes gets harder and is less likely to be effective for at least two reasons.

1. People that are familiar enough with why the code was written a certain way and how to materially improve its design tend to leave organizations over time.
2. Individuals tend to focus on things that get them promoted or rewarded within an organization: they take actions that the incentive structure of the organization rewards. Most organizations incentivize growth[^3] over efficiency in most cases.

  Avoiding the 'special cost' isn't hopeless though: we can improve the models we use for future cost-benefit analyses before we do our work. Thinking of this as inflation (something unavoidable that we need to minimize) instead of debt (something that gives us access to future capital with minimal, computable and bounded cost) results in us doing better at this estimation.  We can see that the 'special cost' reduces future effectiveness in an exponential fashion. We can also avoid the pitfall where we reason, "We deliver software faster! Therefore we actively want to take on technical debt to go faster in most situations, as technical debt is a net benfit!" Actively choosing to add technical inflation to the codebase sounds like a mistake!

  We do not have a good solution to preventing the 'special cost' of creating software, but using inflation instead of debt is a much more accurate metaphor. Please use 'technical inflation' as the term of art when talking about this, so as to provide a more accurate vision to all our colleagues of the costs and benefits of choosing to incur more technical inflation.

[^1]: Above a certain size everything becomes a complex system, but some are more brittle than others.
[^2]: Even though software engineering work is not fungible, this universal weakening of effort affects all software engineering work.  The worsening is fungible?
[^3]: As an example, it is a risky action that requires a higher expected benefit to stop all new feature work on some system to improve some painful aspect of the system as it is.  So that action is rarely taken because the cost/benefit ratio to take it is so outsized.

<sup><sub>This is v1 of this document, there might be small stylistic changes in the future.</sub></sup>

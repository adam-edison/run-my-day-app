# DR-0049: Free Self-Hosting, Paid Hosting, and Donations

**Status:** Accepted  
**Date:** 2026-11-28

## Context

Run My Day is open source under the MIT license, as recorded in DR-0001. It is offered in three ways:

* Local hosting: the server runs on the user's own computer, for that user only.
* Self-hosting: the user runs the server on a machine they control, at home or on a rented server, and reaches it from anywhere.
* Hosted: the project runs the server for its users.

The hosted version has running costs, such as the server and the database. Marketing has no budget.

Facts about earning money:

* Freemium apps convert a median 2.1% of trials to paid. Apps with a hard paywall convert 10.7%.
* About 72% of annual subscribers cancelled in their first year in 2026.
* Plausible sells hosting of its open-source analytics. It passed $1M in annual recurring revenue in 2022, with no advertising.
* Actual Budget, an open-source budgeting app, raised $57,187 in total through donations.

Facts about how users receive this model:

* Plausible, Home Assistant with Nabu Casa, and Immich offer open source, free self-hosting, and paid hosting together.
* HashiCorp changed Terraform from an open-source license to the BSL in 2023, and the community forked it.
* Users criticize projects that hold back features from the self-hosted version to push people to pay.
* Immich promises no paywalled features and keeps its open-source license.

Example: a user who wants no server runs Run My Day on its hosted version for a small monthly fee. A user with a Mac mini at home self-hosts it for free and gets every feature.

## Decision

* Local hosting and self-hosting are free.
* The hosted version costs a small monthly fee, with a free trial.
* A sponsor button accepts donations, such as through GitHub Sponsors.
* Every feature is available in local hosting and self-hosting. No feature is held back for the hosted version.
* Run My Day stays under the MIT license. The license is never changed to one that is not open source.
* The hosted version is sold as a convenience: it saves the work of running a server.
* These promises are stated publicly, in the README and on the hosted version's pricing page.

## Alternatives Considered

Donations only, with a free hosted version, rejected:

* Hosting costs grow with each user, and donations are not tied to use.

Freemium hosted version with a free tier, rejected:

* Free users cost hosting money and pay nothing. Freemium apps convert a median 2.1% of trials to paid.

## Rationale

* Each hosted user pays for the server they use, so the hosted version does not run at a loss.
* Free local hosting and self-hosting keep the full product open to anyone.
* Written promises of feature parity and an open license earn goodwill. Breaking them causes backlash.

## Consequences

* The hosted version needs billing and a free trial.
* The monthly price is not decided.

## Assumptions

* Paid hosting and donations can bring revenue large enough to pay a salary. Not found in official sources as of 2026-11-28.
* Plausible's paid-hosting success with businesses also applies to a consumer app. Not found in official sources as of 2026-11-28.

## Sources

### [State of Subscription Apps 2026: trends and benchmarks (RevenueCat blog)](https://www.revenuecat.com/blog/growth/subscription-app-trends-benchmarks-2026)

Retrieved 2026-11-28.

- Fact: Freemium apps convert a median 2.1% of trials to paid. Apps with a hard paywall convert 10.7%.
  > "hard paywalls have a median Day-35 trial-to-paid conversion rate of 10.7%, compared to just 2.1% for freemium apps"
- Fact: About 72% of annual subscribers cancelled in their first year in 2026.
  > "worsened to ~72% in 2026"

### [About Plausible Analytics](https://plausible.io/about)

Retrieved 2026-11-28.

- Fact: Plausible passed $1M in annual recurring revenue in 2022, with no advertising.
  > "Plausible crossed $1 million in annual recurring revenue in 2022."
  > "We have never spent a cent on advertising, affiliates or paid endorsements."

### [Plausible self-hosted web analytics](https://plausible.io/self-hosted-web-analytics)

Retrieved 2026-11-28.

- Fact: Plausible offers free self-hosting alongside paid hosting.
  > "Anyone can download our software, install it and run it independently."
- Fact: Plausible holds back some features from self-hosting.
  > "are not available to help support the project's long-term sustainability."

### [Actual Budget (Open Collective)](https://opencollective.com/actual)

Retrieved 2026-11-28.

- Fact: Actual Budget raised $57,187 in total through donations.
  > "$57,187.48"

### [The Open Home (Home Assistant)](https://www.home-assistant.io/blog/2021/12/23/the-open-home/)

Retrieved 2026-11-28.

- Fact: Home Assistant treats its paid cloud as optional.
  > "It is okay for a product to offer a cloud connection, but it should be extra and opt-in."

### [Nabu Casa](https://www.nabucasa.com/)

Retrieved 2026-11-28.

- Fact: Paid hosting funds the open-source project.
  > "Your purchases alone fund our work."

### [Immich core team goes full-time](https://immich.app/blog/2024/immich-core-team-goes-fulltime)

Retrieved 2026-11-28.

- Fact: Immich promises no paywalled features and keeps its open-source license.
  > "without limiting features for those who do not pay."
  > "Immich will continue to be licensed under AGPL without a CLA."

### [OpenTF forks Terraform code (The Register)](https://www.theregister.com/2023/08/28/opentf_forks_terraform_code/)

Retrieved 2026-11-28.

- Fact: The community forked Terraform after HashiCorp changed its license.
  > "Since no reversal has been done, and no intent to do one has been communicated,"

### [A critical response to Mattermost's recent changes (Mattermost forum)](https://forum.mattermost.com/t/a-critical-response-to-mattermost-s-recent-changes/25407)

Retrieved 2026-11-28.

- Fact: Users criticize projects that hold back features to push people to pay.
  > "removing features from Teams edition to incentivise people to go onto Enterprise edition"

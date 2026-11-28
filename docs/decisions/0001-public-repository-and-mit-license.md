# DR-0001: Public Repository and MIT License

**Status:** Accepted  
**Date:** 2026-11-24

## Context

This project is primarily a personal productivity application and professional portfolio piece intended for public distribution. Given that REA already exists, there is little incentive to maintain a proprietary implementation.

While profit is not the primary motivation, the project should retain opportunities for monetization and financial sustainability.

## Decision

Maintain a public GitHub repository under the MIT License.

## Rationale

- **Cost efficiency:** Unlimited free GitHub Actions minutes on standard GitHub-hosted runners, subject to usage limits.
- **Community contributions:** Enable external contributions if the project gains traction.
- **Professional visibility:** Demonstrate technical capabilities and end-to-end product ownership.
- **Open-source adoption:** Encourage use, modification, and distribution with minimal restrictions.
- **Commercial flexibility:** Retain the ability to charge for managed deployments, hosted services, and accept voluntary sponsorships.

## Consequences

- Third parties may reuse, modify, and commercially distribute the software, including competing hosted deployments.
- Secrets, credentials, and personal data must remain outside the repository.
- Monetization and pricing strategies can be determined independently of source code licensing.

## Sources

### [The MIT License – Open Source Initiative](https://opensource.org/license/mit)

Retrieved 2026-11-27.

- Fact: The MIT License lets anyone use, modify, distribute, and sell the software with minimal restrictions.
  > "Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the “Software”), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software"
- Fact: Third parties may reuse, modify, and commercially distribute the software.
  > "to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software"

### [GitHub Actions billing](https://docs.github.com/en/billing/concepts/product-billing/github-actions)

Retrieved 2026-11-27.

- Fact: Standard GitHub-hosted runners are free in public repositories.
  > "The use of standard GitHub-hosted runners is free: In public repositories"
- Fact: This free usage does not count against the included minutes.
  > "The following usage remains free independently and does not reduce the included compute usage: Standard GitHub-hosted runners in public repositories."

### [Actions limits](https://docs.github.com/en/actions/reference/limits)

Retrieved 2026-11-27.

- Fact: Free GitHub Actions usage is subject to usage limits.
  > "There are limits in GitHub Actions which you may hit as you scale up, some may be increased by contacting support."

### [GitHub Sponsors](https://github.com/sponsors)

Retrieved 2026-11-27.

- Fact: GitHub Sponsors accepts voluntary sponsorships for open source projects.
  > "GitHub Sponsors allows the developer community to financially support the open source projects they depend on, directly on GitHub"
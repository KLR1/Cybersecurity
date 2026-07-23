# PRD: Admin Console — Multi-Factor Authentication


| **Reference: Auth0 MFA Docs** | https://auth0.com/docs/secure/multi-factor-authentication/enable-mfa |
| **Reference: NeoID MFA Docs** | [MFA in Auth0 — NeoID](https://elsevier.atlassian.net/wiki/spaces/NEOID/pages/119601285008300) |

---

## Background & Strategic Fit

### Context and Background

- Due to increasing phishing attacks on customer-facing administrative applications by bad actors, there is a growing threat to users' identities and credentials, and to the security of the application.
- Several academic institutions were recently targeted by an actor seeking to resell paid entitlements — potential fraud or criminal activity, as reported by the **COPS team (Content Protection and Security Team)**.
- With Admin Console fully onboarded onto NeoID as of Q4 2024 ([IAM-42614](https://elsevier.atlassian.net/browse/IAM-42614)), enabling Multi-Factor Authentication (MFA) will add an additional security layer to the application and protect the digital identities of administrators.
- The scope of this project is to **identify a suitable MFA method and enable it** as a second authentication factor when accessing Admin Console.
- This initiative falls under the broader theme of **Security Enablement**, empowering the Identity team to respond to fraudulent attacks that compromise security.

### Scope

Enable **Multi-Factor Authentication in Admin Console** to enhance security and protect customer and user data.

### Value to the User (Administrator)

- Ensures admin login credentials are secured.
- Protects end-users' Personally Identifiable Information (PII).

### Value to the Business

- Builds trust and enhances brand value by ensuring GDPR compliance.
- Prevents data and content leakage; avoids revenue-impacting unauthorised access.
- Handles phishing attacks and threats with confidence.
- Contributes to the **Shared Tech Objective:** *Strengthen access security mechanisms, compliance, and platform resilience to prevent unauthorised access, mitigate threats, and address vulnerabilities.*

---

## Success Metrics

| Objective | Key Result (Metric) | Monitoring |
|---|---|---|
| Implement MFA as an additional authentication factor for Admin Console | Achieve 0% phishing attack rate on Admin Console administrators after Admin Tool is sunset | Analytics dashboard — link TBD |
| Understand error rates in the MFA process | Email delivery rate above baseline (NeoID invite user benchmark); data available in Snowflake Analytics Dashboard | Dashboard link TBD |
| Measure Admin Console access success rate | Track the AC login funnel: (1) Get Started → (2) Enter email (NeoID) → (3) Enter password or org IdP → (4) MFA factor triggered → (5) Home page / AC Dashboard | Dashboard link TBD |

---

## Milestones

See the Jira timeline filtered by Epic **"Elaboration - Enable MFA for Admin Console"** for user story status and delivery estimates:

- [IAM Board Timeline](https://elsevier.atlassian.net/jira/software/c/projects/IAM/boards/5869/timeline?shared=&atlOrigin=eyJpIjoiZTVmNDE2OGNhY2NkNDMyZDkyMDA5NDBjYjBiODljMGEiLCJwIjoiaiJ9)
- [Timeline — IAM-37750](https://elsevier.atlassian.net/jira/software/c/projects/IAM/boards/5869/timeline?selectedIssue=IAM-37750&shared=&atlOrigin=eyJpIjoiOGI5ZjQ4OTQyY2VmNDVlYTljZjM3NmY3N2Q0YjQ1OGMiLCJwIjoiaiJ9)

---

## Requirements

| Requirement | User Story | Priority | Jira | Notes |
|---|---|---|---|---|
| **New email domain / Anti-Spam** — Set up a dedicated subdomain for MFA emails. Must be configured with DMARC (Quarantine → Reject), DKIM, and SPF to pass anti-spam compliance. OTP via email initially, with other factors to follow. | As an end-user, I want to receive the OTP email without it landing in my spam folder. | Must Have | [IAM-46522](https://elsevier.atlassian.net/browse/IAM-46522) | Use a sending address unique to the system. Do not reuse noreply@elsevier.com. See [email standard](https://elsevier.atlassian.net/wiki/spaces/arch/pages/119601631856350). |
| **Improved email delivery rates** — New domain dedicated to Admin Console MFA emails. | As an end-user, I want to reliably receive OTP emails without them going to spam. | Must Have | TBD | See [email branding guidance](https://elsevier.atlassian.net/wiki/spaces/arch/pages/119601631856350). |
| **Email delivery analytics in Snowflake** | As an Admin Console Product Manager, I want to analyse email delivery performance in a dedicated dashboard. | — | TBD | — |

---

## Technical Implementation

Full implementation guide (Auth0 dashboard MFA options):
- [Enabling MFAs through Auth0 Dashboard](https://elsevier.atlassian.net/wiki/spaces/NEOID/pages/119601760635960)

---

## Available MFA Options in Auth0

| MFA Factor | Description | Auth0 Support (Jan 2025) |
|---|---|---|
| **Push Notifications** | Auth0 Guardian SDK sends push notifications to pre-registered devices. | Supported via [Auth0 Guardian](https://auth0.com/docs/secure/multi-factor-authentication/auth0-guardian) app only. |
| **Phone Message (SMS / Voice)** | One-time code sent via SMS or voice call to a registered phone number. | Supported on Auth0; SMS/Voice provider not yet configured. Requires security sign-off. |
| **One-Time Passwords (OTP)** | Users use an authenticator app (e.g. Google Authenticator) that generates a time-based OTP. | Supported. Compatible apps: Authy, Google Authenticator, Auth0 Guardian, Microsoft Authenticator. |
| **Email OTP** | One-time password sent to the user's email address. | Supported on Auth0. Auth flow documented [here](https://elsevier.atlassian.net/wiki/spaces/NEOID/pages/119601291313508). |
| **WebAuthn / Security Keys** | Physical security key (e.g. YubiKey) as a second factor. | Supported on Auth0; adoption subject to customer consent and product fulfilment sign-off. |
| **Biometrics** | Face ID, fingerprint, or other biometric factors. | Supported on Auth0; adoption subject to customer consent and product fulfilment sign-off. |

### MFA Policy Options

Three policy levels are available in Auth0:

- **Never** — No MFA required. Users log in with a single factor only.
- **Adaptive MFA** — MFA is triggered only when a login is assessed as high-risk, based on Auth0's Adaptive MFA Risk Assessment engine.
- **Always** — MFA is required on every login, regardless of risk level.

---

## User Interaction and Design

### Context for MVP User Experience

- **Assumption:** Motivation for MFA comes from Admin Tool accounts being phished. **Hypothesis:** Users will respond to messaging framed as *"Secure your account"* / *"Protect your account from unauthorised access and fraud."*
- **Assumption:** Users are reluctant to provide their mobile number. **Tech finding:** [Push notification via authenticator app](https://elsevier.atlassian.net/wiki/spaces/NEOID/pages/119601760635960/Enabling+MFAs+through+Auth0+dashboard+and+Actions#Push-Notification-using-Auth0-Gurdian) provides security and mobile convenience without requiring a phone number. **Hypothesis:** This factor will have sufficient appeal that users will adopt it when offered during sign-in.
- **Assumption:** Users are reluctant to use personal mobile devices for work. **Tech finding:** [Emailed OTP](https://elsevier.atlassian.net/wiki/spaces/NEOID/pages/119601760635960/Enabling+MFAs+through+Auth0+dashboard+and+Actions#Email-OTP) requires no phone at all. However, it offers less security (if the user's email is already compromised) and Auth0 requires at least one additional factor to be offered alongside it. **Hypothesis:** Testing a choice between **(A) Push notification via authenticator app** and **(B) Emailed OTP** will reveal user preferences across both factors.
- **Assumption:** Users tend to prefer lower-effort options. **Tech finding:** Auth0 UI can be partially customised, but Emailed OTP may not be surfaceable as the primary choice in the factor selection screen. **Hypothesis:** Testing will reveal whether users notice and select the Emailed OTP option when it is shown as secondary.

### UX Artefacts

- [WIP clickable prototype (Figma)](https://www.figma.com/design/rvrGITWebQmKpV0YaOsAka/MFA?node-id=389-8883&t=cZLia60uMOLxDyOm-4) — combined flow (Authenticator app + Emailed OTP as options)
- [UX research study plan](https://elsevier.atlassian.net/wiki/pages/createpage.action?spaceKey=HMU&title=2025-06%20MFA-Concept%20testing) — currently in development

---

## Open Questions

| Question | Answer | Date Answered |
|---|---|---|
| What authentication methods do admins currently have in Admin Console? | Email address / password only. SAML SSO to be supported in Q2–Q3 2025. | — |
| Is MFA only for email/password login, or for SAML SSO as well? | MFA is ideally applied to SAML SSO as well, since we cannot control how the customer configures SAML on their end. | — |
| Is Email OTP sufficient for security compliance, or is Adaptive MFA / SMS OTP required? | An iterative approach has been recommended — start with email OTP and build from there, depending on effort vs timelines. | — |
| What is MFA (Multi-Factor Authentication)? | A security mechanism requiring users to provide two or more forms of authentication to gain access. The additional layer protects against unauthorised access when the first factor (e.g. password) is compromised. | — |
| What is Adaptive MFA? | A flexible MFA policy that assesses risk at every login and prompts for a second factor only when appropriate — balancing security with user friction. | — |

---

## Out of Scope

*(To be populated)*

---

## PRD Acknowledgement

### Status Definitions

| Status | Definition |
|---|---|
| **Not Requested** | PRD has not yet been sent for review. Product Owner responsible for initiating. |
| **Requested** | PRD sent for review; acknowledgement not yet received. |
| **More Info Requested** | Reviewer has additional questions before acknowledging. Product Owner responsible for notifying reviewers once addressed. |
| **Approved** | Reviewer has acknowledged (must include date received). |
| **Denied** | Reviewer has denied approval (must include date received and reason). |

### Sign-Off Record


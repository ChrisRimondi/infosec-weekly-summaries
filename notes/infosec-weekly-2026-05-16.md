# InfoSec Weekly Briefing - May 16, 2026

This episode is AI-generated and based only on public sources.

## Intro

Welcome to the InfoSec Weekly Briefing for Saturday, May 16, 2026.

This week is a good reminder that security work is rarely one dramatic story. It is usually a braid of very ordinary pressures: patch volume, exploit prioritization, cloud shared-responsibility details, identity hygiene, developer supply-chain exposure, and the question every security leader has to answer on Monday morning: what do we do first?

The major public signals this week are practical rather than cinematic. Microsoft shipped its May Patch Tuesday release, and multiple public writeups agree on the notable part: after a long stretch of months with exploited or publicly disclosed zero-days, this one appears to have landed without a confirmed zero-day in the release set. That does not make it low priority. The release still includes a large number of vulnerabilities, including critical remote code execution issues across Windows, Office, Dynamics, SharePoint, Netlogon, DNS Client, GDI, and other components.

CISA and AWS also pointed defenders toward CVE-2026-31431, a Linux kernel issue that could allow authenticated local privilege escalation. That kind of vulnerability is easy to mentally demote because it is local and authenticated. But in modern infrastructure, local privilege escalation often matters precisely because attackers frequently land first with some lower-privilege foothold: a compromised container, a vulnerable web app, stolen developer credentials, or a misconfigured workload. Once they are inside, local privilege escalation can turn a contained incident into node-level control, credential theft, lateral movement, or persistence.

Then there is the software supply chain story around compromised npm packages tied to the TanStack ecosystem and other packages. Reporting from The Register and other outlets, referencing Socket, StepSecurity, GitHub advisories, and related analysis, described malicious package versions that could read sensitive files from developer and CI environments, including cloud credentials, SSH keys, developer tool config, crypto wallets, VPN configuration, messaging credentials, and shell history. The most important sentence from that entire class of incidents is blunt: any developer or CI environment that installed affected versions during the exposure window should be considered potentially compromised.

That one sentence is the heart of modern application security. Dependency compromise is not only about whether the malicious code made it into production. It is about what secrets were present at install time, what permissions those secrets had, whether CI environments were isolated, whether package manager scripts were allowed to execute, and whether the organization can revoke and rotate credentials quickly without breaking delivery.

So today's briefing has five themes.

First: Patch Tuesday without a zero-day is still a heavy operational event.

Second: local privilege escalation in shared infrastructure deserves serious prioritization.

Third: npm compromise is an identity and secrets-management incident, not just a package-management incident.

Fourth: cloud security posture is moving toward identity, workload identity, and risk-based prioritization rather than only perimeter controls.

And fifth: the best security work this week is boring in the strongest sense of the word: inventory, patching, credential rotation, CI hardening, and detection coverage.

Let's get into it.

## Highlights

The first highlight is Microsoft's May 2026 Patch Tuesday. Public reporting varies slightly in the count, with some sources saying 118, 120, 137, or around 140 vulnerabilities depending on classification and counting methodology. Cisco Talos reported 137 vulnerabilities, including 31 marked critical by Microsoft. Tenable reported 118 CVEs. Other summaries landed around 120 or 137. That variance is normal for Patch Tuesday coverage because different analysts count Chromium-based components, advisories, cumulative updates, and CVE groupings differently.

The important part is the risk shape. Talos noted that Microsoft had not observed the included vulnerabilities being actively exploited in the wild. Multiple other sources called out that this was the first Microsoft Patch Tuesday in a long time without exploited or publicly disclosed zero-days. But Talos also identified 16 critical remote code execution vulnerabilities among the critical entries, affecting services and applications including Microsoft Office, Word, Windows Native WiFi Miniport Driver, Azure, Office for Android, Dynamics 365, Windows GDI, SharePoint, Windows Graphics Component, Netlogon, and DNS Client.

That is a classic prioritization puzzle. No zero-day means you may not need panic-mode emergency change windows for every asset. Critical RCE across common enterprise components means you still need a disciplined rollout. The job is to convert "big patch month" into a ranked plan: internet-exposed systems, domain infrastructure, collaboration platforms, endpoint exposure, vulnerable server roles, high-value user populations, and compensating controls.

The second highlight is CVE-2026-31431, sometimes discussed publicly as the Linux kernel "Copy Fail" issue. CISA added it to the Known Exploited Vulnerabilities catalog on May 1, signaling exploitation in the wild. AWS published a security bulletin on May 7 explaining impact across Amazon Linux, Bottlerocket, ECS, EKS-optimized AMIs, EMR, Fargate, Deep Learning AMIs, and SageMaker-related areas. AWS emphasized that, with exceptions listed in the bulletin, customers were not affected, but affected customer-managed or customer-action services required kernel, AMI, host, or platform updates on a timeline.

The lesson here is shared responsibility in a form that security teams can actually use. Cloud providers may patch provider-managed infrastructure, release fixed AMIs, release updated Bottlerocket or Amazon Linux builds, and communicate timelines for managed services. But customers still need to know which fleets consume those images, which clusters run affected node groups, which ECS or EKS environments need recycle or replacement, which Fargate platform versions are in use, and whether any long-lived instances are drifting away from the patched baseline.

The third highlight is the npm supply-chain compromise. The reported incident touched popular developer ecosystems and raised the highest-value question for defenders: what secrets were reachable from developer workstations and CI jobs at dependency-install time? The best response is not merely "pin the dependency and move on." It is to identify potentially exposed environments, rotate credentials that could have been read, review CI logs and package-lock changes, and reduce the blast radius of future install-time compromise.

The fourth highlight is cloud identity and governance. Google Cloud's security and identity guidance from the last year is still relevant to this week's security posture discussion: workload identity, managed workload identities, privileged access management, deny and principal access boundary simulators, context-aware access, and security baselines all point in the same direction. Cloud security is becoming less about one big perimeter and more about constraining human and non-human identities so that a single stolen token, workload compromise, or misconfiguration has a smaller blast radius.

The fifth highlight is the maturing security market around detection and AI. Google and Mandiant continue to frame modern defense around frontline threat intelligence, cyber resilience, and AI-enabled defense. Grafana and observability vendors are doing similar work on the reliability side. For security, the caution is the same: AI-assisted triage can be helpful when evidence-bound and permissioned, but dangerous if it produces confident narratives without traceable artifacts. For now, the highest-confidence security uses of AI are summarization, enrichment, prioritization, and analyst acceleration, not unsupervised response.

## Vulnerabilities and Patch Priorities

Let's spend a little time on the Microsoft release, because this is where a lot of security programs either become crisp or become mush.

A zero-day-free Patch Tuesday is psychologically dangerous. It can make people relax too much. The absence of known exploitation in Microsoft's release set is good news, but it is not a guarantee that exploitation will remain absent. Patch releases tell attackers what changed. Reverse engineering can turn patches into exploit clues. The day after Patch Tuesday is not jokingly called "Exploit Wednesday" for nothing.

For security operations teams, the practical move is to separate urgency from sequencing. Urgency asks, "How fast should we start?" Sequencing asks, "What gets patched first?" This month, the answer is probably not "patch everything by lunch." The answer is "start immediately, but rank intelligently."

Start with domain and identity infrastructure. Netlogon and authentication-adjacent vulnerabilities deserve special attention because identity systems are control planes for the enterprise. A vulnerability in authentication or domain services can have consequences far beyond one host. Even when exploitability is constrained, attackers love systems that let them move from one machine to many.

Next, look at internet-exposed and document-processing surfaces. Office, Word, SharePoint, and graphics components are common paths for user-assisted or content-triggered compromise. If a vulnerability requires a user to open a malicious file, that does not make it low risk in an enterprise where email, file sharing, ticket attachments, and collaboration tools are central workflows. Many real compromises begin with "someone opened a thing."

Then look at network-adjacent and wireless exposure. Talos called out CVE-2026-32161 in the Windows Native WiFi Miniport Driver as a race condition that could allow adjacent-network code execution. Adjacent-network flaws are often more relevant for laptops, offices, shared spaces, hotels, conferences, and unmanaged networks than server rooms. The asset population matters. A corporate laptop fleet with frequent travel has a different risk shape from a locked-down server subnet.

After that, prioritize high-value servers, administrative workstations, and systems that bridge trust zones. A vulnerability on a random kiosk is not good. A vulnerability on a jump host, build server, domain admin workstation, or management server is much worse. Patch prioritization should incorporate asset criticality and privilege, not only CVSS.

Finally, account for compensating controls. EDR coverage, exploit protection, network segmentation, application control, email sandboxing, macro policy, least privilege, and web isolation can all change risk. But compensating controls should support patching, not become excuses to skip it. The best posture is layered: reduce exposure now, patch promptly, verify coverage, and hunt for suspicious behavior where exploitation would show up.

Now CVE-2026-31431.

The phrase "authenticated local user can escalate privileges" often lands with less urgency than "unauthenticated remote code execution." That is understandable, but incomplete. In cloud and container environments, local privilege escalation can be a second-stage vulnerability. An attacker may first compromise a containerized application, a developer's SSH key, a low-privilege service account, or a scheduled job. Local escalation then helps them break containment, access host resources, read secrets, or tamper with security controls.

AWS's bulletin is especially useful because it maps a kernel CVE to service-specific customer action. Amazon Linux kernels across several versions are affected and have updates. Bottlerocket has updates. ECS on EC2, ECS Managed Instances, EKS-optimized AMIs, EMR, Fargate platform versions, Deep Learning AMIs, and SageMaker have timelines or guidance. That kind of mapping is what internal security teams should emulate.

Do not just send "patch CVE-2026-31431" to engineering. Send a mapped action plan.

For Amazon Linux: update packages, reboot where required, and verify kernel version.

For Bottlerocket: apply available updates and recycle nodes if needed.

For EKS: identify node groups using affected EKS-optimized AMIs, update launch templates or managed node group versions, and roll nodes safely.

For ECS on EC2: update container instances and drain or replace hosts.

For Fargate: check platform versions and provider guidance.

For EMR and machine-learning AMIs: identify long-running clusters or notebooks that do not naturally refresh.

For self-managed Kubernetes on cloud VMs: patch the underlying node OS and verify that workloads are not quietly pinned to old nodes.

The operational lesson is that cloud vulnerability management is not one workflow. It is several workflows stitched together: package patching, image replacement, node rotation, platform version upgrades, cluster lifecycle management, and asset inventory. If any one of those is weak, the vulnerability lingers.

## Threat Activity and Supply Chain

The npm supply-chain incident is the most important defensive-engineering story this week because it strikes at the way software is built, not only the way it is served.

The public reporting described compromised versions associated with widely used JavaScript packages and developer ecosystems. The details matter less than the class of attack: malicious code runs during dependency installation or build workflows and attempts to read sensitive files from the environment. StepSecurity analysis, as summarized by The Register, said the payload read files from more than 100 hardcoded paths, including cloud credentials, SSH keys, developer tool configuration, crypto wallets, VPN configuration, messaging credentials, and shell history.

That is a very specific attacker thesis: developer and CI environments are rich with secrets.

They often are. A developer laptop may have GitHub tokens, cloud CLI credentials, SSH keys, npm tokens, package registry tokens, Kubernetes configs, Terraform state access, Slack tokens, API keys, browser sessions, password-manager helper state, and shell history full of one-off commands. CI jobs may have deployment credentials, signing keys, artifact repository tokens, cloud roles, secrets for integration tests, and permissions to publish packages or deploy infrastructure.

If malicious package code can run in that environment, the compromise scope is not limited to the package. It is whatever the environment could read.

The first response question is exposure: did any developer workstation, CI runner, build container, or dependency cache install an affected package version during the relevant window? That requires package lockfiles, CI logs, registry logs, dependency proxy logs, and endpoint telemetry. If the answer is yes or uncertain, the second question is secrets: what credentials were present and readable?

This is where organizations often struggle. They may be able to find which repo used a package, but not which secrets were available to the job. Or they may know which GitHub Actions workflow ran, but not whether the token was scoped to repo read, package publish, cloud deploy, or organization admin. That ambiguity is expensive. It forces broad rotation.

Good supply-chain response is a secrets-management drill.

Rotate npm tokens and package registry credentials that could publish or pull private packages.

Rotate GitHub tokens, deploy keys, and app credentials available to affected workflows.

Rotate cloud access keys and review cloud role usage for unusual activity.

Invalidate CI secrets exposed to jobs that installed affected packages.

Review package publication history for unauthorized versions.

Check for unexpected workflow changes, new deploy keys, new GitHub Actions secrets, and suspicious repository settings changes.

Review CI runner persistence. Ephemeral runners reduce risk; long-lived shared runners can preserve attacker artifacts across builds.

Review egress from build environments. If build jobs can reach arbitrary destinations, exfiltration is easier. Network egress restrictions for CI are painful but powerful.

The prevention side is equally practical. Use lockfiles and provenance where possible. Restrict lifecycle scripts where feasible. Prefer ephemeral CI. Scope tokens tightly. Separate build, test, publish, and deploy permissions. Use package allowlists or dependency review for sensitive repos. Monitor for installation of newly released package versions in high-risk workflows. Require MFA and trusted publishing for package maintainers. Use artifact signing, SLSA-style provenance, and reproducible build signals where your ecosystem supports them.

But be careful about one trap: do not promise that signing and provenance solve everything. They help answer "who published this artifact" and "how was it built." They do not automatically prevent a legitimate maintainer account, token, or workflow from publishing malicious code if that account or workflow is compromised. Supply-chain security is layered because the attacker can enter at many points.

The best defensive stance is to assume dependency code can be hostile and design the build environment accordingly. That means fewer secrets at install time, narrower credentials, ephemeral workers, no broad cloud admin tokens in CI, and fast rotation. If that sounds like zero trust applied to build systems, good. It is.

## Cloud, Identity, and Defensive Posture

Cloud security this week connects directly to both the Linux kernel bulletin and the supply-chain story. The cloud is not just a place workloads run. It is an identity system, a policy system, a secrets system, a build target, and a management plane.

Google Cloud's security and identity portfolio announcements from the last year are a useful lens. They emphasize workforce identity federation, enhanced security for non-human identities, managed workload identities based on SPIFFE, IAM deny and principal access boundary simulation, privileged access management, context-aware access, identity threat detection and response, security baselines, and application-centric resource management.

Even if you are not a Google Cloud shop, the direction is broadly applicable.

First, non-human identities are now one of the largest security surfaces in modern environments. Workloads, service accounts, CI jobs, Terraform runners, Kubernetes controllers, serverless functions, data pipelines, and automation bots often outnumber human users. They can also hold persistent, high-value permissions. Many breaches are not "the attacker became Alice." They are "the attacker found a token that can deploy, read storage, assume a role, or mint more access."

Second, policy guardrails need simulation. IAM deny policies, principal access boundaries, service control policies, and organization constraints are powerful, but a broken guardrail can break production. Simulators and troubleshooters matter because cloud policy is complex enough that humans need preview and impact analysis before rollout.

Third, privileged access should become time-bound and approval-based wherever possible. Standing admin access is convenient until it becomes the attacker's persistence mechanism. Privileged Access Management and just-in-time grants are ways to reduce the number of credentials that are dangerous every minute of every day.

Fourth, context matters. Device posture, network location, workload identity, behavior, session risk, and resource sensitivity should influence access. This is not a call for brittle conditional policies everywhere. It is a call to stop treating all authenticated requests as equally trustworthy.

Fifth, security baselines are valuable because most organizations do not fail from exotic attacks first. They fail from public storage, overbroad service accounts, unmanaged keys, missing logging, no MFA, default networks, stale images, and unpatched systems. Baselines help make the secure default easier.

Map this back to the week's incidents.

For CVE-2026-31431, strong workload identity and least privilege reduce what an attacker can do after landing on a workload. Node patching matters, but so does ensuring that a compromised workload cannot access broad cloud credentials from instance metadata or mounted secrets.

For npm compromise, short-lived credentials and workload identity reduce the value of stolen static tokens. CI jobs that assume narrowly scoped roles for a short time are safer than jobs with long-lived cloud keys in environment variables. Package publishing workflows with trusted publishing and scoped tokens reduce the blast radius of a compromised package maintainer workflow.

For Patch Tuesday, asset inventory and identity context decide what gets patched first. A Windows server with domain privileges, admin tools, or sensitive data is not the same as a low-risk endpoint. Vulnerability management without identity and asset context is just a CVE spreadsheet.

The overall defensive posture is clear: fewer standing secrets, more ephemeral credentials, tighter scopes, better inventory, faster patch workflows, and visibility into policy decisions.

## Detection and Response

Detection priorities this week should follow the likely attacker paths.

For Microsoft vulnerabilities, detection is partly product-specific, but the general approach is familiar. Monitor exploit-protection events, suspicious Office child processes, unusual document processing behavior, web shell indicators on SharePoint, anomalous Netlogon or domain-controller behavior, suspicious DNS Client crashes or service instability, and endpoint telemetry around vulnerable components. Patch management should be paired with exposure management: know where the vulnerable software is, which systems are internet-facing or high privilege, and which endpoints are behind slower patch rings.

For CVE-2026-31431, look for local privilege escalation symptoms rather than only exploit signatures. Monitor unexpected privilege changes, suspicious kernel messages where available, new root-owned files created by unusual processes, container escape indicators, tampering with security agents, unexpected changes to sudoers or systemd units, and suspicious access to cloud metadata services. In Kubernetes, watch for pods that unexpectedly gain node-level visibility, hostPath abuse, privileged container creation, service account token access, and node anomalies after workload compromise.

For the npm supply-chain compromise, detection needs to focus on build and developer environments.

Review CI jobs that ran package installs during the exposure window.

Search logs for package installation of affected versions.

Look for unexpected outbound connections from CI runners and developer machines during installs.

Review access logs for cloud credentials, GitHub tokens, package registry tokens, and deployment keys that were present in those environments.

Check for newly created GitHub tokens, deploy keys, OAuth app authorizations, repository secrets, workflow changes, and package publications.

Search for unusual reads of local credential files on developer endpoints where endpoint telemetry supports it.

Pay special attention to long-lived shared CI runners. If a runner is reused across jobs and projects, compromise in one build can contaminate the next. Ephemeral runners are not perfect, but they are a strong containment measure.

Incident response should be decisive but scoped. If you know a CI environment installed an affected package while holding cloud admin credentials, rotate those credentials and review their use. If a developer laptop installed the package but had only low-scope read-only tokens, your response may be narrower. If you cannot tell what secrets were present, assume more exposure and use the incident as pressure to improve secrets inventory.

One response habit is especially useful: write down the credential classes, not just individual secrets. For example: "GitHub repo write token," "npm publish token," "AWS deployment role," "Kubernetes kubeconfig," "Slack bot token," "VPN profile," "SSH key to bastion." Credential classes help you reason about blast radius and standardize rotation.

Another habit: separate immediate containment from long-term hardening. Immediate containment is uninstall, block, rotate, hunt, and verify. Long-term hardening is ephemeral CI, scoped credentials, lifecycle-script controls, dependency allowlists, provenance, egress limits, and developer environment hygiene. If you mix them together, the urgent work gets bogged down. If you only do urgent work, the same class of incident repeats.

## Practical Takeaways

The first practical takeaway is to turn Patch Tuesday into a ranked queue, not a panic.

Use the Microsoft release and Talos or Tenable analysis to identify critical RCEs and high-value assets. Prioritize identity infrastructure, internet-facing systems, collaboration platforms, document-processing surfaces, admin workstations, and assets that bridge trust zones. Track completion by asset class, not just aggregate patch percentage. "Eighty percent patched" can hide the fact that the riskiest 20 percent are still exposed.

The second takeaway is to map Linux kernel patching to actual platform ownership.

For CVE-2026-31431, identify Amazon Linux, Bottlerocket, ECS on EC2, EKS nodes, EMR, Fargate versions, Deep Learning AMIs, and long-lived machine-learning environments. Assign owners to each path. Verify patched kernel versions or replaced images. Recycle nodes where necessary. Do not assume that managed service guidance means no customer action. Read the bulletin's affected-service list and map it to your inventory.

The third takeaway is to run a supply-chain secret exposure drill.

Pick one CI workflow that installs npm packages. List every secret available during dependency installation. Then ask: if install-time code were malicious, what could it read and what could it do? Remove secrets from install steps where possible. Split build and deploy jobs. Use short-lived role assumption. Make runners ephemeral. Restrict egress. Reduce token scope. This is one of the highest-leverage exercises a security engineering team can run.

The fourth takeaway is to improve developer workstation hygiene.

Developer machines are production-adjacent systems. They hold source code, credentials, deployment access, package manager tokens, and cloud CLI state. Encourage password-manager use over shell-stored secrets. Reduce long-lived local cloud keys. Monitor for risky credential file locations. Standardize secure developer environment setup. Make it easy to rotate tokens without breaking daily work.

The fifth takeaway is to treat non-human identities as first-class identities.

Inventory service accounts, workload identities, CI roles, GitHub apps, deploy keys, automation bots, Kubernetes service accounts, and cloud roles. Track owners, permissions, last used time, rotation posture, and whether credentials are static or ephemeral. Many organizations have decent human identity programs and chaotic machine identity programs. Attackers notice.

The sixth takeaway is to build detection around credential use after potential exposure.

When a supply-chain incident happens, the malicious install may be over before you notice. The most valuable detection may be after the fact: was a token used from a new location? Did a CI role access unusual resources? Did a package publishing token publish an unexpected version? Did a GitHub token create a deploy key? Did a cloud key enumerate secrets? Build those questions into logs and alerts now.

The seventh takeaway is to make security communication more useful.

A weekly security summary should not merely list CVEs. It should say what changed, who owns the response, what systems are exposed, what decisions are needed, and what can wait. This is especially important for executive and engineering audiences. Good communication turns threat intelligence into action. Bad communication turns action into anxiety.

## Watchlist for Next Week

Watch for exploit development after Patch Tuesday. Even without a confirmed zero-day at release time, high-severity Microsoft vulnerabilities can become practical exploit targets after patches are analyzed. Watch vendor advisories, EDR telemetry, IDS signatures, and exploit chatter around critical RCEs in Office, SharePoint, Netlogon, DNS Client, GDI, and adjacent-network components.

Watch follow-up guidance for CVE-2026-31431 from cloud providers, Linux distributions, and managed platform teams. The important question is not whether a patch exists, but whether your fleet has consumed it. Long-lived nodes, custom AMIs, older platform versions, and data science environments are common laggards.

Watch the npm supply-chain incident for additional affected packages, indicators, and postmortem detail. These incidents often expand as researchers reconstruct dependency graphs, cache behavior, package publishing timelines, and credential exposure paths. Treat early package lists as a starting point, not a final boundary.

Watch for more work around workload identity and non-human identity governance. This is becoming the connective tissue between cloud security, supply-chain security, and incident containment. The organizations that do this well will have a much easier time responding to the next compromised token or package.

Watch AI security tooling with a skeptical but open mind. The useful tools will cite evidence, preserve analyst control, fit existing workflows, and reduce repetitive work. The risky ones will produce confident summaries without provenance or try to automate response before the organization has defined safe boundaries.

Finally, watch your own backlog. If there is one thing to do before next Saturday, make it this: choose a critical CI workflow and remove one unnecessary secret from the dependency-install step. That is a small change with real defensive value.

## Closing

This week's security work is not glamorous. Patch carefully. Rotate where exposure is plausible. Harden CI. Reduce standing secrets. Inventory machine identities. Check cloud shared-responsibility notes. Improve detection around credential use. Write clearer action plans.

That is the craft.

The best security teams are not the ones that panic the fastest. They are the ones that can turn messy public signals into calm, specific, prioritized work. They know which assets matter. They know which credentials can hurt them. They know where their build systems are too trusted. They know when a "local" vulnerability becomes serious because of the environment around it.

This week gave us exactly that kind of work.

Thanks for listening to the InfoSec Weekly Briefing. This episode was generated from public sources on May 16, 2026.

## Sources

- Cisco Talos, "Microsoft Patch Tuesday for May 2026 - Snort rules and prominent vulnerabilities": https://blog.talosintelligence.com/microsoft-patch-tuesday-may-2026/
- Tenable, "Microsoft's May 2026 Patch Tuesday Addresses 118 CVEs": https://www.tenable.com/blog/microsofts-may-2026-patch-tuesday-addresses-118-cves-cve-2026-41103
- CyberScoop, "Microsoft addresses 137 vulnerabilities in May's Patch Tuesday, including 13 rated critical": https://cyberscoop.com/microsoft-patch-tuesday-may-2026/
- Computer Weekly, "Microsoft releases rare zero-day free Patch Tuesday update": https://www.computerweekly.com/news/366642908/Microsoft-releases-rare-zero-day-free-Patch-Tuesday-update
- AWS Security Bulletin 2026-026-AWS, "CVE-2026-31431": https://aws.amazon.com/security/security-bulletins/rss/2026-026-aws/
- AWS Prescriptive Guidance, "Monitor AWS security bulletins": https://docs.aws.amazon.com/prescriptive-guidance/latest/vulnerability-management/monitor-aws-security-bulletins.html
- The Register, "Cache-poisoning caper turns TanStack npm packages toxic": https://www.theregister.com/cyber-crime/2026/05/12/cache-poisoning-caper-turns-tanstack-npm-packages-toxic/5238650
- Tom's Hardware, "Compromised Mistral AI and TanStack packages may have exposed GitHub, cloud and CI/CD credentials": https://www.tomshardware.com/tech-industry/cyber-security/compromised-mistral-ai-and-tanstack-packages-may-have-exposed-github-cloud-and-ci-cd-credentials-in-mini-shai-hulud-malware-infection-supply-chain-campaign-spreads-across-npm-and-ai-developer-ecosystems-like-wildfire
- Google Cloud Blog, "What's new in IAM, Access Risk, and Cloud Governance": https://cloud.google.com/blog/products/identity-security/whats-new-in-iam-access-risk-and-cloud-governance/
- Google Cloud Security and Mandiant, Cyber Defense Summit 26: https://cyberdefensesummit.mandiant.com/

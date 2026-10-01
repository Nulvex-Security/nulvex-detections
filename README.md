# Nulvex Detections

Sigma, YARA, Suricata and Wazuh content from [Nulvex Security
Research](https://nulvex.com/research).

Clone it straight into a SIEM pipeline. That is why this is a separate repository from the
research corpus: you should not have to take a hundred articles to get the rules.

---

## The one thing to read before deploying anything here

**Every rule states whether it has actually been tested against real telemetry, and most of
ours have not been.**

| Marker | What it means |
|---|---|
| `tested: yes` | It has matched real telemetry containing the thing it detects, in a lab or in production. The rule says where. |
| `tested: no` / `TESTED STATUS: NOT TESTED` | It is reasoned from documented behaviour, a patch diff, or a published exploit description. It has **not** been seen to fire. |

An untested rule is still worth having. A rule that lies about its status is not, and it is
the reason a team stops trusting a rule source. Every file here declares one or the other,
and CI fails a rule that declares neither.

**As of the initial release, every rule in this repository is `tested: no`.** We would
rather say that plainly than let the count imply coverage we have not earned.

## What is here

```
sigma/
  linux/         auditd and eBPF-sourced detections
  windows/       Windows process-creation detections
  application/   application-layer and runtime signals
  network/       network-device and router log detections
  web/           web server access-log detections
yara/            (empty until we have something real)
suricata/        network rules, SID range 8000000-8000999 reserved for Nulvex
wazuh/
  linux/         local rulesets, ID range 100000-100099
  windows/
docs/            deployment notes and the conventions below
```

Empty directories are left empty deliberately. A placeholder rule is worse than no rule.

## Rule index

Each rule links to the analysis that explains it: what the flaw is, who is exposed, and what
the rule can and cannot see. **All of them are untested** (see above).

| Rule | Detects | CVE | Analysis |
|---|---|---|---|
| [net_routeros_mikrotrick_ssh_takeover.yml](sigma/network/net_routeros_mikrotrick_ssh_takeover.yml) | RouterOS log markers and source addresses published by CERT Polska for the MikroTrick SSH takeover | CVE-2026-67279, CVE-2026-86060 | [MikroTrick: the 6.5 that takes over MikroTik routers](https://nulvex.com/research/cve/cve-2026-67279-mikrotik-routeros-mikrotrick-ssh-takeover/) |
| [net_checkpoint_cve_2026_85102_vpn_cert_subjects.yml](sigma/network/net_checkpoint_cve_2026_85102_vpn_cert_subjects.yml) | The three VPN certificate subjects Check Point published for attacks on Spark and Quantum gateways | CVE-2026-85102 | [Check Point VPN certificate flaw](https://nulvex.com/research/cve/cve-2026-85102-check-point-vpn-certificate-rce/) |
| [win_screenconnect_client_vbs_stager_chain.yml](sigma/windows/win_screenconnect_client_vbs_stager_chain.yml) | The ScreenConnect client starting the four VBScript stagers, and the WindowsServiceHost persistence, published by Huntress | CVE-2026-84869 | [ScreenConnect: a support session turned against the technician](https://nulvex.com/research/cve/cve-2026-84869-connectwise-screenconnect-host-file-execution/) |
| [net_ncentral_cve_2026_86218_scan_range.yml](sigma/network/net_ncentral_cve_2026_86218_scan_range.yml) | Traffic from the address range N-able says scanned for the N-central pre-auth flaw | CVE-2026-86218 | [N-central: pre-auth takeover of the MSP server](https://nulvex.com/research/cve/cve-2026-86218-n-able-n-central-pre-auth-rce/) |
| [web_wordpress_pagename_traversal_pearcmd.yml](sigma/web/web_wordpress_pagename_traversal_pearcmd.yml) | Encoded traversal in WordPress `pagename`, the pearcmd follow-up, and the published scanner agents | CVE-2026-87902 | [WordPress core file inclusion](https://nulvex.com/research/cve/cve-2026-87902-wordpress-page-template-file-inclusion/) |
| [web_magento_stylesmuggler_template_styles.yml](sigma/web/web_magento_stylesmuggler_template_styles.yml) | The two published request stages of StyleSmuggler | CVE-2026-75650 | [StyleSmuggler: patching Magento is the easy part](https://nulvex.com/research/cve/cve-2026-75650-magento-stylesmuggler-template-injection/) |
| [web_wordpress_rest_batch_endpoint_post.yml](sigma/web/web_wordpress_rest_batch_endpoint_post.yml) | POSTs to the WordPress REST batch endpoint (hunting) | CVE-2026-63030 | [wp2shell](https://nulvex.com/research/cve/cve-2026-63030-wordpress-wp2shell-batch-route-confusion/) |
| [lnx_cisco_fmc_cve_2026_20079_license_tmp.yml](sigma/linux/lnx_cisco_fmc_cve_2026_20079_license_tmp.yml) | The package_info.pl / license.tmp log line Cisco gives as a sign of FMC exploitation | CVE-2026-20079 | [Cisco FMC authentication bypass](https://nulvex.com/research/cve/cve-2026-20079-cisco-fmc-authentication-bypass/) |
| [lnx_auditd_af_alg_setuid_write_chain.yml](sigma/linux/lnx_auditd_af_alg_setuid_write_chain.yml) | AF_ALG socket followed by splice into kernel crypto | CVE-2026-31431 | [Linux AF_ALG page-cache write](https://nulvex.com/research/cve/cve-2026-31431-af-alg-page-cache-write/) |
| [lnx_auditd_af_alg_socket_creation.yml](sigma/linux/lnx_auditd_af_alg_socket_creation.yml) | AF_ALG socket created by a non-root user | CVE-2026-31431 | [Linux AF_ALG page-cache write](https://nulvex.com/research/cve/cve-2026-31431-af-alg-page-cache-write/) |
| [nulvex_af_alg_socket.xml](wazuh/linux/nulvex_af_alg_socket.xml) | Wazuh version of the AF_ALG socket rules | CVE-2026-31431 | [Linux AF_ALG page-cache write](https://nulvex.com/research/cve/cve-2026-31431-af-alg-page-cache-write/) |
| [app_gitlab_cve_2026_85706_file_path_read.yml](sigma/application/app_gitlab_cve_2026_85706_file_path_read.yml) | Unauthenticated `file.path`/`metadata.path` reads in GitLab's api_json.log, from GitLab's own detection notes | CVE-2026-85706 | [GitLab: patch, then rotate secrets](https://nulvex.com/research/cve/cve-2026-85706-gitlab-unauthenticated-file-read/) |
| [app_python_decompression_bomb_oom.yml](sigma/application/app_python_decompression_bomb_oom.yml) | A Python process killed by the OOM killer after outbound HTTP activity | CVE-2026-21441 | [urllib3 redirect decompression](https://nulvex.com/research/cve/cve-2026-21441-urllib3-redirect-decompression/) |
| [nulvex-cms-oversized-aead-iv.rules](suricata/nulvex-cms-oversized-aead-iv.rules) | CMS AES-GCM parameters with an oversized IV over cleartext SMTP | CVE-2025-15467 | [OpenSSL CMS AEAD IV overflow](https://nulvex.com/research/advisories/cve-2025-15467-openssl-cms-aead-iv-overflow/) |

New rules are added here in the same commit that adds the rule.

## Every rule carries

- **Purpose**, and the CVE or technique it relates to.
- **Expected telemetry** — the log source, the fields, and what must be configured on the
  endpoint for the events to exist at all. A rule that assumes an audit configuration nobody
  has is a rule that never fires and nobody notices.
- **False positives**, honestly. Every rule has some. Where we have not measured a base rate,
  we say so rather than implying we have.
- **Tested status.**
- **References**, to primary sources.

## Deploying

**Measure before you alert.** Several of these rules key on events with a near-zero base
rate, and that is exactly what makes them cheap — but "near-zero on most hosts" is our
reasoning, not a measurement of *your* estate. Run a new rule as a hunt for a week, look at
what it actually catches, then promote it to an alert.

A rule that fires daily gets silenced, and a silenced rule is worse than an absent one
because it looks like coverage on a dashboard.

### Sigma

Convert with [sigma-cli](https://github.com/SigmaHQ/sigma-cli) for your backend. The
correlation rules use the Sigma correlations schema and need a backend that supports it.

### Suricata

SIDs 8000000-8000999 are reserved for Nulvex local rules. If that collides with your own
local range, renumber into your range - never into a vendor's.

### Wazuh

Rule IDs use 100000-100099, which Wazuh reserves for local rules. **If that range is already
in use on your manager, renumber before deploying**: duplicate IDs stop the manager starting,
which is a bad way to find out.

## What we will not do

- **Vendor other projects' rules.** Everything here is ours. We do not copy from SigmaHQ or
  anyone else unless the licence explicitly permits it and attribution is met, and so far we
  have not needed to.
- **Ship a rule that suppresses a scanner finding** rather than detecting the thing.
- **Pad the count.** Fewer rules that state their limits beat a large repository nobody
  trusts.

## Contributing

See `CONTRIBUTING.md`. A rule with no false-positive section will be asked for one.

## Reporting a problem

A false positive or a rule that cannot fire is a **bug**, and we would rather know. Open an
issue with what fired, on what telemetry, and what it should have done.

Security issues in Nulvex systems: <https://nulvex.com/security>.

## Licence

Apache-2.0. Use these in commercial products, modify them, ship them inside something else.
The patent grant is why Apache rather than MIT: nobody deploying detection content in a
commercial SOC should have to think about it.

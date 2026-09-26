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
  windows/       (empty until we have something real)
  application/   application-layer and runtime signals
  network/       (empty until we have something real)
  web/           web server access-log detections
yara/            (empty until we have something real)
suricata/        network rules, SID range 8000000-8000999 reserved for Nulvex
wazuh/
  linux/         local rulesets, ID range 100000-100099
  windows/
docs/            deployment notes and the conventions below
```

Empty directories are left empty deliberately. A placeholder rule is worse than no rule.

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

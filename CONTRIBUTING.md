# Contributing detection content

The bar is the one we hold ourselves to, and it is mostly about honesty rather than skill.

## Every rule must state

1. **Purpose**, and the CVE or technique it relates to.
2. **Expected telemetry**: the log source, the specific fields, and what has to be
   configured on the endpoint for those events to exist. Include the auditd line, the
   Sysmon config, the Wazuh decoder - whatever it depends on. A rule that assumes a
   configuration nobody has is a rule that silently never fires.
3. **False positives.** Every rule has some. If you cannot name one, you have not thought
   about it long enough. Where you have not measured a base rate, say that rather than
   implying you have.
4. **Tested status.** `tested: yes` only if you have run it against telemetry that actually
   contained the thing. Otherwise say so. CI fails a rule declaring neither.
5. **References**, to primary sources - the vendor advisory, the CVE record, the commit.
   Not a blog post summarising one.

## What gets rejected

- A rule with no false-positive section.
- `tested: yes` you cannot evidence.
- Content copied from another project without a licence that permits it and the attribution
  it requires. Say where a rule came from.
- A rule that suppresses a scanner finding instead of detecting the thing.
- Anything naming Nulvex or a client's infrastructure: hostnames, internal addresses,
  topology, or which products are deployed where.

## Numbering

- Suricata: SIDs 8000000-8000999 are the Nulvex local range.
- Wazuh: rule IDs 100000-100099, the range Wazuh reserves for local rules.

Do not renumber into a vendor's range to avoid a collision. Renumber into your own.

## Style

English throughout, including comments and rule descriptions. No em-dashes.

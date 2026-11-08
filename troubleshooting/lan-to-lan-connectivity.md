# Troubleshooting: LAN-to-LAN Connectivity

## Problem

The route-based IPsec tunnel was established and tunnel endpoint pings succeeded, but end devices at HQ and Branch initially could not communicate.

## Initial Static Routes

Branch to HQ:
```text
192.168.10.0/29 → st0.0
192.168.20.0/29 → st0.0
192.168.30.0/29 → st0.0
```

HQ to Branch:
```text
192.168.50.0/29 → st0.0
192.168.60.0/29 → st0.0
```

Using the opposite tunnel endpoint IP (100.0.0.1 / 100.0.0.2) as a static route next hop was also tested, but did not resolve the issue.

## Flow Session Investigation

The captured HQ flow table showed:
- An existing TCP/179 session associated with BGP.
- ESP-related entries for the IPsec peer.
- No user LAN-to-LAN sessions in the captured output.

## Security Zone Finding

The tunnel interface was moved into the `internal` zone on both SRX devices, and the previous HQ `branch` and Branch `hq` zone memberships were removed. HQ did not have an `internal` to `internal` policy matching the forwarded traffic. Branch already had a corresponding policy from earlier work.

## Corrective Action on HQ

```text
set security policies from-zone internal to-zone internal policy internal-internal match source-address any
set security policies from-zone internal to-zone internal policy internal-internal match destination-address any
set security policies from-zone internal to-zone internal policy internal-internal match application any
set security policies from-zone internal to-zone internal policy internal-internal then permit
```

After adding this policy, bidirectional LAN-to-LAN connectivity began working.

## Post-Fix Verification

- HQ-PC3 to 192.168.50.3: 27 replies captured; no timeouts in the sample.
- Branch-PC3 to 192.168.10.3: 22 replies captured; no timeouts in the sample.
- Both tests were manually stopped with Ctrl+C.

## Root-Cause Assessment

The missing HQ intra-zone permit policy was the corrective action that resolved the observed issue, based on the troubleshooting sequence and subsequent successful bidirectional tests.

## Lessons Learned

- A healthy IKE/IPsec control plane does not by itself prove end-to-end LAN connectivity.
- Verify routes to remote LAN prefixes on both gateways.
- Inspect flow sessions when forwarded traffic is not behaving as expected.
- Check the actual ingress/egress zones and whether a matching security policy exists.
- The any/any/any policy is a broad lab troubleshooting measure. Production policies should apply least privilege.

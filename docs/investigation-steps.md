# SOC Investigation Steps (Analyst Guide)

1. Validate alert metadata (source, destination, ports, protocol)
2. Pull firewall connection logs for state sequence
3. Confirm whether connection was:
   - Established (TP candidate)
   - Dropped/Closed (FP)
4. Perform TI enrichment (MISP, VT, AbuseIPDB)
5. Review endpoint logs for suspicious process or network activity
6. Check historical logs for repeated attempts
7. Classify:
   - True Positive → escalate with full evidence
   - False Positive → document and close
8. Update KB/runbook if new patterns observed

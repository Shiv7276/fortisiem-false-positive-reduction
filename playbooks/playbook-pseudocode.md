# SOAR Playbook — Enrich & Ticket (Pseudocode)
**Trigger:** Alert from "Permitted_Traffic_From_Malicious_MISP_IP"

## Objective
Automatically enrich alerts, decide true positive vs false positive, and either create an ITSM ticket or suppress the alert. Keep human analyst in loop for high-confidence actions.

---

## Workflow Steps (detailed)

1. **Ingest alert**
   - Extract flow keys: `src_ip`, `dst_ip`, `src_port`, `dst_port`, `protocol`, `reporting_device`, `time`.

2. **Initial validation**
   - Confirm alert contains required flow keys.
   - If missing keys → escalate to analyst with raw logs.

3. **Threat Intelligence enrichment**
   - Query MISP for `src_ip` tags & confidence
   - Query VirusTotal and AbuseIPDB for `src_ip` reputation
   - Build an enrichment object with scores and metadata

4. **Asset & Context lookup**
   - Query CMDB / Inventory by `dst_ip` to get owner, criticality, OS
   - Check asset tags for “Internet-facing” / “Critical” / “Dev/Test”

5. **Connection history check**
   - Query last 24h logs for the same flow keys
   - Check for `connection_established` vs `connection_closed` patterns
   - Calculate attempt frequency and recurrence

6. **Decision logic**
   - If `TI_score` >= threshold OR `asset.criticality` == High AND `connection_established` found:
     - Mark as **High Confidence True Positive**
     - Proceed to create ITSM ticket and notify SOC
   - Else if `connection_open` followed by `connection_closed/dropped` and TI low:
     - Mark as **Likely False Positive**
     - Suppress alert for this flow for TTL (e.g., 24h) and add a note
   - Else:
     - Mark as **Requires Analyst Review** and create a low-priority ticket with enrichment details

7. **Actions (based on decision)**
   - **True Positive**
     - Create ITSM ticket (attach enrichment, logs)
     - Notify SOC channel (Teams/Slack) with playbook summary
     - Add watchlist tag (e.g., `MalwareLikelyIP`)
   - **False Positive**
     - Add suppression tag & TTL to the alert
     - Add contextual note explaining why FP and update KB
   - **Analyst Review**
     - Create work item with curated data & suggested next steps

8. **Logging & metrics**
   - Record playbook run status, decision, actions taken
   - Emit metrics: `alerts_processed`, `tickets_created`, `alerts_suppressed`, `fp_reduction_estimated`

9. **Post-action**
   - If a true positive, schedule a follow-up automation to collect forensic artifacts (PCAP, endpoint logs)
   - Store playbook run details for audit

---

## Notes / Implementation suggestions
- Use rate-limiting for TI queries to avoid throttling.
- Keep enrichment TTL short (e.g., 6 hours) and refresh for repeat alerts.
- Make thresholds configurable (TI_score, criticality mapping).
- Always keep a human approval step before wide-scale blocking actions.

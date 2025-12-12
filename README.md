# FortiSIEM False-Positive Reduction (MISP Malware IP Use Case)

This project demonstrates how false positives were reduced by **~90%** in FortiSIEM by correlating connection state events.  
Instead of alerting on every attempted connection from a MISP malware IP, this rule triggers **only when the connection actually establishes**, ignoring cases where the firewall drops the traffic.

---

## 🎯 Objective
To design a high-fidelity detection rule that:
- Reduces noise in SOC  
- Prioritizes real threats  
- Improves analyst efficiency  
- Ensures only meaningful alerts are triggered  

---

## 🛠 Tools & Technologies
- **FortiSIEM**
- **Azure Sentinel** (parallel testing / validation with KQL)
- **SOAR (FortiSOAR / Logic Apps)**
- Python (optional for enrichment)
- MISP Threat Intelligence
- Syslog / CEF logs

---

## 📂 Repository Structure

---

## ⚙ Detection Logic Summary
The old rule fired alerts when:
- `connection_open` event occurred  
- **Even if** the firewall immediately blocked it with `connection_closed` / `connection_dropped`  

This caused a high number of false positives.

### ✔ New Rule Logic (High-Level)
Alert **only when**:
- `connection_open` → followed by → `connection_established`

Ignore when:
- `connection_open` → `connection_closed/dropped` happens immediately (firewall block)

This ensures only *successful* malicious connections generate alerts.

---

## 🔍 Investigation Steps
See `docs/investigation-steps.md` for:
- Flow validation  
- Firewall log correlation  
- TI enrichment  
- Endpoint investigation  
- Decision tree (TP/FP)

---

## 🤖 SOAR Automation (Pseudocode)
See `playbooks/playbook-pseudocode.md` for:
- IOC enrichment  
- Lookup steps  
- Auto-ticket creation  
- Analyst notification  
- Suppression logic  

---

## 📊 Results
- False positives reduced by **~90%**
- Analysts regained focus on real threats
- MTTR improved due to reduced noise
- Clean audit trail for detection reasoning

---

## 📈 Future Enhancements
- Add ML/behavior-based correlation  
- Add geo-based suppression rules  
- Extend to other threat intelligence sources  
- Integrate into Sentinel as analytic rule  

---

## 👤 Author
**Shivankur Kulkarni**  
Cybersecurity Engineer — SOC | SIEM | SOAR | Cloud Security  
LinkedIn: <your link here>  

---

> ⚠️ All logs and rule examples in this repository are anonymized and used only for educational purposes.

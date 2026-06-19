### I. SCENARIO & OBJECTIVES

#### 1. Scenario

- Mô tả môi trường hệ thống (web server / AD / endpoint / network)
- Mô tả attacker:
    - Attacker muốn khai thác gì?
    - Vector tấn công (upload, RCE, phishing, brute force...)

#### 2. Attack Objectives

- Initial Access
- Command Execution
- Persistence (nếu có)
- Privilege Escalation (nếu có)
- Discovery
- Data Access
- Exfiltration (nếu có)

#### 3. SOC Objectives

Mục tiêu của SOC trong lab:

- Xác định các log sinh ra từ cuộc tấn công
- Dựng lại chuỗi tấn công từ log
- Thu thập IOC và TTP
- Mapping MITRE ATT&CK
- Xây dựng detection rule
- Kiểm tra khả năng phát hiện của SIEM

### II. PREPARATION (ENVIRONMENT SETUP)

#### Victim side

- Web server / AD / endpoint
- Service đang chạy (IIS, Apache, SSH…)
- Data giả lập (file nhạy cảm)
- User accounts (admin / normal user)

#### Attacker side

- Web shell / payload
- Reverse shell tools
- Scanner (nmap, burp, hydra)
- Exploit tools

### II. ATTACK EXECUTION (SIMULATION)

#### 1. Attack Flow (ghi lại timeline để đối chiếu)

- Initial Access
- Payload Delivery
- Execution
- Persistence (nếu có)
- Privilege Escalation (nếu có)
- Data Access
- Exfiltration (nếu có)

#### 2. Attack Output

- Commands executed
- Screenshots
- PCAP (nếu có)
- Proof of Compromise

#### 3. Ground Truth Timeline

Ghi lại timeline thực tế của cuộc tấn công.

| Time | Action |
| --- | --- |
| HH:MM | Attack Step |
| HH:MM | Attack Step |

### IV. LOG ANALYSIS & ATTACK RECONSTRUCTION

#### 1. Event Identification

Tìm các log tương ứng với từng bước tấn công.

Thu thập:

- Process Creation
- Command Line
- Network Connection
- File Activity
- Authentication Events

---

#### 2. Attack Reconstruction

Dựng lại chuỗi tấn công từ log.

Ví dụ:

```
apache2
 └─ sh
     └─ nc
```

hoặc

```
w3wp.exe
 └─ cmd.exe
      └─ powershell.exe
```

---

#### 3. Timeline Reconstruction

Đối chiếu timeline từ log với timeline thực tế.

---

#### 4. IOC & TTP Collection

**IOC**

- Network: IP, domain, URL, port
- File: filename, file path, file hash
- Host/Endpoint: user, process name, Command line

**MITRE Mapping**

| Technique ID | Technique | Evidence |
| --- | --- | --- |
| Txxxx | Technique |  |

### V. DETECTION ENGINEERING

#### 1. IOC-Based Detection

- IP
- Hash
- Domain
- Filename

→ Rule: ĐK → alert

#### 2. Behavior-Based Detection

- Web server spawning shell
- Reverse shell behavior
- Encoded command execution
- Persistence creation
- Log deletion activities

→ Rule: ĐK → alert

#### 3. Correlation Detection

Nhiều sự kiện kết hợp:

```
Process Execution
+
Network Connection
=
Attack Alert
```

- nhiều alert/rule cùng xảy ra → incident severity cao hơn

#### 4. Detection Validation

Thực hiện lại attack.

Kiểm tra:

- Rule hit hay không
- Alert có sinh ra không
- FP/FN

#### Output required

- SIEM rule (Splunk / ELK)
- IDS rule (Suricata)
- Correlation rule
- Alert generation

### VI. MONITORING & ALERTING

#### Dashboards

- Attack Overview
- Network Activity
- Endpoint Activity
- Incident Timeline

---

#### Alerting rules

- Severity classification:
    - Low / Medium / High / Critical
- Trigger conditions:
    - process anomaly
    - network anomaly
    - login anomaly

---

#### Output required

- Working dashboard
- Real-time alert system

### VII. INCIDENT RESPONSE (EXECUTION PHASE)

#### 1. Detection Validation

- Confirm alert:
    - True Positive or False Positive

---

#### 2. Investigation (Decision Level Only)

đã làm ở IV

Chỉ xác định:

- Severity (Low / Medium / High / Critical)
- Scope:
    - 1 host or multiple hosts
    - user compromised?
    - C2 present?
- Business impact:
    - service affected?
    - data risk?
    - lateral movement?

---

#### 3. Containment (Immediate Action)

**Network containment**

- Block attacker IP
- Firewall rules

**Host containment**

- Isolate endpoint (EDR)
- Disconnect network

**Account containment**

- Disable compromised user
- Reset credentials

---

#### 4. Eradication

- Remove malware / web shell
- Delete persistence mechanisms
- Fix root vulnerability:
    - RCE patch
    - upload fix
- Validate system clean

---

#### 5. Recovery

- Restore services
- Restart systems
- Redeploy clean image
- Re-enable accounts (safe state)
- Post monitoring:
    - watch for reinfection
    - log monitoring

---

#### 6. Incident Report

**Required sections:**

- Summary
- Severity
- Attack timeline (from IV)
- Impact
- Actions taken
- IOC list
- Lessons learned

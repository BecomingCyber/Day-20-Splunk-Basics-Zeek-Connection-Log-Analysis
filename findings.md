# 🔎 Investigation Findings — Zeek Connection Log Analysis

## Investigation Overview

This investigation analyzed 3,000 Zeek connection events using Splunk to identify network communication patterns, high-volume systems, commonly observed services, and long-duration connections.

The analysis used the following Splunk dataset configuration:

- **Source:** `ZeekConnLogFile.json`
- **Host:** `ubuntu`
- **Sourcetype:** `_json`

The findings below are based on the Splunk query results preserved in the `images/` directory.

---

## 🔹 Finding 1 — Most Active Connection Originator

The most active originating host was:

**`192.168.8.94` — 87 connections**

The next most active systems included:

| Originating Host | Connection Count |
|---|---:|
| `192.168.8.94` | 87 |
| `192.168.7.61` | 80 |
| `192.168.8.42` | 74 |
| `192.168.6.81` | 73 |
| `192.168.6.112` | 70 |

### Analysis

`192.168.8.94` generated the highest number of observed connection events.

High connection volume can provide an investigative lead, but connection count alone does not establish malicious behavior. Additional telemetry would be required to determine whether the activity was expected.

**Evidence:** ![Top Client IPs](images/task1.png)

---

## 🔹 Finding 2 — Most Common Network Services

DNS was the most frequently observed service:

**DNS — 395 events**

The observed service distribution included:

| Service | Count |
|---|---:|
| DNS | 395 |
| DHCP | 370 |
| SNMP | 369 |
| NTP | 348 |
| LDAP | 271 |
| SSH | 263 |
| SMTP | 254 |
| FTP | 251 |
| HTTP | 247 |
| IMAP | 232 |

### Analysis

DNS represented the largest service category in the analyzed connection data.

The dataset also contained administrative and application protocols including SSH, SNMP, LDAP, FTP, SMTP, HTTP, and IMAP.

Service frequency provides useful network-baseline information but does not independently indicate malicious activity.

**Evidence:** ![Most Common Services](images/task2.png)

---

## 🔹 Finding 3 — Longest-Duration Connections

Connection events were sorted by duration to identify the longest observed sessions.

The longest visible connection was:

- **Source:** `192.168.5.208`
- **Destination:** `10.0.0.8`
- **Service:** NTP
- **Duration:** `1.999793` seconds

Other high-duration connections visible in the evidence involved IMAP, SMTP, HTTP, DHCP, SNMP, and FTP.

### Analysis

Reviewing connection duration can help identify unusual or persistent communication patterns.

The observed duration alone does not establish malicious activity. Investigation of suspicious long-duration sessions would normally require correlation with additional network, endpoint, or authentication telemetry.

**Evidence:** ![Long Duration Connections](images/task3.png)

---

## 🔹 Finding 4 — Most Frequently Contacted Responding Host

The most frequently contacted responding host was:

**`10.0.0.1` — 284 connections**

The leading destination systems were:

| Responding Host | Connection Count |
|---|---:|
| `10.0.0.1` | 284 |
| `10.0.0.2` | 273 |
| `10.0.0.6` | 268 |
| `10.0.0.10` | 257 |
| `10.0.0.4` | 254 |
| `10.0.0.7` | 252 |
| `10.0.0.3` | 250 |
| `10.0.0.5` | 250 |
| `10.0.0.8` | 245 |
| `10.0.0.12` | 235 |

### Analysis

`10.0.0.1` received the highest number of connections in the analyzed dataset.

Frequently contacted systems may represent important network services, infrastructure, or other high-use resources. Additional context would be necessary before classifying the activity as suspicious.

**Evidence:** ![Most Accessed Servers](images/task4.png)

---

# 🧠 Overall Assessment

The Zeek connection data demonstrates communication between multiple originating systems and responding hosts across several network services.

The analysis established:

- `192.168.8.94` as the most active connection originator.
- DNS as the most frequently observed service.
- `192.168.5.208 → 10.0.0.8` as the longest visible connection in the sorted results.
- `10.0.0.1` as the most frequently contacted responding host.

No individual result reviewed in this investigation is sufficient by itself to classify the observed activity as malicious.

These findings instead establish a network-activity baseline and demonstrate how Zeek telemetry and Splunk can be used to identify systems and communication patterns that warrant further investigation.

---

## 🛡️ Recommended Next Steps

For a real SOC investigation, additional analysis could include:

1. Correlating high-volume hosts with authentication and endpoint telemetry.
2. Investigating unusual services or unexpected source/destination relationships.
3. Reviewing connection frequency over time to identify spikes or periodic behavior.
4. Comparing observed network activity against an established baseline.
5. Correlating suspicious IP addresses with threat intelligence when appropriate.
6. Reviewing Zeek DNS, HTTP, SSL/TLS, and other protocol-specific logs for additional context.

---

## 📁 Evidence

| Evidence | Description |
|---|---|
| `images/task1.png` | Top connection originators |
| `images/task2.png` | Most common services |
| `images/task3.png` | Connection-duration analysis |
| `images/task4.png` | Most frequently contacted responding hosts |

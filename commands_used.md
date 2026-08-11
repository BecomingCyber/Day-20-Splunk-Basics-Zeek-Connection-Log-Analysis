# 🛠️ Commands Used — Zeek Connection Log Analysis

## Overview

This document contains the Splunk Search Processing Language (SPL) queries used during the Day 20 Zeek connection log investigation.

The analysis was performed against 3,000 Zeek connection events using the following dataset configuration:

- **Source:** `ZeekConnLogFile.json`
- **Host:** `ubuntu`
- **Sourcetype:** `_json`

Each query corresponds to evidence preserved in the `images/` directory.

---

## 🔎 Query 1 — Identify the Most Active Originating Hosts

```spl
source="ZeekConnLogFile.json" host="ubuntu" sourcetype="_json"
| stats count by id.orig_h
| sort -count
| head 10
```

### Purpose

This query groups connection events by the Zeek `id.orig_h` field, which represents the originating host.

The results are sorted by connection count to identify systems responsible for the largest number of observed connections.

### Result

The most active originating host was:

**`192.168.8.94` — 87 connections**

**Evidence:** ![Top Client IPs](images/task1.png)

---

## 🔎 Query 2 — Identify the Most Common Services

```spl
source="ZeekConnLogFile.json" host="ubuntu" sourcetype="_json"
| stats count by service
| sort -count
```

### Purpose

This query groups connection events by the Zeek `service` field to determine which network services appeared most frequently in the dataset.

### Result

The most frequently observed service was:

**DNS — 395 events**

Other frequently observed services included DHCP, SNMP, NTP, LDAP, SSH, SMTP, FTP, HTTP, and IMAP.

**Evidence:** ![Most Common Services](images/task2.png)

---

## 🔎 Query 3 — Analyze Connection Duration

```spl
source="ZeekConnLogFile.json" host="ubuntu" sourcetype="_json"
| table ts id.orig_h id.resp_h service duration
| sort -duration
```

### Purpose

This query displays connection timestamps, originating hosts, responding hosts, services, and durations.

The results are sorted by duration to identify the longest observed connection events.

### Result

The longest visible connection in the captured evidence was:

- **Originating Host:** `192.168.5.208`
- **Responding Host:** `10.0.0.8`
- **Service:** NTP
- **Duration:** `1.999793` seconds

**Evidence:** ![Long Duration Connections](images/task3.png)

---

## 🔎 Query 4 — Identify the Most Frequently Contacted Responding Hosts

```spl
source="ZeekConnLogFile.json" host="ubuntu" sourcetype="_json"
| stats count by "id.resp_h"
| sort -count
| head 10
```

### Purpose

This query groups connection events by the Zeek `id.resp_h` field, which represents the responding host.

Sorting the results by count identifies the systems receiving the greatest number of observed connections.

### Result

The most frequently contacted responding host was:

**`10.0.0.1` — 284 connections**

**Evidence:** ![Most Accessed Servers](images/task4.png)

---

# 🧠 SPL Techniques Demonstrated

This investigation demonstrates practical use of several Splunk SPL capabilities:

| SPL Command | Purpose |
|---|---|
| `stats` | Aggregate connection events and calculate counts |
| `count` | Measure the frequency of matching events |
| `sort` | Rank results by count or connection duration |
| `head` | Limit results to the highest-ranked entries |
| `table` | Select relevant fields for investigation |

---

# 🛡️ Investigative Fields

Important Zeek connection fields used during this analysis include:

| Field | Investigative Purpose |
|---|---|
| `id.orig_h` | Originating host IP address |
| `id.resp_h` | Responding host IP address |
| `service` | Identified network service |
| `duration` | Length of the observed connection |
| `ts` | Event timestamp |

These fields allow an analyst to pivot between systems, services, and connection behavior during network investigations.

---

## 📁 Evidence Mapping

| Query | Evidence |
|---|---|
| Top originating hosts | `images/task1.png` |
| Most common services | `images/task2.png` |
| Connection duration analysis | `images/task3.png` |
| Top responding hosts | `images/task4.png` |

---

## 🎯 Investigation Value

Together, these SPL queries provide a repeatable workflow for examining Zeek connection telemetry in Splunk.

The searches can help an analyst establish baseline network behavior, identify high-volume systems, examine service usage, and prioritize connections for deeper investigation.

The results should be correlated with additional network, endpoint, authentication, or threat-intelligence data before classifying activity as malicious.

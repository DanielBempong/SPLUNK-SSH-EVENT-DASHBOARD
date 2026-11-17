# SPLUNK-SSH-EVENT-DASHBOARD
# Splunk SOC Analyst lab investigating Linux SSH authentication activity, failed login attempts, suspicious source IPs, successful logins, IP geolocation, and security dashboard development.

# Splunk SSH Event Dashboard – SOC Analyst Lab

## Project Overview
This project demonstrates a hands-on SOC investigation using Splunk Enterprise to analyze Linux SSH authentication logs, identify repeated failed login attempts, investigate successful SSH logins, enrich source IP addresses with geolocation data, and build an interactive security dashboard.

## Tools & Technologies
- Splunk Enterprise
- Search Processing Language (SPL)
- Linux SSH Authentication Logs
- Linux `auth.log`
- IP Geolocation Enrichment
- Security Dashboarding
- SOC Investigation and Triage

## Lab Environment
- Index: `mydfir-lab1`
- Host: `linuxvm`
- Log Source: `auth.log`
- Sourcetype: `linux_auth_logs`
- Analysis Period: January 7–8, 2024

## Investigation Findings

| Finding | Result |
|---|---:|
| Failed authentication events | 284 |
| Top targeted account | root |
| Failed attempts against root | 240 (84.51%) |
| Leading source IP | 178.128.225.222 |
| Events from leading source IP | 140 (49.30%) |
| Successful SSH password events displayed | 6 |

**Security observation:** The leading failed-authentication source IP also appeared in multiple successful SSH login records. This warrants further investigation but does not independently prove compromise.

## SPL Queries

### 1. Identify Failed SSH Authentication
```spl
index="mydfir-lab1" failed host=linuxvm source="auth.log" sourcetype="linux_auth_logs"
| top user limit=10
```

### 2. Identify Top Source IP Addresses
```spl
index="mydfir-lab1" failed host=linuxvm source="auth.log" sourcetype="linux_auth_logs"
| top src_ip limit=20
```

### 3. Failed Attempts by User
```spl
index="mydfir-lab1" failed host=linuxvm source="auth.log" sourcetype="linux_auth_logs"
| stats count by user
| sort - count
```

### 4. Investigate Successful SSH Logins
```spl
index="mydfir-lab1" host=linuxvm source="auth.log" sourcetype="linux_auth_logs" msg="Accepted password"
| iplocation src_ip
| stats count by _time, Country, user, src_ip
```

### 5. Geographic Network Activity
```spl
index="mydfir-lab1" host=linuxvm
| iplocation src_ip
| stats count by Country
| geom geo_countries allFeatures=True featureIdField=Country
```

## Dashboard Components
The SSH Activity dashboard contains five panels:

1. Top Failed Account
2. Top Source IP
3. Failed Attempts by User
4. Successful Logins by User
5. Heatmap Network Activity

## SOC Analyst Investigation Process
1. Identify repeated SSH authentication failures.
2. Determine the most targeted user accounts.
3. Identify high-volume source IP addresses.
4. Review successful SSH authentication events.
5. Enrich source IP addresses with geolocation.
6. Correlate successful and failed authentication activity.
7. Recommend additional investigation and security controls.

## Recommended Follow-Up
- Investigate source IP `178.128.225.222` across the SSH dataset.
- Determine whether failed attempts were followed by successful authentication for the same account.
- Validate whether direct root SSH access is expected.
- Review successful sessions for suspicious commands, privilege changes, persistence, and outbound connections.
- Document findings and remediation actions.

## Skills Demonstrated
Splunk Enterprise | SPL | Linux Authentication Log Analysis | SSH Investigation | Failed Login Analysis | Source IP Analysis | IP Geolocation | Security Dashboarding | SOC Triage | Incident Investigation

## Project Evidence
See the attached PDF report for investigation screenshots, SPL queries, dashboard visualizations, findings, and the reconstructed lab timeline.


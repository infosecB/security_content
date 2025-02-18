---
title: "Gdrive suspicious file sharing"
excerpt: "Phishing"
categories:
  - Cloud
last_modified_at: 2021-10-24
toc: true
toc_label: ""
tags:
  - Phishing
  - Initial Access
  - Splunk Enterprise
  - Splunk Enterprise Security
  - Splunk Cloud
---

### ⚠️ WARNING THIS IS A EXPERIMENTAL DETECTION
We have not been able to test, simulate or build datasets for it, use at your own risk!


[Try in Splunk Security Cloud](https://www.splunk.com/en_us/cyber-security.html){: .btn .btn--success}

#### Description

This search can help the detection of compromised accounts or internal users sharing potentially malicious/classified documents with users outside your organization via GSuite file sharing .

- **Type**: Hunting
- **Product**: Splunk Enterprise, Splunk Enterprise Security, Splunk Cloud
- **Datamodel**: 
- **Last Updated**: 2021-10-24
- **Author**: Rod Soto, Teoderick Contreras
- **ID**: a7131dae-34e3-11ec-a2de-acde48001122


#### [ATT&CK](https://attack.mitre.org/)

| ID          | Technique   | Tactic         |
| ----------- | ----------- |--------------- |
| [T1566](https://attack.mitre.org/techniques/T1566/) | Phishing | Initial Access |

#### Search

```
`gsuite_drive` name=change_user_access 
| rename parameters.* as * 
| search email = "*@yourdomain.com" target_user != "*@yourdomain.com" 
| stats count values(owner) as owner values(target_user) as target values(doc_type) as doc_type values(doc_title) as doc_title dc(target_user) as distinct_target by src_ip email 
| where distinct_target > 50 
| `gdrive_suspicious_file_sharing_filter`
```

#### Associated Analytic Story
* [Spearphishing Attachments](/stories/spearphishing_attachments)
* [Data Exfiltration](/stories/data_exfiltration)


#### How To Implement
Need to implement Gsuite logging targeting Google suite drive activity. In order for the search to work for your environment please update `yourdomain.com` value in the query with the domain relavant for your organization.

#### Required field
* _time
* src_ip
* parameters.owner
* parameters.target_user
* parameters.doc_title
* parameters.doc_type


#### Kill Chain Phase
* Exploitation


#### Known False Positives
This is an anomaly search, you must specify your domain in the parameters so it either filters outside domains or focus on internal domains. This search may also help investigate compromise of accounts. By looking at for example source ip addresses, document titles and abnormal number of shares and shared target users.





#### Reference

* [https://www.splunk.com/en_us/blog/security/investigating-gsuite-phishing-attacks-with-splunk.html](https://www.splunk.com/en_us/blog/security/investigating-gsuite-phishing-attacks-with-splunk.html)



#### Test Dataset
Replay any dataset to Splunk Enterprise by using our [`replay.py`](https://github.com/splunk/attack_data#using-replaypy) tool or the [UI](https://github.com/splunk/attack_data#using-ui).
Alternatively you can replay a dataset into a [Splunk Attack Range](https://github.com/splunk/attack_range#replay-dumps-into-attack-range-splunk-server)

* [[]]([])



[*source*](https://github.com/splunk/security_content/tree/develop/detections/experimental/cloud/gdrive_suspicious_file_sharing.yml) \| *version*: **1**
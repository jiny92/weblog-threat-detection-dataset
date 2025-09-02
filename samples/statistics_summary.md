# Dataset Statistical Summary

## Basic Dataset Metrics

- Total log entries: 43,615
- Normal logs: 16,718 (38.3%)
- Malicious logs: 26,897 (61.7%)
- Class imbalance ratio: 1.6:1 (malicious:normal)

## IP Address Statistics
- Total unique IPs: 3,198
- Normal IPs: 76 (2.37%)
- Malicious IPs: 3,122 (97.62%)

## Activity Patterns

- Average logs per normal IP: 220.0
- Average logs per malicious IP: 8.6

## HTTP Method Distribution
- GET: 37,665 (86.4%)
- POST: 4,571 (10.5%)
- -: 1,217 (2.8%)
- HEAD: 82 (0.2%)
- PUT: 42 (0.1%)
- OPTIONS: 15 (0.0%)
- DELETE: 10 (0.0%)
- SSTP_DUPLEX_POST: 7 (0.0%)
- PATCH: 6 (0.0%)

## HTTP Status Code Distribution
- 200: 14,743 (33.8%)
- 404: 8,105 (18.6%)
- 302: 7,828 (18.0%)
- 301: 6,715 (15.4%)
- 400: 2,705 (6.2%)
- 304: 2,624 (6.0%)
- 422: 464 (1.1%)
- 401: 168 (0.4%)
- 204: 117 (0.3%)
- 201: 67 (0.2%)
- 502: 35 (0.1%)
- 406: 14 (0.0%)
- 499: 10 (0.0%)
- 500: 10 (0.0%)
- 403: 2 (0.0%)
- 501: 2 (0.0%)
- 413: 1 (0.0%)
- 408: 1 (0.0%)
- 410: 1 (0.0%)

## Log Characteristics
- Average log length (overall): 221.8 characters
- Average normal log length: 237.3 characters  
- Average malicious log length: 212.2 characters
- Log length range: 66 - 7953 characters

## URI Category Distribution
- Other: 20,507 (47.0%)
- API: 13,611 (31.2%)
- Root/Home: 4,373 (10.0%)
- User: 2,828 (6.5%)
- Suspicious: 2,028 (4.7%)
- Admin: 268 (0.6%)

## Temporal Patterns (Relative)
- Peak activity hours: Business hours (9-17)
- Weekend activity: 20.5% of total logs
- Weekday vs Weekend ratio: 3.9:1

## User Agent Categories
- Browser: 28,213 (64.7%)
- Other: 12,002 (27.5%)
- Empty: 2,357 (5.4%)
- Script/Tool: 922 (2.1%)
- Bot/Crawler: 121 (0.3%)

## Attack Pattern Categories (High-level)
- Common Web Attack: 20,987 (78.0%)
- Php Cgi Bin Vulnerability Attempt: 3,371 (12.5%)
- Suspicious Url Access: 2,265 (8.4%)
- Sql Injection Attempt: 272 (1.0%)
- Xss Attempt: 2 (0.0%)

## Attack Complexity
- Single-request attacks: 52.1%
- Multi-request attack sequences: 47.9%

---
*Note: All statistics are anonymized and aggregated to protect sensitive information. 
Actual IP addresses, timestamps, and specific system details are not disclosed.*

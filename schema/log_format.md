# Dataset Schema Documentation

## Log Format Specification

### Custom Format
The dataset uses a **customized Nginx log format** based on Combined Log Format with additional response time field.

### Nginx Log Format Configuration
```nginx
log_format combined_with_response_time '$remote_addr - $remote_user [$time_local] '
                                       '"$request" $status $body_bytes_sent '
                                       '"$http_referer" "$http_user_agent" $request_time';
```

Where:
- `$request_time` = Request processing time in seconds (including response time)

### Actual Format Pattern
```
[IP_ADDRESS] [IDENT] [AUTHUSER] [TIMESTAMP] "[METHOD] [URI] [PROTOCOL]" [STATUS] [BYTES] "[REFERER]" "[USER_AGENT]" [RESPONSE_TIME]
```

### Field Descriptions

| Field | Description | Example | Notes |
|-------|-------------|---------|-------|
| `IP_ADDRESS` | Client IP address | `165.232.185.173` | IPv4 format, anonymized in samples |
| `IDENT` | RFC 1413 identity | `-` | Usually empty (hyphen) |
| `AUTHUSER` | Authenticated username | `-` or `username` | Hyphen if no authentication |
| `TIMESTAMP` | Request timestamp | `[17/Nov/2024:00:56:08 +0900]` | Bracketed, includes timezone offset |
| `METHOD` | HTTP request method | `GET`, `POST`, `PUT` | Standard HTTP verbs |
| `URI` | Request URI and query parameters | `/uploads/-/system/appearance/favicon/1/kafe_logo_fcon.png` | Full path with parameters |
| `PROTOCOL` | HTTP protocol version | `HTTP/1.1`, `HTTP/2.0` | Protocol specification |
| `STATUS` | HTTP response status code | `200`, `404`, `500` | Standard HTTP status codes |
| `BYTES` | Response size in bytes | `1293` or `-` | Hyphen if no content |
| `REFERER` | HTTP referer header | `""` or `"https://example.com"` | Quoted, may be empty string |
| `USER_AGENT` | Client user agent string | `"Go-http-client/1.1"` | Quoted browser/tool info |
| `RESPONSE_TIME` | Server response time | `0.045` or `-` | **Custom field**: Server processing time in seconds, hyphen if unavailable |

### Format Customization Notes

**Modified from Standard CLF:**
- Added response time field as the 12th field
- Maintains compatibility with standard log parsing tools
- Custom Apache configuration required for log generation
- Response time measured server-side (excludes network latency)

**Common URI Patterns:**
- `/api/v4/*` - GitLab API endpoints
- `/users/sign_in` - Authentication endpoints  
- `/admin/*` - Administrative interfaces
- `/-/health_check` - System health monitoring
- `/projects/*` - Repository and project access

**Authentication States:**
- Authenticated requests include username in AUTHUSER field
- Unauthenticated requests show `-` in AUTHUSER field
- API requests may include token-based authentication

### Data Types and Constraints

| Field | Type | Max Length | Required | Validation |
|-------|------|------------|----------|------------|
| IP_ADDRESS | IPv4/IPv6 | 45 chars | Yes | Valid IP format |
| TIMESTAMP | DateTime | 30 chars | Yes | CLF timestamp format |
| METHOD | String | 20 chars | Yes | HTTP method verbs |
| URI | String | 8000 chars | Yes | URI-encoded path |
| STATUS | Integer | 3 digits | Yes | HTTP status codes (100-599) |
| BYTES | Integer | 10 digits | No | Non-negative, `-` if empty |
| USER_AGENT | String | 1000 chars | No | Quoted string, may be `-` |
| RESPONSE_TIME | Float | 10 chars | No | Seconds, `-` if unavailable |

### Encoding and Character Set
- **Character Encoding**: UTF-8
- **Line Termination**: Unix-style (`\n`)
- **URI Encoding**: Percent-encoded for special characters
- **Quote Handling**: Double quotes escaped as `\"`

### Sample Log Entries

#### Normal Request Example
```
XXX.XXX.XXX.XXX - user123 [17/Nov/2024:10:30:45 +0900] "GET /api/v4/projects HTTP/1.1" 200 1024 "https://gitlab.example.com/dashboard" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36" 0.125
```

#### Malicious Request Example  
```
XXX.XXX.XXX.XXX - - [17/Nov/2024:11:15:22 +0900] "POST /login HTTP/1.1" 200 512 "" "curl/7.68.0" 0.034
```

#### Static Resource Request Example
```
XXX.XXX.XXX.XXX - - [17/Nov/2024:00:56:08 +0900] "GET //uploads/-/system/appearance/favicon/1/kafe_logo_fcon.png HTTP/1.1" 200 1293 "" "Go-http-client/1.1" -
```

### Sequence Construction

**Sequence Parameters:**
- **Window Size (Lw)**: Maximum 3 logs per sequence
- **Time Window (Tw)**: Variable (1-300 seconds depending on scenario)
- **Grouping**: By source IP address
- **Ordering**: Chronological by timestamp

**Sequence Labeling:**
- **Binary Classification**: 0 (normal) or 1 (malicious)
- **Label Assignment**: Based on source IP classification
- **Consistency**: All logs from same IP receive same label

### Data Quality Characteristics

**Log Completeness:**
- All fields present (may contain `-` for empty values)
- No truncated or malformed entries in final dataset
- Consistent timestamp formatting throughout

**Attack Pattern Representation:**
- Payloads preserved in URI field
- No sanitization of malicious content
- Original attack strings maintained for analysis

**IP Diversity:**
- 3,198 unique source IP addresses
- Geographic distribution across multiple regions
- Mix of residential, commercial, and cloud provider IPs

### Privacy and Anonymization

**Applied in Samples:**
- IP addresses masked as `XXX.XXX.XXX.XXX`
- Usernames anonymized or replaced with generic identifiers
- Sensitive paths generalized (e.g., `/path/[SANITIZED]`)

**Preserved for Analysis:**
- Attack payload patterns maintained
- HTTP method and status code distributions
- Temporal relationship patterns
- Request/response size characteristics

---

*This schema represents the structure of production web server logs used in the study. Actual log entries contain sensitive information and cannot be shared publicly.*
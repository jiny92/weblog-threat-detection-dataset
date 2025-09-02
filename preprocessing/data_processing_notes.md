# Data Processing Notes

## Overview
This document describes the data processing pipeline used to prepare web logs for threat detection analysis. While the actual dataset cannot be shared due to security constraints, this methodology can be applied to similar web log environments.

## Data Collection

### Log Source
- **Application**: GitLab Community Edition
- **Web Server**: Nginx
- **Log Type**: Access logs with custom response time field
- **Collection Period**: One month of continuous operation
- **Environment**: Production web application server

### Custom Log Configuration
```nginx
# Nginx configuration used
log_format combined_with_response_time '$remote_addr - $remote_user [$time_local] '
                                       '"$request" $status $body_bytes_sent '
                                       '"$http_referer" "$http_user_agent" $request_time';

access_log /var/log/nginx/access.log combined_with_response_time;
```

## LLM-Assisted Labeling Process

### Initial Data Preparation
1. **Raw Log Collection**: Collect complete log entries without parsing
2. **IP Analysis**: 
   - Extract unique source IP addresses from raw logs
   - Analyze user account information and usage history
   - Identify legitimate (benign) IPs based on established usage patterns
   - Create whitelist of known legitimate IP addresses
3. **Batch Processing**: Group complete log entries into manageable chunks (500 logs per batch)

### LLM Analysis Workflow
```
INPUT: Raw log file + whitelist of known legitimate IPs
OUTPUT: Cleaned log file + threat IP list file

PROCESS:
1. Assume presence of threats
2. Iterate until termination conditions met:
   a. Read log file in batches (500 logs per batch)
   b. Submit batch to LLM for analysis
   c. LLM classifies threats with severity levels
   d. Record identified malicious IPs
   e. Filter logs to remove malicious entries
   f. Continue with remaining logs
   
3. Termination conditions:
   - No additional threats detected in current batch, OR
   - Remaining logs < batch size (500)
   
4. Final processing:
   - Apply manual labeling to remaining unprocessed logs
   - Validate edge cases and ambiguous entries
   - Complete final threat IP list
```

### LLM Configuration
- **Model Used**: Commercial LLM (Company A) - selected for accuracy and low hallucination rate
- **Severity Levels**: Critical, High, Medium, Low
- **Batch Size**: 500 logs (optimal balance between context and efficiency)
- **Validation**: Human intervention for edge cases and API failures
- **Note**: Performance varies significantly between different commercial LLM providers. Model selection should be validated through pilot testing on representative data samples.

### Quality Assurance
- **False Positive Handling**: Cross-validation with OSSEC logtest
- **Reliability Assessment**: Wilson Score Interval calculation (93.37% confidence)
- **Manual Review**: Sophisticated distributed attacks and ambiguous cases

## Sequence Construction

### IP-Based Grouping
- **Primary Key**: Source IP address
- **Temporal Ordering**: Chronological by timestamp
- **Label Inheritance**: All sequences from malicious IPs labeled as malicious

### Window-Based Approaches

#### Parameters
- **Window Size (Lw)**: 1-3 logs per sequence
- **Time Window (Tw)**: 1-300 seconds depending on scenario
- **Maximum Sequence Length**: 3 logs (due to BERT 512-token limitation)

#### Scenarios
1. **Immediate Processing**: Lw=1 (single log analysis)
2. **Near Real-time**: Sliding window, Lw=3, Tw=300s
3. **Soft Real-time**: Non-overlapping, Lw=3, Tw=1s  
4. **Batch Processing**: Non-overlapping, Lw=3, Tw=300s

## Data Preprocessing

### Raw Log Processing
- **No Parsing**: Complete log entries processed without field extraction
- **Encoding**: UTF-8 throughout pipeline
- **URI Handling**: Preserve original attack payloads in full log context
- **Special Characters**: No sanitization of malicious content
- **Token Limits**: Sequence length constrained by BERT limitations

### BERT Input Preparation
- **Log Concatenation**: Multiple complete logs joined with [SEP] tokens
- **BERT Tokenization**: WordPiece tokenization applied to entire log strings
- **Embedding Generation**: 768-dimensional vectors reduced to 256-dimensional
- **Sequence Padding**: Variable-length sequences handled consistently

## Data Quality Metrics

### Completeness
- **Missing Fields**: Less than 0.1% of logs had missing fields
- **Timestamp Coverage**: Complete temporal continuity
- **IP Coverage**: All requests include source IP information

### Balance and Distribution  
- **Class Balance**: 61.7% malicious, 38.3% normal logs
- **IP Diversity**: 3,198 unique IPs (97.6% malicious, 2.4% normal)
- **Geographic Spread**: Multiple regions and network providers represented

### Attack Diversity
- **Common Web Attacks**: 78.0% of malicious logs
- **SQL Injection**: 1.0% of malicious logs  
- **XSS Attempts**: <0.1% of malicious logs
- **Directory Traversal**: 8.4% (Suspicious URL Access)
- **Application-Specific**: 12.5% (PHP CGI-bin vulnerabilities)

## Validation and Ground Truth

### Cross-Validation with OSSEC
- **Reference System**: OSSEC logtest for severity level 6+ threats
- **Comparison Results**: LLM identified same threats plus additional patterns
- **False Positive Analysis**: 5 IPs flagged by OSSEC were confirmed as false positives

### Statistical Validation
- **Wilson Score Interval**: 93.37% reliability at 95% confidence level
- **Sample Size**: 54 confirmed threat IPs for validation
- **Detection Rate**: 100% recall on confirmed threats

## Reproducibility Guidelines

### For Similar Environments
1. **Configure custom log format** with response time field
2. **Implement LLM-assisted labeling** with batch processing
3. **Apply IP-based sequence construction** with temporal constraints
4. **Process complete raw logs** without field parsing
5. **Use statistical validation** to assess labeling quality

### Required Adaptations
- **Log Format**: Ensure consistent format across all log entries
- **LLM Prompts**: Customize threat detection criteria for specific applications
- **Sequence Parameters**: Optimize Lw and Tw for target response time requirements
- **Raw Log Processing**: Adapt BERT tokenization for different log structures
- **Validation Method**: Adapt ground truth comparison to available reference systems

## Limitations and Considerations

### Data Constraints
- **Single Application**: Limited to GitLab/Nginx environment
- **Geographic Limitation**: Data collected from single organizational deployment
- **Temporal Scope**: One-month collection period
- **Language Limitation**: Primarily English-language attacks

### Technical Constraints  
- **Token Limits**: BERT 512-token restriction affects sequence length

### Generalization Factors
- **Attack Patterns**: Web attack techniques are generally application-agnostic
- **HTTP Protocol**: Standard web protocols ensure broad applicability  
- **Log Structure**: Common log formats facilitate adaptation to other environments
- **Methodology**: LLM-assisted labeling applicable to any log type

---

*Note: This methodology represents a production-tested approach to web log threat detection. While specific implementation details may vary, the core principles are broadly applicable to similar web application environments.*
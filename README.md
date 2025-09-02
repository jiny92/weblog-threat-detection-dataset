# Web Log Threat Detection Dataset Documentation

## Overview

This repository contains dataset schema, samples, and documentation for web log threat detection research. The dataset represents real-world web access logs from a production GitLab and Nginx environment, labeled for threat detection using LLM-assisted annotation.

## Dataset Summary

- **Total log entries**: 43,615
- **Normal logs**: 16,718 (38.3%)
- **Malicious logs**: 26,897 (61.7%)
- **Unique IP addresses**: 3,198
- **Collection period**: 1 month
- **Environment**: Production GitLab + Nginx

## Why Proprietary Data?

Public security datasets often suffer from:
- **Synthetic attack patterns** lacking real-world complexity
- **Missing temporal correlations** between attack stages
- **Outdated threat signatures** not reflecting current attack methods
- **Lack of enterprise context** (authentication flows, API usage patterns)

Our dataset provides authentic attack sequences, production environment complexity, and contemporary threat patterns from active attackers.

## Repository Structure

```
dataset-schema/
├── README.md                     # This navigation guide
├── schema/
│   └── log_format.md            # Detailed log format specification
├── samples/
│   ├── normal_samples.txt       # Legitimate activity examples
│   ├── malicious_samples.txt    # Attack pattern examples
│   └── statistics_summary.md    # Complete statistical analysis
└── preprocessing/
    └── data_processing_notes.md # Full technical methodology
```

## Quick Start

1. **Understanding the Data**: Review `schema/log_format.md` for log structure
2. **See Examples**: Check `samples/` for realistic attack and normal patterns
3. **Get Statistics**: Full dataset analysis in `samples/statistics_summary.md`
4. **Implement Methodology**: Complete technical guide in `preprocessing/data_processing_notes.md`

## Key Dataset Features

### Attack Sophistication
- **Multi-request sequences**: 47.9% of attacks span multiple requests
- **Temporal patterns**: Attack sequences from subseconds to minutes
- **Real-world complexity**: Production environment authentication and API flows

### Attack Categories
- Common Web Attack (78.0%)
- PHP CGI-bin Vulnerability (12.5%)
- Suspicious URL Access (8.4%)
- SQL Injection (1.0%)
- XSS Attempts (0.1%)

*Complete breakdown available in `samples/statistics_summary.md`*

### Behavioral Realism
- **Normal users**: Average 220 logs per IP (legitimate session patterns)
- **Attackers**: Average 8.6 logs per IP (scanning behavior)
- **Enterprise patterns**: 31.2% API traffic typical for GitLab environments

## Methodology Overview

### LLM-Assisted Labeling
- Commercial LLM with 93.37% validated reliability
- Iterative batch processing (500 logs per batch)
- Human oversight for edge cases
- Cross-validation with OSSEC rule-based system

*Full implementation details in `preprocessing/data_processing_notes.md`*

### Sequence Construction
- IP-based temporal grouping
- Window sizes: 1-3 logs per sequence
- Four processing scenarios: Immediate, Near-RT, Soft-RT, Batch
- Temporal windows: 1-300 seconds depending on use case

*Technical specifications in `preprocessing/data_processing_notes.md`*

## Data Protection

### Anonymization
- IP addresses masked (XXX.XXX.XXX.XXX)
- Domain names generalized (example.org)
- Attack payloads preserved for pattern recognition
- System-specific details removed

### Security Considerations
While samples contain standard attack patterns from security literature, researchers should handle attack payloads responsibly and not use for malicious purposes.

## Reproducibility

### What's Provided
- **Complete methodology** for dataset creation
- **LLM labeling workflows** with validation procedures  
- **Sequence construction algorithms** with parameters
- **Statistical validation methods** including reliability assessment
- **Sample data** demonstrating pattern complexity

### What's Not Shared
Raw production logs cannot be released due to security constraints, but the methodology enables researchers to:
1. Apply the same techniques to their own log data
2. Validate results using similar statistical methods
3. Reproduce the labeling and sequence construction processes

## Limitations

- **Single environment**: GitLab/Nginx specific patterns
- **Geographic scope**: Primarily single organizational deployment  
- **Temporal scope**: One-month collection period
- **Language**: Primarily English-language attacks

*Detailed limitations and considerations in `preprocessing/data_processing_notes.md`*

## Contact

For questions regarding the dataset or methodology:
- **Corresponding Author**: Jinyong Jo (jiny92@kisti.re.kr)
- **Institution**: KREONET Center, Korea Institute of Science and Technology Information

## Citation

If you use this dataset documentation or methodology in your research:

```bibtex
@misc{weblog_threat_dataset_2024,
  title={Web Log Threat Detection Dataset Documentation},
  author={Jo, Jinyong and Jang, Minseok and Kim, Dongkyun and Cho, Buseung},
  year={2024},
  institution={KREONET Center, Korea Institute of Science and Technology Information}
}
```

---

**Note**: This dataset documentation supports ongoing threat detection research. The methodology and samples are provided for academic research purposes.
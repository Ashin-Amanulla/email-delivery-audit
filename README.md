# Email Deliverability Audit Process Guide

This guide outlines the process of conducting an email deliverability audit and generating the deliverability report. It's designed for both developers and operations staff to ensure consistent email delivery monitoring and improvement.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Detailed Audit Process](#detailed-audit-process)
- [Tools & Resources](#tools--resources)
- [Interpreting Results](#interpreting-results)
- [Taking Action](#taking-action)
- [Regular Monitoring](#regular-monitoring)

## Prerequisites

### Required Access

- DNS management console access
- Email service provider (ESP) admin access
- Google Postmaster Tools access
- MXToolbox account (or similar)
- DMARC aggregate report access

### Tools Installation

```bash
# Install required tools
npm install -g mailcheck-cli
npm install -g email-verifier
pip install mail-tester-cli
```

## Quick Start

1. **Basic Health Check**

```bash
# Quick domain health check
mailcheck audit example.com --quick

# Generate preliminary report
mailcheck report --output preliminary.html
```

2. **View Report**

```bash
# Start local server to view report
python -m http.server 8000
# Visit http://localhost:8000
```

## Detailed Audit Process

### 1. DNS Records Verification

```bash
# Check SPF record
dig TXT example.com

# Check DKIM
dig TXT selector._domainkey.example.com

# Check DMARC
dig TXT _dmarc.example.com

# Verify BIMI (if implemented)
dig TXT default._bimi.example.com
```

#### Expected Results

- SPF: `v=spf1 include:_spf.google.com ~all`
- DKIM: `v=DKIM1; k=rsa; p=MIGfMA0...`
- DMARC: `v=DMARC1; p=none; rua=mailto:dmarc@example.com`

### 2. Reputation Monitoring

#### Google Postmaster Tools

1. Access [Google Postmaster Tools](https://postmaster.google.com/)
2. Check following metrics:
   - Domain reputation
   - IP reputation
   - Authentication results
   - Spam rate

#### Other Reputation Tools

```bash
# Check multiple blacklists
mxtoolbox check example.com

# Get SenderScore
senderscore check example.com

# Check Talos reputation
talos-check example.com
```

### 3. Inbox Placement Testing

```bash
# Setup test accounts
./setup-seed-accounts.sh

# Send test emails
./send-test-emails.sh

# Check placement
./check-placement.sh
```

Test across major providers:

- Gmail
- Outlook
- Yahoo
- Zoho

### 4. DMARC Analysis

```bash
# Aggregate DMARC reports
dmarc-aggregate --days 30 --output dmarc-report.json

# Analyze authentication results
dmarc-analyze dmarc-report.json
```

Key metrics to monitor:

- Pass/fail rate
- Alignment issues
- Unknown sources

### 5. Content & Headers Analysis

```bash
# Analyze recent emails
email-analyze --last 100 --check-headers --check-content

# Check spam triggers
spam-check content.txt
```

## Tools & Resources

### Required Tools

- [MXToolbox](https://mxtoolbox.com/) - DNS and blacklist checking
- [Google Postmaster Tools](https://postmaster.google.com/) - Gmail delivery data
- [Mail-Tester](https://www.mail-tester.com/) - Email testing
- [DMARC Analyzer](https://dmarc.postmarkapp.com/) - DMARC report analysis

### Optional Tools

- [GlockApps](https://glockapps.com/) - Inbox placement testing
- [250ok](https://250ok.com/) - Advanced monitoring
- [ReturnPath](https://returnpath.com/) - Deliverability tools

## Interpreting Results

### Health Score Calculation

```python
health_score = (
    authentication_score * 0.3 +
    reputation_score * 0.3 +
    inbox_placement_score * 0.25 +
    compliance_score * 0.15
)
```

### Benchmark Values

- Delivery Rate: >95% excellent, 90-95% good, <90% needs improvement
- Bounce Rate: <2% excellent, 2-5% concerning, >5% critical
- Spam Rate: <0.1% excellent, 0.1-1% good, >1% poor
- Authentication: >98% excellent, 95-98% good, <95% poor

## Taking Action

### Critical Issues (24-48h Response)

- Blacklist listings
- Authentication failures
- Major provider blocks
- Spam folder placement

### High Priority (1 Week)

- DMARC policy updates
- SPF/DKIM misalignment
- High bounce rates
- Compliance gaps

### Medium Priority (2 Weeks)

- Minor authentication issues
- Content optimization
- Documentation updates

### Low Priority (1 Month)

- BIMI implementation
- Optional security enhancements
- Process documentation

## Regular Monitoring

### Daily Checks

```bash
# Run daily health check
./daily-check.sh

# Monitor key metrics
./monitor-metrics.sh
```

### Weekly Tasks

1. Review DMARC reports
2. Check blacklist status
3. Monitor bounce rates
4. Update action items

### Monthly Tasks

1. Full audit report generation
2. Compliance review
3. Policy updates
4. Team review meeting

## Report Generation

```bash
# Generate full report
./generate-report.sh --full

# Update specific sections
./update-report.sh --section dns
./update-report.sh --section reputation
./update-report.sh --section delivery
```

### Customizing the Report

1. Edit `template.html` for layout changes
2. Modify `style.css` for styling
3. Update `config.json` for thresholds and scoring

## Contributing

1. Follow the standard PR process
2. Include test results
3. Update documentation
4. Add changelog entry

## Support

For internal support:

- Email: devops@example.com
- Slack: #email-deliverability
- Wiki: Internal/EmailDeliverability

## License

Internal use only. Do not distribute.

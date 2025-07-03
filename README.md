# TestSplunkApp

A Splunk application for collecting and analyzing VPN logs, authentication logs, and purchase transaction data.

## Overview

TestSplunkApp is a simple Splunk application designed for testing and monitoring various system activities including:
- VPN connection logs
- Authentication server logs  
- Purchase transaction logs with credit card data masking

## Features

- **VPN Log Monitoring**: Tracks user VPN connections and disconnections
- **Authentication Logging**: Monitors authentication server activities
- **Purchase Transaction Tracking**: Records purchase details with automatic credit card number masking
- **Custom Field Extraction**: Automatically extracts relevant fields from log data
- **Real-time Data Collection**: Configurable data collection intervals

## Installation

1. Extract the app to your Splunk apps directory:
   ```
   $SPLUNK_HOME/etc/apps/TestSplunkApp/
   ```

2. Restart Splunk to load the application:
   ```
   $SPLUNK_HOME/bin/splunk restart
   ```

3. The app will appear in your Splunk web interface under "Apps"

## Configuration

### Data Sources

The app is configured to collect data from three main sources:

#### VPN Logs
- **Script**: `bin/vpnlogs`
- **Index**: main
- **Source**: vpn
- **Sourcetype**: vpn_logs
- **Interval**: 5 seconds

#### Authentication Logs
- **Script**: `bin/authentication_logs`
- **Index**: main
- **Sourcetype**: auth_logs
- **Host**: auth_server
- **Interval**: 5 seconds

#### Purchase Logs
- **Script**: `bin/purchase-details`
- **Index**: main
- **Source**: purchase_logs
- **Sourcetype**: purchase_logs
- **Host**: order_server
- **Interval**: 5 seconds

### Custom Fields

The following fields are automatically extracted and indexed:

- **Username**: User identifier from logs
- **Server**: Server information from VPN logs
- **Action**: Action performed (CONNECT/DISCONNECT)
- **Credit-Card**: Masked credit card numbers from purchase logs

### Data Processing

- **Credit Card Masking**: Credit card numbers are automatically masked to show only the last 4 digits (format: -XXXX-XXXX-XXXX)
- **Line Breaking**: Configured for proper event separation based on log patterns
- **Field Extraction**: Custom regex patterns extract relevant information into searchable fields

## Usage

### Searching VPN Logs
```spl
index=main sourcetype=vpn_logs
| table _time Username Server Action
```

### Searching Authentication Logs
```spl
index=main sourcetype=auth_logs
| table _time Username
```

### Searching Purchase Logs
```spl
index=main sourcetype=purchase_logs
| table _time Username Credit_Card
```

### Common Searches

**Monitor VPN Connections:**
```spl
index=main sourcetype=vpn_logs Action=CONNECT
| stats count by Username, Server
```

**Track Purchase Activity:**
```spl
index=main sourcetype=purchase_logs
| stats count by Username
| sort -count
```

## File Structure

```
TestSplunkApp/
├── bin/
│   └── README.txt
├── default/
│   ├── app.conf
│   ├── fields.conf
│   ├── inputs.conf
│   ├── props.conf
│   └── transforms.conf
└── .gitattributes
```

## Security Features

- **Credit Card Protection**: Automatic masking of credit card numbers in purchase logs
- **Field Indexing**: Controlled indexing of sensitive fields
- **Data Sanitization**: Regex-based data cleaning for security compliance

## Requirements

- Splunk Enterprise or Splunk Cloud
- Appropriate permissions to install apps
- Access to the configured data sources

## Version Information

- **Version**: 1.0.0
- **Author**: RCGAProds
- **Description**: Simple App for testing

## Scripts

Place any additional scripts in the `bin/` directory. The app expects the following scripts to be present:
- `vpnlogs`
- `authentication_logs`
- `purchase-details`

## Troubleshooting

1. **App not visible**: Ensure `is_visible = 1` in app.conf
2. **No data ingestion**: Check script permissions and paths in inputs.conf
3. **Field extraction issues**: Verify regex patterns in transforms.conf
4. **Line breaking problems**: Review props.conf settings for your log format

## Support

For issues or questions regarding this Splunk app, please contact the development team.

## License

This project is provided as-is for testing and educational purposes.

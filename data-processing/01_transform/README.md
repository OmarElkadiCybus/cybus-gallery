# Transform Rule: Reshape Your Data

## What This Does
**Transform rule** reshapes your message data into any JSON structure you want. Rename fields, add calculations, include timestamps, or completely restructure your data.

**Quick Example:**
```yaml
# Turn {"temp": 25, "device": "sensor-01"} 
# Into {"temperature": 25, "device_name": "sensor-01", "unit": "celsius"}
- transform:
    expression: |
      {
        "temperature": $.temp,      # Rename field
        "device_name": $.device,    # Rename field  
        "unit": "celsius",          # Add new field
        "timestamp": $now()         # Add timestamp
      }
```

## Quick Setup
1. **Deploy the service**: `docker exec -it connectware-container cybus-ctl commissioning-file apply service.scf.yaml`
2. **Send test data**: Publish to input topics (see examples below)
3. **Check results**: Subscribe to output topics to see transformed data

## Key Examples

### **1. Field Renaming** (`sensors/raw` → `sensors/renamed`)
```yaml
# Input: {"temp": 25, "hum": 60, "id": "sensor-01", "loc": "warehouse"}
- transform:
    expression: |
      {
        "temperature": $.temp,     # temp → temperature
        "humidity": $.hum,         # hum → humidity
        "device_id": $.id,         # id → device_id
        "location": $.loc          # loc → location
      }
# Output: {"temperature": 25, "humidity": 60, "device_id": "sensor-01", "location": "warehouse"}
```

### **2. Temperature Conversion** (`temperature/fahrenheit` → `temperature/converted`)
```yaml
# Input: {"temp": 72, "sensor": "room-01"}
- transform:
    expression: |
      {
        "fahrenheit": $.temp,
        "celsius": ($.temp - 32) * 5/9,
        "kelvin": (($.temp - 32) * 5/9) + 273.15,
        "sensor": $.sensor
      }
# Output: {"fahrenheit": 72, "celsius": 22.22, "kelvin": 295.37, "sensor": "room-01"}
```

### **3. Data Enrichment** (`devices/reading` → `devices/enriched`)
```yaml  
# Input: {"value": 150, "sensor": "temp-01"}
- transform:
    expression: |
      {
        "original_data": $,           # Keep all original data
        "timestamp": $now(),          # Add current time
        "source": "sensor-network",   # Add static value
        "is_valid": $.value > 0,      # Add validation
        "processor": "cybus"          # Add metadata
      }
```

### **4. Conditional Status** (`sensors/battery` → `alerts/battery_status`)
```yaml
# Input: {"device": "sensor-01", "battery": 85}
- transform:
    expression: |
      {
        "device_id": $.device,
        "battery_level": $.battery,
        "status": $.battery > 80 ? "excellent" : 
                 $.battery > 50 ? "good" :
                 $.battery > 20 ? "low" : "critical",
        "needs_replacement": $.battery < 20,
        "alert_priority": $.battery < 20 ? "high" : "low"
      }
```

## JSONata Reference

| Operation | Syntax | Example |
|-----------|--------|---------|
| **Get field** | `$.field` | `$.temperature` |
| **Get nested** | `$.field.subfield` | `$.device.name` |
| **Math** | `$.a + $.b * 2` | `($.temp - 32) * 5/9` |
| **Conditions** | `condition ? "true" : "false"` | `$.battery > 20 ? "ok" : "low"` |
| **Timestamp** | `$now()` | `"timestamp": $now()` |
| **Keep all** | `$` | `"original": $` |

## Testing Your Transforms

**MQTT Commands:**
```bash
# Send test data
mosquitto_pub -h localhost -t "sensors/raw" -m '{"temp": 25, "hum": 60, "id": "sensor-01"}'

# Check transformed output  
mosquitto_sub -h localhost -t "sensors/renamed"
```

**Expected Output:**
```json
{"temperature": 25, "humidity": 60, "device_id": "sensor-01", "location": "warehouse"}
```
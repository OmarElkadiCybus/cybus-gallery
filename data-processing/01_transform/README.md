# Transform Rules: Data Conversion & Restructuring

# Transform Rule Tutorial

> **Purpose**: Convert and manipulate data formats using JSONata expressions  
> **Prerequisites**: Basic understanding of JSON data structures## What You'll Learn

By the end of this tutorial, you'll understand:
- ✅ **Field renaming and mapping** between different data formats
- ✅ **Mathematical calculations** for unit conversions and derived values  
- ✅ **Data enrichment** by adding timestamps, metadata, and computed fields
- ✅ **Conditional logic** for status determination and data validation
- ✅ **JSONata expressions** for powerful data manipulation

## The Problem: Data Format Mismatches

Raw sensor data often doesn't match the format your applications need:

```json
// ❌ Raw sensor format (abbreviated field names, missing metadata)
{"temp": 25, "hum": 60, "id": "sensor-01", "loc": "warehouse"}

// ✅ Required application format (descriptive names, enriched data)
{
  "temperature": 25,
  "humidity": 60, 
  "device_id": "sensor-01",
  "location": "warehouse",
  "timestamp": "2025-10-31T10:30:00Z",
  "unit": "celsius"
}
```

**Solution**: Transform rules reshape data into the exact format you need.

## Step 1: Basic Field Renaming

Start with simple field mapping - the most common transform use case:

```yaml
rules:
- transform:
    expression: |
      {
        "temperature": $.temp,     # Rename temp → temperature
        "humidity": $.hum,         # Rename hum → humidity  
        "device_id": $.id,         # Rename id → device_id
        "location": $.loc          # Rename loc → location
      }
```

**Example Flow:**
- **Input**: `{"temp": 25, "hum": 60, "id": "sensor-01", "loc": "warehouse"}`
- **Output**: `{"temperature": 25, "humidity": 60, "device_id": "sensor-01", "location": "warehouse"}`

## Step 2: Data Enrichment

Add new fields with timestamps, metadata, and computed values:

```yaml
rules:
- transform:
    expression: |
      {
        "original_data": $,           # Keep all original data
        "timestamp": $now(),          # Add current timestamp
        "source": "sensor-network",   # Add static metadata  
        "is_valid": $.value > 0,      # Add validation flag
        "processing_info": {
          "processor": "cybus-connectware",
          "version": "1.0"
        }
      }
```

**Example Flow:**
- **Input**: `{"value": 150, "sensor": "temp-01"}`
- **Output**: 
```json
{
  "original_data": {"value": 150, "sensor": "temp-01"},
  "timestamp": 1730203200000,
  "source": "sensor-network", 
  "is_valid": true,
  "processing_info": {"processor": "cybus-connectware", "version": "1.0"}
}
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
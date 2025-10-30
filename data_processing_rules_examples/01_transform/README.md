# Transform Rule: Simple Examples

The `transform` rule changes your message data into a new format. Think of it as **reshaping your data** exactly how you want it.

## What Transform Does

**Simple Concept**: Take your input message and create a completely new message structure with the data you want.

```yaml
# Transform your data into any structure you want
- transform:
    expression: |
      {
        "new_field": $.old_field,           # Rename fields
        "calculated": $.value * 2,          # Do calculations
        "timestamp": $now()                 # Add new data
      }
```

## Basic Example: Rename and Add Fields

**Input Message:**
```json
{"temp": 25, "device": "sensor-01"}
```

**Transform Rule:**
```yaml
- transform:
    expression: |
      {
        "temperature": $.temp,      # Rename temp → temperature
        "device_name": $.device,    # Rename device → device_name
        "unit": "celsius",          # Add new field
        "timestamp": $now()         # Add current time
      }
```

**Output Message:**
```json
{
  "temperature": 25,
  "device_name": "sensor-01", 
  "unit": "celsius",
  "timestamp": "2024-10-29T10:30:00Z"
}
```

## Common Transform Patterns

### **1. Field Renaming**
```yaml
- transform:
    expression: |
      {
        "temperature": $.temp,           # temp → temperature
        "humidity": $.hum,               # hum → humidity
        "device_id": $.id                # id → device_id
      }
```

### **2. Calculations**
```yaml
- transform:
    expression: |
      {
        "celsius": $.fahrenheit,
        "fahrenheit_to_celsius": ($.fahrenheit - 32) * 5/9,
        "doubled_value": $.value * 2
      }
```

### **3. Adding New Fields**
```yaml
- transform:
    expression: |
      {
        "original_data": $,              # Keep all original data
        "processed_time": $now(),        # Add timestamp
        "source": "sensor-network",      # Add static value
        "is_valid": $.value > 0          # Add boolean check
      }
```

### **4. Restructuring Data**
```yaml
# Input: {"name": "sensor-01", "temp": 25, "hum": 60}
- transform:
    expression: |
      {
        "device": {
          "name": $.name,
          "type": "environmental"
        },
        "readings": {
          "temperature": $.temp,
          "humidity": $.hum
        }
      }
# Output: {"device": {"name": "sensor-01", "type": "environmental"}, "readings": {"temperature": 25, "humidity": 60}}
```

## Complete Examples with Input/Output

### **Example 1: Simple Sensor Data**

**Input:**
```json
{"temp": 72, "sensor": "room-01", "battery": 85}
```

**Transform:**
```yaml
- transform:
    expression: |
      {
        "temperature_f": $.temp,
        "temperature_c": ($.temp - 32) * 5/9,
        "sensor_id": $.sensor,
        "battery_percent": $.battery,
        "status": $.battery > 20 ? "ok" : "low_battery"
      }
```

**Output:**
```json
{
  "temperature_f": 72,
  "temperature_c": 22.22,
  "sensor_id": "room-01", 
  "battery_percent": 85,
  "status": "ok"
}
```

### **Example 2: Adding Metadata**

**Input:**
```json
{"value": 150, "unit": "ppm"}
```

**Transform:**
```yaml
- transform:
    expression: |
      {
        "measurement": {
          "value": $.value,
          "unit": $.unit,
          "reading_time": $now()
        },
        "quality": {
          "is_valid": $.value > 0,
          "within_range": $.value >= 50 and $.value <= 200
        },
        "metadata": {
          "processor": "cybus-transform",
          "version": "1.0"
        }
      }
```

**Output:**
```json
{
  "measurement": {
    "value": 150,
    "unit": "ppm",
    "reading_time": "2024-10-29T10:30:00Z"
  },
  "quality": {
    "is_valid": true,
    "within_range": true
  },
  "metadata": {
    "processor": "cybus-transform",
    "version": "1.0"
  }
}
```

## JSONata Quick Reference

**Basic Operations:**
- `$.field` - Get field value
- `$.field.subfield` - Get nested field
- `$now()` - Current timestamp
- `$number($)` - Convert to number
- `$string($)` - Convert to string

**Calculations:**
- `$.a + $.b` - Addition
- `$.a * 2` - Multiplication
- `($.temp - 32) * 5/9` - Formula

**Conditions:**
- `$.value > 10 ? "high" : "low"` - If/else
- `$.battery > 20 ? "ok" : "low"` - Status check

## Common Use Cases

1. **Data Cleaning**: Remove unwanted fields, rename fields
2. **Unit Conversion**: Fahrenheit to Celsius, etc.
3. **Adding Metadata**: Timestamps, source information
4. **Data Validation**: Add status flags, validity checks
5. **Restructuring**: Change JSON structure completely
6. **Calculations**: Derived values, formulas

## Key Points

- ✅ **Complete Control**: Create any JSON structure you want
- ✅ **Keep or Drop**: Include only the fields you need
- ✅ **Add New Data**: Timestamps, calculations, static values
- ✅ **JSONata Power**: Use powerful expressions for complex logic

## Important Notes

- **Creates New Message**: Transform completely replaces the message
- **JSONata Language**: Uses JSONata for expressions (like JavaScript for JSON)
- **Order Matters**: Transform processes data as it exists at that point
- **Test First**: Complex expressions should be tested with sample data

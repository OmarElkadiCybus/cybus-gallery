```markdown
# setContextVars Rule: Simple Examples

The `setContextVars` rule extracts values from message data and stores them as variables that can be used in later rules or publish topics.

## What setContextVars Does

**Simple Concept**: Take values from your message data and save them as variables for later use.

```yaml
# Step 1: Extract values and store as variables
- setContextVars:
    vars:
      deviceName: $.device.name       # Save device name
      location: $.device.location     # Save device location

# Step 2: Use those variables in transform or publish topics  
- transform:
    expression: |
      {
        "message": "Device " & $context.vars.deviceName & " in " & $context.vars.location,
        "data": $
      }
```

## Basic Example: Device Information

**Input Message:**
```json
{
  "device": {"name": "sensor-01", "location": "warehouse"},
  "temperature": 23.5
}
```

**setContextVars Rule:**
```yaml
- setContextVars:
    vars:
      deviceName: $.device.name      # Saves "sensor-01"
      location: $.device.location    # Saves "warehouse"
```

**Result:** You can now use `$context.vars.deviceName` and `$context.vars.location` in later rules.

## Dynamic Topic Routing

Use stored variables to create dynamic publish topics:

```yaml
mappings:
- subscribe:
    topic: sensors/data
  publish:
    topic: 'devices/{deviceName}/{location}/processed'  # Uses stored variables
  rules:
  - setContextVars:
      vars:
        deviceName: $.device.name
        location: $.device.location
  - transform:
      expression: |
        {
          "device_info": {
            "name": $context.vars.deviceName,
            "location": $context.vars.location
          },
          "sensor_data": $
        }
```

**Complete Flow Example:**

**Input:**
- **Topic:** `sensors/data`
- **Message:** 
```json
{"device": {"name": "temp-01", "location": "factory"}, "value": 25.0}
```

**Processing:**
1. **setContextVars extracts:** `deviceName = "temp-01"`, `location = "factory"`
2. **Variables available:** `$context.vars.deviceName`, `$context.vars.location`

**Output:**
- **Topic:** `devices/temp-01/factory/processed`
- **Message:**
```json
{
  "device_info": {
    "name": "temp-01",
    "location": "factory"
  },
  "sensor_data": {"device": {"name": "temp-01", "location": "factory"}, "value": 25.0}
}
```

## Conditional Variables

Create variables based on conditions:

```yaml
- setContextVars:
    vars:
      priority: |
        $.temperature > 30 ? "high" : 
        $.temperature > 20 ? "normal" : "low"
      status: |
        $.temperature > 40 ? "alert" : "ok"
```

**Why this is useful:**

1. **Dynamic Routing**: Route to different topics based on data values
   ```yaml
   publish:
     topic: 'alerts/{priority}/temperature'  # Routes to alerts/high/temperature
   ```

2. **Business Logic**: Apply different processing rules
   ```yaml
   - transform:
       expression: |
         {
           "temperature": $.temperature,
           "needs_immediate_action": $context.vars.status = "alert",
           "notification_level": $context.vars.priority
         }
   ```

3. **Clean Code**: Write the logic once, use it multiple times
   - Without setContextVars: Repeat `$.temperature > 30 ? "high" : "normal"` everywhere
   - With setContextVars: Use simple `$context.vars.priority`

**Complete Example:**

**Input Message:**
```json
{"temperature": 35, "sensor_id": "temp-sensor-01"}
```

**Processing:**
- `priority = "high"` (because 35 > 30)
- `status = "ok"` (because 35 < 40)

**Output:**
- **Topic:** `alerts/high/temperature`
- **Message:**
```json
{
  "temperature": 35,
  "needs_immediate_action": false,
  "notification_level": "high",
  "sensor_id": "temp-sensor-01"
}
```

**Real-world benefit**: Your message gets routed to `alerts/high/temperature` and processed with the right urgency level automatically!

## Common Use Cases

1. **Device Routing**: Route messages based on device ID or type
2. **Multi-tenant Systems**: Route by customer/tenant from message data
3. **Alert Systems**: Route by severity level
4. **Business Logic**: Apply routing rules based on data values

## Key Points

- ✅ **Extract Once, Use Many**: Store values once, use in multiple places
- ✅ **Dynamic Topics**: Build publish topics from message content
- ✅ **Conditional Logic**: Create variables with if/else logic
- ✅ **Clean Code**: Avoid repeating complex JSONata expressions

```

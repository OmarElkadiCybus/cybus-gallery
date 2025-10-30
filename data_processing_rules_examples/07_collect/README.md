# Collect Rule: Labels vs Dynamic Labels

The collect rule creates a Last Value Cache (LVC) that aggregates messages from multiple sources. Understanding **labels** and **dynamic labels** is crucial for effective data organization and preventing cache key conflicts.

## Core Concept: Cache Keys and Data Preservation

The collect rule uses **cache keys** to store messages. Without labels, topic paths become cache keys, which can lead to:
- Long, unwieldy cache keys
- Difficulty accessing data with JSONata
- Potential conflicts in complex topic structures

**Labels solve these problems** by providing clean, predictable cache keys.

## How Collect Rule Triggering Works

**CRITICAL**: The collect rule **triggers on ANY new message** from ANY subscribed source:

1. **Message arrives** from any subscribed topic/endpoint
2. **Cache is updated** with the new message (using topic path or label as key)
3. **Mapping immediately triggers** and outputs the **complete cache contents**
4. **All previously cached messages** are included in the output

### Example Trigger Behavior:
```
Time 10:00:01 - sensors/temp: {"value": 22.0} arrives
                → Cache: {"sensors/temp": {"value": 22.0}}
                → Output: {"sensors/temp": {"value": 22.0}}

Time 10:00:02 - sensors/humidity: {"value": 65} arrives  
                → Cache: {"sensors/temp": {"value": 22.0}, "sensors/humidity": {"value": 65}}
                → Output: {"sensors/temp": {"value": 22.0}, "sensors/humidity": {"value": 65}}

Time 10:00:03 - sensors/temp: {"value": 22.5} arrives (UPDATE)
                → Cache: {"sensors/temp": {"value": 22.5}, "sensors/humidity": {"value": 65}}
                → Output: {"sensors/temp": {"value": 22.5}, "sensors/humidity": {"value": 65}}
```

**Key Points:**
- **Every message triggers output** regardless of which source sent it
- **Updated values overwrite** previous values for the same cache key
- **Complete cache is always output** - not just the triggering message
- **No batching or delays** - immediate trigger on every incoming message

## Label Types and When to Use Them

### 1. No Labels (Topic Paths as Keys)
- **When**: Simple, known topic structures
- **Pros**: No configuration needed
- **Cons**: Long cache keys, harder data access

### 2. Static Labels  
- **When**: Fixed, known data sources
- **Pros**: Clean cache keys, easy JSONata access with `$lookup()`
- **Cons**: Requires predefined subscription list

### 3. Dynamic Labels (with Topic Dynamic Labels)
- **When**: Unknown number of sources, scalable systems
- **Pros**: Automatic scaling, pattern-based organization
- **Critical**: Prevents data overwrite in wildcard scenarios

## Why Dynamic Labels are Essential for Wildcards

**Without dynamic labels**, wildcard subscriptions create a **critical data loss problem**:

```yaml
# WRONG - Data Loss Risk
subscribe:
  topic: building/+room/temperature  # Multiple rooms
  # No label = same cache key for all rooms!
```

**Problem**: All messages use the same cache key, so:
- `building/office-a/temperature` overwrites `building/office-b/temperature` in cache
- Only the last received message is kept
- **Data from all other rooms is lost!**
- **Every new message from ANY room triggers output** but cache only contains the latest room's data

**Solution - Dynamic Labels**:
```yaml
# CORRECT - Data Preserved  
subscribe:
  topic: building/+room/temperature
  label: 'temp_{room}'  # Unique cache key per room
```

**Result**: Each room gets its own cache entry:
- `temp_office-a` stores office-a data
- `temp_office-b` stores office-b data  
- **All room data is preserved**
- **Any new message from ANY room** triggers output with ALL rooms' latest data

## Comprehensive Examples

### Example 1: Static Labels for Known Sources
```yaml
# Clean cache keys for easier data processing
mappings:
- subscribe:
    - topic: sensors/office/temperature
      label: temp
    - topic: sensors/office/humidity  
      label: humidity
  rules:
  - collect: {}
  - transform:
      expression: |
        {
          "comfort": ($lookup($, 'temp').value > 20 and $lookup($, 'humidity').value < 60) ? "good" : "poor"
        }
```

**Benefits**:
- Simple JSONata access: `$lookup($, 'temp')`
- Clean cache structure
- Easy data correlation

### Example 2: Single Dynamic Label
```yaml  
# Scale automatically with new rooms
mappings:
- subscribe:
    topic: building/+room/sensors/temperature
    label: 'temp_{room}'
  rules:
  - collect: {}
```

**Message Flow & Triggering**:
```
10:00:01 - building/office-a/sensors/temperature: {"value": 23.2} arrives
           → Cache updated: {"temp_office-a": {"value": 23.2}}
           → Mapping triggers, outputs: {"temp_office-a": {"value": 23.2}}

10:00:02 - building/conference/sensors/temperature: {"value": 26.8} arrives  
           → Cache updated: {"temp_office-a": {"value": 23.2}, "temp_conference": {"value": 26.8}}
           → Mapping triggers, outputs: {"temp_office-a": {"value": 23.2}, "temp_conference": {"value": 26.8}}

10:00:03 - building/office-a/sensors/temperature: {"value": 23.5} arrives (update)
           → Cache updated: {"temp_office-a": {"value": 23.5}, "temp_conference": {"value": 26.8}}
           → Mapping triggers, outputs: {"temp_office-a": {"value": 23.5}, "temp_conference": {"value": 26.8}}
```

**Benefits**:
- **Every room message triggers complete output** with all rooms' data
- Automatic scaling - new rooms automatically included
- Structured cache keys  
- No data loss between rooms

### Example 3: Multi-Level Dynamic Labels
```yaml
# Handle complex hierarchies
mappings:
- subscribe:
    topic: factory/+line/+machine/+sensor/data
    label: '{line}_{machine}_{sensor}'
  rules:
  - collect: {}
```

**Result Cache**:
```json
{
  "line-a_cnc-001_temperature": {"value": 68.5},
  "line-a_cnc-001_vibration": {"value": 2.1}, 
  "line-a_robot-arm_position": {"x": 145.2, "y": 78.9},
  "line-b_press-002_pressure": {"value": 850.3}
}
```

**Benefits**:
- Hierarchical organization
- Easy filtering by line, machine, or sensor type
- Scales to enterprise complexity

## Data Processing Patterns

### Pattern 1: Statistical Analysis
```jsonata
{
  "temperature_stats": {
    "average": $average($map($keys($), function($k) { 
      $contains($k, 'temp') ? $lookup($, $k).value 
    })),
    "rooms_above_25": $filter($keys($), function($k) { 
      $contains($k, 'temp') and $lookup($, $k).value > 25 
    })
  }
}
```

### Pattern 2: Equipment Health Monitoring  
```jsonata
{
  "operational_equipment": $filter($keys($), function($k) { 
    $lookup($, $k).status = "running" 
  }),
  "alert_count": $count($filter($keys($), function($k) { 
    $lookup($, $k).alarm = true 
  }))
}
```

## Best Practices

1. **Always use dynamic labels with wildcards** to prevent data loss
2. **Use meaningful label patterns** that reflect your data hierarchy  
3. **Keep labels short but descriptive** for easier JSONata processing
4. **Consider your access patterns** when designing label structure
5. **Test with multiple data sources** to verify no overwrites occur

## Manufacturing Use Case Example

Real-world scenario: Production line monitoring with mixed static and dynamic labeling.

**Challenge**: Monitor known critical equipment + unknown number of sensors per machine.

**Solution**:
```yaml
mappings:
- subscribe:
    # Critical equipment - static labels for easy access
    - topic: factory/line-a/cnc-001/status
      label: cnc_001_status
    - topic: factory/line-a/robot-arm/status  
      label: robot_status
    # Variable sensors - dynamic labels
    - topic: factory/line-a/+machine/sensors/+type/readings
      label: '{machine}_{type}'
  rules:
  - collect: {}
  - transform:
      expression: |
        {
          "critical_status": {
            "cnc": $lookup($, 'cnc_001_status').operational,
            "robot": $lookup($, 'robot_status').operational
          },
          "sensor_readings": $filter($, function($v, $k) { 
            $contains($k, '_') and not $contains($k, 'status')
          }),
          "line_operational": $lookup($, 'cnc_001_status').operational and $lookup($, 'robot_status').operational
        }
```

This pattern provides:
- **Reliable access** to critical equipment status
- **Flexible scaling** for additional sensors  
- **Clear separation** between critical and supplementary data
- **Data preservation** across all sources

## Configuration Reference

See `service.scf.yaml` for complete implementation examples including:
- Basic collect without labels
- Static labeling for known sources  
- Single-level dynamic labels
- Multi-level dynamic labels
- Manufacturing equipment monitoring
# Basic Wildcard Patterns

**Purpose**: Learn fundamental wildcard subscription patterns for multi-topic mapping  
**Focus**: Wildcard syntax and context variables  
**Prerequisites**: Basic understanding of MQTT topics and JSONata expressions

## What You'll Learn

By the end of this tutorial, you'll understand:
- ✅ **Wildcard syntax** and when to use `+` vs `#`
- ✅ **Context variables** for accessing wildcard values
- ✅ **Dynamic topic handling** without hardcoding paths
- ✅ **Best practices** for wildcard naming and organization

## The Problem: Multiple Similar Topics

Instead of creating separate mappings for each topic:
```yaml
# ❌ Repetitive approach - not scalable
- subscribe: { topic: "factory/line-A/status" }
- subscribe: { topic: "factory/line-B/status" }  
- subscribe: { topic: "factory/line-C/status" }
```

Use wildcards to handle them all with one pattern:
```yaml
# ✅ Scalable approach - handles all lines
- subscribe: { topic: "factory/+line/status" }
```

## Wildcard Syntax

- **`+`** = Matches **exactly one** topic level 
- **`#`** = Matches **zero or more** remaining levels

### Examples:
```yaml
factory/+/status         # Matches: factory/line-A/status, factory/pump-3/status
sensors/+/+/temperature  # Matches: sensors/floor1/room1/temperature  
building/#               # Matches: building/floor1, building/floor1/room1/temp
```

## Step 1: Basic Wildcards (No Context Variables)

Start simple - use wildcards without names:

```yaml
subscribe:
  topic: factory/+/status    # Matches any factory equipment
rules:
- transform:
    expression: |
      {
        "timestamp": $now(),
        "status": $.status,
        "temperature": $.temperature
      }
```

**Problem**: You lose information about **which** equipment sent the data!

## Step 2: Named Wildcards (With Context Variables)

Add names to wildcards to capture the matched segments:

```yaml
subscribe:
  topic: factory/+equipment/status    # Named wildcard creates $context.vars.equipment
rules:
- transform:
    expression: |
      {
        "timestamp": $now(),
        "equipment_id": $context.vars.equipment,  # Now you know which equipment!
        "status": $.status,
        "temperature": $.temperature
      }
```

**Input**: `factory/pump-005/status` → `{"status": "running", "temperature": 68.5}`  
**Output**: `{"timestamp": 1730115600000, "equipment_id": "pump-005", "status": "running", "temperature": 68.5}`

## Complete Example

```yaml
topic: factory/+line/+machine/status
# Input: factory/assembly/cnc-001/status
# Creates: $context.vars.line = "assembly"
#         $context.vars.machine = "cnc-001"  
```

## Accessing Wildcard Values

Use `$context.vars.<name>` to access wildcard values in your transform:

```yaml
topic: building/+floor/+room/+sensor/reading

transform:
  expression: |
    {
      "device_location": {
        "floor": $context.vars.floor,      # Gets floor value
        "room": $context.vars.room,        # Gets room value  
        "sensor": $context.vars.sensor     # Gets sensor value
      },
      "full_path": $context.vars.floor & "/" & $context.vars.room,
      "sensor_reading": $.value,
      "timestamp": $now()
    }

# Input: building/2/office-201/temperature/reading → {"value": 23.5}
# Output: {
#   "device_location": {
#     "floor": "2",
#     "room": "office-201", 
#     "sensor": "temperature"
#   },
#   "full_path": "2/office-201",
#   "sensor_reading": 23.5,
#   "timestamp": "2025-10-28T10:30:00Z"
# }
```

## Best Practices

**✅ Use descriptive wildcard names:**
```yaml
topic: building/+floor/+room/+sensor/reading
```

**❌ Avoid cryptic wildcard names:**
```yaml
topic: building/+a/+b/+c/reading  
```

## When to Use Wildcards

**✅ Good for same root path:**
```yaml
topic: building/+floor/+room/temperature
# Matches: building/1/office/temperature, building/2/lobby/temperature
```

**❌ Use arrays for different roots:**
```yaml
# Different domains - use arrays instead
- building/sensors/temperature
- factory/machines/temp  
- vehicle/engine/temperature
```

## Quick Reference

| Wildcard | Meaning | Example |
|----------|---------|---------|
| `+` | Single level | `factory/+/status` |
| `#` | Multi-level | `sensors/#` |
| `+name` | Creates variable | `$context.vars.name` |

## Real Examples

See the [factory-monitoring.scf.yaml](./factory-monitoring.scf.yaml) file for complete working examples.

## Next Steps

- **[Wildcards with Collect](../03_wildcards_with_collect/)** - Combine wildcards with data aggregation
- **[Dynamic Publishing](../04_dynamic_publish_topic/wildcards/)** - Use wildcards for dynamic topic routing
- **[Array Mapping](../01_array/)** - Handle multiple incompatible topic patterns
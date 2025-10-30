# Wildcard-Based Multi-Topic Mapping

Subscribe to multiple MQTT topics using wildcards (`+` and `#`) when they share the same root path.

## Wildcards

- `+` = Matches exactly one topic level 
- `#` = Matches zero or more remaining levels

## Basic Examples

```yaml
# Basic wildcard
topic: factory/+/status
# Matches: factory/machine-a/status, factory/line-b/status

# With wildcard names to create context variables
topic: factory/+machine/status # Creates $context.vars.machine
```

## Why Use Wildcard Names?

**With Wildcard Names** - Automatic context variables:
```yaml
topic: factory/+area/status
# No label needed - wildcard name creates context variable automatically
transform: |
  {
    "area": $context.vars.area  # Clean!
  }
```

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
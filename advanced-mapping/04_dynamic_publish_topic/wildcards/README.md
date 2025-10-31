# Dynamic Publish Topics with Wildcards

**Purpose**: Use wildcard segments from input topics to construct dynamic publish topics
**Key Technique**: `$context.vars.{name}` in publish topic expressions

## How Wildcard Dynamic Publishing Works

When you use wildcards in subscribe topics, you can access those wildcard values in your publish topic using `$context.vars.{name}`:

```yaml
subscribe:
  topic: sensors/+room/+sensor/data
publish:
  topic: 'alerts/{room}/{sensor}/processed'  # Uses wildcard values!
```

**Flow:**
1. **Input**: `sensors/kitchen/temperature/data` → message
2. **Wildcards**: `room="kitchen"`, `sensor="temperature"`  
3. **Output**: `alerts/kitchen/temperature/processed` → processed message

## Basic Pattern

```yaml
mappings:
- subscribe:
    topic: input/+segment1/+segment2/data
  publish:
    topic: 'output/{segment1}/{segment2}/result'
  rules:
  - transform:
      expression: |
        {
          "processed_by": "wildcard_routing",
          "original_topic": $context.topic,
          "extracted_values": {
            "segment1": $context.vars.segment1,
            "segment2": $context.vars.segment2
          }
        }
```

## Key Benefits

✅ **Topic-based Routing**: Route messages based on their topic structure
✅ **Hierarchical Organization**: Maintain topic hierarchies in output
✅ **System Integration**: Map input structures to output requirements
✅ **Scalability**: Handle new topic variations automatically

## Examples Included

- **Building Automation**: Route sensor data by room and sensor type
- **Device Management**: Route device data by location and device ID
- **Multi-tenant Systems**: Route data by tenant and service
- **Alert Systems**: Route alerts by severity and source
- **UNS Implementation**: Transform enterprise data into unified namespace hierarchies

## When to Use

**Perfect for:**
- Input topics contain routing information in their structure
- Need to maintain hierarchical organization
- Routing logic is based on topic segments
- Simple, predictable routing patterns
- **UNS (Unified Namespace)**: Map enterprise data to standardized topic hierarchies

**Not ideal for:**
- Routing based on payload content (use setContextVars instead)
- Complex conditional routing logic
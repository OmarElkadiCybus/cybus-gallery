# Dynamic Publish Topics with Wildcards

**Purpose**: Use wildcard segments from input topics to construct dynamic publish topics  
**Focus**: Topic-based routing with wildcard values  
**Prerequisites**: [Basic Wildcards](../../01_basic_wildcards/) understanding

## What You'll Learn

By the end of this tutorial, you'll understand:
- ✅ **Dynamic topic construction** using wildcard values in publish topics
- ✅ **Context variable usage** with `{variable_name}` syntax in topic strings
- ✅ **Routing patterns** for building automation and device management
- ✅ **UNS transformation** for enterprise namespace standardization

## The Problem: Static vs Dynamic Output Topics

Traditional static publishing sends all messages to the same destination:
```yaml
# ❌ Static approach - all sensors go to same topic
subscribe:
  topic: sensors/kitchen/temperature/data
publish:
  topic: processed/sensor/data        # Always the same output topic
```

But what if you want different destinations based on the **input topic structure**?
```yaml
# ✅ Dynamic approach - route based on input topic segments  
subscribe:
  topic: sensors/+room/+sensor/data
publish:
  topic: processed/{room}/{sensor}/data  # Dynamic based on wildcards!
```

## Step 1: Basic Wildcard Routing

Start with simple wildcard value extraction:

```yaml
subscribe:
  topic: sensors/+room/temperature/data
publish:
  topic: 'alerts/{room}/temperature'    # Use {room} from wildcard
rules:
- transform:
    expression: |
      {
        "room": $context.vars.room,
        "temperature": $.value,
        "alert_level": $.value > 25 ? "high" : "normal"
      }
```

**Example Flow:**
- **Input**: `sensors/kitchen/temperature/data` → `{"value": 27.5}`
- **Wildcard**: `room = "kitchen"`  
- **Output Topic**: `alerts/kitchen/temperature`
- **Output**: `{"room": "kitchen", "temperature": 27.5, "alert_level": "high"}`

## Step 2: Multi-Level Wildcard Routing

Use multiple wildcard segments for complex routing:

```yaml
subscribe:
  topic: devices/+location/+deviceId/+dataType
publish:
  topic: 'processed/{location}/devices/{deviceId}/{dataType}'
rules:
- transform:
    expression: |
      {
        "device_info": {
          "location": $context.vars.location,
          "device_id": $context.vars.deviceId,
          "data_type": $context.vars.dataType
        },
        "data": $,
        "routing_path": $context.vars.location & "/" & $context.vars.deviceId
      }
```

**Example Flow:**
- **Input**: `devices/warehouse-a/sensor-001/diagnostics` → `{"battery": 85, "signal": -65}`
- **Wildcards**: `location="warehouse-a"`, `deviceId="sensor-001"`, `dataType="diagnostics"`
- **Output Topic**: `processed/warehouse-a/devices/sensor-001/diagnostics`
- **Output**: `{"device_info": {...}, "data": {...}, "routing_path": "warehouse-a/sensor-001"}`

## Step 3: Enterprise UNS Transformation

Transform legacy topic structures into standardized enterprise namespaces:

```yaml
subscribe:
  topic: legacy/+site/+area/+line/+asset/data
publish:
  topic: 'UNS/Enterprise/{site}/Area/{area}/Line/{line}/Asset/{asset}'
rules:
- transform:
    expression: |
      {
        "uns_metadata": {
          "enterprise": "Manufacturing Corp",
          "site": $context.vars.site,
          "hierarchy_path": "Enterprise/" & $context.vars.site & "/Area/" & $context.vars.area,
          "legacy_source": $context.topic
        },
        "process_data": $,
        "uns_compliant": true
      }
```

**Example Flow:**
- **Input**: `legacy/plant-01/production/line-a/robot-arm-02/data` → `{"status": "running", "oee": 0.85}`
- **Wildcards**: `site="plant-01"`, `area="production"`, `line="line-a"`, `asset="robot-arm-02"`
- **Output Topic**: `UNS/Enterprise/plant-01/Area/production/Line/line-a/Asset/robot-arm-02`
- **Result**: ISA-95 compliant enterprise hierarchy with full traceability

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
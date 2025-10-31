# Array-Based Multi-Topic Mapping

**Purpose**: Handle multiple topics with completely different structures in a single mapping  
**Focus**: Array subscriptions, labels, and collect rules  
**Prerequisites**: Understanding of [Basic Wildcards](../01_basic_wildcards/) and collect rules

## What You'll Learn

By the end of this tutorial, you'll understand:
- ✅ **When arrays are required** vs when wildcards work
- ✅ **Label-based organization** for multi-source data
- ✅ **Collect rule patterns** for data aggregation
- ✅ **Cross-system integration** techniques

## The Problem: Incompatible Topic Structures

Wildcards work great when topics share a common root:
```yaml
# ✅ Wildcards work - same root structure
building/+floor/+room/temperature
building/1/office/temperature     # Matches
building/2/lobby/temperature      # Matches
```

But what about completely different topic structures?
```yaml
# ❌ Wildcards CAN'T handle these - different roots
factory/machine/pump-001/status       # Factory system
building/hvac/main/temperature        # Building automation  
vehicle/truck-005/engine/diagnostics  # Fleet management
```

**Solution**: Use array subscriptions to handle incompatible structures.

## When to Use Arrays

✅ **Use arrays when:**
- Topics have **different roots**: `factory/machine` vs `building/hvac` vs `vehicle/engine`
- **Mixed protocols**: MQTT topics + OPC UA endpoints + HTTP APIs
- **Enterprise integration**: Combining disparate business systems

❌ **Use wildcards instead when:**
- Topics **share common root**: `building/+floor/+room/temperature`
- **Similar structure**: All topics follow the same pattern

## Step 1: Basic Array Syntax

Start with a simple array of different topics:

```yaml
subscribe:
  - topic: factory/machine/pump-001/status     # Factory system
  - topic: building/hvac/main/temperature       # Building automation
  - topic: vehicle/truck-005/diagnostics        # Fleet management
rules:
- transform:
    expression: |
      {
        "timestamp": $now(),
        "data": $,
        "source": "unknown"    # ❌ Problem: Can't tell which system sent this!
      }
```

**Problem**: Without labels, you lose track of which topic sent the data.

## Step 2: Arrays with Labels

Add labels to identify each data source:

```yaml
subscribe:
  - topic: factory/machine/pump-001/status
    label: 'factory_system'              # Label identifies the source
  - topic: building/hvac/main/temperature  
    label: 'building_system'
  - topic: vehicle/truck-005/diagnostics
    label: 'vehicle_system'
rules:
- transform:
    expression: |
      {
        "timestamp": $now(),
        "data": $,
        "source_label": $context.label    # ✅ Now you know the source!
      }
```

**Each topic triggers separately** - you get one message per input topic.

## Step 3: Arrays with Collect (The Power Pattern)

Use collect rule to aggregate ALL array sources into one payload:

```yaml
subscribe:
  - topic: factory/machine/pump-001/status
    label: 'factory'
  - topic: building/hvac/main/temperature  
    label: 'building'
  - topic: vehicle/truck-005/diagnostics
    label: 'vehicle'
rules:
- collect: {}                             # Collect all labeled sources
- transform:
    expression: |
      {
        "timestamp": $now(),
        "factory_data": $lookup($, 'factory'),      # Access by label
        "building_data": $lookup($, 'building'),    # Access by label  
        "vehicle_data": $lookup($, 'vehicle'),      # Access by label
        "all_systems_ok": $lookup($, 'factory').status = "ok" and 
                         $lookup($, 'building').temp < 25 and
                         $lookup($, 'vehicle').engine = "running"
      }
```

**Key Benefits:**
- 🔄 **Cross-system analytics**: Compare data from all sources
- 📊 **Correlated processing**: Make decisions based on multiple systems
- 🎯 **Single output**: One enriched message instead of separate outputs

## Core Concept: Array with Labels and Collect

The example demonstrates the most common pattern - using arrays with labels and collect rule:

```yaml
mappings:
- subscribe:
    # Array of topics with different roots
    - topic: factory/machine/status
      label: factory                    # Label each source
    - topic: building/hvac/status
      label: building
    - topic: vehicle/sensor/status
      label: vehicle
  publish:
    topic: system/monitoring/status
  rules:
  - collect: {}                         # Collect all labeled payloads
  - transform:
      expression: |
        {
          "SystemStatus": $lookup($, 'factory').status and $lookup($, 'building').status and $lookup($, 'vehicle').status
        }
```

### Input Messages
```json
// Message from factory/machine/status
{"status": true, "id": "machine-001", "temperature": 45}

// Message from building/hvac/status  
{"status": true, "id": "hvac-main", "setpoint": 22}

// Message from vehicle/sensor/status
{"status": false, "id": "truck-001", "fuel": 75}
```

### After Collect Rule
```json
{
  "factory": {"status": true, "id": "machine-001", "temperature": 45},
  "building": {"status": true, "id": "hvac-main", "setpoint": 22},
  "vehicle": {"status": false, "id": "truck-001", "fuel": 75}
}
```

### Output Message
```json
// Published to system/monitoring/status
{
  "SystemStatus": false
}
```

## How This Works

1. **Array Subscription**: Subscribe to multiple topics with different roots
2. **Labels**: Each topic gets a label (`factory`, `building`, `vehicle`)
3. **Collect Rule**: Aggregates all labeled payloads into one object
4. **JSONata Access Pattern**: Use `$lookup($, 'label_name')` to access cached data by label

## Key Benefits

- ✅ **Different Roots**: Handle topics that don't share common structure
- ✅ **Source Identification**: Labels identify which topic sent data
- ✅ **Correlated Processing**: Collect rule enables processing all sources together
- ✅ **Boolean Logic**: Can combine status from multiple systems

## Mixed Topics and Endpoints Example

Arrays are perfect for combining MQTT topics with other endpoint types:

```yaml
# Define OPC UA endpoint first
opcuaEndpoint:
  type: Cybus::Endpoint
  properties:
    protocol: Opcua
    connection:
      host: 192.168.1.100
      port: 4840

# Mixed subscription array
mixedMapping:
  type: Cybus::Mapping
  properties:
    mappings:
    - subscribe:
        # Mix MQTT topics with OPC UA endpoints
        - topic: factory/conveyor/speed          # MQTT topic
          label: conveyor_mqtt
        - endpoint: !ref opcuaEndpoint           # OPC UA endpoint
          nodeId: ns=2;s=Machine.Temperature
          label: cnc_opcua
        - topic: warehouse/inventory/count       # Another MQTT topic
          label: inventory_mqtt
      publish:
        topic: monitoring/mixed-sources
      rules:
      - collect: {}
      - transform:
          expression: |
            {
              "timestamp": $now(),
              "conveyor_speed": $lookup($, 'conveyor_mqtt').speed,
              "cnc_temperature": $lookup($, 'cnc_opcua').value,
              "inventory_count": $lookup($, 'inventory_mqtt').count,
              "all_systems_active": $lookup($, 'conveyor_mqtt').speed > 0 and $lookup($, 'cnc_opcua').value < 80 and $lookup($, 'inventory_mqtt').count > 0
            }
```

### Input Messages
```json
// MQTT message from factory/conveyor/speed
{"speed": 150, "direction": "forward", "load": 75}

// OPC UA data from ns=2;s=Machine.Temperature  
{"value": 68.5, "quality": "Good", "timestamp": "2024-10-27T10:30:00Z"}

// MQTT message from warehouse/inventory/count
{"count": 1250, "location": "A1-B3", "last_updated": "2024-10-27T10:29:45Z"}
```

### After Collect Rule
```json
{
  "conveyor_mqtt": {"speed": 150, "direction": "forward", "load": 75},
  "cnc_opcua": {"value": 68.5, "quality": "Good", "timestamp": "2024-10-27T10:30:00Z"},
  "inventory_mqtt": {"count": 1250, "location": "A1-B3", "last_updated": "2024-10-27T10:29:45Z"}
}
```

### Output Message
```json
// Published to monitoring/mixed-sources
{
  "timestamp": "2024-10-27T10:30:15.123Z",
  "conveyor_speed": 150,
  "cnc_temperature": 68.5,
  "inventory_count": 1250,
  "all_systems_active": true  // speed > 0 AND temp < 80 AND count > 0
}
```

## Only Endpoints Example

You can also use arrays with only endpoints (no MQTT topics):

```yaml
endpointsOnlyMapping:
  type: Cybus::Mapping
  properties:
    mappings:
    - subscribe:
        # Array of different endpoints
        - endpoint: !ref opcuaEndpoint1
          nodeId: ns=2;s=Machine1.Status
          label: machine1_data
        - endpoint: !ref opcuaEndpoint2
          nodeId: ns=2;s=Machine2.Status  
          label: machine2_data
        - endpoint: !ref httpEndpoint
          path: /api/sensors/temperature
          label: http_sensor
      publish:
        topic: monitoring/endpoints-only
      rules:
      - collect: {}
      - transform:
          expression: |
            {
              "machine1_status": $lookup($, 'machine1_data').status,
              "machine2_status": $lookup($, 'machine2_data').status,
              "external_temp": $lookup($, 'http_sensor').temperature
            }
```

### Input Data
```json
// OPC UA from ns=2;s=Machine1.Status
{"status": "running", "cycles": 1456, "efficiency": 87.5}

// OPC UA from ns=2;s=Machine2.Status
{"status": "idle", "cycles": 892, "efficiency": 92.1}

// HTTP from /api/sensors/temperature
{"temperature": 23.4, "unit": "celsius", "sensor_id": "ext-001"}
```

### After Collect Rule
```json
{
  "machine1_data": {"status": "running", "cycles": 1456, "efficiency": 87.5},
  "machine2_data": {"status": "idle", "cycles": 892, "efficiency": 92.1},
  "http_sensor": {"temperature": 23.4, "unit": "celsius", "sensor_id": "ext-001"}
}
```

### Output Message
```json
// Published to monitoring/endpoints-only
{
  "machine1_status": "running",
  "machine2_status": "idle", 
  "external_temp": 23.4
}
```

## Common Array Patterns

### Pattern 1: Cross-System Status Monitoring
```yaml
subscribe:
  - topic: factory/production/status
    label: 'production'
  - topic: warehouse/inventory/status  
    label: 'inventory'
  - topic: quality/inspection/status
    label: 'quality'
rules:
- collect: {}
- transform:
    expression: |
      {
        "overall_status": $lookup($, 'production').status = "ok" and $lookup($, 'inventory').status = "ok" and $lookup($, 'quality').status = "ok",
        "systems": {
          "production": $lookup($, 'production'),
          "inventory": $lookup($, 'inventory'), 
          "quality": $lookup($, 'quality')
        }
      }
```

### Pattern 2: Mixed Protocol Integration
```yaml
subscribe:
  - topic: sensors/temperature      # MQTT
    label: 'mqtt_temp'
  - endpoint: !ref opcuaEndpoint    # OPC UA
    label: 'opcua_pressure'
  - endpoint: !ref httpEndpoint     # HTTP
    label: 'http_humidity'
rules:
- collect: {}
- transform:
    expression: |
      {
        "sensor_data": {
          "temperature": $lookup($, 'mqtt_temp').value,
          "pressure": $lookup($, 'opcua_pressure').value,
          "humidity": $lookup($, 'http_humidity').reading
        }
      }
```
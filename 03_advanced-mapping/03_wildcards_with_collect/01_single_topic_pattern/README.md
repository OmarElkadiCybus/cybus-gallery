# Single Wildcard with Collect & Dynamic Labels

**Specialty**: Combine messages from multiple topics matching the same wildcard pattern
**Focus**: Dynamic labels prevent data loss when collecting from similar topic structures

## The Single Wildcard Specialty

**Single wildcard with collect** is the solution when you need to:
- **Aggregate similar topics** (same structure, different identifiers)
- **Cross-correlate data** from matching topic patterns
- **Build analytics** across topic variations

### ✅ Single Wildcard Solution
```yaml
# This COMBINES all matching topics in one pattern:
subscribe:
  topic: sensors/+room/temperature  # Single pattern matches all rooms
  label: 'room_{room}' # Dynamic label per room

rules:
- collect: {}  # Stores ALL matching topics
- transform:   # Access to complete dataset when any triggers
    expression: |
      {
        "all_rooms": $,
        "triggered_by": $context.vars.room
      }
```

**Complexity Handled:**
- ✅ **Multiple topics**: lobby, kitchen, office, bedroom sensors
- ✅ **Same structure**: All topics follow sensors/{room}/temperature pattern  
- ✅ **Cross-correlation**: Compare and analyze all rooms together

## How Wildcard Names Solve Aggregation

**Wildcard names** (`{room}`) create **unique cache keys** for each matching topic:

### Problem: Topic Pattern Matching
```yaml
# Single wildcard pattern matches multiple real topics:
sensors/lobby/temperature     # Room: lobby
sensors/kitchen/temperature   # Room: kitchen  
sensors/office/temperature    # Room: office
sensors/bedroom/temperature   # Room: bedroom
```

### Solution: Dynamic Labels for Unique Storage
```yaml
subscribe:
  topic: sensors/+room/temperature
  label: 'room_{room}'          # Creates unique labels per room

# Results in cache keys:
# room_lobby    → lobby temperature data
# room_kitchen  → kitchen temperature data  
# room_office   → office temperature data
# room_bedroom  → bedroom temperature data
```

**Aggregation Benefits:**
- 🎯 **Unique identification**: Each room gets its own cache slot
- 🔄 **Cross-room analysis**: Transform can compare all rooms
- 📊 **Statistical processing**: Calculate averages, find outliers

## What is a Collect Rule?

A **collect rule** stores the **last received message** from each matching topic in a **key-value cache** using **dynamic labels as keys**. When any new message arrives, the collect rule triggers and provides access to **all cached data** from all matching topics.

```yaml
subscribe:
  topic: sensors/+room/temperature
  label: 'room_{room}'

rules:
- collect: {}  # Stores all matching topic messages
- transform:   # Triggered on each new message
    expression: |
      {
        "all_rooms": $,           # Access to ALL room data
        "trigger": $context.vars.room # Which room triggered this transform
      }
```

**Input/Output Example:**
```
📨 Message 1: sensors/kitchen/temperature → {"value": 22.5, "unit": "celsius"}
📨 Message 2: sensors/office/temperature → {"value": 24.1, "unit": "celsius"}
📨 Message 3: sensors/lobby/temperature → {"value": 21.8, "unit": "celsius"}

🗄️ Payload after Collect Rule (before Transform):
{
  "room_kitchen": {"value": 22.5, "unit": "celsius"},
  "room_office": {"value": 24.1, "unit": "celsius"}, 
  "room_lobby": {"value": 21.8, "unit": "celsius"}
}

✨ Transform Output (triggered by lobby message):
{
  "all_rooms": {
    "room_kitchen": {"value": 22.5, "unit": "celsius"},
    "room_office": {"value": 24.1, "unit": "celsius"}, 
    "room_lobby": {"value": 21.8, "unit": "celsius"}
  },
  "trigger": "lobby"  // Last room that triggered
}
```

## Why Dynamic Labels are Essential

**The Problem**: Without dynamic labels, data gets overwritten!

### ❌ Static Labels Example - Data Loss Disaster

```yaml
subscribe:
  topic: sensors/+room/temperature
  label: 'temperature'  # Same label for ALL rooms!

rules:
- collect: {}
- transform:
    expression: |
      {
        "cached_data": $,
        "message_count": $count($keys($))
      }
```

**Input Messages (Same Pattern, Different Rooms):**
```
📨 sensors/lobby/temperature → {"value": 22.8, "unit": "celsius"}
📨 sensors/kitchen/temperature → {"value": 26.3, "unit": "celsius"}  
📨 sensors/office/temperature → {"value": 24.1, "unit": "celsius"}
📨 sensors/bedroom/temperature → {"value": 21.5, "unit": "celsius"}
```

**❌ Static Labels Result:**
```json
{
  "cached_data": {
    "temperature": {"value": 21.5, "unit": "celsius"}  // Only last room!
  },
  "message_count": 1  // Lost 3 out of 4 messages! ⚠️
}
```

### ✅ Dynamic Labels Example - All Data Preserved

```yaml
subscribe:
  topic: sensors/+room/temperature
  label: 'room_{room}'  # Unique label per room

rules:
- collect: {}
- transform:
    expression: |
      {
        "cached_data": $,
        "message_count": $count($keys($)),
        "rooms_monitored": $map($keys($), function($k) { $substringAfter($k, 'room_') }),
        "temperature_analysis": {
          "average": $average($map($keys($), function($k) { $lookup($, $k).value })),
          "min": $min($map($keys($), function($k) { $lookup($, $k).value })),
          "max": $max($map($keys($), function($k) { $lookup($, $k).value }))
        }
      }
```

**Same Input Messages (Same Pattern, Different Rooms):**
```
📨 sensors/lobby/temperature → {"value": 22.8, "unit": "celsius"}
📨 sensors/kitchen/temperature → {"value": 26.3, "unit": "celsius"}  
📨 sensors/office/temperature → {"value": 24.1, "unit": "celsius"}
📨 sensors/bedroom/temperature → {"value": 21.5, "unit": "celsius"}
```

**✅ Dynamic Labels Result:**
```json
{
  "cached_data": {
    "room_lobby": {"value": 22.8, "unit": "celsius"},
    "room_kitchen": {"value": 26.3, "unit": "celsius"},
    "room_office": {"value": 24.1, "unit": "celsius"},
    "room_bedroom": {"value": 21.5, "unit": "celsius"}
  },
  "message_count": 4,  // All 4 messages preserved! 🎉
  "rooms_monitored": ["lobby", "kitchen", "office", "bedroom"],
  "temperature_analysis": {
    "average": 23.675,
    "min": 21.5,
    "max": 26.3
  }
}
```

## Cross-Room Correlation Example

**Advanced Use Case**: Building HVAC system monitoring all rooms

### Input Messages
```
📨 sensors/lobby/temperature → {"value": 22.8, "humidity": 45, "occupancy": 12}
📨 sensors/kitchen/temperature → {"value": 26.3, "humidity": 60, "occupancy": 3}  
📨 sensors/office/temperature → {"value": 24.1, "humidity": 40, "occupancy": 8}
📨 sensors/bedroom/temperature → {"value": 21.5, "humidity": 50, "occupancy": 2}
```

## Key Technique: $lookup Pattern

The `$lookup` pattern is essential for accessing specific room data from the collected cache:

```javascript
// Build the dynamic label key for the triggering room
$trigger := 'room_' & $context.vars.room;  // e.g., 'room_kitchen'

// Get the specific data for the triggering room
$triggerData := $lookup($, $trigger); // kitchen's temperature data

// Access all collected data
$allData := $; // complete room cache
```

**Usage Examples:**
```javascript
// Compare trigger room to others
$triggerTemp := $lookup($, 'room_' & $context.vars.room).value;
$otherRooms := $filter($keys($), function($k) { $k != 'room_' & $context.vars.room });

// Statistical analysis
$allTemperatures := *.value;
$average := $average($allTemperatures);
$deviation := $triggerTemp - $average;
```

## When to Use Single Wildcard Collection

**Perfect Use Cases:**
- **Building Systems**: All rooms follow `building/{floor}/{room}/temperature` pattern
- **Device Fleets**: All devices use `devices/{type}/{id}/status` pattern  
- **Sensor Networks**: All sensors follow `sensors/{location}/{sensor}/data` pattern
- **Regional Monitoring**: All regions use `network/{region}/{node}/metrics` pattern

**Key Benefits:**
- **Unified Analysis**: Compare and correlate across all matching topics
- **Statistical Processing**: Calculate averages, detect outliers, trend analysis
- **Efficient Caching**: Single pattern, predictable memory usage
- **Cross-Topic Intelligence**: Rich analytics across the topic set

**Next**: [Array Subscription](../02_array/) for handling multiple incompatible topic structures
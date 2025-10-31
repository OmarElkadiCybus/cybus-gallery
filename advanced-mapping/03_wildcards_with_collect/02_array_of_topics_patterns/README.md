# Array Subscription with Wildcards & Dynamic Labels

- **Specialty**: Handle multiple incompatible topic structures in a single mapping
- **Complexity Solved**: Wildcard names + dynamic labels manage heterogeneous data streams

## The Array Subscription Specialty

**Array subscription with wildcards** is the **only solution** when you need to integrate topics that have:
- **Different root paths** (cannot use single wildcard)
- **Different hierarchical depths** (varying topic levels)
- **Different business domains** (incompatible naming conventions)


### ✅ Array Subscription Solution
```yaml
# This HANDLES any topic structure complexity:
subscribe:
  - topic: factory/production/+line/status      # 4 levels
    label: 'prod_{line}'
  - topic: maintenance/+equipment/next          # 3 levels  
    label: 'maint_{equipment}'
  - topic: quality/inspection/+batch/+test/result  # 5 levels
    label: 'quality_{batch}_{test}'
```

**Complexity Handled:**
- ✅ **Mixed depths**: 3, 4, 5 topic levels in same service
- ✅ **Different domains**: factory, maintenance, quality systems
- ✅ **Unique naming**: Each domain uses its own conventions

## How Wildcard Names Solve Complexity

**Wildcard names** (`{name}`) are the **key innovation** that makes array subscription manageable:

### Problem: Heterogeneous Topic Structures
```yaml
# Real enterprise scenarios - completely different structures:
factory/production/line-a/status           # Manufacturing
maintenance/schedule/pump-005/next         # Maintenance  
quality/inspection/batch-001/pcr/result    # Laboratory
inventory/warehouse-b/steel/level          # Supply chain
security/building-1/floor-3/camera-12/alert # Security
```

### Solution: Semantic Wildcard Names
```yaml
subscribe:
  - topic: factory/production/+line/status
    label: 'prod_{line}'                   # Extracts: line="line-a"
  - topic: maintenance/schedule/+equipment/next  
    label: 'maint_{equipment}'             # Extracts: equipment="pump-005"
  - topic: quality/inspection/+batch/+test/result
    label: 'qual_{batch}_{test}'           # Extracts: batch="batch-001", test="pcr"
  - topic: inventory/+warehouse/+material/level
    label: 'inv_{warehouse}_{material}'    # Extracts: warehouse="warehouse-b", material="steel"
  - topic: security/+building/+floor/+camera/alert
    label: 'sec_{building}_{floor}_{camera}' # Extracts: building="building-1", floor="floor-3", camera="camera-12"
```

**Complexity Management:**
- 🎯 **Semantic extraction**: Names like `{line}`, `{equipment}` have business meaning
- 🔑 **Unique identification**: Each wildcard combination creates distinct cache keys
- 🏗️ **Structured access**: Transform can process by domain using naming patterns

## What is a Collect Rule?

A **collect rule** stores the **last received message** from each subscribed topic in a **key-value cache** using **dynamic labels as keys**. When any new message arrives, the collect rule triggers and provides access to **all cached data** from all topics.

```yaml
subscribe:
  - topic: sensors/+room/temperature
    label: '{room}_temp'

rules:
- collect: {}  # Stores all incoming messages
- transform:   # Triggered on each new message
    expression: |
      {
        "all_data": $,           # Access to ALL cached messages
        "trigger": $context.topic # Which topic triggered this transform
      }
```

**Input/Output Example:**
```
📨 Message 1: sensors/kitchen/temperature → {"value": 22.5}
📨 Message 2: sensors/office/temperature → {"value": 24.1}
📨 Message 3: sensors/lobby/temperature → {"value": 21.8}

✨ Transform Output (triggered by any message):
{
  "all_data": {
    "kitchen_temp": {"value": 22.5},
    "office_temp": {"value": 24.1}, 
    "lobby_temp": {"value": 21.8}
  },
  "trigger": "sensors/lobby/temperature"  // Last message that triggered
}
```

## Why Dynamic Labels are Essential

**The Problem**: Without dynamic labels, data gets overwritten!

### ❌ Static Labels Example - Different Topic Structures with Data Loss

```yaml
subscribe:
  - topic: factory/production/+line/status      # 4 levels
    label: 'production_data'                    # Same label for all production!
  - topic: maintenance/+equipment/next          # 3 levels
    label: 'maintenance_data'                   # Same label for all maintenance!
  - topic: quality/inspection/+batch/+test/result  # 5 levels
    label: 'quality_data'                       # Same label for all quality!

rules:
- collect: {}
- transform:
    expression: |
      {
        "cached_data": $,
        "message_count": $count($keys($))
      }
```

**Input Messages (Different Topic Structures):**
```
📨 factory/production/line-a/status → {"line": "line-a", "status": "running", "output": 45}
📨 factory/production/line-b/status → {"line": "line-b", "status": "idle", "output": 0}  
📨 maintenance/pump-005/next → {"equipment": "pump-005", "date": "2024-10-28", "hours": 4}
📨 maintenance/conveyor-belt/next → {"equipment": "conveyor-belt", "date": "2024-10-29", "hours": 2}
📨 quality/inspection/batch-001/pcr/result → {"batch": "batch-001", "test": "pcr", "passed": true}
📨 quality/inspection/batch-002/visual/result → {"batch": "batch-002", "test": "visual", "passed": false}
```

**❌ Static Labels Result:**
```json
{
  "cached_data": {
    "production_data": {"line": "line-b", "status": "idle", "output": 0},      // Only last production!
    "maintenance_data": {"equipment": "conveyor-belt", "date": "2024-10-29", "hours": 2}, // Only last maintenance!
    "quality_data": {"batch": "batch-002", "test": "visual", "passed": false}  // Only last quality!
  },
  "message_count": 3  // Lost 3 out of 6 messages! ⚠️
}
```

### ✅ Dynamic Labels Example - Different Structures, All Data Preserved

```yaml
subscribe:
  - topic: factory/production/+line/status      # 4 levels
    label: 'prod_{line}'                        # Unique label per production line
  - topic: maintenance/+equipment/next          # 3 levels  
    label: 'maint_{equipment}'                  # Unique label per equipment
  - topic: quality/inspection/+batch/+test/result  # 5 levels
    label: 'qual_{batch}_{test}'                # Unique label per batch+test

rules:
- collect: {}
- transform:
    expression: |
      {
        "cached_data": $,
        "message_count": $count($keys($)),
        "domain_breakdown": {
          "production_lines": $count($filter($keys($), function($k) { $contains($k, 'prod_') })),
          "maintenance_items": $count($filter($keys($), function($k) { $contains($k, 'maint_') })),
          "quality_tests": $count($filter($keys($), function($k) { $contains($k, 'qual_') }))
        }
      }
```

**Same Input Messages (Different Topic Structures):**
```
📨 factory/production/line-a/status → {"line": "line-a", "status": "running", "output": 45}
📨 factory/production/line-b/status → {"line": "line-b", "status": "idle", "output": 0}  
📨 maintenance/pump-005/next → {"equipment": "pump-005", "date": "2024-10-28", "hours": 4}
📨 maintenance/conveyor-belt/next → {"equipment": "conveyor-belt", "date": "2024-10-29", "hours": 2}
📨 quality/inspection/batch-001/pcr/result → {"batch": "batch-001", "test": "pcr", "passed": true}
📨 quality/inspection/batch-002/visual/result → {"batch": "batch-002", "test": "visual", "passed": false}
```

**✅ Dynamic Labels Result:**
```json
{
  "cached_data": {
    "prod_line-a": {"line": "line-a", "status": "running", "output": 45},
    "prod_line-b": {"line": "line-b", "status": "idle", "output": 0},
    "maint_pump-005": {"equipment": "pump-005", "date": "2024-10-28", "hours": 4},
    "maint_conveyor-belt": {"equipment": "conveyor-belt", "date": "2024-10-29", "hours": 2},
    "qual_batch-001_pcr": {"batch": "batch-001", "test": "pcr", "passed": true},
    "qual_batch-002_visual": {"batch": "batch-002", "test": "visual", "passed": false}
  },
  "message_count": 6,  // All 6 messages preserved! 🎉
  "domain_breakdown": {
    "production_lines": 2,
    "maintenance_items": 2, 
    "quality_tests": 2
  }
}
```
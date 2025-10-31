# Stash Rule: Simple Examples

The `stash` rule stores a copy of your message data that you can access in the **next message**. Think of it as "remember this for later".

## What Stash Does

**Simple Concept**: Save the **ENTIRE payload** from the previous step so you can access it in the next message.

**CRITICAL**: Stash stores the **complete payload** from the step right before it, not individual fields!

```yaml
# Step 1: Process current data and access previous COMPLETE payload
- transform:
    expression: |
      {
        "current": $,                    # Current message
        "previous": $last("myStash")     # ENTIRE previous payload from stash
      }

# Step 2: Store COMPLETE current payload for the next message
- stash:
    label: myStash    # Stores the ENTIRE payload from step 1
```

**What gets stored**: The complete transformed object `{"current": ..., "previous": ...}`  
**What you can access**: Any field from that complete object using `$last("myStash").current` or `$last("myStash").previous`

## Basic Example: Temperature Comparison

**What we want**: Compare current temperature with the previous temperature reading.

```yaml
rules:
- transform:
    expression: |
      {
        "current_temp": $.temperature,
        "previous_temp": $last("tempHistory").temperature,
        "change": $.temperature - $last("tempHistory").temperature
      }
- stash:
    label: tempHistory    # Store this message for next time
```

**Flow:**
1. **Message 1**: `{"temperature": 25}` → No previous data yet
2. **Message 2**: `{"temperature": 27}` → Can compare with Message 1
3. **Message 3**: `{"temperature": 24}` → Can compare with Message 2

## Complete Example with Input/Output

**Message Sequence:**

**First Message:**
- **Input:** `{"temperature": 25, "sensor": "room-01"}`
- **Previous:** `null` (nothing stashed yet)
- **Output:** 
```json
{
  "current_temp": 25,
  "previous_temp": null,
  "change": null,
  "sensor": "room-01"
}
```
- **Stashed:** `{"temperature": 25, "sensor": "room-01"}`

**Second Message:**
- **Input:** `{"temperature": 27, "sensor": "room-01"}`
- **Previous:** `{"temperature": 25, "sensor": "room-01"}` (from stash)
- **Output:**
```json
{
  "current_temp": 27,
  "previous_temp": 25,
  "change": 2,
  "sensor": "room-01"
}
```
- **Stashed:** `{"temperature": 27, "sensor": "room-01"}`

**Third Message:**
- **Input:** `{"temperature": 24, "sensor": "room-01"}`
- **Previous:** `{"temperature": 27, "sensor": "room-01"}` (from stash)
- **Output:**
```json
{
  "current_temp": 24,
  "previous_temp": 27,
  "change": -3,
  "sensor": "room-01"
}
```

## Examples Comparison from service.scf.yaml

The service file contains 5 different stash patterns. Here's how they compare:

### **Stash Positioning Comparison**

| Example | Stash Position | What Gets Stored | When To Use |
|---------|---------------|------------------|-------------|
| **stashFirst** | Before processing | Raw input message | Need original values for comparison |
| **stashLast** | After processing | Transformed result | Need processed values for comparison |

### **Complexity Comparison**

| Example | Complexity | Purpose | Data Flow |
|---------|-----------|---------|-----------|
| **simpleComparison** | **Simplest** | Current vs previous value | `input` → `transform+compare` → `stash` |
| **stashFirst** | **Simple** | Original input comparison | `stash` → `transform+compare` |
| **stashLast** | **Simple** | Processed result comparison | `transform+compare` → `stash` |
| **multipleStashes** | **Complex** | Compare both raw AND processed | `stash` → `transform` → `compare both` → `stash` |
| **perStationCounter** | **Most Complex** | Per-station state tracking | `collect` → `lookup+increment` → `stash` → `format` |

### **Use Case Analysis**

**📊 Simple Data Comparison (simpleComparison, stashFirst, stashLast)**
- **Goal**: Compare current message with previous message
- **Pattern**: One stash, simple comparison
- **Difference**: Only **when** the stash happens (before/after processing)

**🎯 Dual History Tracking (multipleStashes)**
- **Goal**: Compare both original input AND processed values
- **Pattern**: Two stashes at different stages
- **Why needed**: Final output requires both raw and processed history

**🏭 Stateful Processing (perStationCounter)**
- **Goal**: Maintain separate state per dynamic key (station name)
- **Pattern**: Collect → Stateful Transform → Stash → Clean Output
- **Why essential**: No other way to track per-station counters across messages

### **Key Insights**

**🔍 When Position Matters:**
- **Early stash**: Preserves original input for later comparison
- **Late stash**: Stores processed result for next message

**📈 Complexity Justified:**
- **Multiple stashes**: Only when you need BOTH raw AND processed history
- **Stateful stash**: Only when tracking dynamic per-key state (like counters)
- **Simple stash**: For basic current vs previous comparisons

**⚡ Performance Considerations:**
- More stashes = more memory usage
- Complex expressions = more processing time
- Stateful patterns = necessary complexity for real-world scenarios

## Key Understanding: Stash Stores ENTIRE Payload

**IMPORTANT**: Each stash stores the **complete payload** from the previous step, not individual fields!

```yaml
# CORRECT: One stash stores the entire transformed object
- stash:
    label: myData    # Stores the COMPLETE payload from previous step

# WRONG: You cannot stash individual fields separately
- stash:
    label: temperature    # This doesn't store just temperature!
- stash:
    label: pressure       # This doesn't store just pressure!
```

## Common Use Cases

1. **Trend Detection**: Compare current vs previous values
2. **Change Alerts**: Trigger alerts when values change significantly  
3. **Data Validation**: Check if new data makes sense compared to previous
4. **Rate Calculations**: Calculate speed, acceleration, etc.
5. **State Tracking**: Remember previous state to detect transitions

## When Multiple Stashes Are Actually Needed

Based on the service examples, you need multiple stashes when:

### **✅ ESSENTIAL: Per-Station Counter**
- **Why essential**: Must maintain separate counters for each station
- **Cannot be simplified**: No other way to track per-station state
- **Real-world use case**: Production line monitoring, multi-device tracking

### **✅ USEFUL: Multiple Processing Stages**
- **Why useful**: Need access to both original AND processed data in final output
- **Example**: Compare raw sensor reading vs calculated/processed value
- **Use case**: Data validation, change analysis across processing stages

### **⚠️ ORDER MATTERS: Different Stash Positions**
- **Stash First**: Store original input before any processing
- **Stash Last**: Store final processed result
- **Multiple Stashes**: Store data at different stages

### **❌ NOT NEEDED: Simple Current vs Previous**
- **Single stash is enough**: Just comparing current message with previous message
- **Direct field access**: Can access `$.temperature`, `$.pressure` etc. directly
- **No extra complexity needed**: One stash + direct current access works fine

## Key Points

- ✅ **Memory Between Messages**: Access previous message data
- ✅ **Simple Comparisons**: Compare current vs previous values
- ✅ **Order Flexibility**: Stash at different stages of processing
- ✅ **Multiple Labels**: Store different data with different labels

## Important Notes

- **First Message**: `$last("label")` returns `null` for the first message
- **Per Topic**: Each topic has its own stash storage
- **Labels**: Use descriptive labels to identify different stashes
- **Order Matters**: Different positions store different data

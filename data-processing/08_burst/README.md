# Data Processing Rule: burst

## Rule Description
The `burst` rule consolidates multiple incoming messages into a single array message. It operates on an **OR relationship** between two trigger conditions:
- **maxSize**: Maximum number of messages to collect before bursting
- **interval**: Maximum time to wait before bursting (in milliseconds)

**Key Concept**: The burst triggers when **EITHER** condition is met first (whichever comes first).

## Trigger Logic (OR Relationship)

The burst rule uses **OR logic**, meaning it triggers when the **first condition** is satisfied:

```
Burst Triggers = (Message Count ≥ maxSize) || (Time Elapsed ≥ interval)
```

### Configuration Options

| Configuration | Behavior | Use Case |
|---------------|----------|----------|
| **maxSize only** | Burst when N messages collected | High-frequency data with consistent rate |
| **interval only** | Burst every N milliseconds | Low-frequency data with irregular timing |
| **Both together** | Burst when EITHER condition met | Variable-frequency data requiring adaptive batching |

## Detailed Examples

### Example 1: Size-Based Bursting (`maxSize` only)
**Perfect for**: High-frequency sensors with predictable data rates

```yaml
rules:
- burst:
    maxSize: 3  # Burst after collecting exactly 3 messages
```

**Behavior**: Accumulates messages until 3 are collected, then immediately publishes the batch.

**Timeline Example**:
```
t=0ms:   Message 1 arrives → Buffer: [msg1]
t=100ms: Message 2 arrives → Buffer: [msg1, msg2]  
t=200ms: Message 3 arrives → Buffer: [msg1, msg2, msg3] → BURST TRIGGERED!
t=200ms: Output: [msg1, msg2, msg3]
```

### Example 2: Time-Based Bursting (`interval` only)
**Perfect for**: Sporadic data that needs regular processing

```yaml
rules:
- burst:
    interval: 2000  # Burst every 2 seconds
```

**Behavior**: Publishes whatever messages have accumulated every 2 seconds.

**Timeline Example**:
```
t=0ms:    Message 1 arrives → Buffer: [msg1]
t=500ms:  Message 2 arrives → Buffer: [msg1, msg2]
t=2000ms: Timer expires → BURST TRIGGERED!
t=2000ms: Output: [msg1, msg2] (only 2 messages, but time limit reached)
```

### Example 3: Adaptive Bursting (Both `maxSize` AND `interval`)
**Perfect for**: Variable-frequency data requiring flexible batching

```yaml
rules:
- burst:
    maxSize: 5      # Burst when 5 messages collected
    interval: 3000  # OR burst every 3 seconds (whichever comes first)
```

**Scenario A - Size Limit Reached First**:
```
t=0ms:   Message 1 → Buffer: [msg1]
t=100ms: Message 2 → Buffer: [msg1, msg2]
t=200ms: Message 3 → Buffer: [msg1, msg2, msg3]
t=300ms: Message 4 → Buffer: [msg1, msg2, msg3, msg4]
t=400ms: Message 5 → Buffer: [msg1, msg2, msg3, msg4, msg5] → SIZE LIMIT REACHED!
t=400ms: Output: [msg1, msg2, msg3, msg4, msg5]
```

**Scenario B - Time Limit Reached First**:
```
t=0ms:    Message 1 → Buffer: [msg1]
t=1000ms: Message 2 → Buffer: [msg1, msg2]
t=3000ms: Timer expires → TIME LIMIT REACHED!
t=3000ms: Output: [msg1, msg2] (only 2 messages, but time limit reached)
```

## Real-World Use Cases

### Manufacturing Line Monitoring
```yaml
rules:
- burst:
    maxSize: 10     # High production: batch every 10 readings
    interval: 5000  # Low production: batch every 5 seconds
```

**High Production Scenario**: Sensors firing rapidly → Size limit triggers frequent small batches
**Maintenance Period**: Few sensors active → Time limit ensures regular processing

### IoT Sensor Networks
```yaml
rules:
- burst:
    maxSize: 20     # Peak hours: batch every 20 readings  
    interval: 30000 # Off-peak: batch every 30 seconds
```

**Peak Traffic**: Quick batching prevents buffer overflow
**Low Traffic**: Regular batching ensures timely processing

## Input/Output Examples

### Input Messages (Various Scenarios)
```json
# High-frequency sensor data
{"sensorId": "temp-01", "value": 23.1, "timestamp": "2025-10-29T10:00:00.000Z"}
{"sensorId": "temp-01", "value": 23.2, "timestamp": "2025-10-29T10:00:00.100Z"}
{"sensorId": "temp-01", "value": 23.3, "timestamp": "2025-10-29T10:00:00.200Z"}

# Low-frequency sensor data  
{"sensorId": "press-01", "value": 1013.1, "timestamp": "2025-10-29T10:00:00.000Z"}
{"sensorId": "press-01", "value": 1013.2, "timestamp": "2025-10-29T10:00:02.500Z"}
```

### Output Messages
```json
# Size-triggered burst (3 messages reached maxSize=3)
[
  {"sensorId": "temp-01", "value": 23.1, "timestamp": "2025-10-29T10:00:00.000Z"},
  {"sensorId": "temp-01", "value": 23.2, "timestamp": "2025-10-29T10:00:00.100Z"},
  {"sensorId": "temp-01", "value": 23.3, "timestamp": "2025-10-29T10:00:00.200Z"}
]

# Time-triggered burst (interval=2000ms reached with only 2 messages)
[
  {"sensorId": "press-01", "value": 1013.1, "timestamp": "2025-10-29T10:00:00.000Z"},
  {"sensorId": "press-01", "value": 1013.2, "timestamp": "2025-10-29T10:00:02.500Z"}
]
```

## Configuration Best Practices

### Choosing maxSize
- **Small values (2-5)**: Low-latency processing, frequent small batches
- **Medium values (10-50)**: Balanced efficiency and latency  
- **Large values (100+)**: High-throughput processing, potential higher latency

### Choosing interval
- **Short intervals (1-5 seconds)**: Near real-time processing
- **Medium intervals (10-30 seconds)**: Regular processing cycles
- **Long intervals (60+ seconds)**: Batch processing, analytics workloads

### Combining Both
Use both parameters when:
- **Data rate varies** throughout the day
- **Need guaranteed maximum latency** (interval provides upper bound)
- **Need efficient batching** during high-traffic periods (maxSize optimizes throughput)
- **Building resilient systems** that handle both peak and off-peak loads

The complete implementation with all examples is available in the `service.scf.yaml` file in this directory.

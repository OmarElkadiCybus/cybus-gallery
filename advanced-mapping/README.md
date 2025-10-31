# Advanced Mapping Patterns

**Purpose**: Master advanced Cybus Connectware mapping techniques for enterprise-scale Industrial IoT systems
**Complexity**: ⭐⭐⭐ Advanced | **Focus**: Multi-topic patterns, wildcards, and dynamic routing

## Overview

This section covers sophisticated mapping patterns that go beyond basic single-topic transformations. Learn how to handle complex enterprise scenarios involving multiple data sources, dynamic routing, and advanced aggregation patterns.

## Tutorial Structure

### 📁 [01_basic_wildcards/](./01_basic_wildcards/) - Fundamental Wildcard Patterns  
**Complexity**: ⭐⭐ Beginner to Intermediate | **Focus**: Single wildcard patterns with context variables

**What You'll Learn:**
- **Wildcard syntax**: `+` for single level, `#` for multi-level matching
- **Context variables**: Access wildcard values via `$context.vars.{name}`
- **Dynamic topic handling** without hardcoding specific paths
- **Factory monitoring patterns** across production lines

**Perfect For:**
- **Same root structure**: `factory/+line/+machine/status`
- **Building automation**: `building/+floor/+room/+sensor`
- **Device fleets**: `devices/+location/+type/+id`

---

### 📁 [02_array/](./02_array/) - Array-Based Multi-Topic Mapping
**Complexity**: ⭐⭐⭐ Intermediate | **Focus**: Multiple topics with different roots

**What You'll Learn:**
- **Array subscriptions** for topics with incompatible structures  
- **Labels and collect rules** for data correlation
- **Multi-system integration** patterns
- **Cross-domain analytics** from heterogeneous sources

**Perfect For:**
- **Different root paths**: `factory/machine`, `building/hvac`, `vehicle/sensor`
- **Mixed protocols**: MQTT topics + OPC UA endpoints + HTTP APIs
- **Enterprise integration**: Combine disparate business systems

---

### 📁 [03_wildcards_with_collect/](./03_wildcards_with_collect/) - Advanced Aggregation
**Complexity**: ⭐⭐⭐ Advanced | **Focus**: Wildcard + collect rule combinations

**What You'll Learn:**
- **Single pattern aggregation**: Collect data from multiple topics matching one pattern
- **Array pattern aggregation**: Handle multiple incompatible wildcard patterns  
- **Dynamic labels**: Prevent data loss with semantic cache keys
- **Cross-correlation analytics**: Build enterprise-grade analytics systems

**Perfect For:**
- **Multi-room analytics**: Compare and correlate sensor data across buildings
- **Production monitoring**: Aggregate performance across different manufacturing lines
- **Enterprise dashboards**: Combine data from various business domains

---

### 📁 [04_dynamic_publish_topic/](./04_dynamic_publish_topic/) - Context-Based Routing
**Complexity**: ⭐⭐⭐ Advanced | **Focus**: Dynamic topic construction and routing

**What You'll Learn:**
- **Wildcard-based routing**: Use topic segments for output topic construction
- **Payload-based routing**: Route messages based on content using setContextVars
- **Multi-tenant systems**: Separate data streams by organization or service
- **UNS implementation**: Transform legacy systems into Unified Namespace hierarchies

**Perfect For:**
- **Multi-tenant SaaS**: Route by customer and service type
- **Alert systems**: Route by severity and destination
- **Legacy modernization**: Transform to ISA-95 compliant hierarchies

## Quick Decision Guide

**🔧 Choose Basic Wildcards ([01_basic_wildcards/](./01_basic_wildcards/)) When:**
- Topics **share root structure**: `sensors/+room/+type` or `devices/+location/+id`
- Need **simple dynamic handling** without complex aggregation
- **Getting started** with wildcard patterns

**🎯 Choose Array Mapping ([02_array/](./02_array/)) When:**
- Topics have **completely different roots**: `factory/line` vs `building/hvac` vs `vehicle/engine`
- **Mixed endpoint types**: MQTT + OPC UA + HTTP in same service
- **Enterprise integration**: Combining disparate business systems

**📊 Choose Wildcards + Collect ([03_wildcards_with_collect/](./03_wildcards_with_collect/)) When:**
- Need **data aggregation** across wildcard matches
- **Cross-correlation analytics**: Compare data from multiple similar sources
- **Statistical processing**: Averages, ranges, outlier detection across topic sets

**🚀 Choose Dynamic Publishing ([04_dynamic_publish_topic/](./04_dynamic_publish_topic/)) When:**
- **Routing requirements**: Messages need different destinations based on content
- **Multi-tenant architecture**: Separate data streams by tenant or service
- **UNS transformation**: Converting legacy systems to standardized hierarchies

## Learning Path

### 🎯 **Recommended Sequence**

1. **Start with [Basic Wildcards](./01_basic_wildcards/)** - Master fundamental patterns
   - Learn wildcard syntax and context variables
   - Understand when wildcards are appropriate
   - Practice with factory monitoring examples

2. **Progress to [Array Mapping](./02_array/)** - Handle complexity
   - Learn when arrays are required vs wildcards
   - Master label-based data organization
   - Integrate multiple system types

3. **Advance to [Wildcards + Collect](./03_wildcards_with_collect/)** - Add analytics
   - Combine wildcard flexibility with data aggregation
   - Build cross-correlation analytics systems
   - Handle enterprise-scale data processing

4. **Master [Dynamic Publishing](./04_dynamic_publish_topic/)** - Complete the picture  
   - Route messages based on content and structure
   - Implement multi-tenant architectures
   - Transform legacy systems with UNS patterns

### 💡 **Key Success Factors**

- **Pattern Recognition**: Choose the right approach for your specific use case
- **JSONata Proficiency**: Master expressions for data transformation and routing
- **Label Strategy**: Design meaningful labels that support your business logic
- **Performance Awareness**: Understand the trade-offs between flexibility and efficiency

Each tutorial includes complete working examples, detailed explanations, and real-world enterprise use cases you can adapt for your Industrial IoT deployments.
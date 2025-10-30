# Multi-Topic Mapping Examples

This folder contains comprehensive examples for mapping data from multiple MQTT topics using Cybus Connectware. Learn progressive techniques from basic arrays to advanced enterprise integration patterns including UNS (Unified Namespace) implementation.

## Mapping Approaches Overview

| Approach | Complexity | Use Case | Best For |
|----------|------------|----------|----------|
| **Arrays** | ⭐ Basic | Different topic roots, mixed endpoints | Different systems, precise control |
| **Wildcards** | ⭐⭐ Intermediate | Same topic root, pattern matching | Topic discovery, hierarchical data |
| **Wildcards + Collect** | ⭐⭐⭐ Advanced | Aggregate wildcard matches, cross-correlation | Analytics, enterprise integration |
| **Dynamic Publish Topics** | ⭐⭐⭐⭐ Expert | Content-based routing, UNS implementation | Multi-tenant, business logic routing |

## Detailed Folder Descriptions

### 📁 [01_array/](./01_array/) - Array Subscription Fundamentals
**Complexity**: ⭐ Basic | **Focus**: Explicit topic control with different structures

Learn the foundation of multi-topic mapping using array subscriptions. Perfect for:
- **Mixed System Integration**: Combine data from `factory/machines`, `building/sensors`, `vehicle/telemetry`
- **Protocol Flexibility**: Handle MQTT topics alongside simulated OPC UA and HTTP endpoints
- **Precise Source Control**: Explicitly define each topic with custom labels
- **Enterprise Basics**: Foundation for complex industrial data integration

**Key Learning**: How collect rules create key-value caches from multiple sources and why proper JSONata syntax (`$lookup()`) is essential for accessing cached data.

### 📁 [02_wildcards/](./02_wildcards/) - Wildcard Pattern Matching  
**Complexity**: ⭐⭐ Intermediate | **Focus**: Automatic topic discovery and context variables

Master wildcard-based topic subscriptions for scalable, pattern-driven data collection:
- **Hierarchical Data**: Handle `building/+floor/+room/+sensor` patterns automatically
- **Context Variables**: Extract wildcard values using `$context.vars.{name}` for transforms
- **Scalable Discovery**: Automatically process new topics matching patterns
- **Device Fleet Management**: Monitor `devices/+type/+id/status` across entire fleets

**Key Learning**: How wildcard names create context variables and the difference between MQTT wildcards (`+`, `#`) and topic dynamic labels.

### 📁 [03_wildcards_with_collect/](./03_wildcards_with_collect/) - Advanced Wildcard Aggregation
**Complexity**: ⭐⭐⭐ Advanced | **Focus**: Cross-correlation and dynamic label strategies

Combine wildcard patterns with collect rules for sophisticated data aggregation and analytics:
- **Cross-Domain Intelligence**: Integrate `factory/+line/status`, `maintenance/+equipment/schedule`, `quality/+batch/results`
- **Dynamic Label Prevention**: Avoid data loss with semantic labeling (`prod_{line}`, `maint_{equipment}`)
- **Enterprise Analytics**: Build manufacturing execution systems with real-time correlation
- **Statistical Processing**: Calculate averages, detect outliers, and trend analysis across topic sets

**Key Learning**: Why dynamic labels are critical for preventing data overwrite in collect caches and advanced JSONata patterns for enterprise-scale data processing.

### 📁 [04_dynamic_publish_topic/](./04_dynamic_publish_topic/) - Content-Based Routing & UNS
**Complexity**: ⭐⭐⭐⭐ Expert | **Focus**: Dynamic routing and Unified Namespace implementation

Master advanced routing techniques and enterprise standardization:

#### 🔀 **[wildcards/](./04_dynamic_publish_topic/wildcards/)** - Topic Structure Routing
Route messages based on input topic structure using wildcard variables:
- **Multi-Tenant SaaS**: `saas/{tenant}/{service}/events` → `analytics/{tenant}/services/{service}/processed`
- **Building Automation**: `sensors/{room}/{sensor}/data` → `alerts/{room}/{sensor}/status`
- **UNS Transformation**: `legacy/{site}/{line}/data` → `UNS/Enterprise/{site}/Line/{line}`
- **ISA-95 Compliance**: Transform legacy systems into standardized enterprise hierarchies

#### ⚙️ **[set_context_vars/](./04_dynamic_publish_topic/set_context_vars/)** - Payload-Based Routing
Route messages based on payload content using setContextVars rule:
- **Alert Prioritization**: Route by `$.priority` to `alerts/{tenant}/{priority}/notifications`
- **Business Logic**: `$.order.amount > 1000` → `processing/enterprise/urgent/orders`
- **IoT Device Health**: Route by device status to `devices/{type}/{location}/{health}/data`
- **Conditional Routing**: Apply complex business rules for dynamic destination selection

**Key Learning**: UNS (Unified Namespace) implementation patterns, setContextVars syntax with `vars:` property, and enterprise-grade routing strategies for multi-tenant systems.

## Quick Decision Guide

**🎯 Use Arrays When:**
- Topics have **different roots**: `factory/machine`, `car/sensor`, `building/hvac`
- Mixing **different endpoints**: MQTT + OPC UA + HTTP
- Need **precise control** over specific sources
- **Learning foundation** mapping concepts

**🔍 Use Wildcards When:**
- Topics share **common root**: `building/+floor/+room/temperature`
- Want **automatic discovery** of matching topics
- All sources are **MQTT topics**
- **Scalable device fleets** or hierarchical systems

**📊 Use Wildcards + Collect When:**
- Need **cross-correlation** between related topics
- Building **analytics and dashboards**
- **Enterprise integration** across multiple systems
- **Manufacturing execution** or process monitoring

**🚀 Use Dynamic Publish Topics When:**
- **Multi-tenant systems** requiring isolation
- **Content-based routing** and business logic
- **UNS (Unified Namespace)** standardization
- **Legacy system modernization**

## Progressive Learning Path

### 🚀 **Recommended Learning Sequence**

1. **[01_array/](./01_array/)** - **Foundation** 
   - Start here to understand basic multi-topic mapping
   - Learn collect rules and JSONata cache access patterns
   - Essential for all advanced techniques

2. **[02_wildcards/](./02_wildcards/)** - **Pattern Matching**
   - Build on array knowledge with automatic topic discovery
   - Master context variables and wildcard naming
   - Prepare for enterprise-scale applications

3. **[03_wildcards_with_collect/](./03_wildcards_with_collect/)** - **Enterprise Integration**
   - Combine wildcards with collect for advanced analytics
   - Learn dynamic labeling strategies to prevent data loss
   - Master cross-domain correlation techniques

4. **[04_dynamic_publish_topic/](./04_dynamic_publish_topic/)** - **Advanced Routing**
   - Implement content-based and structure-based routing
   - Master UNS (Unified Namespace) transformation patterns
   - Learn enterprise-grade multi-tenant architectures

### 💡 **Integration Flexibility**

All approaches can be **combined within the same service** for maximum flexibility:

```yaml
# Example: Mixed approach in one service
mappings:
- subscribe:  # Array for different systems
    - topic: factory/production/+line/status
      label: 'prod_{line}'
    - topic: maintenance/+equipment/schedule  
      label: 'maint_{equipment}'
  publish:
    topic: 'integrated/{line}/status'  # Dynamic publishing
  rules:
  - collect: {}  # Collect for correlation
  - setContextVars:  # Content-based routing
      vars:
        line: $.production_line_id
  - transform: { ... }
```

### 🎯 **Key Success Factors**

- **JSONata Mastery**: Learn proper `$lookup($, 'key')` syntax for cache access
- **Label Strategy**: Use semantic naming (`prod_{line}`, `maint_{equipment}`)
- **UNS Planning**: Design topic hierarchies for enterprise standardization
- **Performance Considerations**: Understand collect cache sizing and transform optimization
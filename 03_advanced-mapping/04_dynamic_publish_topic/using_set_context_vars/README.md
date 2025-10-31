# Dynamic Publish Topics with SetContextVars

**Purpose**: Use payload data to construct dynamic publish topics using the setContextVars rule  
**Focus**: Content-based routing with business logic  
**Prerequisites**: [Basic Wildcards](../../01_basic_wildcards/), JSONata expressions, and conditional logic

## What You'll Learn

By the end of this tutorial, you'll master:
- ✅ **SetContextVars rule** for extracting routing info from message payloads
- ✅ **Business logic routing** with conditional expressions and data-driven decisions
- ✅ **Multi-tenant architectures** with organization and service-based routing
- ✅ **Complex routing patterns** combining multiple payload fields and business rules

## The Challenge: Content-Driven Routing

Wildcard routing works great when routing info is in the topic structure. But what about routing based on **message content**?

```yaml
# ❌ Topic-based routing can't handle this
Input Topic: system/alerts/incoming           # Same topic for all alerts
Payload 1:   {"tenant": "acme", "priority": "critical", "service": "billing"}
Payload 2:   {"tenant": "globex", "priority": "low", "service": "reports"}

# Need different destinations based on payload content:
# → alerts/acme/critical/billing
# → alerts/globex/low/reports
```

**Solution**: Use `setContextVars` to extract routing information from the payload.

## Step 1: Basic Payload-Based Routing

Start by extracting simple values from the payload:

```yaml
subscribe:
  topic: system/alerts/incoming
publish:
  topic: 'alerts/{tenant}/{priority}/notifications'
rules:
- setContextVars:
    vars:
      tenant: $.organization.tenant_id      # Extract from payload
      priority: $.alert.severity_level      # Extract from payload
- transform:
    expression: |
      {
        "alert_info": $,
        "routing_context": {
          "tenant": $context.vars.tenant,
          "priority": $context.vars.priority
        }
      }
```

**Example Flow:**
- **Input Topic**: `system/alerts/incoming`
- **Payload**: `{"organization": {"tenant_id": "acme"}, "alert": {"severity_level": "critical", "message": "DB down"}}`
- **SetContextVars**: `tenant="acme"`, `priority="critical"`
- **Output Topic**: `alerts/acme/critical/notifications`

## Step 2: Conditional Business Logic Routing

Add business rules and conditional logic to routing decisions:

```yaml
subscribe:
  topic: business/events/orders
publish:
  topic: 'processing/{region}/{category}/{urgency}/orders'
rules:
- setContextVars:
    vars:
      region: $.customer.shipping_address.region
      category: |                               # Business logic in expressions
        $.order.total_amount > 1000 ? "enterprise" :
        $.order.total_amount > 100 ? "business" : "standard"
      urgency: |                                # Conditional routing rules
        $.order.rush_delivery = true ? "urgent" :
        $.order.total_amount > 500 ? "priority" : "normal"
- transform:
    expression: |
      {
        "order_data": $,
        "business_routing": {
          "region": $context.vars.region,
          "category": $context.vars.category,
          "urgency": $context.vars.urgency,
          "routing_logic": "Amount: " & $string($.order.total_amount) & " → " & $context.vars.category
        }
      }
```

**Example Flow:**
- **Payload**: `{"customer": {"shipping_address": {"region": "us-west"}}, "order": {"total_amount": 1250, "rush_delivery": true}}`
- **Business Logic**: Amount > 1000 → "enterprise", rush_delivery = true → "urgent"
- **Output Topic**: `processing/us-west/enterprise/urgent/orders`

## Step 3: Advanced Multi-Field Routing

Combine multiple payload fields with complex conditional logic:

```yaml
subscribe:
  topic: iot/telemetry/incoming
publish:
  topic: 'devices/{device_type}/{location}/{status}/data'
rules:
- setContextVars:
    vars:
      device_type: $.device.type
      location: $.device.metadata.installation_site
      status: |                                 # Complex multi-condition logic
        $.telemetry.battery_level < 20 ? "low-battery" :
        $.telemetry.signal_strength < -80 ? "poor-signal" :
        $.telemetry.error_count > 0 ? "maintenance-needed" : 
        $.device.last_maintenance_days > 90 ? "maintenance-due" : "operational"
- transform:
    expression: |
      {
        "telemetry_data": $,
        "device_routing": {
          "health_status": $context.vars.status,
          "needs_attention": $context.vars.status != "operational",
          "routing_decisions": {
            "battery_ok": $.telemetry.battery_level >= 20,
            "signal_ok": $.telemetry.signal_strength >= -80,
            "no_errors": $.telemetry.error_count = 0
          }
        }
      }
```

**Example Flow:**
- **Payload**: `{"device": {"type": "temp-sensor", "metadata": {"installation_site": "warehouse-a"}}, "telemetry": {"battery_level": 15, "error_count": 0}}`
- **Complex Logic**: Battery < 20 → "low-battery" status  
- **Output Topic**: `devices/temp-sensor/warehouse-a/low-battery/data`

## Key Benefits of SetContextVars Routing

✅ **Business Logic Integration**: Apply complex conditional rules based on data values  
✅ **Multi-tenant Architecture**: Route by organization, customer, or service from payload  
✅ **Dynamic Decision Making**: Different destinations based on real-time data analysis  
✅ **Content Awareness**: Route based on message meaning, not just structure  
✅ **Flexible Conditions**: Handle any JSONata expression for routing logic

## When to Use SetContextVars Routing

### ✅ **Perfect For:**
- **Multi-tenant SaaS platforms**: Route by organization, customer, service level
- **Alert and notification systems**: Route by severity, escalation, destination rules  
- **Business process automation**: Apply workflow routing based on data content
- **IoT device management**: Route by device health, location, type from telemetry
- **E-commerce order processing**: Route by value, region, priority, customer tier
- **Complex conditional scenarios**: When routing depends on multiple data fields

### ❌ **Not Ideal For:**
- **Simple topic-based routing**: Use [wildcards](../using_wildcards/) instead for better performance
- **Static routing patterns**: No need for setContextVars overhead
- **High-frequency data streams**: Processing overhead may impact performance
- **Simple hierarchical routing**: Wildcard patterns are more efficient

### 🔄 **Combining Both Approaches**

For ultimate flexibility, combine wildcard and payload routing:

```yaml
subscribe:
  topic: tenant/+tenant_id/+service/events    # Wildcards for basic structure
publish:
  topic: 'processing/{tenant_id}/{service}/{priority}/{region}/data'
rules:
- setContextVars:
    vars:
      # Use wildcard values
      tenant_id: $context.vars.tenant_id
      service: $context.vars.service
      # Extract from payload  
      priority: $.metadata.priority_level
      region: $.customer.region
```

This pattern gives you the best of both worlds: efficient topic-based routing with intelligent content-driven decisions.

## Real-World Applications

See the [contextvar-routing.scf.yaml](./contextvar-routing.scf.yaml) file for complete working examples including:
- **Multi-tenant alert routing** with organization and priority-based destinations
- **Business order processing** with amount-based categorization and urgency rules
- **IoT device health monitoring** with condition-based maintenance routing
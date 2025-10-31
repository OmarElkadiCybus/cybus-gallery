# Dynamic Publish Topics with SetContextVars

**Purpose**: Use payload data to construct dynamic publish topics using the setContextVars rule  
**Complexity**: ⭐⭐⭐ Advanced | **Focus**: Content-based routing with business logic  
**Prerequisites**: Understanding of JSONata expressions and conditional logic

## How SetContextVars Dynamic Publishing Works

The `setContextVars` rule extracts values from the payload and makes them available as context variables for dynamic topic construction:

```yaml
rules:
- setContextVars:
    vars:
      tenant: $.organization_id
      priority: $.alert_level
- transform:
    expression: |
      {
        "routed_message": $,
        "routing_info": {
          "tenant": $context.vars.tenant,
          "priority": $context.vars.priority
        }
      }
publish:
  topic: 'alerts/{tenant}/{priority}/notification'
```

**Flow:**
1. **Input**: `{"organization_id": "acme", "alert_level": "high", "message": "System down"}`
2. **SetContextVars**: `tenant="acme"`, `priority="high"`
3. **Output Topic**: `alerts/acme/high/notification`

## Basic Pattern

```yaml
mappings:
- subscribe:
    topic: input/data
  publish:
    topic: 'output/{category}/{subcategory}/processed'
  rules:
  - setContextVars:
      vars:
        category: $.data.category
        subcategory: $.metadata.type
  - transform:
      expression: |
        {
          "processed_data": $,
          "routing": {
            "category": $context.vars.category,
            "subcategory": $context.vars.subcategory,
            "final_topic": "output/" & $context.vars.category & "/" & $context.vars.subcategory & "/processed"
          }
        }
```

## Key Benefits

✅ **Content-based Routing**: Route messages based on their payload content
✅ **Business Logic**: Apply routing rules based on data values
✅ **Multi-tenancy**: Route to tenant-specific topics from payload
✅ **Conditional Routing**: Different routes based on data conditions

## Advanced Patterns

### Conditional Routing
```yaml
- setContextVars:
    vars:
      route: |
        $.priority = "critical" ? "urgent" : 
        $.priority = "high" ? "priority" : "standard"
      destination: |
        $.system_type = "production" ? "prod-alerts" : "dev-alerts"
```

### Complex Topic Construction
```yaml
- setContextVars:
    vars:
      tenant: $.customer.organization_id
      region: $.metadata.datacenter_region  
      service: $.service_info.name
      level: $.alert.severity
publish:
  topic: 'tenants/{tenant}/regions/{region}/services/{service}/alerts/{level}'
```

## Examples Included

- **Multi-tenant SaaS**: Route by organization and service
- **Alert Systems**: Route by severity and destination system
- **IoT Device Management**: Route by device type and location from payload
- **Business Process Routing**: Route based on business rules in data

## When to Use

**Perfect for:**
- Routing based on payload content
- Multi-tenant systems with tenant ID in payload
- Business rule-based routing
- Complex conditional routing logic

**Not ideal for:**
- Simple routing based on topic structure (use wildcards instead)
- Static routing patterns
- Performance-critical paths (adds processing overhead)
# Data Processing

Transform, filter, and route your industrial data with Cybus Connectware.

## Data Processing Rules

### Core Rules
- **[Transform Rules](./01_transform/)** - Convert and manipulate data formats
- **[Filter Rules](./02_filter/)** - Conditionally process messages
- **[Parse Rules](./03_parse/)** - Extract data from strings and complex formats
- **[Stash Rules](./04_stash/)** - Store and retrieve state between messages
- **[Collect Rules](./07_collect/)** - Aggregate data from multiple sources
- **[Set Context Variables](./05_setContextVars/)** - Manage dynamic configuration
- **[Change of Value (COV)](./06_cov/)** - Detect and process data changes
- **[Burst Rules](./08_burst/)** - Split single messages into multiple outputs
- **[Complete Rule Chain](./complete_rule_chain_example.scf.yaml)** - See all rules working together

### Advanced Patterns
- **[Array Mapping](./01_array/)** - Handle arrays of topics and data
- **[Wildcard Patterns](./02_wildcards/)** - Dynamic topic discovery and routing
- **[Advanced Collection](./03_wildcards_with_collect/)** - Combine wildcards with data aggregation
- **[Dynamic Routing](./04_dynamic_publish_topic/)** - Context-based message routing

## Need Help?

- **[JSONata Documentation](https://jsonata.org/)** - Expression language used in rules
- **[Cybus Services Documentation](https://docs.cybus.io/documentation/services)** - Service configuration guide
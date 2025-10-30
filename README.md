# Cybus Gallery

A curated collection of **Cybus Connectware** examples, tutorials, and best practices.

## 🎯 What is this?

This repository contains **real-world examples** and **learning resources** for Cybus Connectware, the Industrial IoT platform. Whether you're a beginner or an expert, you'll find practical examples to help you build better Industrial IoT solutions.

## 📚 What's Inside

### 📊 [Data Processing Rules Examples](./data_processing_rules_examples/)

**🎯 Featured Examples:**
- **[Complete Rule Chain](./data_processing_rules_examples/complete_rule_chain_example.scf.yaml)** - Simple 3-rule temperature processing
- **[Stash Patterns](./data_processing_rules_examples/04_stash/)** - 5 different stash usage patterns
- **[Multi-Station Counter](./data_processing_rules_examples/04_stash/service.scf.yaml#L214)** - Real-world per-station event counting

## 🚀 Quick Start

### 1. **Browse Examples**
Each folder contains:
- 📖 **README.md** - Detailed explanations and concepts
- 🔧 **service.scf.yaml** - Ready-to-use Cybus service configurations
- 💡 **Input/Output examples** - See exactly how data flows

### 2. **Try an Example**
```bash
# Clone the repository
git clone https://github.com/OmarElkadiCybus/cybus-gallery.git
cd cybus-gallery

# Start with simple transform example
cd data_processing_rules_examples/01_transform
```

### 3. **Deploy to Connectware**
```bash
# Upload to your Cybus Connectware instance
cybus-tool upload service.scf.yaml
```

## 🎓 Learning Path

**👶 New to Cybus Connectware?**
1. Start with **[Transform Rules](./data_processing_rules_examples/01_transform/)** - Learn basic data manipulation
2. Try **[Filter Rules](./data_processing_rules_examples/02_filter/)** - Understand conditional processing
3. Explore **[Complete Rule Chain](./data_processing_rules_examples/complete_rule_chain_example.scf.yaml)** - See rules working together

**🔧 Ready for Advanced Patterns?**
1. **[Stash Rules](./data_processing_rules_examples/04_stash/)** - Master stateful processing
2. **[Collect Rules](./data_processing_rules_examples/07_collect/)** - Handle multiple data sources
3. **[Multi-Station Counter Pattern](./data_processing_rules_examples/04_stash/service.scf.yaml#L214)** - Real-world state management

## 🏭 Real-World Use Cases

These examples are based on **actual Industrial IoT scenarios**:

- **🌡️ Temperature Monitoring** - Sensor data processing with alerts
- **🏭 Production Line Tracking** - Multi-station event counting
- **📊 Data Validation** - Compare current vs historical values
- **🔄 Protocol Translation** - Transform between different data formats
- **📈 Trend Detection** - Identify changes and patterns in sensor data

## 🤝 Contributing

Found a bug? Have a great example? Want to improve documentation?

1. **Fork the repository**
2. **Create your example** following the existing structure
3. **Submit a pull request**

### 📝 Example Structure
```
your_example/
├── README.md           # Clear explanation and concepts
├── service.scf.yaml    # Working Cybus service configuration  
└── examples/           # Input/output samples (optional)
```

## 📖 Documentation

- **[Cybus Connectware Documentation](https://docs.cybus.io/)**
- **[JSONata Expression Language](https://jsonata.org/)**
- **[MQTT Protocol Guide](https://mqtt.org/)**

## 📞 Support

- **Documentation**: [docs.cybus.io](https://docs.cybus.io/)
- **Issues**: [GitHub Issues](https://github.com/OmarElkadiCybus/cybus-gallery/issues)

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

**Made with ❤️ by the Cybus Community**

*Industrial IoT made simple with Cybus Connectware*

# iOS 26 + Foundation Models - Claude Code Skill

> Expert-level iOS 26 development skill for Claude Code, covering Apple Intelligence, HealthKit State of Mind, Swift Charts, StoreKit 2, and Swift 6 strict concurrency.

[![iOS 26](https://img.shields.io/badge/iOS-26+-blue.svg)](https://developer.apple.com/ios/)
[![Swift 6](https://img.shields.io/badge/Swift-6-orange.svg)](https://swift.org)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

## 🌟 What is This?

A comprehensive **Claude Code skill** built from real-world experience developing a production iOS 26 app. This skill teaches Claude to generate modern iOS code using the latest Apple Intelligence features, with production-ready patterns for Foundation Models, HealthKit, StoreKit 2, and Swift 6 strict concurrency.

## ✨ Features

### 🤖 Foundation Models (iOS 26+)
- Type-safe AI responses with `@Generable` macro
- `LanguageModelSession` configuration and management
- Field-level guidance with `@Guide` attributes
- Prompt engineering best practices
- Fail-soft design patterns

### 🏥 HealthKit State of Mind (iOS 18+)
- Mental health tracking with `HKStateOfMind`
- Valence classification (-1.0 to +1.0 scale)
- Labels and associations mapping
- Proper iOS version availability checks
- Authorization flow patterns

### 📊 Swift Charts (iOS 16+)
- BarMark, LineMark, PointMark visualizations
- Custom axis formatting and styling
- Gradient and modern design patterns
- Data aggregation strategies
- Responsive chart layouts

### 💳 StoreKit 2 (iOS 15+)
- Product loading and caching
- Purchase flow with transaction verification
- Real-time transaction updates
- Restore purchases functionality
- Subscription entitlement management

### ⚡ Swift 6 Strict Concurrency
- Actor isolation patterns
- `@MainActor` best practices
- Sendable conformance
- Task namespace conflict resolution
- Static vs instance method patterns

### 🗄️ Core Data Best Practices
- Automatic lightweight migration
- Migration error recovery
- JSON encoding for complex types
- Thread safety patterns

## 🚀 Installation

### Option 1: Manual Installation

1. Clone this repository:
```bash
git clone https://github.com/EldestGruff/claude-ios26-skill.git
cd claude-ios26-skill
```

2. Copy to Claude Code skills directory:
```bash
mkdir -p ~/.claude/skills
cp -r . ~/.claude/skills/ios26-foundation-models
```

3. Restart Claude Code or reload skills

### Option 2: Direct Download

Download the skill files and place them in `~/.claude/skills/ios26-foundation-models/`

## 📚 Usage

Once installed, Claude Code will automatically use this skill when you ask iOS 26 development questions:

```
"Create a Foundation Models classification service"
"Add HealthKit State of Mind tracking to my app"
"Set up StoreKit 2 with monthly and annual subscriptions"
"Fix this Swift 6 concurrency error"
"Build a Swift Charts analytics dashboard"
```

## 💡 Example Outputs

### Foundation Models Classification
```swift
@Generable
struct ClassificationResponse: Codable, Sendable {
    @Guide(description: "Category (task, idea, note)")
    var category: String

    @Guide(description: "Sentiment from -1.0 to +1.0")
    var sentiment: Double

    @Guide(description: "Suggested tags (hyphen-separated)")
    var suggestedTags: [String]
}
```

### HealthKit State of Mind
```swift
@available(iOS 18.0, *)
func getStateOfMind() async -> StateOfMindSnapshot? {
    // Production-ready HealthKit State of Mind fetching
    // with proper error handling and iOS version checks
}
```

### StoreKit 2 Subscriptions
```swift
@MainActor
@Observable
class SubscriptionManager {
    // Complete subscription management with
    // purchase flow, verification, and entitlements
}
```

## 🎯 What Makes This Special

✅ **Real Production Code** - All patterns tested in a live iOS 26 app
✅ **iOS 26 Bleeding Edge** - Foundation Models, State of Mind APIs
✅ **Swift 6 Compliant** - Strict concurrency, actor isolation
✅ **Fail-Soft Design** - Graceful degradation, never crashes
✅ **Modern Architecture** - @Observable, debouncing, parallel tasks

## 📖 Documentation

- [**instructions.md**](instructions.md) - Comprehensive technical guide (17KB)
- [**examples.md**](examples.md) - 5 detailed working examples (20KB)
- [**skill.json**](skill.json) - Skill metadata and configuration

## 🏗️ Built From

This skill was developed while building **PersonalAI** - a context-aware thought capture app using:
- Foundation Models for AI-powered classification
- HealthKit State of Mind for mental health tracking
- Swift Charts for analytics dashboards
- StoreKit 2 for Free/Pro subscription tiers
- Swift 6 strict concurrency throughout

All code patterns in this skill are **production-tested** and **battle-proven**.

## 🤝 Contributing

Contributions are welcome! This skill will evolve as we discover new patterns and best practices.

### Future Additions
- App Intents integration
- Live Activities
- Widgets with App Intents
- TipKit patterns
- Additional HealthKit data types

## 📝 License

MIT License - See [LICENSE](LICENSE) for details

## 🙏 Credits

Created by Andy with Claude Sonnet 4.5 while developing PersonalAI.

## 🔗 Links

- [Claude Code](https://claude.com/claude-code)
- [Apple Developer Documentation](https://developer.apple.com)
- [Swift.org](https://swift.org)

---

**Made with ❤️ for the iOS development community**

*If this skill helps you build better iOS 26 apps, give it a ⭐!*

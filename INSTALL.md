# Installation Guide

## Quick Install

```bash
# Clone the repository
git clone https://github.com/EldestGruff/claude-ios26-skill.git

# Copy to Claude Code skills directory
mkdir -p ~/.claude/skills
cp -r claude-ios26-skill ~/.claude/skills/ios26-foundation-models
```

That's it! The skill is now available in Claude Code.

## Verify Installation

1. Open Claude Code
2. Ask: "Create a Foundation Models classification service"
3. Claude should respond with production-ready iOS 26 code

## Alternative: Direct Download

If you don't have git installed:

1. Visit https://github.com/EldestGruff/claude-ios26-skill
2. Click "Code" → "Download ZIP"
3. Extract the ZIP file
4. Copy the extracted folder to `~/.claude/skills/ios26-foundation-models`

## Updating the Skill

```bash
cd ~/.claude/skills/ios26-foundation-models
git pull origin main
```

Or simply re-download and replace the files.

## Troubleshooting

**Skill not working?**
- Ensure files are in `~/.claude/skills/ios26-foundation-models/`
- Check that `skill.json` is present
- Restart Claude Code

**Getting outdated responses?**
- Pull the latest changes with `git pull`
- Clear Claude Code cache if available
- Try asking more specific questions

## What's Included

```
~/.claude/skills/ios26-foundation-models/
├── skill.json              # Skill metadata
├── instructions.md         # Technical guide (17KB)
├── examples.md            # Code examples (20KB)
├── README.md              # Documentation
├── LICENSE                # MIT License
└── INSTALL.md            # This file
```

## Usage Examples

Once installed, try these prompts:

```
"Create a Foundation Models service to classify user feedback"
"Add HealthKit State of Mind tracking to my context service"
"Set up StoreKit 2 with free and pro tiers"
"Fix this Swift 6 actor isolation error"
"Build a Swift Charts dashboard with sentiment trends"
```

## Need Help?

- [Open an issue](https://github.com/EldestGruff/claude-ios26-skill/issues)
- [View examples](examples.md)
- [Read the guide](instructions.md)

## Next Steps

After installation:
1. Test the skill with a simple prompt
2. Explore the examples in `examples.md`
3. Read `instructions.md` for deep technical details
4. Start building your iOS 26 app!

Enjoy building with Foundation Models, HealthKit State of Mind, and modern iOS patterns! 🚀

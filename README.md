# Tadpole for Claude and Codex

This plugin connects Claude or Codex to your Tadpole account. You can ask about your calendar, students and finances, and make changes in Tadpole. Claude or Codex shows you each change before it saves it. You sign in with your Tadpole login.

## Codex

1. In the Codex app, open Settings, then Plugins.
2. Click Add, then Add a marketplace. Enter `TadpoleTutoring/tadpole-plugin` and click Add marketplace.
3. Find Tadpole and click Install.
4. On the MCPs tab, click Authenticate next to Tadpole, then sign in.

## Claude

In the Claude app, open Customize, then Plugins. Click Add, then Add marketplace, enter `TadpoleTutoring/tadpole-plugin`, and install Tadpole.

In Claude Code, run these two commands, then type `/mcp`, choose Tadpole and sign in:

```bash
claude plugin marketplace add TadpoleTutoring/tadpole-plugin
claude plugin install tadpole@tadpole
```

Sign in uses port 8080 on your computer. If sign in fails, close anything else using that port and try again.

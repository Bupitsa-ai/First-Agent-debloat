# Session Tools Reference

> Complete guide to Devin's Shell, IDE, Browser, and Desktop tools.

## Overview

Every Devin session provides four integrated tools that give you full visibility and control over the development environment. The **Progress tab** brings them together in one unified view.

```
┌──────────────────────────────────────────────┐
│                Progress Tab                   │
│  ┌─────────┐ ┌─────────┐ ┌────────────────┐ │
│  │  Shell   │ │   IDE   │ │    Browser     │ │
│  └─────────┘ └─────────┘ └────────────────┘ │
│  ┌──────────────────────────────────────────┐│
│  │             Desktop (GUI)                ││
│  └──────────────────────────────────────────┘│
└──────────────────────────────────────────────┘
```

---

## Shell & Terminal

Full command-line access to Devin's development environment.

### Features

| Feature | Description |
|---------|-------------|
| **Full command list** | View every command Devin has executed during the session |
| **Output preview** | See command output without switching contexts |
| **Copy functionality** | Copy commands and outputs to clipboard |
| **Time navigation** | Jump to different points in the session by clicking commands |
| **Progress integration** | Shell commands linked to Devin's progress updates |

### Running Your Own Commands

When you take over Devin's machine, you get full terminal access:
- Run any shell command in Devin's environment
- Install packages, run tests, start servers
- Debug issues interactively
- Commands you run are visible in the session history

### Tips

- Greyed-out commands in history are from future points in the session timeline
- Click any command in the history to jump to that point in time
- Use the three-dots icon to copy a command and its output

---

## IDE

A VS Code-based editor integrated into the session.

### Capabilities

- **Real-time code review**: Watch Devin's edits as they happen
- **Take over editing**: Switch to manual editing when needed
- **Full VS Code features**: Syntax highlighting, search, diff view
- **File navigation**: Browse the entire project file tree

### Taking Over Devin's Task

When taking over:
1. Devin pauses its current work
2. You get full access to the IDE, shell, and browser
3. Make your changes or provide guidance
4. Hand control back to Devin to continue

### Best Practices

- Review changes in real-time to catch issues early
- Use the IDE's diff view to understand what Devin changed
- Take over for complex logic that needs human judgment
- Let Devin handle boilerplate and repetitive edits

---

## Browser

A Chrome browser instance running in Devin's environment.

### Use Cases

| Use Case | Example |
|----------|---------|
| **Testing web UIs** | Navigate your app, fill forms, verify rendering |
| **Reading documentation** | Fetch library docs, API references |
| **OAuth/authentication flows** | Complete login flows that require a browser |
| **Visual verification** | Take screenshots to confirm UI changes |

### Cookie Persistence

Cookies and browser state persist within a session. If you log into a service through the browser, Devin can continue using that authenticated session.

### Browser Automation

Devin can script browser interactions using Playwright via CDP at `http://localhost:29229`:

```python
import playwright
p = playwright.chromium.connect_over_cdp("http://localhost:29229")
# Script login flows, data entry, etc.
# Session state persists after the script finishes
```

---

## Desktop (GUI)

A full Linux desktop environment accessible through the webapp's "Desktop" tab.

### Features

- **Live viewing**: Watch Devin's desktop activity in real-time
- **Interactive control**: Click, type, and interact through the desktop
- **Screen recording**: Devin can record its testing sessions as proof
- **Screenshot capture**: Take and share screenshots of the current state

### When to Use

- Visual testing of frontend applications
- Verifying UI changes render correctly
- Watching Devin interact with GUI applications
- Debugging visual issues interactively

---

## Typical Workflow

```
1. Start session → Devin begins working
2. Monitor Progress tab → Watch shell, IDE, and browser activity
3. Review code in IDE → Check Devin's changes in real-time
4. Test in Browser → Verify web UI behavior
5. Take over if needed → Run commands, edit code, provide guidance
6. Hand back to Devin → Let it complete the task
7. Review PR → Devin creates the pull request
```

## When to Use Each Tool

| Scenario | Tool |
|----------|------|
| Check build output or test results | **Shell** |
| Review or edit code | **IDE** |
| Test a web application | **Browser** |
| Watch GUI interactions | **Desktop** |
| Overview of all activity | **Progress Tab** |

---

## Source

- [Devin Session Tools](https://docs.devin.ai/work-with-devin/devin-session-tools)
- [Computer Use](https://docs.devin.ai/work-with-devin/computer-use)
- [Testing & Video Recordings](https://docs.devin.ai/work-with-devin/testing-and-recordings)

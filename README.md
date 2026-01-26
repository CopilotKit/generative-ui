<div align="center">
  <h1>🔮 Generative UI SDK for Agentic Apps</h1>

  <p>
    <a href="https://www.copilotkit.ai/generative-ui">
      <img alt="Website: Generative UI" src="https://img.shields.io/badge/Website-Generative%20UI-6963ff" />
    </a>
    <a href="https://docs.copilotkit.ai/generative-ui">
      <img alt="Docs: Generative UI" src="https://img.shields.io/badge/Docs-Generative%20UI-6963ff?style=flat" />
    </a>
    <a href="https://www.copilotkit.ai/blog/ag-ui-protocol-bridging-agents-to-any-front-end">
      <img alt="Protocol: AG-UI" src="https://img.shields.io/badge/Protocol-AG--UI-6963ff?style=flat" />
    </a>
    <a href="https://discord.gg/6dffbvGU3D">
      <img alt="Discord" src="https://img.shields.io/discord/1122926057641742418?logo=discord&logoColor=%23FFFFFF&label=Discord&color=%236963ff" />
    </a>
    <a href="https://github.com/CopilotKit/CopilotKit">
      <img alt="GitHub stars" src="https://img.shields.io/github/stars/CopilotKit/CopilotKit" />
    </a>
  </p>

  <p>Build apps that adapt to your users.</p>

  <img width="800" height="533" alt="image" src="https://github.com/user-attachments/assets/a8357fdd-5477-418c-886d-2ca169c54a28" />

</div>

<br />

This repository walks through how CopilotKit works together with agentic UI protocols (AG-UI, A2UI, MCP Apps) to enable Generative UI in practice.

---

## What is Generative UI?

Generative UI is a pattern where parts of the user interface are generated, selected or controlled by an AI agent at runtime, rather than being fully predefined by developers.

Instead of only generating text, agents can send UI state, structured UI specs or interactive UI blocks that the frontend renders in real time. This turns UI from fixed, developer-defined screens into an interface that adapts as the agent works and as context changes.

In the CopilotKit ecosystem, Generative UI is approached in three practical patterns, implemented using different agentic UI protocols and specifications that define how agents communicate UI updates to applications:

- Static Generative UI → AG-UI
- Declarative Generative UI → [A2UI](https://docs.copilotkit.ai/generative-ui-specs/a2ui), [Open-JSON-UI](https://docs.copilotkit.ai/generative-ui-specs/open-json-ui)
- Open-ended Generative UI → [MCP Apps](https://docs.copilotkit.ai/generative-ui-specs/mcp-apps) / Custom UIs

[AG-UI (Agent-User Interaction Protocol)](https://github.com/ag-ui-protocol/ag-ui) acts as the bi-directional runtime interaction layer underneath these patterns, providing the agent ↔ application connection that enables Generative UI and works uniformly across A2UI, MCP Apps, Open-JSON-UI and custom UI specifications.

<img width="800" alt="AG-UI" src="https://github.com/user-attachments/assets/06ee6d3e-58ca-4ec5-bd9d-6982953150c7" />

<br />

The rest of this repo walks through each pattern from most constrained to most open-ended and shows how to implement them using CopilotKit.

---

## Static Generative UI (AG-UI)

Static Generative UI means you pre-build UI components and the agent chooses which component to show and passes it the data it needs. 

This is the most controlled approach: you own the layout, styling and interaction patterns, while the agent controls when and which UI appears.

A common CopilotKit pattern is to `render` UI for an action based on its status using `useCopilotAction` (for example, show a loading view while args stream, then show the final component).

```typescript
"use client";
import { useCopilotAction } from "@copilotkit/react-core";

export function ShowCalendarMeetingAction() {
  useCopilotAction({
    name: "showCalendarMeeting",
    description: "Displays calendar meeting information",
    parameters: [
      { name: "date", type: "string", required: true },
      { name: "time", type: "string", required: true },
      { name: "meetingName", type: "string", required: false },
    ],
    render: ({ status, args }) => {
      if (status === "inProgress") return <LoadingView />;
      return <CalendarMeetingCardComponent {...args} />;
    },
  });

  return null;
}
```

- Try it out: [go.copilotkit.ai/gen-ui-demo](https://go.copilotkit.ai/gen-ui-demo)
- Docs: [docs.copilotkit.ai/generative-ui](https://docs.copilotkit.ai/generative-ui)
- Specs hub (overview): [docs.copilotkit.ai/generative-ui-specs](https://docs.copilotkit.ai/generative-ui-specs)
- Ecosystem (how specs + runtime fit): [copilotkit.ai/generative-ui](https://www.copilotkit.ai/generative-ui)

-- demo --

---

## Declarative Generative UI (A2UI + Open‑JSON‑UI)

Declarative Generative UI sits between static and open-ended approaches. Here, the agent returns a structured UI description (cards, lists, forms, widgets) and the frontend renders it.

Two common declarative specifications used for Generative UI are A2UI and Open-JSON-UI.

1) [A2UI](https://github.com/google/A2UI) → declarative Generative UI spec from Google, described as JSONL-based and streaming, designed for platform-agnostic rendering

2) [Open‑JSON‑UI](https://docs.copilotkit.ai/generative-ui-specs/open-json-ui) → open standardization of OpenAI’s internal declarative Generative UI schema

For example, an agent can respond with an Open‑JSON‑UI payload that describes a UI “card” in JSON and the frontend renders it.

```js
// Example: Agent returns Open-JSON-UI specification
{
  type: "open-json-ui",
  spec: {
    components: [
      {
        type: "card",
        properties: {
          title: "Data Visualization",
          content: { ... }
        }
      }
    ]
  }
}
```

<img width="1038" height="337" alt="Open-JSON-UI" src="https://github.com/user-attachments/assets/67b82b6e-e272-406e-a5b6-d5b14a9bcfd6" />

- Try it out: [go.copilotkit.ai/gen-ui-demo](https://go.copilotkit.ai/gen-ui-demo)
- Docs: [docs.copilotkit.ai/generative-ui](https://docs.copilotkit.ai/generative-ui)
- Open‑JSON‑UI Specs (CopilotKit docs): [docs.copilotkit.ai/generative-ui-specs/open-json-ui](https://docs.copilotkit.ai/generative-ui-specs/open-json-ui)
- A2UI Specs (CopilotKit docs): [docs.copilotkit.ai/generative-ui-specs/a2ui](https://docs.copilotkit.ai/generative-ui-specs/a2ui)
- Ecosystem (how specs + runtime fit): https://www.copilotkit.ai/generative-ui
- How AG‑UI and A2UI fit together: [copilotkit.ai/ag-ui-and-a2ui](https://www.copilotkit.ai/ag-ui-and-a2ui)

---

## Open-ended Generative UI (MCP Apps)

Open-ended Generative UI is when the agent can return a complete UI surface (often HTML/iframes/free-form content) and the frontend mostly acts as a container that displays it.

The tradeoffs are higher: security/performance concerns when rendering arbitrary content, harder styling consistency and reduced portability outside the web.

This pattern is commonly used for MCP Apps. In CopilotKit, MCP Apps support is added by attaching `MCPAppsMiddleware` to your agent, which lets the runtime connect to one or more MCP Apps servers.

```typescript
import { BuiltInAgent } from "@copilotkit/runtime/v2";
import { MCPAppsMiddleware } from "@ag-ui/mcp-apps-middleware";

const agent = new BuiltInAgent({
  model: "openai/gpt-4o",
  prompt: "You are a helpful assistant.",
}).use(
   new MCPAppsMiddleware({
    mcpServers: [
      {
        type: "http",
        url: "http://localhost:3108/mcp",
        serverId: "my-server" // Recommended: stable identifier
      },
    ],
  }),
)
```

- Try it out: [go.copilotkit.ai/gen-ui-demo](https://go.copilotkit.ai/gen-ui-demo)
- Docs: [docs.copilotkit.ai/generative-ui](https://docs.copilotkit.ai/generative-ui)
- MCP Apps spec: [docs.copilotkit.ai/generative-ui-specs/mcp-apps](https://docs.copilotkit.ai/generative-ui-specs/mcp-apps)
- Practical guide (complete integration flow): [Bring MCP Apps into your OWN app with CopilotKit & AG-UI](https://www.copilotkit.ai/blog/bring-mcp-apps-into-your-own-app-with-copilotkit-and-ag-ui)

https://github.com/user-attachments/assets/585add6a-a37a-4a09-a009-a353591c2101

---

## Generative UI Playground

The Generative UI Playground is a hands-on environment for exploring how all three patterns work in practice and see how agent outputs map to UI in real time.

Get Started:
```

```

- Try it out: [go.copilotkit.ai/gen-ui-demo](https://go.copilotkit.ai/gen-ui-demo)
- Repo: [go.copilotkit.ai/gen-ui-repo-playground](https://go.copilotkit.ai/gen-ui-repo-playground)

https://github.com/user-attachments/assets/0e2d9f3b-16c5-4556-bc10-fe0643cebdc3

---

## 🤝 Contributions are welcome

Contributions welcome: PRs adding examples (Static/Declarative/Open‑ended), improving explanations or adding assets.

[Discord](https://discord.com/invite/6dffbvGU3D) for help and discussions. [GitHub](https://github.com/CopilotKit/CopilotKit) to contribute. @CopilotKit for updates.

| Project | Preview | Description | Links |
| ------- | ------- | ----------- | ----- |
| [name of project](deployed url) by [@xyz](github profile link) | <img src="" alt="" width="300"> | description... | [GitHub](repo) • [Demo](deployed) |

Built something? [Open a PR](https://github.com/CopilotKit/CopilotKit/pulls) or [share it in Discord](https://discord.com/invite/6dffbvGU3D).

For AI/LLM agents: [docs.copilotkit.ai/llms.txt](https://docs.copilotkit.ai/llms.txt)
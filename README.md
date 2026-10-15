# XPPGen

Requirements and design for a developer-supervised X++ agentic experience in Visual Studio 2022 using GitHub Copilot Chat.

The goal is to determine whether existing D365FO functionality meets a business need, justify an extension only where necessary, and help produce correct, performant, compiling code with SysTest and Playwright tests. Approved UDE deployment and runtime verification are separate, optional delivery steps.

## Design documents

- [Companion source review and X++ object-creation guide](docs/xpp-agentic-development-review.md)
- [Agreed requirements and brain dump](docs/brain-dumps/xpp-agentic-development-brain-dump.md)
- [Developer journey](docs/ux/xpp-agentic-development-journey.md)

The starting architecture is the VSIX, MCP server, and in-process Dynamics metadata bridge in [XppAiCopilotCompanion](https://github.com/nazmolla/XppAiCopilotCompanion). Its source is not vendored here; code-reuse rights and installed-version compatibility still require verification.

This repository currently contains authored documentation, not an implemented or runtime-validated extension. Private FinOps source, environment credentials, browser authentication state, and customer data are not included.

# MCP – Model Context Protocol

Model Context Protocol (MCP) is a secure, smart middleware designed to help AI agents interact with structured business APIs such as NeonPanel, Amazon SP API, QuickBooks, and more.

This repository contains:
- 📄 `MCP.md` – Protocol overview and structure
- ⚙️ `mcp.config.json` – Example configuration for access, routing, and caching
- 📜 `resolve.openapi.yaml` – OpenAPI spec for the `/resolve` endpoint

## 🚀 Getting Started

1. Read `MCP.md` to understand how the system works.
2. Customize `mcp.config.json` for your application.
3. Use `/resolve` API to connect human input to structured internal IDs.

## 🔐 Security & Access

MCP supports token-based access control and permission enforcement per user and company.

## 📦 Future Work

- Plugin support for third-party APIs
- Event-driven async jobs
- Admin dashboard

---

Licensed under MIT.

# Archodex

[![Fair Source](https://img.shields.io/badge/Fair%20Source-FCL--1.0--MIT-blue)](https://fcl.dev/)
[![eBPF](https://img.shields.io/badge/eBPF-powered-green)](https://ebpf.io/)
[![Matrix Chat](https://img.shields.io/badge/matrix-join-blueviolet)](https://matrix.to/#/#archodex:matrix.org)

> **This is the central coordination repository for the Archodex project.**\
> Issue tracking, discussions, and project roadmap live here. For the actual implementation code, see the [component repositories](#repository-guide) below.

## Know Every Secret. Rotate Without Fear.

Archodex provides operational intelligence about resource access patterns in your infrastructure. Using [eBPF](https://ebpf.io/) to observe network traffic, it reveals which services depend on which secrets, databases, and APIs - even across encrypted connections - so you can make changes safely.

Archodex enables you to see:

- **Resources**: What gets accessed (databases, secrets, APIs, etc)
- **Principals**: Who access them (services, users, CI/CD workflows)
- **Events**: What happens (reads, writes, creates, deletes)
- **Relationships**: How everything connects across environments

This data aggregates into a graph that answers critical questions: *Which services will break if I rotate this secret? What shares
this database? Who triggered this access?*


## How It Works

```
┌─────────────────┐      ┌─────────────────┐      ┌─────────────────┐
│   Agent (eBPF)  │─────▶│  Backend (API)  │◀─────│ Dashboard (UI)  │
│  Observes       │      │  Aggregates     │      │  Visualizes     │
└─────────────────┘      └─────────────────┘      └─────────────────┘
     ↓                         ↓                         ↓
 Network Traffic        Graph Database            Interactive Map
```

1. **eBPF Agent** observes network traffic at the kernel level (no code changes required)
2. **Ruleset Engine** filters and extracts relevant resource accesses based on configurable rules
3. **Agent Output** logs observations locally by default, or reports to backend when given a license key
4. **Backend API** aggregates observations into a graph database
5. **Dashboard** visualizes relationships, dependencies, and access patterns

### Key Features

- **eBPF-based observation**: Kernel-level efficiency with minimal overhead
- **Ruleset-driven intelligence**: Customizable rules define what to observe. Contribute rulesets for your services!
- **Encrypted traffic visibility**: Observes HTTPS/TLS traffic via eBPF without decryption
- **Multi-environment support**: Works in CI/CD, Kubernetes, cloud, and on-premises
- **Graph visualization**: See relationships and dependencies, not just lists
- **Log-only mode**: Keep all data local when security demands it
- **Principal chain tracking**: Understand causality: who triggered what and why

## Data Privacy & Security

**By default the Archodex Agent doesn't send data anywhere.** Observations are logged locally unless you provide a license key. You can run it completely firewalled off from network access.

**When connected, your data is protected.** Observations are encrypted in transit using TLS 1.3 and stored encrypted at rest. For Archodex.com accounts, your data is stored in an isolated database in a locality of your choice.

**Secret values are never transmitted or stored.** The agent cryptographically hashes observed secret values using account-specific salts. This allows Archodex to correlate secret usage across workloads without ever knowing the actual values.

**Full control when you need it.** Self-host the entire stack to control exactly where your data lives and how it's accessed.

For complete details, see [Data Confidentiality](https://archodex.com/docs/agent#data-confidentiality).

## Quick Start

Choose how you want to explore Archodex:

- **🎮 [Interactive Demo](https://play.archodex.com)**: See what insights look like without installing anything
- **📊 [Archodex.com](https://app.archodex.com/signup) (recommended)**: Zero infrastructure, instant dashboard, free to start
- **🧪 Test Locally**: Run the agent and see observations in logs (no account needed)
- **🔒 [Self-Host](https://archodex.com/docs/self-hosting)**: Full control over your infrastructure and data

### GitHub Actions (30 seconds)

Add Archodex to any workflow:

```yaml
- name: Observe with Archodex
  uses: archodex/archodex-agent-action@v1
  with:
    # Omit report_api_key for log-only mode (data stays local)
    report_api_key: ${{ secrets.ARCHODEX_API_KEY }}
```

**What happens:** The agent observes network traffic, extracts resource accesses based on rulesets, and either logs locally or reports to your backend.

### Kubernetes (2 minutes)

Deploy as a DaemonSet to observe your entire cluster:

```bash
# Quick start with default rulesets
kubectl apply -f https://raw.githubusercontent.com/Archodex/archodex-helm-charts/main/quickstart.yaml

# Or use Helm for customization
helm repo add archodex https://charts.archodex.com
helm install archodex archodex/archodex-agent \
  --set reportApiKey=$ARCHODEX_API_KEY
```

### Local Testing (Log-Only Mode)

Try Archodex without sending any data:

```bash
# Download and run the agent
curl -L https://get.archodex.com | sh
./archodex-agent --enable-ruleset github_actions@v1 --log-only

# Observations are logged locally - nothing sent externally
```

For detailed installation guides, see our [documentation](https://archodex.com/docs).

## Repository Guide

The Archodex project consists of focused repositories for each component:

### Core Components
- **[archodex](https://github.com/Archodex/archodex)** ← You are here - Central coordination, issues, discussions
- **[archodex-agent](https://github.com/Archodex/archodex-agent)**: eBPF-based observability agent (Rust + eBPF C)
- **[archodex-backend](https://github.com/Archodex/archodex-backend)**: API server and graph aggregation (Rust)
- **[archodex-frontend](https://github.com/Archodex/archodex-frontend)**: Interactive dashboard (React + TypeScript)

### Rulesets & Configuration
- **[archodex-rules](https://github.com/Archodex/archodex-rules)**: Observation rulesets (contribute yours!)

### Deployment & Infrastructure
- **[archodex-helm-charts](https://github.com/Archodex/archodex-helm-charts)**: Kubernetes deployment configurations
- **[archodex-www](https://github.com/Archodex/archodex-www)**: Website and documentation source

## Community

Archodex is built by a [small team](https://archodex.com/team) and we're building in the open. We're just starting to grow our community and would love your help making platform engineering safer for everyone.

### Start the Conversation

We're setting up our community spaces and would love to hear from you:

- **[GitHub Discussions](https://github.com/Archodex/archodex/discussions)**: Start a discussion
- **[GitHub Issues](https://github.com/Archodex/archodex/issues)**: Report bugs or request features
- **[Matrix Chat](https://matrix.to/#/#archodex:matrix.org)**: Drop by and say hello

### Ways to Contribute

Your early contributions will help shape Archodex. Whether this is your first open source contribution or your hundredth, there's a place for you here.

#### Easy Ways to Start

- **⭐ Star this repo**: Help us gain visibility
- **Add a Ruleset**: Know a service that needs observability? [Contribute a community ruleset](https://github.com/Archodex/archodex-rules)!
- **Improve Documentation**: Fix typos, clarify explanations, add examples
- **Share Feedback**: Tell us what works, what doesn't, and what you need in [Discussions](https://github.com/Archodex/archodex/discussions)

See our [Contributing Guide](https://archodex.com/contributing) for details.

## Self-Hosting

Archodex can run entirely in your infrastructure with no external dependencies required.

### Quick Self-Host Setup

```bash
# Backend API Server
docker run -d \
  --name archodex-backend \
  -e SURREALDB_URL=ws://your-surrealdb:8000 \
  -p 8080:8080 \
  ghcr.io/archodex/archodex-backend:latest

# Dashboard UI
docker run -d \
  --name archodex-frontend \
  -e VITE_BACKEND_URL=http://localhost:8080 \
  -p 3000:3000 \
  ghcr.io/archodex/archodex-frontend:latest
```

For more details, see our [self-hosting guide](https://archodex.com/docs/self-hosting).

## Documentation

- **[Getting Started](https://archodex.com/docs)**: Installation and first steps
- **[Rulesets Guide](https://archodex.com/docs/rulesets)**: Understanding and creating rulesets
- **[API Reference](https://archodex.com/docs/api)**: Backend API documentation
- **[Architecture](https://archodex.com/docs/architecture)**: Deep dive into how Archodex works

## Support

- **Documentation**: [archodex.com/docs](https://archodex.com/docs)
- **Community**: [GitHub Discussions](https://github.com/Archodex/archodex/discussions)
- **Chat**: [Matrix Room](https://matrix.to/#/#archodex:matrix.org)
- **Email**: support@archodex.com

## License

Archodex is **Fair Source** software licensed under the [Fair Core License – MIT (FCL-1.0-MIT)](https://fcl.dev/)[^1]. This means:

You **can** use Archodex for your company, self-host it, inspect and modify the code, and contribute improvements

You **cannot** build a competing product or bypass license key enforcement

For complete details about Fair Source, our patents, and what this means for you, see our [Licensing page](https://archodex.com/licensing).

[^1]: eBPF code in the [Archodex Agent `/src/bpf`](https://github.com/Archodex/archodex-agent/tree/main/src/bpf) is GPL-2.0

---
1.


<p align="center">
  <a href="https://app.archodex.com/signup">Get started</a>
  &nbsp;·&nbsp;
  <a href="https://archodex.com/docs">Read the docs</a>
  &nbsp;·&nbsp;
  <a href="https://github.com/Archodex/archodex">Star this repo ⭐</a>
</p>

2.

<table>
  <tr>
    <td align="center"><a href="https://app.archodex.com/signup">Get started</a></td>
    <td align="center"><a href="https://archodex.com/docs">Read the docs</a></td>
    <td align="center"><a href="https://github.com/Archodex/archodex">Star this repo ⭐</a></td>
  </tr>
</table>

3.

<table width="100%">
  <tr>
    <td align="left"><a href="https://app.archodex.com/signup">Get started</a></td>
    <td align="center"><a href="https://archodex.com/docs">Read the docs</a></td>
    <td align="right"><a href="https://github.com/Archodex/archodex">Star this repo ⭐</a></td>
  </tr>
</table>


[Get started](https://app.archodex.com/signup) • [Read the docs](https://archodex.com/docs) • [Star this repo](https://github.com/Archodex/archodex) ⭐

Built with care by the [Archodex team](https://archodex.com/team). Join us!
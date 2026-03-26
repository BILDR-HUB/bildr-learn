---
tags:
  - eszkoz
  - docker
  - dev-tool
datum: 2026-03-06
szint: "🧱 Scout"
kapcsolodo:
  - "[[cloud/docker-alapok|Docker alapok]]"
  - "[[cloud/docker-compose|Docker Compose]]"
  - "[[toolbox/claude-code-projekt-setup|Claude Code projekt setup]]"
  - "[[_moc/moc-docker|MOC - Docker]]"
  - "[[_moc/moc-environment-setup|MOC - Environment Setup]]"
---

# Dev Containers

## Összefoglaló

A **Dev Containers** egy szabvány, ami lehetővé teszi, hogy a fejlesztői környezetedet egy Docker konténerben definiáld. A csapat minden tagja **pontosan ugyanazt a környezetet** kapja — Node.js verzió, eszközök, extension-ök, beállítások mind benne vannak.

## Miért hasznos?

- **"Nálam működik" szindróma** — mindenkinél ugyanaz a környezet
- **Onboarding** — új csaptag 1 paranccsal indíthat, nem kell 2 órás setup
- **[[foundations/projekt-szintu-izolacio|Izoláció]]** — a projektek nem zavarják egymás dependency-jeit
- **AI-barát** — Claude Code is futhat konténerben, konzisztens környezettel

## Setup

### `.devcontainer/devcontainer.json`

```json
{
  "name": "My SaaS App",
  "image": "mcr.microsoft.com/devcontainers/typescript-node:20",
  "features": {
    "ghcr.io/devcontainers/features/docker-in-docker:2": {},
    "ghcr.io/devcontainers/features/github-cli:1": {}
  },
  "postCreateCommand": "npm install",
  "forwardPorts": [3000, 5432],
  "customizations": {
    "vscode": {
      "extensions": [
        "dbaeumer.vscode-eslint",
        "esbenp.prettier-vscode",
        "bradlc.vscode-tailwindcss"
      ],
      "settings": {
        "editor.defaultFormatter": "esbenp.prettier-vscode",
        "editor.formatOnSave": true
      }
    }
  }
}
```

### [[cloud/docker-compose|Docker Compose]]-szal (ha DB is kell)

```json
{
  "name": "Full Stack Dev",
  "dockerComposeFile": "docker-compose.yml",
  "service": "app",
  "workspaceFolder": "/workspace",
  "forwardPorts": [3000, 5432]
}
```

```yaml
# .devcontainer/docker-compose.yml
services:
  app:
    build:
      context: ..
      dockerfile: .devcontainer/Dockerfile
    volumes:
      - ..:/workspace:cached
    command: sleep infinity
  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_PASSWORD: dev
      POSTGRES_DB: myapp
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

## VS Code vs CLI

```bash
# VS Code: "Reopen in Container" parancs
# CLI: devcontainer CLI
npm install -g @devcontainers/cli
devcontainer up --workspace-folder .
devcontainer exec --workspace-folder . bash
```

## Kapcsolódó

- [[cloud/docker-alapok|Docker alapok]] — a konténer alapok amire dev containers épül
- [[cloud/docker-compose|Docker Compose]] — multi-service dev container setup
- [[toolbox/claude-code-projekt-setup|Claude Code projekt setup]] — AI tool-ok konfigurálása

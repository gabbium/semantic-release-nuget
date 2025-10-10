# semantic release nuget

A [semantic-release](https://semantic-release.gitbook.io) plugin to build and publish NuGet packages automatically for .NET projects.

## ✅ What it does

- Detects the next release version from `semantic-release`
- Runs:
  - `dotnet build --configuration Release`
  - `dotnet pack` with the same version
  - `dotnet nuget push` to publish to **nuget.org**

All automatically in the `publish` step of semantic-release.

---

## 📦 Installation

```bash
npm install --save-dev @gabbium/semantic-release-nuget
```

---

## ⚙️ Usage

In your `.releaserc` (or `release.config.js`), add the plugin after your standard plugins:

```json
{
  "branches": ["main"],
  "plugins": [
    "@gabbium/semantic-release-nuget",
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator",
    "@semantic-release/changelog",
    "@semantic-release/git",
    "@semantic-release/github"
  ]
}
```

---

## 🔑 Required environment variables

You need to provide:

- `GITHUB_TOKEN` → for semantic-release GitHub integration
- `NUGET_TOKEN` → your NuGet.org API key

In GitHub Actions, you would set it like:

```yaml
- name: Run semantic-release
  run: npx semantic-release
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    NUGET_API_KEY: ${{ secrets.NUGET_API_KEY }}
```

---

## 🛠 How it works internally

This plugin hooks into the `publish` step of `semantic-release`:

1. Uses the `nextRelease.version` provided by semantic-release
2. Builds the project with `dotnet build --configuration Release`
3. Packs a NuGet with `/p:Version=${nextRelease.version}`
4. Pushes the `.nupkg` to nuget.org using `dotnet nuget push`

---

## ✅ Example with GitHub Actions

```yaml
name: Release

on:
  push:
    branches: ["main"]

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 23.x

      - name: Setup .NET SDK
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: "9.0.x"

      - name: Install deps
        run: npm ci

      - name: Run semantic-release
        run: npx semantic-release
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          NUGET_API_KEY: ${{ secrets.NUGET_API_KEY }}
```

---

## 🪪 License

This project is licensed under the MIT License – see [LICENSE](LICENSE) for details.

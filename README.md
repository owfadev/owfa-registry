# vault-wiki package registry

The official package index of the [vault-wiki](https://github.com/owfadev/owfa) framework — the Obsidian-model registry: packages self-host in their own repos; a one-line PR here is the listing (and trust) gate.

- **Agents / users**: `kernel list-remote` reads [INDEX.yaml](INDEX.yaml); `kernel install <pkg>` resolves against it
- **Authors**: see [how to register](#registering-a-package) below; until listed, your package is still installable directly (`kernel install <owner/repo> --yes`, trust: unknown)

## Packages

| Package | Version | Compat | Contents | Trust | Summary |
|---|---|---|---|---|---|
| [personal](https://github.com/owfadev/owfa-personal) | 0.1.3 | ≥ 0.4 | 21 members (protocol: vaultwiki, default) | framework-bundled | OWFA personal suite — the default protocol package; purely local · no credentials |
| [project](https://github.com/owfadev/owfa-project) | 0.1.1 | ≥ 0.4.5 | project/decisions/journal/index/check/owfadevelop (protocol: project) | framework-bundled | project PROTOCOL — supervised-development workspace; ABI face since 0.1.1 |
| [bridge](https://github.com/owfadev/owfa-bridge) | 0.3.1 | ≥ 0.4.6 | bridge | framework-bundled | cross-workspace references — read-only toward targets, write path = jurisdiction switch |
| [cuhksz](https://github.com/owfadev/owfa-cuhksz) | 0.2.1 | ≥ 0.2 | cuhksz · bb · bb-map · bb-track · bb-teach · bb-quiz · sis · registry | framework-bundled | CUHK-SZ school domain — bb course ops · sis records · registry |
| [lark](https://github.com/owfadev/owfa-lark) | 0.2 | ≥ 0.2 | lark · lark-docs · lark-im · lark-calendar | framework-bundled | Feishu/Lark domain — docs · im · calendar |
| [email](https://github.com/owfadev/owfa-email) | 0.2 | ≥ 0.2 | email | framework-bundled | personal mailbox domain — mail-cli |
| [bili](https://github.com/owfadev/owfa-bili) | 0.5 | ≥ 0.2 | bili · bili-pet | framework-bundled | bilibili content domain — bili-cli · optional raised-risk member bili-pet |
| [github](https://github.com/owfadev/owfa-github) | 0.2 | ≥ 0.3 | github | framework-bundled | GitHub state cache domain with system gh connector and offline reads |
| [diary](https://github.com/owfadev/owfa-diary) | 0.3 | ≥ 0.2 | diary | framework-bundled | personal diary zone — vault-backed daily entries |
| [qq](https://github.com/owfadev/owfa-qq) | 0.3.0 | ≥ 0.3 | qq | framework-bundled | Official QQ Bot domain — qq-cli WebSocket intake |

> The table is a human-readable mirror — [INDEX.yaml](INDEX.yaml) is the machine source of truth; when they disagree, INDEX wins and the table needs a PR.

## Registering a package

One PR, one line in `INDEX.yaml`:

```yaml
  <name>: {repo: https://github.com/<owner>/<repo>, version: X.Y, compat: ">= X.Y", trust: reviewed-3rd-party, members: <slash/separated>, summary: one line — this is the discovery surface, write it for agents}
```

The PR **is** the human-review gate (issue #12 security faces: `inject` text is a constitution-level instruction channel; connectors hold credentials and execute code). Review checks: manifest shape, members == family closure, connector disclosure. On merge: listed in `list-remote`, installable without `--yes`.

### Release discipline (per version bump)

`kernel install` fetches the package repo at tag `<name>-v<version>`; without the tag the row is uninstallable (hit in the wild: bridge 0.3.0 shipped without `bridge-v0.3.0`). Every INDEX version bump MUST have, in the package repo at PR time:

1. tag `<name>-v<version>` pushed at the reviewed commit — REQUIRED: it is the only ref `kernel install` fetches (`git clone --branch <name>-v<version>`); a missing tag makes the row uninstallable (hit in the wild: bridge 0.3.0 shipped without `bridge-v0.3.0`);
2. a `commit:` SHA pin in the INDEX row is an additional post-clone verification anchor — install refuses the clone when HEAD != pin; it never substitutes for the tag;
3. INDEX row `version` == the tag's/PKG.yaml version (kernel refuses the mismatch at install).

Landing rules are mechanical, not editorial — where a plugin lives follows its `depends`:

| Your plugin is… | It lands… |
|---|---|
| a domain member (depends chain reaches a domain root) | that domain package's repo (PR there — the family closure claims it) |
| a new domain of its own | your own repo (PKG.yaml package) + a registry PR here |
| cross-cutting / non-domain | the framework repo's `core/` (owner-reviewed) |

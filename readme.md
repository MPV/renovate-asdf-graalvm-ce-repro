# renovate-asdf-graalvm-ce-repro

Minimal reproduction: Renovate doesn't update GraalVM CE `java` pins (`graalvm-<version>+java<N>`) in asdf's `.tool-versions` or in `mise.toml`.

## Current behavior

Renovate skips every case below with `unsupported-datasource`, so these pins never get updates.

```json
{ "depName": "java", "skipReason": "unsupported-datasource" }
```

## Expected behavior

| case | pin | expected update | update type |
| --- | --- | --- | --- |
| `cases/22.3.1-java17` | `java graalvm-22.3.1+java17` | `java graalvm-22.3.3+java17` | patch |
| `cases/22.3.0-java17` | `java graalvm-22.3.0+java17` | `java graalvm-22.3.3+java17` | patch |
| `cases/22.0.0.2-java17` | `java graalvm-22.0.0.2+java17` | `java graalvm-22.3.3+java17` | minor |
| `cases/22.3.1-java19` | `java graalvm-22.3.1+java19` | none: 22.3.1 is the last GraalVM CE build for Java 19 | |
| `cases/22.3.3-java17` | `java graalvm-22.3.3+java17` | none: 22.3.3 is the last GraalVM CE release | |
| `cases/mise` | `java = "graalvm-22.3.1+java17"` | `java = "graalvm-22.3.3+java17"` | patch |

- The `+java<N>` suffix never changes, and Renovate never offers a version that has no build for that Java major.
- Every expected version exists in asdf-java's list of installable versions, so `asdf install` works: https://raw.githubusercontent.com/halcyon/asdf-java/master/data/jdk-linux-x86_64-ga.tsv
- `renovate.json` extends `config:recommended`, which includes `workarounds:javaLTSVersions`. GraalVM versions are not Java versions, so that preset must not filter these updates.
- `renovate.json` sets `minimumReleaseAge`. The updates carry release timestamps, so they're not left pending.

## How to run

```sh
git clone https://github.com/MPV/renovate-asdf-graalvm-ce-repro
cd renovate-asdf-graalvm-ce-repro
LOG_LEVEL=debug pnpm dlx renovate --platform=local --dry-run=lookup
```

Look for the `packageFiles with updates` log entry and the `java` deps in it.

## Links

- Issue: https://github.com/renovatebot/renovate/issues/19183
- Pull request: https://github.com/renovatebot/renovate/pull/<PR number>

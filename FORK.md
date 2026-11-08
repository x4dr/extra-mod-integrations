# Fork notes

This is a fork of [Extra Mod Integrations](https://github.com/Kneelawk/extra-mod-integrations)
by Kneelawk, abbie and KirboEatsFood. It is maintained for a private modpack. It is not a
replacement for upstream, and it must not be installed alongside upstream ExMI: both declare the
`extra_mod_integrations` mod id, and NeoForge refuses to start with a duplicate.

- Upstream branch: `1.21.1`
- Upstream commit this fork is based on: `dca942fcbe40318938ef0e659970b8b0ab9497ae` (2026-05-15)

## What this fork changes

One property in `gradle.properties`:

```
pneumaticcraft_version = 8.2.9+mc1.21.1   ->   pneumaticcraft_version = 8.2.23+mc1.21.1
```

No Java source is modified.

### Why

PneumaticCraft: Repressurized 8.2.18 changed the return type of `HeatUtil.formatHeatString(String)`
from `Component` to `MutableComponent`. A changed return type changes the JVM method descriptor, so
the call site compiled into ExMI 1.0.3 (`AbstractPNCEmiRecipe.addTemperatureGauge`) no longer
resolves and throws `NoSuchMethodError` at runtime. EMI catches that and draws "Rendering error" in
place of the Thermopneumatic Processing Plant and Refinery Controller recipe pages.

The source compiles against either signature, so rebuilding against 8.2.23 fixes it with no code
change. Upstream issue: <https://github.com/Kneelawk/extra-mod-integrations/issues/68>

## Building

Requires a JDK 21 toolchain. Pass it through `JAVA_HOME` rather than committing a machine-specific
path to `org.gradle.java.home`:

```
JAVA_HOME=/path/to/jdk-21 RELEASE_TAG=v1.0.3+1.21.1-stk.1 ./gradlew :all-neoforge:build
```

The distributable is `build/libs/extra-mod-integrations-all-neoforge-<version>.jar`. `RELEASE_TAG`
drives the version through `com.kneelawk.versioning`; without it the build produces a
`1.0.99+1.21.1.local` development version.

## Licensing and attribution

- `LICENSE` at the repository root is **CC0 1.0 Universal**. It is upstream's file, unchanged. CC0
  places the work in the public domain: copying, modifying and redistributing it requires no
  permission and no attribution.
- Every module's `META-INF/neoforge.mods.toml` declares `license = "MIT"`. That is also upstream's,
  unchanged. The declaration and the `LICENSE` file have disagreed since the repository's first
  commit (`8ece645`); both are permissive and neither restricts this fork. This fork claims no
  license of its own beyond what CC0 already grants, and does not relicense upstream's work.
- Authorship belongs to Kneelawk, abbie and KirboEatsFood. The `authors` field in the mod metadata
  is left exactly as upstream wrote it.
- Everything under `assets/` is upstream's. The `assets/emi/...` files are EMI data files that this
  mod provides from its own namespace, not copies of EMI's code or art.
- The publishing configuration in `.github/workflows/` and the Modrinth and CurseForge project ids
  in `gradle.properties` are upstream's. This fork's builds do not use them.

## Versioning

Fork builds append `-stk.N` to the upstream version they are based on, for example
`1.0.3+1.21.1-stk.1`, so they stay distinguishable from an official 1.0.3 build while remaining in
the format the versioning plugin parses.

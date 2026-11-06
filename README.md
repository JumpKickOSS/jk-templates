# jk-templates

Official [Giter8](http://www.foundweekends.org/giter8/) starter templates for
[JumpKick (`jk`)](https://github.com/JumpKickOSS/jk). `jk new -t <ref>` shallow-clones this
repository into the artifact store, refreshes it on later runs, and applies the template with
StringTemplate: `$name$`, `$organization$`, `$package$`, `$java$`, and `$jk_version$` (the jk that
scaffolds, which is what a template's rule pack is pinned to).

**Upstream is `templates/` in the jk repository.** Fix a template there; this catalog mirrors it,
and `diff -r` between the two must stay empty apart from this README, the LICENSE and `.github/`.

## Use

```bash
jk new -t cli my-tool
jk new -t cli --lang kotlin my-tool
jk new -t app my-app
jk new -t app -p deps=guava -p assembly=true my-app
jk new -t library my-lib
jk new -t library --lang kotlin my-lib
jk new -t ktor-3 my-svc
jk new -t spring-boot/mcp my-mcp
jk new -t spring-boot/webapp my-app
```

Bare `-t <name>` is framework `none` (so `-t cli` → `java/none/cli`). Qualify a framework with
`-t spring-boot/webapp`. `--lang` defaults to java; a `framework/name` ref walks
java → kotlin → groovy → scala on a miss. Override the catalog with `[templates] official = "…"` in
`~/.jk/config.toml`; any template can also be used directly by local path, `owner/repo`, or git URL.

## Layout

```
<lang>/<framework>/<name>.g8/
  .jk-template.toml       # language, framework, name, description, layouts; [[ask]] and internal
  default.properties      # g8 fields + defaults (name, organization, package, java, …)
  src/main/g8/            # the templated project tree ($name$, $package$, …)
```

`<lang>` is `java`, `kotlin`, `groovy`, or `scala`. `<framework>` is `none` for unframed templates
(cli, library, ktor-3) or a plugin table (`spring-boot`, `quarkus`, …).

A template's own questions are `[[ask]]` tables in `.jk-template.toml` (`key`, `label`, `kind` of
`text`, `bool`, `choice` or `multi`, `options`, `default`, `when`, `advanced`); `-p key=value`
answers one. A `choice` answer also sets `<key>_<option>` to `yes` for the chosen option and
`no` for the rest, a `multi` the same per selected option, and a `bool` is `yes` or `no`, so the
templated tree tests `$if(deps_guava.truthy)$`. jk refuses a template that fails the catalogue lint,
naming the property: every `default.properties` entry is jk's own (`name`, `organization`,
`package`, `java`, `jk_languages`, `jk_layout`, …), an `[[ask]]`, or listed under
`internal = [...]`; a `when` names earlier asks only; a default is one of its options. The full
table is in jk's [templates guide](https://github.com/JumpKickOSS/jk/blob/main/docs/user/templates.md).

`jk template lint .` runs that lint over every template here, and the `lint` workflow runs it on
every push and pull request. The command is newer than the latest jk release: until a release
includes it, the workflow fails at that step.

## Templates

| Name | Framework | Languages | Description | Rule pack |
|------|-----------|-----------|-------------|-----------|
| `app` | none | java, kotlin, groovy, scala | Blank application: a main and its test; asks for common libraries, a fat jar and a native binary | `library` |
| `cli` | none | java, kotlin | Simple executable (Mill SIMPLE layout), unit test | `library` (java), house baseline (kotlin) |
| `cli-native` | none | java | Interactive Java CLI with JLine (`jk native` ready) | house baseline |
| `library` | none | java, kotlin, scala | Published library: sources and javadoc jars (Dokka's for Kotlin, scaladoc for Scala), `[build-info]`, unit test; the Java one a `@NullMarked` API package; asks for common libraries | `library` |
| `plugin` | none | java | Build plugin (`jk new -P`): `jk-plugin.toml` manifest, service file, a `@NullMarked` Plugin class and its test, against the scaffolding jk's SDK | `library` |
| `ktor-3` | none | kotlin | Ktor service with Koin DI and Exposed/H2 | house baseline |
| `mcp` | spring-boot | java | Spring Boot MCP server (Spring AI, `@Tool` over SSE) | `spring` |
| `webapp` | spring-boot | java, kotlin | Spring Boot API + Vite/React SPA in a node `web` module (`node = 24`) jk installs, builds and packages; `jk dev` runs Vite beside the app | `spring`, `monorepo` |
| `webmvc-security-actuator-jpa-h2` | spring-boot | java, kotlin | Spring Boot WebMVC + JPA/H2 + Actuator | `spring` |

Plugin hello-apps (`spring-boot/hello`, `spring-boot/webmvc`, `quarkus/hello`, `micronaut/hello`,
`grails/hello`, `android/compose`) ship in the plugin jars, not this catalog.

## What every template ships

- **`jk-guards.toml`** — house rules run inside `jk build` from the first build. Framework templates
  `[guards] extends` the pack for their framework (`cc.jumpkick.guards:spring`, `library`,
  `monorepo`, …), and the Java `cli` extends `library`; the other `none` templates carry a small
  baseline of their own with a `guard-fixtures/` proof. `jk guard explain` lists the rules.
- **Test tiers** — `[test] exclude-tags` names the cost tags, and one `[profiles.<tag>]` per tag
  runs them, so every tagged test belongs to exactly one tier.
- **`[format] style = "standard"`**, and **`[image]`** (a JRE 25 base, `aot-cache = true`) on the
  runnable JVM starters; `cli-native` has none, because its native binary ships as itself.
- **`[nullness]`** on every Java template: each package it generates is `@NullMarked` in a
  `package-info.java`, and NullAway checks it from the first build.
- **`[build-info]`** on the Spring Boot and `library` templates: the jar carries `git.properties`
  (and Boot's `META-INF/build-info.properties`, what `/actuator/info` reports) with the commit,
  branch and version it was built from. The `library` templates also ship sources and javadoc
  jars beside the jar — javadoc's for Java, Dokka's javadoc-format output (`[dokka] version`) for
  Kotlin.
- **`java = 25`** — the language level, never a `jdk =` pin.
- **No `AGENTS.md`** — jk writes the canonical one into every new project; a copy here would shadow it.

## Versions

Every library and framework version a template writes is today's current stable as an exact
pin (`[spring-boot] version = "4.1.1"`, not `"latest"`): the committed file says what the
project builds against, and `jk update` moves it. Dependencies are spelled as catalog short
names with a version (`h2 = "2.5.250"`) or as coordinate strings; a BOM-managed artifact is a
catalog short name set to `"managed"` (`kotlin-reflect = "managed"`) or, under a handle that is
not its catalog name, a versionless coordinate
(`starter-webmvc = "org.springframework.boot:spring-boot-starter-webmvc"`). Inline tables
are for the extra fields only (`optional`, `features`, `classifier`, `git`, `path`).

## Contributing

Change `templates/` in the jk repository; a template must generate a project where `jk build`,
`jk test` and `jk guard` pass with no manual edits, and keep `default.properties` minimal —
sensible defaults, no required interaction. jk's engine tests run the catalogue lint over every
template there, and its slow tier scaffolds each one with its defaults and again with every option
on. Then run `scripts/templates-sync.sh` in jk, which mirrors `templates/` here; `--check` names any
file that has drifted.

## License

Apache-2.0 (see [LICENSE](LICENSE)).

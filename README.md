# jk-templates

Official [Giter8](http://www.foundweekends.org/giter8/) starter templates for
[JumpKick (`jk`)](https://github.com/JumpKickOSS/jk). `jk new -t <ref>` shallow-clones this
repository into the artifact store, refreshes it on later runs, and applies the template with
StringTemplate: `$name$`, `$organization$`, `$package$`, `$java$`, and `$jk_version$` (the jk that
scaffolds, which is what a template's rule pack is pinned to).

**Upstream is `templates/` in the jk repository.** Fix a template there; this catalog mirrors it,
and `diff -r` between the two must stay empty apart from this README and the LICENSE.

## Use

```bash
jk new -t cli my-tool
jk new -t cli --lang kotlin my-tool
jk new -t library my-lib
jk new -t ktor-3 my-svc
jk new -t spring-boot/mcp my-mcp
jk new -t spring-boot/webapp my-app
```

Bare `-t <name>` is framework `none` (so `-t cli` → `java/none/cli`). Qualify a framework with
`-t spring-boot/webapp`. `--lang` defaults to java; a `framework/name` ref walks
java → kotlin → groovy on a miss. Override the catalog with `[templates] official = "…"` in
`~/.jk/config.toml`; any template can also be used directly by local path, `owner/repo`, or git URL.

## Layout

```
<lang>/<framework>/<name>.g8/
  .jk-template.toml       # language, framework, name, description, layouts
  default.properties      # g8 fields + defaults (name, organization, package, java, …)
  src/main/g8/            # the templated project tree ($name$, $package$, …)
```

`<lang>` is `java`, `kotlin`, or `groovy`. `<framework>` is `none` for unframed templates
(cli, library, ktor-3) or a plugin table (`spring-boot`, `quarkus`, …).

## Templates

| Name | Framework | Languages | Description | Rule pack |
|------|-----------|-----------|-------------|-----------|
| `cli` | none | java, kotlin | Simple executable (Mill SIMPLE layout) | house baseline |
| `cli-native` | none | java | Interactive Java CLI with JLine (`jk native` ready) | house baseline |
| `library` | none | java | Published library: `@NullMarked` API package, unit test | `library` |
| `ktor-3` | none | kotlin | Ktor service with Koin DI and Exposed/H2 | house baseline |
| `mcp` | spring-boot | java | Spring Boot MCP server (Spring AI, `@Tool` over SSE) | `spring` |
| `webapp` | spring-boot | java, kotlin | Spring Boot API + Vite/React SPA in a resource-only `web` module, `[dev.sidecars]` runs Vite beside `jk dev` | `spring`, `monorepo` |
| `webmvc-security-actuator-jpa-h2` | spring-boot | java, kotlin | Spring Boot WebMVC + JPA/H2 + Actuator | `spring` |

Plugin hello-apps (`spring-boot/hello`, `spring-boot/webmvc`, `quarkus/hello`, `micronaut/hello`,
`grails/hello`, `android/compose`) ship in the plugin jars, not this catalog.

## What every template ships

- **`jk-guards.toml`** — house rules run inside `jk build` from the first build. Framework templates
  `[guards] extends` the pack for their framework (`cc.jumpkick.guards:spring`, `library`,
  `monorepo`, …); `none` templates carry a small baseline of their own with a `guard-fixtures/`
  proof. `jk guard explain` lists the rules.
- **Test tiers** — `[test] exclude-tags` names the cost tags, and one `[profiles.<tag>]` per tag
  runs them, so every tagged test belongs to exactly one tier.
- **`[format] style = "standard"`**, and **`[image]`** (a JRE 25 base, `aot-cache = true`) on the
  runnable starters.
- **`[build-info]`** on the Spring Boot and `library` templates: the jar carries `git.properties`
  (and Boot's `META-INF/build-info.properties`, what `/actuator/info` reports) with the commit,
  branch and version it was built from.
- **`java = 25`** — the language level, never a `jdk =` pin.
- **No `AGENTS.md`** — jk writes the canonical one into every new project; a copy here would shadow it.

## Versions

Every library and framework version a template writes is today's current stable as an exact
pin (`[spring-boot] version = "4.1.1"`, not `"latest"`): the committed file says what the
project builds against, and `jk update` moves it. Dependencies are spelled as catalog short
names with a version (`h2 = "2.5.250"`) or as coordinate strings; a BOM-managed artifact is a
versionless coordinate (`"org.springframework.boot:spring-boot-starter-webmvc"`). Inline tables
are for the extra fields only (`optional`, `features`, `classifier`, `git`, `path`).

## Contributing

Change `templates/` in the jk repository; a template must generate a project where `jk build`,
`jk test` and `jk guard` pass with no manual edits, and keep `default.properties` minimal —
sensible defaults, no required interaction. Then mirror the tree here.

## License

Apache-2.0 (see [LICENSE](LICENSE)).

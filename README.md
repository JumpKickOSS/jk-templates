# jk-templates

Official [Giter8](http://www.foundweekends.org/giter8/) starter templates for
[JumpKick (`jk`)](https://github.com/jkbuild/jk).

## Use

```bash
jk new -t cli my-tool
jk new -t cli --lang kotlin my-tool
jk new -t ktor-3 my-svc
jk new -t spring-boot/hello my-api
```

Short names resolve in this order: `$JK_TEMPLATES`, `~/.jk/templates/`, a dev-checkout
walk-up, configured `[templates.sources]`, then **this repo** (overridable with
`[templates] official = "…"` in `config.toml`). Any template can also be used directly by
git URL or `owner/repo` ref.

Bare `-t <name>` is framework `none` (so `-t cli` → `java/none/cli`). Qualify a framework
with `-t spring-boot/hello`. `--lang` defaults to java; `framework/name` walks
java → kotlin → groovy on a miss.

## Layout

```
<lang>/<framework>/<name>.g8/
  .jk-template.toml       # language, framework, name, description, layouts
  default.properties      # g8 fields + defaults (name, organization, package, …)
  src/main/g8/            # the templated project tree ($name$, $package$, …)
```

`<lang>` is `java`, `kotlin`, or `groovy`. `<framework>` is `none` for unframed templates
(cli, ktor-3) or a plugin table (`spring-boot`, `quarkus`, …).

## Templates

| Name | Framework | Languages | Description |
|------|-----------|-----------|-------------|
| `cli` | none | java, kotlin | Simple executable (Mill SIMPLE layout) |
| `cli-native` | none | java | Interactive Java CLI with JLine (`jk native` ready) |
| `ktor-3` | none | kotlin | Ktor service with Koin DI and Exposed/H2 |
| `mcp` | spring-boot | java | Spring Boot MCP server (Spring AI, `@Tool` over SSE) |
| `webmvc-security-actuator-jpa-h2` | spring-boot | java, kotlin | Spring Boot WebMVC + JPA/H2 + Actuator |

Plugin hello-apps (`spring-boot/hello`, `quarkus/hello`, `micronaut/hello`, `grails/hello`)
ship in the plugin jars, not this catalog.

Library and plugin versions in generated `jk.toml` files are `"latest"`. First
`jk lock` pins the current stable set; `jk update` refreshes. Do not pin (or
pseudo-pin) versions in these templates.

## Contributing

Add a new `<lang>/<framework>/<name>.g8/` directory following the layout above, with
`.jk-template.toml` matching the path. A template must generate a project where `jk build`
(and `jk test` where tests are included) passes with no manual edits. Keep
`default.properties` minimal — sensible defaults, no required interaction.

## License

Apache-2.0 (see [LICENSE](LICENSE)).

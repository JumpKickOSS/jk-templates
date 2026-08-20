# jk-templates

Official [Giter8](http://www.foundweekends.org/giter8/) starter templates for
[JumpKick (`jk`)](https://github.com/jkbuild/jk).

## Use

```bash
jk new -t cli my-tool
jk new -t cli --lang kotlin my-tool
jk new -t ktor-3 my-svc
jk new -t grails-8 my-app
```

Short names resolve in this order: `$JK_TEMPLATES`, `~/.jk/templates/`, a dev-checkout
walk-up, configured `[templates.sources]`, then **this repo** (overridable with
`[templates] official = "…"` in `config.toml`). Any template can also be used directly by
git URL or `owner/repo` ref.

`--lang` defaults to java if that directory exists, else kotlin, else groovy.

## Layout

```
<lang>/<kind>.g8/
  default.properties      # g8 fields + defaults (name, organization, package, …)
  src/main/g8/            # the templated project tree ($name$, $package$, …)
```

`<lang>` is `java`, `kotlin`, or `groovy`. `<kind>` is the `-t` name (`cli`, `ktor-3`, …).

## Templates

| Kind | Languages | Description |
|------|-----------|-------------|
| `cli` | java, kotlin | Simple executable (Mill SIMPLE layout) |
| `cli-native` | java | Interactive Java CLI with JLine (`jk native` ready) |
| `quarkus` | java | Quarkus REST application (`[quarkus]` plugin) |
| `spring-boot-webmvc` | java, kotlin | Spring Boot WebMVC + JPA/H2 + Actuator |
| `spring-boot-mcp` | java | Spring Boot MCP server (Spring AI, `@Tool` over SSE) |
| `ktor-3` | kotlin | Ktor service with Koin DI and Exposed/H2 |
| `grails-8` | groovy | Grails 8 REST app (GORM, H2, Groovy) |
| `micronaut` | java, kotlin | Micronaut HTTP service (compile-time DI, Netty) |

Library and plugin versions in generated `jk.toml` files are `"latest"`. First
`jk lock` pins the current stable set; `jk update` refreshes. Do not pin (or
pseudo-pin) versions in these templates. Grails 8 is still a milestone, so that
template floors `[grails] version` at `8.0.0-M4` until GA.

## Contributing

Add a new `<lang>/<kind>.g8/` directory following the layout above. A template must generate a
project where `jk build` (and `jk test` where tests are included) passes with no manual
edits. Keep `default.properties` minimal — sensible defaults, no required interaction.

## License

Apache-2.0 (see [LICENSE](LICENSE)).

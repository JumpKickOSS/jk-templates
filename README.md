# jk-templates

Official [Giter8](http://www.foundweekends.org/giter8/) starter templates for
[JumpKick (`jk`)](https://github.com/jkbuild/jk).

## Use

```bash
jk new --template java-cli        # short name resolves against this repo
jk new --template kotlin-cli
jk new --template quarkus
```

Short names resolve in this order: `$JK_TEMPLATES`, `~/.jk/templates/`, a dev-checkout
walk-up, configured `[templates.sources]`, then **this repo** (overridable with
`[templates] official = "…"` in `config.toml`). Any template can also be used directly by
git URL or `owner/repo` ref.

## Layout

One template per top-level `<name>.g8/` directory:

```
<name>.g8/
  default.properties      # g8 fields + defaults (name, organization, package, …)
  src/main/g8/            # the templated project tree ($name$, $package$, …)
```

## Templates

| Name | Description |
|------|-------------|
| `java-cli` | Simple Java 25 executable (Mill SIMPLE layout) |
| `kotlin-cli` | Simple Kotlin executable (Mill SIMPLE layout) |
| `quarkus` | Quarkus 3.x REST application (`[quarkus]` plugin) |
| `java-cli-native` | Interactive Java CLI with JLine (`jk native` ready) |
| `spring-boot-webmvc` | Spring Boot WebMVC + JPA/H2 + Actuator |
| `spring-boot-webmvc-kotlin` | Kotlin Spring Boot WebMVC + JPA/H2 + Actuator |

Each template pins the latest stable framework versions at the time it was last updated;
regenerate with `jk new` and bump pins in the generated `jk.toml` as needed.

## Contributing

Add a new `<name>.g8/` directory following the layout above. A template must generate a
project where `jk build` (and `jk test` where tests are included) passes with no manual
edits. Keep `default.properties` minimal — sensible defaults, no required interaction.

## License

Apache-2.0 (see [LICENSE](LICENSE)).

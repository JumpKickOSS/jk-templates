# $name$

Interactive Java CLI built on [JLine]. Scaffolded from the jk `java-cli-native` template.

```bash
jk build          # JVM build — no GraalVM required
jk run            # start the interactive shell
jk test           # unit tests for the command evaluator
```

## Native image

```bash
jk native         # GraalVM native-image build (jk provisions/uses a Graal JDK)
```

JLine ships GraalVM reachability metadata, and the shell falls back to a dumb terminal
where a native terminal provider is unavailable, so the default native build works
without extra configuration. Add `[native] always = true` to `jk.toml` to make every
build produce the native binary.

[JLine]: https://github.com/jline/jline3

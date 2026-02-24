# C# Coding Standards

- Use specific exception types and actionable messages.
- Use `async` and `await` for I/O bound work; avoid sync over async patterns.
- Prefer `const` whenever possible; otherwise use `readonly` where values should not change after construction.
- Use short circuit operators `&&` and `||` for conditional logic.
- Use `PascalCase` for types, public members, constants, and record primary constructor parameters.
- Use `camelCase` for local variables, parameters, private fields, and class/struct primary constructor parameters.
- Use `_camelCase` for private, protected, and internal instance fields.
- Prefix interface names with `I`.
- Place `using` directives at the top of the file, keep them sorted alphabetically, and place `System` imports first.
- Use four spaces for indentation; do not use tabs.
- Use object initializers to improve readability during construction.
- Use target typed `new` where type context is explicit and clear.
- Prefer `Func<>` and `Action<>` over custom delegates when custom delegate types add no value.
- Prefer string interpolation for short formatted strings.
- Use expression based interpolation (`$"{name}"`) instead of positional formatting.
- Use `StringBuilder` for repeated string concatenation in loops.

## Directory Structure

```text
├── src/
│   ├── web/
│   ├── console/
│   ├── ui/
│   ├── application/
│   ├── core/
│   ├── domain/
│   └── infrastructure/
└── tests/
```

## Acknowledgements

- [Microsoft C# Coding Conventions](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions)
- [Google C# Style Guide](https://google.github.io/styleguide/csharp-style.htm)
- [JetBrains: Structure and Organize .NET Projects](https://blog.jetbrains.com/dotnet/2022/05/11/structure-and-organize-net-projects-with-rider/)

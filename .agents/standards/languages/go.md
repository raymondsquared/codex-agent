# Go Coding Standards

## Coding Standards

- Format all code with `gofmt` (or `go fmt`).
- Check all returned errors.
- Do not ignore errors with `_` in production code.
- Return `error` values; do not use in band sentinel success/failure values.
- Return `(value, error)` for operations that can fail.
- Use `panic` only for unrecoverable programmer/system faults.
- Use `recover` only in deferred functions at clear boundaries.
- Keep package names aligned with directory names.
- Use `MixedCaps` or `mixedCaps`; do not use snake_case.
- Add doc comments for exported packages, types, funcs, methods, vars, and consts.
- Do not prefix getters with `Get`; use `Owner()` not `GetOwner()`.
- Name setters `SetX` when needed.
- Name one method interfaces with idiomatic `-er` names when appropriate.
- Reuse canonical method names/signatures only when behaviour matches (`Read`, `Write`, `Close`, `String`).
- Use `defer` for cleanup that must always run (`Close`, `Unlock`).
- Prefer early returns and omit unnecessary `else` blocks.
- Keep `init` functions minimal and deterministic.
- Define small interfaces at the point of use.
- Accept interfaces and return concrete types where practical.
- Prefer embedding for composition when it improves clarity and reduces boilerplate.
- Design to avoid races; use channels or `sync` primitives to protect shared state.
- Prefer communicating over shared memory coordination.
- Start goroutines with clear ownership, lifecycle, and shutdown behaviour.
- Avoid unbounded goroutine creation; use bounded workers/semaphores.
- Use buffered channels only with explicit, intentional buffering semantics.
- Use pointer receivers when mutating state or avoiding expensive copies.
- Use value receivers for small immutable like types.
- Keep receiver type choice consistent across methods on the same type.
- Use `const` for compile time constants; use `iota` for related constant groups.

## Directory Structure

```text
├── cmd/
├── internal/
├── pkg/
├── configs/
├── docs/
├── scripts/
└── test/
```

## Acknowledgements

- [Effective Go](https://go.dev/doc/effective_go)
- [Standard Go Project Layout](https://github.com/golang-standards/project-layout)

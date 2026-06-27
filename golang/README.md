# Golang Conventions

This folder provides conventions for building robust, readable, and scalable Go (Golang) applications, focusing on simplicity, modularity, and maintainability.

## Key Principles

- **Project Structure:** Use the `/cmd`, `/internal`, `/pkg`, `/errors`, and `/logging` layout. Keep main logic out of `main.go`.
- **Separation of Concerns:** Each module in `/internal` is responsible for a single feature/domain.
- **Concurrency:** Use goroutines and channels when it improves performance and clarity, but avoid over-complicating code.
- **Vector Computation:** Prefer vectorized operations over explicit looping when possible for data processing.
- **Error & Logging Management:** All errors are propagated and handled in `/errors`; all logging is managed centrally in `/logging`.
- **Code Quality:** Short, single-purpose functions; clear, descriptive names; and code that is easy to maintain and extend.

## How To Use

1. Review [`CONVENTIONS.md`](CONVENTIONS.md) for detailed guidelines and examples.
2. Organize your repositories and modules as described.
3. Centralize and standardize all error and logging logic.
4. Write code that is easy for others to understand and extend.

## References

- [Go Project Layout](https://github.com/golang-standards/project-layout)
- [Google Go Style Guide](https://google.github.io/styleguide/go/decisions)
- [Effective Go](https://go.dev/doc/effective_go)

---

Contributions and suggestions are welcome!

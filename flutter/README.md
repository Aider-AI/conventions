# Flutter Conventions

This repository contains a comprehensive set of conventions for building Flutter applications with a focus on:

- **Separation of Concerns**
- **GetX for State Management and Routing**
- **Dio for HTTP Requests**
- **Pure Material Design 3 (M3) UI by Default**
- **Functional Programming Principles**
- **Short, Modular, Reusable Functions**
- **Readable, Maintainable, Clean Code**

## What’s Inside?

- [`CONVENTIONS.md`](CONVENTIONS.md): The main document outlining folder structure, best practices, code organization, and detailed guidelines for using GetX, Dio, and Material 3 in your Flutter projects.

## Key Highlights

- **Folder Structure:** Organize your project using the GetX modular pattern for scalability and maintainability.
- **State Management:** Centralize business logic in GetX Controllers and keep UI pure and presentational.
- **Theme Management:** Use Material 3 and centralize themes for consistency.
- **HTTP Requests:** Perform all API calls via Dio, through repositories, never directly in controllers or widgets.
- **Functional Programming:** Favor pure functions, stateless widgets, and reusable modular code.
- **Code Quality:** Write short, readable, and documented functions for improved maintainability.

## Getting Started

1. Review [CONVENTIONS.md](CONVENTIONS.md) and use it as a baseline for all new Flutter projects.
2. Apply the folder structure and patterns described in the document.
3. Adhere to the code style and best practices to ensure project consistency.

## References

- [GetX Documentation](https://pub.dev/packages/get)
- [Dio Documentation](https://pub.dev/packages/dio)
- [Material 3 Guidelines](https://m3.material.io/)

---

Feel free to propose improvements or open issues for questions about the conventions.

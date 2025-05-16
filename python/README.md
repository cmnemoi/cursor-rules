# Python Project Cursor Rules

This folder contains Cursor rules for my Python projects, forked from [📐 AI-Driven Dev {Rules}](https://github.com/ai-driven-dev/rules)

## Purpose

These rules are designed to enforce best practices, maintain code quality, and ensure consistency across all my Python codebases. They are intended to be used with the Cursor editor to automate and standardize code review and development workflows.

## Architecture & Principles

The Python projects in this repository typically follow these architectural and development principles:

- **Modular Design**: Code is organized into clear modules and packages, each with a single responsibility.
- **Separation of Concerns**: Business logic, data access, and presentation layers are separated.
- **Type Hints**: Use of Python type hints for better readability and tooling support.
- **Testing**: Emphasis on unit and integration tests, using in-memory fakes over mocks when possible.
- **Clean Code**: Readable, maintainable, and well-documented code with minimal comments (prefer extracting methods over comments).
- **PEP 8 Compliance**: Adherence to Python's official style guide.

## Rule Organization

Rules are organized by category in subfolders:

- `00-architecture`: High-level architectural rules
- `01-standards`: Coding standards and conventions
- `02-programming-languages`: Python-specific language rules
- `03-frameworks-and-libraries`: Usage of frameworks (e.g., Django, FastAPI) and libraries
- `04-tools-and-configurations`: Tooling, linters, and configuration files
- `05-workflows-and-processes`: Development workflows and processes
- `06-templates-and-models`: Code templates and model definitions
- `07-quality-assurance`: Testing and quality assurance
- `08-domain-specific-rules`: Rules specific to the project's domain
- `09-other`: Miscellaneous rules

## Rule Format

Each rule file follows a standard template with a frontmatter header specifying:

- **description**: A comprehensive, scenario-driven summary of the rule
- **globs**: File patterns where the rule applies (e.g., `src/**/*.py`)
- **alwaysApply**: Whether the rule should always be enforced

## Development Principles Enforced

- **Test-Driven Development (TDD)**: Write tests before code, prefer in-memory fakes over mocks
- **Clean Code**: Prioritize readability and maintainability
- **Separation of Concerns**: Keep layers and responsibilities distinct
- **Repository Pattern**: Use repository interfaces and implementations where appropriate

## Testing Approach

- **Unit Tests**: Use in-memory repositories and Given-When-Then pattern
- **Functional Tests**: Use pytest or similar frameworks
- **No Mocks**: Prefer handwritten in-memory fakes

---

For more details on rule authoring and structure, see the `meta-generator.mdc` file in this directory.

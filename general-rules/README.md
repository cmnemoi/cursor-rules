# General Project AI-Driven Dev Rules

This folder contains general AIDD rules, forked from [📐 AI-Driven Dev {Rules}](https://github.com/ai-driven-dev/rules).

## Purpose

These rules are designed to enforce best practices, maintain quality, and ensure consistency across all projects, regardless of programming language or technology. 

They are intended for use with the Cursor and Windsurf editor to automate and standardize code review and development workflows.

## Architecture & Principles

The rules in this directory promote universal software engineering principles, including:

- **Modularity**: Organize code into clear, logical components with single responsibilities.
- **Separation of Concerns**: Keep business logic, data access, and presentation distinct.
- **Documentation**: Ensure code and processes are well-documented for maintainability.
- **Testing**: Encourage robust testing practices and quality assurance.
- **Clean Code**: Write readable, maintainable, and consistent code.

## Rule Organization

Rules are organized by category in subfolders:

- `00-architecture`: High-level architectural and design rules
- `01-standards`: General coding standards and conventions
- `02-programming-languages`: Language-agnostic rules
- `03-frameworks-and-libraries`: Guidelines for using frameworks and libraries
- `04-tools-and-configurations`: Tooling, linters, and configuration files
- `05-workflows-and-processes`: Development workflows and processes
- `06-templates-and-models`: Code templates and model definitions
- `07-quality-assurance`: Testing and quality assurance
- `08-domain-specific-rules`: Rules specific to the project's domain
- `09-other`: Miscellaneous rules

## Rule Format

Each rule file follows a standard template with a frontmatter header specifying:

- **description**: A scenario-driven summary of the rule
- **globs**: File patterns where the rule applies (e.g., `src/**/*`)
- **alwaysApply**: Whether the rule should always be enforced

---

For more details, see the main [📐 AI-Driven Dev {Rules}](https://github.com/ai-driven-dev/rules) repository.

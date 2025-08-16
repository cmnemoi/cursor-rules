# eMush Project Guide for AI Agents

## Project Rules
The following files contain the project conventions and rules:

@ai/rules/project-rules.md
@ai/rules/spec-prompts.md

## Project Specifications
The following directory contains feature specifications:

@ai/specs/

## Build & Test Commands
- **Test single module**: `docker exec -u dev mush-php php -d error_reporting="E_ALL & ~E_DEPRECATED & ~E_USER_DEPRECATED" vendor/bin/codecept run tests/functional/[module] && docker exec -u dev mush-php php -d error_reporting="E_ALL & ~E_DEPRECATED & ~E_USER_DEPRECATED" vendor/bin/codecept run tests/unit/[module]`
- **Run all tests**: `docker exec -u dev mush-php php -d error_reporting="E_ALL & ~E_DEPRECATED & ~E_USER_DEPRECATED" vendor/bin/codecept run`
- **Unit tests only**: `docker exec -u dev mush-php php -d error_reporting="E_ALL & ~E_DEPRECATED & ~E_USER_DEPRECATED" vendor/bin/codecept run tests/unit`
- **Functional tests**: `docker exec -u dev mush-php php -d error_reporting="E_ALL & ~E_DEPRECATED & ~E_USER_DEPRECATED" vendor/bin/codecept run tests/functional`
- **Lint backend**: `docker exec -u dev mush-php php -d error_reporting="E_ALL & ~E_DEPRECATED & ~E_USER_DEPRECATED" vendor/bin/phpcs`
- **Lint frontend**: `docker exec -u node mush-front yarn lint`
- **Build frontend**: `docker exec -u node mush-front yarn build`

## Code Style & Structure
- **Architecture**: Domain-first modular structure in `Api/src/[ModuleName]/`
- **PHP**: Strict types (`declare(strict_types=1)`), final classes, readonly properties, constructor injection
- **Naming**: PascalCase classes, camelCase methods/properties, descriptive service names (e.g., `CreateLinkWithSolService`)
- **Services**: Single responsibility command pattern with `execute()` method
- **Tests**: Use InMemory repositories, Given-When-Then pattern, descriptive method names
- **Frontend**: Vue 3 + TypeScript, 4-space indentation, semicolons required, object spacing
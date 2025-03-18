# eMush project Cursor rules

This folder contains Cursor rules for the eMush project : https://gitlab.com/eternaltwin/mush/mush

eMush is an open-source remake of Mush, a multiplayer browser-based video game. The project follows a monorepo structure with two main components:

1. **Backend API** - An API serving JSON developed using Symfony 6.4 that manages the game logic
2. **Frontend App** - A front-end developed using Vue.js 3 for the website

## Architecture

The project follows a well-defined architecture based on several principles:

### Backend Architecture (Clean Architecture & Domain-Driven Design)

The backend implements **Clean Architecture** and **Domain-Driven Design** principles with clearly separated layers:

1. **Domain/Entity Layer**: Contains business entities, value objects, and repository interfaces
2. **Service Layer**: Implements use cases and orchestrates domain logic
3. **Repository Layer**: Handles data persistence
4. **Controller Layer**: Handles HTTP requests and responses

Each domain module has its own set of:
- Entities
- Repository interfaces and implementations
- Services
- Controllers
- Events and listeners

### Frontend Architecture (Vue Component-based Architecture)

The frontend follows a **Feature-Based Architecture** with Vue 3 and includes:

1. **Component Structure**: Components with clear single responsibilities
2. **State Management**: Uses Vuex for global state management
3. **Feature Organization**: Code organized by business features
4. **TypeScript**: Strong typing throughout the application
5. **Phaser**: For game-related functionality

## Tech Stack

### Backend
- **Language**: PHP 8.3
- **Framework**: Symfony 6.4
- **ORM**: Doctrine
- **Testing**: Codeception and PHPUnit

### Frontend
- **Framework**: Vue.js 3.5
- **Language**: TypeScript
- **State Management**: Vuex 4.1
- **Game Engine**: Phaser
- **Build Tool**: Vite 5.4

### Infrastructure
- **Database**: PostgreSQL
- **Docker**: For development environment

## Development Principles

The project adheres to several key principles that are enforced through rules:

1. **Test-Driven Development (TDD)**: Tests are written first, using in-memory repositories instead of mocks.
2. **Clean Code**: Emphasis on readability, maintainability, and simplicity. Huge usage of "Extract method" instead of comments.
3. **Domain-Driven Design**: Business logic is confined to Domain and Application layers.
4. **Separation of Concerns**: Clear boundaries between application layers.
5. **Repository Pattern**: Use of repository interfaces in the domain layer with implementations in infrastructure.

## Testing Approach

The project uses a specific testing approach:

- **Unit Tests**: Use in-memory repositories and follow the Given-When-Then pattern
- **Functional Tests**: Use Codeception for backend and Vitest for frontend
- **No Mocks**: Preference for handwritten in-memory fakes over mock libraries

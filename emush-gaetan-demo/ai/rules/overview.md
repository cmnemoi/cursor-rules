# Project Overview

## Goal
This project is **eMush** - an open source remake of Mush: the greatest space opera of Humanity. eMush is a multiplayer online game where players take on the roles of crew members aboard the spaceship Daedalus, working together to survive in space while some players may secretly be infected by the Mush.

## Key Features

### Game Mechanics
- **Crew Survival** - Players must manage resources, maintain ship systems, and complete missions
- **Social Deduction** - Hidden role gameplay where Mush players work against the crew
- **Resource Management** - Oxygen, fuel, food, and equipment management
- **Action System** - Turn-based actions with costs and consequences
- **Disease System** - Physical diseases, troubles, and injuries affecting gameplay
- **Equipment System** - Tools, weapons, doors, and interactive objects

### Core Functionality
- **Actions** - Players can perform various actions like move, repair, cook, explore, communicate
- **Communication** - Multiple chat channels including public, private, and proximity-based
- **Exploration** - Planet exploration with procedurally generated sectors and events
- **Projects** - PILGRED and NERON research projects for ship advancement
- **Moderation** - Admin tools for game management and player moderation
- **Hunter System** - Hostile NPCs that attack the ship's hull
- **Status Effects** - Temporary and permanent effects on players and equipment

## Technical Stack

### Backend (API)+
- **Framework**: Symfony 6.4 (PHP 8.4)
- **Database**: PostgreSQL with Doctrine ORM
- **API**: API Platform for JSON-serving endpoints
- **Testing**: Codeception for integration and unit tests
- **Architecture**: Domain-driven-inspired design with modular structure. Dependency inversion on borders (repositories)

### Frontend (App)
- **Framework**: Vue.js 3 + TypeScript
- **State Management**: Vuex store
- **Build Tool**: Vite
- **UI Components**: Custom components with responsive design
- **Testing**: Vitest

### Infrastructure
- **Containerization**: Docker with Docker Compose

## Game Experience
eMush provides an immersive space survival experience where players must:
1. Collaborate to maintain ship systems and complete objectives
2. Manage limited resources while dealing with random events
3. Navigate social dynamics and hidden information
4. Explore alien planets for resources and discoveries
5. Survive against external threats like hunters and internal sabotage
6. Progress through research projects to improve ship capabilities

The game combines strategic resource management, social deduction, and cooperative survival elements to create a unique multiplayer experience set in a science fiction universe.

## Development Philosophy
- **Modular Architecture** - Each game system is isolated in its own module
- **Event-Driven Design** - Actions trigger events that other systems can respond to
- **Test Coverage** - Comprehensive testing with both unit and integration tests

This project serves as both an entertaining game and a showcase of modern web development practices in a complex, real-time multiplayer environment.
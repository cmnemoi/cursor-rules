# eMush Backend Project Convention

## General Rules

* Follow all rules strictly.
* Organize code by **business domain** (domain-first).
* Each domain lives in its **own module folder**.
* Use modular architecture with clear separation of concerns.
* Place all business logic under `Api/src/`.
* Do not add unnecessary comments.

## Module Structure

* A **module** represents a business domain (e.g. `Action`, `Communications`, `Player`, `Equipment`).
* Located in:

  ```
  src/[ModuleName]/
  ```

* Cross-module dependencies should go through well-defined interfaces.

### Example Structure (Communications)

```
src/
  Communications/
    ConfigData/
      RebelBaseConfigDataLoader.php
      TradeConfigData.php
    DataFixtures/
      RebelBaseConfigFixtures.php
      TradeConfigFixtures.php
    Dto/
      RebelBaseConfigDto.php
      TradeConfigDto.php
    Entity/
      LinkWithSol.php
      RebelBase.php
      Trade.php
    Enum/
      RebelBaseEnum.php
      TradeEnum.php
    Event/
      LinkWithSolCreatedEvent.php
      RebelBaseStartedContactEvent.php
    Factory/
      TradeFactory.php
    Listener/
      ActionEventSubscriber.php
      DaedalusEventSubscriber.php
    Normalizer/
      RebelBaseNormalizer.php
      TradeNormalizer.php
    Repository/
      LinkWithSolRepository.php
      LinkWithSolRepositoryInterface.php
    Service/
      CreateLinkWithSolForDaedalusService.php
      DecodeRebelSignalService.php
    ValueObject/
      LinkStrength.php
    config/
      services.yaml
    README.md
```

## Module File Types

* `ConfigData/` – Game configuration data and loaders
* `DataFixtures/` – Test data fixtures for development
* `Dto/` – Data transfer objects for API serialization
* `Entity/` – Doctrine entities (database models)
* `Enum/` – PHP enums for constants and string values
* `Event/` – Domain events for inter-module communication
* `Factory/` – Entity creation factories
* `Listener/` – Event listeners and subscribers
* `Normalizer/` – API response normalization logic
* `Repository/` – Data access layer with interfaces
* `Service/` – Business logic services (command pattern)
* `ValueObject/` – Immutable value objects
* `Validator/` – Custom validation constraints
* `config/` – Symfony service configuration

## Dependency Injection

* All services use Symfony DI container
* Repository interfaces for testability
* Service classes follow constructor injection
* Use `readonly` for immutable services when possible

## Code Conventions

### ✅ PHP Classes

```php
// Good
final class CreateLinkWithSolForDaedalusService
{
    public function __construct(
        private EventServiceInterface $eventService,
        private LinkWithSolRepositoryInterface $linkWithSolRepository
    ) {}

    public function execute(int $daedalusId): void
    {
        // Implementation
    }
}

// Bad - mutable service properties
class CreateLinkWithSolForDaedalusService
{
    public EventServiceInterface $eventService;
}
```

### ✅ Service Pattern

```php
// Good - Single responsibility command pattern
final readonly class CreateLinkWithSolForDaedalusService
{
    public function execute(int $daedalusId): void
    {
        $linkWithSol = new LinkWithSol($daedalusId);
        $this->linkWithSolRepository->save($linkWithSol);
        
        $this->eventService->callEvent(
            event: new LinkWithSolCreatedEvent($daedalusId),
            name: LinkWithSolCreatedEvent::class
        );
    }
}
```

### ✅ Action Classes

```php
// Good - Actions extend AbstractAction
final class ExchangeBody extends AbstractAction
{
    protected ActionEnum $name = ActionEnum::EXCHANGE_BODY;

    public static function loadValidatorMetadata(ClassMetadata $metadata): void
    {
        $metadata->addConstraints([
            new Reach(['reach' => ReachEnum::ROOM]),
            new HasStatus(['status' => PlayerStatusEnum::MUSH]),
        ]);
    }

    protected function applyEffect(ActionResult $result): void
    {
        $this->exchangePlayerUsers();
        $this->makeTargetMush();
        $this->transferMushSkills();
    }
}
```

### ✅ Unit Tests

```php
// Good - Use InMemory repositories and Fake services
final class AddSkillToPlayerServiceTest extends TestCase
{
    private InMemoryPlayerRepository $playerRepository;
    private InMemorySkillConfigRepository $skillConfigRepository;

    protected function setUp(): void
    {
        $this->playerRepository = new InMemoryPlayerRepository();
        $this->skillConfigRepository = new InMemorySkillConfigRepository();
    }

    public function testShouldAddSkillToPlayer(): void
    {
        $this->whenIAddSkillToPlayer(SkillEnum::TECHNICIAN);
        $this->thenPlayerShouldHaveSkill(SkillEnum::TECHNICIAN);
    }
}
```

### ✅ Functional Tests

```php
// Good - Use AbstractFunctionalTest base class
final class ExchangeBodyCest extends AbstractFunctionalTest
{
    public function shouldMakeUsersExchangePlayers(FunctionalTester $I): void
    {
        $this->whenSourceExchangesBodyWithTarget();
        $this->thenPlayerUsersAreSwapped($I);
    }

    private function whenSourceExchangesBodyWithTarget(): void
    {
        $this->exchangeBody->loadParameters(
            actionConfig: $this->actionConfig,
            actionProvider: $this->source,
            player: $this->source,
            target: $this->target,
        );
        $this->exchangeBody->execute();
    }
}
```

## Tech Stack

* **Framework**: Symfony 6.4
* **Language**: PHP 8.4 with strict types
* **Database**: PostgreSQL with Doctrine ORM
* **API**: API Platform for REST endpoints
* **Testing**: Codeception (functional) + PHPUnit (unit)
* **Validation**: Symfony Validator with custom constraints
* **Events**: Symfony EventDispatcher
* **Containerization**: Docker

## Architecture Patterns

### Event-Driven Design

* Actions trigger domain events (`PlayerEvent::CONVERSION_PLAYER`)
* Events are handled by listeners in relevant modules
* Loose coupling between modules through events

### Repository Pattern

* All data access goes through repository interfaces
* `InMemoryRepository` implementations for unit testing
* Clear separation between domain logic and persistence

### Command Pattern

* Services follow single responsibility (one action per service)
* Service names describe what they do (`CreateLinkWithSolForDaedalusService`)
* Clear `execute()` method for service entry point

### Validation Layer

* Actions use Symfony Validator constraints
* Custom constraints for game-specific rules
* Separation between visibility and execution validation

## Testing Conventions

* **Unit Tests**: Test individual services with fake dependencies
* **Functional Tests**: Test complete user workflows end-to-end
* Use descriptive test method names (`shouldMakeUsersExchangePlayers`)
* Follow Given-When-Then pattern in test methods
* Prefer `InMemory` and `Fake` implementations over mocks

## Other Rules

* Use `declare(strict_types=1)` in all PHP files
* Mark classes `final` unless designed for inheritance
* Use `readonly` for immutable service properties
* Prefer constructor injection over setter injection
* Do **not** remove type declarations or strict typing
* Keep domain logic separate from framework concerns
* Use enums for constants and string values
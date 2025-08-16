# Player Inactivity Status Management

## Overview
This specification defines the requirements for managing player inactivity status based on user actions and login events. The system should automatically apply and remove inactivity statuses based on player behavior patterns.

## Business Rules

### Inactivity Detection Criteria
A player should be marked as **inactive** when:
- Player has ALL their action points (hasn't spent any), AND
- 24 hours have passed since their last action in-game, OR
- The associated user hasn't logged into the system for 24 hours

A player should be marked as **highly inactive** when:
- Player has ALL their action points (hasn't spent any), AND
- 48 hours (2 days) have passed since their last action in-game, OR  
- The associated user hasn't logged into the system for 48 hours

### Inactivity Status Removal
Player inactivity status should be **automatically removed** when:
- The associated user logs into the system
- The player performs any in-game action

## Current System Analysis

### Existing Entities
- **Player Entity** (`Api/src/Player/Entity/Player.php`):
  - `lastActionDate` (DateTime): Tracks when player last performed an action
  - `isInactive()`: Checks for INACTIVE or HIGHLY_INACTIVE status
  - `updateLastActionDate()`: Updates last action timestamp
  - `hasSpentActionPoints()`: Returns true if player has used some action points
  - `lastActionIsFromYesterdayOrLater()`: Returns true if last action >= 24h ago
  - `lastActionIsFromTwoDaysAgoOrLater()`: Returns true if last action >= 48h ago
  - `getUser()`: Returns the associated User entity

- **User Entity** (`Api/src/User/Entity/User.php`):
  - `updatedAt` (TimestampableEntity): Last modification time
  - Needs new `loginAt` field to track explicit login time

### Existing Inactivity Services
- **MakePlayerInactiveService** (`Api/src/Status/Service/MakePlayerInactiveService.php`):
  - Currently applies INACTIVE status if `lastActionIsFromYesterdayOrLater()`
  - Currently applies HIGHLY_INACTIVE status if `lastActionIsFromTwoDaysAgoOrLater()`
  - Only runs if player has spent action points (keep this rule)

- **MakePlayerActiveService** (`Api/src/Status/Service/MakePlayerActiveService.php`):
  - Currently removes INACTIVE and HIGHLY_INACTIVE statuses
  - Currently only runs if player's last action is NOT from yesterday or later
  - Needs to be enhanced to also be callable from login events

### Existing Inactivity Statuses
- `PlayerStatusEnum::INACTIVE`: Basic inactivity status (24h threshold)
- `PlayerStatusEnum::HIGHLY_INACTIVE`: Extended inactivity status (48h threshold)

## Technical Requirements

### 1. Enhanced Inactivity Detection
**Update**: `Api/src/Status/Service/MakePlayerInactiveService.php`

The service should be enhanced to consider both player action dates AND user login dates:

```php
final class MakePlayerInactiveService
{
    public function __construct(private StatusServiceInterface $statusService) {}

    public function execute(Player $player): void
    {
        // Keep existing logic that requires having all action points
        if ($player->hasSpentActionPoints()) {
            return;
        }
        
        if ($this->shouldPlayerBeHighlyInactive($player)) {
            $this->statusService->removeStatus(
                statusName: PlayerStatusEnum::INACTIVE,
                holder: $player,
                tags: [],
                time: new \DateTime(),
            );
            $this->statusService->createStatusFromName(
                statusName: PlayerStatusEnum::HIGHLY_INACTIVE,
                holder: $player,
                tags: [],
                time: new \DateTime(),
                visibility: VisibilityEnum::PUBLIC
            );
        } elseif ($this->shouldPlayerBeInactive($player)) {
            $this->statusService->createStatusFromName(
                statusName: PlayerStatusEnum::INACTIVE,
                holder: $player,
                tags: [],
                time: new \DateTime(),
                visibility: VisibilityEnum::PUBLIC
            );
        }
    }

    private function shouldPlayerBeInactive(Player $player): bool
    {
        return $this->hasExceeded24HoursSinceLastAction($player) 
            || $this->hasExceeded24HoursSinceUserLogin($player);
    }

    private function shouldPlayerBeHighlyInactive(Player $player): bool
    {
        return $this->hasExceeded48HoursSinceLastAction($player) 
            || $this->hasExceeded48HoursSinceUserLogin($player);
    }

    private function hasExceeded24HoursSinceLastAction(Player $player): bool
    {
        return $player->lastActionIsFromYesterdayOrLater();
    }

    private function hasExceeded48HoursSinceLastAction(Player $player): bool
    {
        return $player->lastActionIsFromTwoDaysAgoOrLater();
    }

    private function hasExceeded24HoursSinceUserLogin(Player $player): bool
    {
        $user = $player->getUser();
        return $user->getLoginAt() <= new \DateTime('-1 day');
    }

    private function hasExceeded48HoursSinceUserLogin(Player $player): bool
    {
        $user = $player->getUser();
        return $user->getLoginAt() <= new \DateTime('-2 days');
    }
}
```

### 2. Enhanced Inactivity Removal
**Update**: `Api/src/Status/Service/MakePlayerActiveService.php`

The service should be enhanced to optionally remove inactivity status on user login:

```php
final class MakePlayerActiveService
{
    public function __construct(private StatusServiceInterface $statusService) {}

    public function execute(Player $player): void
    {
        if ($player->getUser()->lastLoginFromYesterdayOrLater() && $player->lastActionIsFromYesterdayOrLater()) {
            return;
        }

        $this->statusService->removeStatus(
            statusName: PlayerStatusEnum::INACTIVE,
            holder: $player,
            tags: [],
            time: new \DateTime(),
            visibility: VisibilityEnum::PUBLIC,
        );
        $this->statusService->removeStatus(
            statusName: PlayerStatusEnum::HIGHLY_INACTIVE,
            holder: $player,
            tags: [],
            time: new \DateTime(),
            visibility: VisibilityEnum::PUBLIC,
        );
    }
}
```

### 3. User Login Event Subscriber
**New**: `Api/src/User/Subscriber/UserLoginSubscriber.php`

Create an event subscriber that listens for user login events:

```php
final readonly class UserLoginSubscriber implements EventSubscriberInterface
{
    public function __construct(
        private MakePlayerActiveService $makePlayerActiveService,
        private PlayerInfoRepositoryInterface $playerInfoRepository
    ) {}

    public static function getSubscribedEvents(): array
    {
        return [
            UserLoginEvent::class => 'onUserLogin',
        ];
    }

    public function onUserLogin(UserLoginEvent $event): void
    {
        $user = $event->getUser();
        $player = $this->playerInfoRepository->getCurrentPlayerInfoForUserOrNull($user)?->getPlayer();

        if ($player) {
            $this->makePlayerActiveService->execute($player);
        }
    }
}
```

### 4. Action Event Integration
**Enhancement**: Action system integration

Ensure that `MakePlayerActiveService` is called whenever a player performs an action, in addition to updating the `lastActionDate`.

### 4. User Entity Enhancement
**Update**: `Api/src/User/Entity/User.php`

Add explicit login tracking field:

```php
#[ORM\Column(type: 'datetime', nullable: false, options: ['default' => 'CURRENT_TIMESTAMP'])]
private \DateTime $loginAt;

public function __construct()
{   
    // existing constructor logic
    $this->loginAt = new \DateTime();
}

public function getLoginAt(): \DateTime
{
    return $this->loginAt;
}

public function updateLoginAt(): self
{
    $this->loginAt = new \DateTime();
    
    return $this;
}
```

### 5. User Login Event
**New**: `Api/src/User/Event/UserLoginEvent.php`

Create an event that is dispatched when a user logs in:

```php
final readonly class UserLoginEvent
{
    public function __construct(private User $user) {}

    public function getUser(): User
    {
        return $this->user;
    }
}
```

## Database Schema Changes

### Required Migration
Add explicit login tracking field migration with `composer diff && composer apply`. Don't forget default value.

## Integration Points

### 1. Authentication System
- Dispatch `UserLoginEvent` when user successfully logs in
- Update User's `loginAt` field on successful login

### 2 . User login event
- Listen for `UserLoginEvent` in `UserLoginSubscriber` to remove inactivity status

## Testing Requirements

### Unit Tests
1. Update `MakePlayerActiveUseCaseTest` and `MakePlayerInactiveUseCaseTest`
2. Test that `UserService` updates `loginAt` on login
3. Test that `UserService` dispatches `UserLoginEvent` on login

### Functional Tests
1. **UserService** : Test full workflow: inactivity → login → removal

## Implementation Steps

1. **Phase 1**: Add `loginAt` field to User entity and migration
3. **Phase 3**: Update `MakePlayerInactiveService` to consider user login dates
4. **Phase 4**: Create `UserLoginEvent` and `UserLoginSubscriber`
5. **Phase 5**: Integrate login event dispatch in `LoginService`
6. **Phase 6**: Add comprehensive testing
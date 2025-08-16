# Username History Tracking

## Overview
Allow viewing the previous usernames of players who have changed their username over time by leveraging Eternaltwin's existing username history functionality.

## Background
The Eternaltwin authentication system already tracks username history through `UserDisplayNameVersions` which contains:
- Current display name (`getCurrent()`)
- Old display names (`getOld()`)

Currently in LoginService:112-114, we only use the current username from `$apiUser->getUser()->getDisplayName()->getCurrent()->getValue()->toString()`, but Eternaltwin provides access to the full history.

## Requirements

### Functional Requirements
1. Extract and display username history from Eternaltwin API
2. Show username history in user profile/information pages
3. Allow moderators to view full username history for moderation purposes
4. Cache username history to avoid repeated API calls

### Non-Functional Requirements
1. Maintain backwards compatibility with existing User entity
2. Preserve performance of login flow
3. Handle cases where Eternaltwin history is unavailable

## Technical Design

### User Entity Enhancement
Add username history field to store previous usernames:
```php
#[ORM\Column(type: 'array', nullable: false)]
private array $usernameHistory = [];

public function getUsernameHistory(): array
{
    return $this->usernameHistory;
}

public function addToUsernameHistory(string $oldUsername): self
{
    if (!in_array($oldUsername, $this->usernameHistory, true)) {
        $this->usernameHistory[] = $oldUsername;
    }
    return $this;
}
```

### LoginService Enhancement
In `verifyCode()` method, extract username history from Eternaltwin and store it:
```php
$displayNameVersions = $apiUser->getUser()->getDisplayName();
$currentUsername = $displayNameVersions->getCurrent()->getValue()->toString();

// Extract old usernames from Eternaltwin
$oldUsernames = [];
foreach ($displayNameVersions->getOld() as $oldDisplayName) {
    $oldUsernames[] = $oldDisplayName->getValue()->toString();
}

// Update user with current username and history
if ($user->getUsername() !== $currentUsername) {
    $user->setUsername($currentUsername);
}
foreach ($oldUsernames as $oldUsername) {
    $user->addToUsernameHistory($oldUsername);
}
$this->userService->persist($user);
```

## API Changes

### User API Enhancement
Add `usernameHistory` field to User serialization (for moderators only) in Api/config/api_platform/serialization/user.yaml

## Testing Strategy
1. Unit tests for LoginService::verifyCode()

## Implementation Order
1. Add username_history field to User entity
2. Create database migration with `composer diff && composer apply`
3. Update LoginService to extract and store username history
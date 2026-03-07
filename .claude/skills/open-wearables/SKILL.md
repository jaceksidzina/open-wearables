# open-wearables Development Patterns

> Auto-generated skill from repository analysis

## Overview

This skill teaches development patterns for the open-wearables project, a comprehensive wearable data integration platform that connects with multiple health providers (Garmin, Apple Health, etc.). The codebase follows modern Python backend practices with FastAPI, includes a React frontend, mobile SDKs, and extensive documentation. The project emphasizes provider integrations, data synchronization, and comprehensive API coverage for wearable device data.

## Coding Conventions

### File Naming
- Use `snake_case` for all Python files and directories
- Examples: `apple_health_service.py`, `garmin_webhooks.py`, `data_import_tasks.py`

### Import Style
```python
# Use aliases for common imports
import pandas as pd
import numpy as np
from datetime import datetime as dt
from backend.app.services.providers.garmin import garmin_service as gs
```

### Export Style
- Use named exports for functions and classes
- Organize exports by functionality in `__init__.py` files

### Commit Messages
- Follow conventional commit format
- Use prefixes: `feat:`, `fix:`, `docs:`, `chore:`, `refactor:`
- Keep messages around 61 characters
- Examples:
  ```
  feat: add heart rate zones support for Garmin provider
  fix: resolve Apple Health data sync timeout issues
  docs: update Flutter SDK integration examples
  ```

## Workflows

### Documentation Update
**Trigger:** When someone wants to update docs with new information or fix documentation issues
**Command:** `/update-docs`

1. Identify documentation files that need updating in `docs/**/*.mdx`
2. Update the relevant `.mdx` files with new content, examples, or corrections
3. If adding new pages, update `docs/docs.json` to include the new documentation
4. Update `README.md` if the changes affect main project documentation
5. Commit with `docs:` prefix describing the changes

```bash
# Example commit
git commit -m "docs: add troubleshooting guide for Garmin sync issues"
```

### Provider Integration Fix
**Trigger:** When someone needs to fix bugs or improve reliability of provider data sync
**Command:** `/fix-provider`

1. Navigate to the specific provider service files in `backend/app/services/providers/{provider}/`
2. Update the provider service logic to fix sync issues or improve reliability
3. Modify webhook handlers in `backend/app/api/routes/v1/{provider}_webhooks.py` if needed
4. Update or add tests in `backend/tests/**/{provider}*.py`
5. Add structured logging for better debugging
6. Test the integration thoroughly

```python
# Example provider service update
class GarminService:
    def sync_activities(self, user_id: str):
        logger.info(f"Starting activity sync for user {user_id}")
        try:
            # Enhanced error handling and retry logic
            activities = self._fetch_activities_with_retry(user_id)
            return self._process_activities(activities)
        except Exception as e:
            logger.error(f"Activity sync failed for user {user_id}: {str(e)}")
            raise
```

### Backend Service Enhancement
**Trigger:** When someone wants to improve backend functionality or add new service capabilities
**Command:** `/enhance-backend`

1. Update service files in `backend/app/services/**/*.py` with new features or improvements
2. Modify Celery tasks in `backend/app/integrations/celery/tasks/*.py` if background processing is involved
3. Update related schemas in `backend/app/schemas/*.py` for data validation
4. Add or update tests in `backend/tests/**/*.py`
5. Add structured logging for monitoring and debugging
6. Update API documentation if endpoints are affected

```python
# Example service enhancement
class DataSyncService:
    async def enhanced_sync(self, provider: str, user_id: str):
        """Enhanced sync with better error handling and progress tracking."""
        logger.info(f"Starting enhanced sync for {provider}, user: {user_id}")
        
        progress = SyncProgress(user_id=user_id, provider=provider)
        try:
            await progress.start()
            result = await self._perform_sync(provider, user_id)
            await progress.complete(result)
            return result
        except Exception as e:
            await progress.fail(str(e))
            raise
```

### Coverage Matrix Update
**Trigger:** When provider data type support changes and coverage docs need updating
**Command:** `/update-coverage`

1. Review the actual provider implementation in service files
2. Compare current implementation with documented coverage matrix
3. Update `docs/providers/coverage.mdx` to reflect actual support
4. Ensure documentation accurately represents what data types each provider supports
5. Include any limitations or special considerations

### SDK Documentation Update
**Trigger:** When mobile SDKs (Flutter, iOS, Android) need documentation updates
**Command:** `/update-sdk-docs`

1. Update SDK-specific documentation files in `docs/sdk/**/*.mdx`
2. Add new integration examples with code snippets
3. Update troubleshooting guides with common issues and solutions
4. Update the main SDK index in `docs/docs.json` if adding new sections
5. Ensure examples are tested and working

```dart
// Example Flutter SDK documentation
class HealthDataSync {
  Future<void> syncWithProvider(String provider) async {
    try {
      final result = await OpenWearablesSDK.sync(provider);
      print('Sync completed: ${result.recordsProcessed} records');
    } catch (e) {
      print('Sync failed: $e');
      // Handle error appropriately
    }
  }
}
```

### Frontend UI Enhancement
**Trigger:** When someone wants to enhance user interface or add new frontend features
**Command:** `/enhance-ui`

1. Update React components in `frontend/src/components/**/*.tsx`
2. Modify TypeScript types in `frontend/src/lib/api/types.ts` if API changes are involved
3. Update hooks or services in `frontend/src/hooks/**/*.ts` for state management
4. Test UI changes across different screen sizes and user scenarios
5. Ensure accessibility standards are maintained

```typescript
// Example UI component enhancement
interface ProviderStatusProps {
  provider: Provider;
  onSync: (providerId: string) => void;
}

export const ProviderStatus: React.FC<ProviderStatusProps> = ({ 
  provider, 
  onSync 
}) => {
  const { data, isLoading, error } = useProviderStatus(provider.id);
  
  return (
    <div className="provider-status">
      <StatusIndicator status={data?.status} />
      <SyncButton 
        onClick={() => onSync(provider.id)}
        disabled={isLoading}
      />
    </div>
  );
};
```

### Apple Health Integration Update
**Trigger:** When Apple Health data handling needs improvements or new data types support
**Command:** `/update-apple-health`

1. Update Apple Health schemas in `backend/app/schemas/apple/**/*.py`
2. Modify import service logic in `backend/app/services/apple/**/*.py`
3. Update constants in `backend/app/constants/**/*.py` for new data types
4. Update tests in `backend/tests/**/*apple*.py`
5. Add structured logging for Apple Health specific operations
6. Ensure data privacy and security standards are maintained

```python
# Example Apple Health schema update
class AppleHealthWorkout(BaseModel):
    workout_type: str
    duration: int
    calories_burned: Optional[float]
    distance: Optional[float]
    heart_rate_zones: Optional[List[HeartRateZone]]  # New field
    
    class Config:
        schema_extra = {
            "example": {
                "workout_type": "running",
                "duration": 1800,
                "calories_burned": 245.5,
                "heart_rate_zones": [
                    {"zone": "fat_burn", "duration": 600},
                    {"zone": "cardio", "duration": 1200}
                ]
            }
        }
```

## Testing Patterns

Tests follow the `*.test.*` pattern and are located in provider-specific directories:

```python
# Example test structure
class TestGarminProvider:
    def test_activity_sync_success(self):
        # Arrange
        user_id = "test_user_123"
        mock_activities = self._create_mock_activities()
        
        # Act
        result = garmin_service.sync_activities(user_id)
        
        # Assert
        assert result.success is True
        assert len(result.activities) == 3
        
    def test_sync_handles_api_timeout(self):
        # Test error handling scenarios
        with pytest.raises(TimeoutError):
            garmin_service.sync_activities_with_timeout("user_123", timeout=1)
```

## Commands

| Command | Purpose |
|---------|---------|
| `/update-docs` | Update documentation files with new features or corrections |
| `/fix-provider` | Fix issues with external provider integrations |
| `/enhance-backend` | Add new features or fix bugs in backend services |
| `/update-coverage` | Update provider coverage documentation matrix |
| `/update-sdk-docs` | Update mobile SDK documentation and examples |
| `/enhance-ui` | Improve frontend user interface components |
| `/update-apple-health` | Update Apple Health data processing and schemas |
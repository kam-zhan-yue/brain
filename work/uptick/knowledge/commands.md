```
mise run test:frontend:lint
mise run fix:frontend:lint
bun biome check
inv up
```

### Toggling a Feature Flag
```python
from abas.apps.feature_flags import FeatureFlag, toggle_feature

toggle_feature(FeatureFlag[name], True)
```

### Patching
```
mise use bun@1.2.19
mise x -- bun patch @tanstack-router
# make changes
mise x -- bun commit @tanstack-router
```
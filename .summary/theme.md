# Ghost Theme System

## Overview

Ghost's theme system allows site owners to customize the appearance of their site. Themes can be either official (free and available for all users, from the TryGhost organization) or custom (third-party or user-created). Ghost includes mechanisms to restrict which themes can be used based on subscription plans or self-hosted configurations.

## Theme Restrictions

Ghost includes a system to restrict theme usage based on subscription plans or self-hosted configurations:

## Theme Restriction Mechanism
Ghost uses a "limits" service to control access to custom themes. The main restriction is between:
1. Official themes - Themes from the TryGhost organization (like Source, Casper, etc.)
2. Custom themes - Third-party or user-created themes

### Key Implementation Points
1. Limit Service Checks
When users try to install or activate a theme, Ghost checks if they're allowed to use custom themes:

```js
//installer.js
//TODO: move the organization check to config
if (limitService.isLimited('customThemes') && org.toLowerCase() !== 'tryghost') {
    await limitService.errorIfWouldGoOverLimit('customThemes', {value: repo.toLowerCase()});
}
```
2. Theme Upload Restrictions

```js
//ghost/core/core/server/api/endpoints/themes.js
if (limitService.isLimited('customThemes') && org.toLowerCase() !== 'tryghost') {
    await limitService.errorIfWouldGoOverLimit('customThemes', {value: repo.toLowerCase()});
}
```
3. UI Handling
The admin interface shows different options based on whether custom themes are allowed:

```js
// apps/admin-x-settings/src/components/settings/site/ThemeModal.tsx
const handleUpload = () => {
    if (uploadConfig?.enabled) {
        // Show upload modal
        NiceModal.show(ConfirmationModal, {
            title: 'Upload theme',
            prompt: <UploadModalContent onUpload={onThemeUpload} />,
            okLabel: '',
            formSheet: false
        });
    } else {
        // Show upgrade modal
        NiceModal.show(LimitModal, {
            title: 'Upgrade to enable custom themes',
            prompt: uploadConfig?.error || <>Your current plan only supports official themes. You can install them from the <a href="https://ghost.org/marketplace/">Ghost theme marketplace</a>.</>,
            onOk: () => updateRoute({route: '/pro', isExternal: true})
        });
    }
};
```



## Configuring Theme Restrictions

You can restrict theme usage without modifying Ghost's code by using configuration:

#### Using Config Files

Add to your `config.production.json` or equivalent:

```json
{
  "url": "https://your-ghost-blog.com",
  "server": {
    "host": "127.0.0.1",
    "port": 2368
  },
  "database": {
    // your database config
  },
  "limits": {
    "customThemes": {
      "disabled": true,
      "error": "Custom themes are not allowed on this installation"
    },
    "allowedThemes": ["TryGhost/Casper", "TryGhost/Source"]
  }
}
```

This configuration:
- Disables custom themes completely with "disabled": true
- Provides a custom error message
- Optionally specifies an allowlist of themes that can be installed

OR Docker config file:
```yaml
# Disable custom themes
export GHOST_LIMITS_CUSTOM_THEMES_DISABLED=true
export GHOST_LIMITS_CUSTOM_THEMES_ERROR="Custom themes are not allowed"

# Start Ghost
ghost start
```
#### Database Approach
If you prefer a database approach without code changes:
Access your Ghost database
Find the settings table
Add a new setting with:
`key`: `limits_custom_themes`
`value`: `{"disabled":true,"error":"Custom themes are not allowed"}`
`group`: `core`

```sql
INSERT INTO settings (key, value, group)
VALUES ('limits_custom_themes', '{"disabled":true,"error":"Custom themes are not allowed"}', 'core');
```

## Disabling Theme Downloads

In addition to restricting theme usage, you can also disable the ability to download themes from the admin UI. This prevents users from exporting themes for use elsewhere.

### Understanding the Implementation

The theme download functionality is controlled by the `download` endpoint in the themes API. Here's how it's implemented in Ghost's core code:

```javascript
// ghost/core/core/server/api/endpoints/themes.js
download: {
    headers: {
        cacheInvalidate: false
    },
    options: [
        'name'
    ],
    validation: {
        options: {
            name: {
                required: true
            }
        }
    },
    permissions: {
        method: 'read'
    },
    query(frame) {
        let themeName = frame.options.name;

        return themeService.api.getZip(themeName);
    }
}
```

This endpoint is accessible via the `/themes/:name/download` URL and is used by the admin UI's download button. By intercepting requests to this endpoint, we can disable theme downloads.






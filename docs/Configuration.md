---
description: Information on how to get Maintainerr up and running.
title: Configuration
---

All configuration is done inside the application, no extra config is required in files. </br>
When you first access the webUI, you will be redirected to the settings page. If this doesn't happen, refresh the page.

!!! info
    You need to configure either Plex or Jellyfin. Both are not supported simultaneously.

!!! info
    All Base URL settings are to be entered without the leading slash.

    - Right: `tautulli`
    - Wrong: `/tautulli`

## General

These settings are OK for most installations.

| Param        | Description           |
| ------------- |:-------------:|
| Hostname      | The hostname or IP of the host Maintainerr runs on |
| Api key       | Maintainerr's API key. Which has no use yet. |

## Media Server

### Plex

When using a local Plex instance, make sure your Plex's 'secure connections' network setting is set to 'preferred' instead of 'required'.
If you'd like Maintainerr to communicate securely, you'll have to use your '*.plex.direct' URL as the hostname. You can copy this URL from Overseerr's configuration. Make sure you include 'https://'.

| Param        | Description           |
| ------------- |:-------------:|
| Name          | The name of your server |
| Hostname or IP| The domain name or local IP of the host your server runs on |
| Port          | The port Plex runs on, default is 32400 |
| Authentication| Authenticate with your Plex server using an **admin** account |

!!! tip
    The typical flow is to Authenticate with Plex -> click on the refresh icon -> choose your server from the dropdown menu -> click on Save Changes -> click on Test Saved.

### Jellyfin

Jellyfin uses API keys for authentication. You'll need to select an admin user for full access.

| Param        | Description           |
| ------------- |:-------------:|
| Hostname or IP| The domain name or local IP of your Jellyfin server (e.g., `http://192.168.1.10`) |
| Port          | The port Jellyfin runs on, default is 8096 |
| API Key       | API key from Jellyfin settings (Dashboard > API Keys) |
| Admin User    | Select an admin user from the dropdown |

!!! warning "Admin User Required"
    API key authentication doesn't provide a user context. An admin user is needed for:
    
    - Watch history across all users
    - Access to all libraries
    - Collection management

!!! tip
    Enter URL and port -> Add API key -> Test connection -> Select admin user from dropdown -> Save Changes.

### Switching Media Servers

You can switch between Plex and Jellyfin. See the [Migration](Migration.md) page for details on how switching works and what gets migrated.

## Overseerr

Overseerr's configuration is required to use its parameters in rules and to remove requests. </br>

| Param | Description |
| ------------- |:-------------:|
| Hostname or IP| The domain name or local IP of the host your server runs on |
| Port          | The port Overseerr runs on |
| Api key  | Overseerr API key, should be visible in the Overseerr settings |

## Jellyseerr

Jellyseerr's configuration is required to use its parameters in rules and to remove requests. </br>

| Param | Description |
| ------------- |:-------------:|
| Hostname or IP| The domain name or local IP of the host your server runs on |
| Port          | The port Jellyseerr runs on |
| Api key  | Jellyseerr API key, should be visible in the Jellyseerr settings |

## Radarr

Radarr's configuration is required to use its parameters in rules and to remove or unmonitor movies.

| Param | Description           |
| ------------- |:-------------:|
| Server Name   | A friendly name to help identify the server |
| Hostname or IP| The domain name or local IP of the host your server runs on |
| Port          | The port Radarr runs on |
| Base URL      | URL Base set in Radarr settings (if set)|
| Api key   | Radarr API key, should be visible in the Radarr settings |

## Sonarr

Sonarr's configuration is required to use its parameters in rules and to remove or unmonitor shows.

| Param | Description           |
| ------------- |:-------------:|
| Server Name   | A friendly name to help identify the server |
| Hostname or IP| The domain name or local IP of the host your server runs on |
| Port          | The port Sonarr runs on |
| Base URL      | URL Base set in Sonarr Settings (if set)|
| Api key   | Sonarr API key, should be visible in the Sonarr settings |

## Tautulli

Tautulli's configuration is required to use its parameters in rules.

| Param | Description |
| ------------- |:-------------:|
| Hostname or IP| The domain name or local IP of the host your server runs on |
| Port          | The port Tautulli runs on |
| Base URL      | HTTP Root set in Tautulli Settings (if set )|
| Api key  | Tautulli API key, should be visible in the Tautulli settings |

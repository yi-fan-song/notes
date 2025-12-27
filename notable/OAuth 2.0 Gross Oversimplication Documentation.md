---
tags: [Notebooks/Documentation]
title: OAuth 2.0 Gross Oversimplication Documentation
created: '2019-11-13T00:42:14.410Z'
modified: '2019-11-13T02:08:29.437Z'
---

# OAuth 2.0 Gross Oversimplication Documentation

## Some definitions first
### Client
It's the application attempting to access the user's ressources.

### Ressource server
It's the API serving the user's ressource to a client.

### Authorization server
It's the server that the user interacts with to approve or deny a request.

### Ressource owner
It's the user who is giving access to his ressources.

## Password grant
This is only to be used by the original application (i.e.: twitter logins only happens on twitter website or twitter app).

Usually the app makes a POST request to the token endpoint like so:
```json
POST https://api.auth-server.com/token

// the body looks like this
{
  "grant_type": "password",
  "username": "{username}",
  "password": "{password}",
  "client_id": "{client_id}"
}
```

The server returns an access token.

## Basic auth flow

1. The user opens the app and is sent to a login page.
1. Login page asks for user's permission to access his ressources.
1. The user returns to the app with a authorization code.
1. The app exchanges the authorization code with an access token.

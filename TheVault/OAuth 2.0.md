before we start, a few basic thing
```
client: an application making request for protected resource on behalf of resource owner

resource owner: entity capable of granting access to a protected resource, called an end-user when it's a person

resource server: server hosting the protected resource

authorization server: server issuing the access tokens after authenticating resource owner

user-agent: The software which initiates the requests, typically browsers
```

- This is how a general OAuth 2.0 flow looks like
![[OAuth2.0_general_flow.excalidraw]]
Mostly, you don't directly request for `authorization-grant` from Resource Owner, but `authorization server` working as an intermediary
![[OAuth2.0_refresh_token_flow.excalidraw]]
It's not necessary that you get a refresh token along side a new access token.

- `OAuth 2.0` flow have 4 grant types, but mostly we would be concerning with **primarily** two grant-types, 
	- `Authorization code` ![[OAuth2.0_authorization_code_flow.excalidraw]]
	- `Implicit`
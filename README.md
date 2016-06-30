# box_backup
File backup with box storage.

Required: curl, jq

Getting Box authrization code:
- Make a new app at box with CLIENT_ID, CLIENT_SECRET, REDIRECT_URI(https).
- Construct a uri:
    https://account.box.com/api/oauth2/authorize?response_type=code&client_id=CLIENT_ID&redirect_uri=REDIRECT_URI&state=security_token%3DKnhMJatFipTAnM0nHlZA
- Go to the uri with browser and login.
- Find AUTHORIZATION_CODE in redirected uri.

Getting first access token and refresh token:
    curl https://api.box.com/oauth2/token -d 'grant_type=authorization_code&code=AUTHORIZATION_CODE&client_id=CLIENT_ID&client_secret=CLIENT_SECRET' -X POST

Refreshing token:
    curl https://api.box.com/oauth2/token -d 'grant_type=refresh_token&refresh_token=REFRESH_TOKEN&client_id=CLIENT_ID&client_secret=CLIENT_SECRET' -X POST


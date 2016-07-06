# box_backup
File backup with box storage.

####Required: linux, curl, jq

###Usage

####Installing:

Put all files into some folder and chmod +x to files other than *.json.

####Getting Box authrization code:

- Make a new app at box with CLIENT_ID, CLIENT_SECRET, REDIRECT_URI(https).

- Construct a uri on the text editor:

```
https://account.box.com/api/oauth2/authorize?response_type=code&client_id=CLIENT_ID&redirect_uri=REDIRECT_URI&state=authrization
```

- Go to the uri with browser and login.

- Find AUTHORIZATION_CODE in redirected uri. It expires in 30 seconds so do next step quickly!

####Getting first access token and refresh token:

```
curl https://api.box.com/oauth2/token -d 'grant_type=authorization_code&code=AUTHORIZATION_CODE&client_id=CLIENT_ID&client_secret=CLIENT_SECRET' -X POST
```
Put the output into ```box_token.json``` file.
Also set CLIENT_ID/CLIENT_SECRET into ```box_client.json``` file

####Initialy uploading your backup:

```
export BOX_BACKUP_DIR=/usr/local/box_backup # Installed directory
$BOX_BACKUP_DIR/box_backup -I -p /tmp/messages.tgz -n messages.tgz -f Docs
# -I [Initialy uploading]
# -p [backup file Path]
# -n [box file Name]
# -f [box Folder name]
```

####Updating your backup:

```
export BOX_BACKUP_DIR=/usr/local/box_backup # Installed directory
$BOX_BACKUP_DIR/box_backup -U -p /tmp/messages.tgz -n messages.tgz -f Docs -v 2
# -I [Updating backup]
# -p [backup file Path]
# -n [box file Name]
# -f [box Folder name]
# -v [number of Versions to keep]
```

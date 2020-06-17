# radarrV3_foldername


```###
### Custom Post-Processing script for renaming folder according to movie file name
###

### You should enable this script for "On Import" and "On Upgrade"
if [[ $radarr_eventtype == "Download" ]] ; then
        ### Replace "/movies/1080p/" by your root path
        ROOTPATHMOVIE="/movies/1080p/"
        ### Get the newly imported movie json file and retrieve the movie file name. Don't forget to replace the radarr ip and the api key.
        curl -X GET "https://radarr-ip-or-domain/api/movie/$radarr_movie_id?apikey=YOURAPIKEY" -o /tmp/temp.json
        MOVIEFILENAME=$radarr_moviefile_relativepath
        ### Add the movie filename without the extension (.mkv) to the root path in a new Env Variable
        export FOLDERNAME=$ROOTPATHMOVIE${MOVIEFILENAME:0:-4}
        ### Move the movie file to the new folder
        mv $radarr_movie_path $FOLDERNAME
        ### Edit the json file to overwriting the new path and the new folderName
        CHANGEPATH="$(jq ''.path' = $ENV.FOLDERNAME | '.folderName' = $ENV.FOLDERNAME' /tmp/temp.json)" && echo "${CHANGEPATH}" > /tmp/temp.json
        ### Send back the json file to the radarr database. Don't forget to replace the radarr ip and the api key
        curl -X PUT -H "Content-Type: application/json" --data @/tmp/temp.json "https://radarr-ip-or-domain/api/movie/$radarr_movie_id?apikey=YOURAPIKEY"
fi
```

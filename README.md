
Note: Built to work with Flarum `v0.1.0-beta.5`.

# Flarum & Dataporten Docker Image

This image features PHP/Nginx/Flarum, with:

- The [Dataporten extension] (https://github.com/skrodal/flarum-ext-auth-dataporten), which allows users to login using Dataporten from UNINETT
- Command-line installation with YAML config file (see below) 
- Uses an external DB (making Flarum content persistent) 
- No need to create DB ahead of time (the config file takes care of that)

Although the image is adapted to suit higher education in Norway, the workflow (and Dataporten OAuth extension) may be useful to others wanting to create something similar.

## Install

As you will need to populate a Flarum configuration file before build, first clone this repository.

### 1. etc/flarum/config.yml

Populate Flarum's config-file used for command-line install, which will be used by Docker build. 

If you have already registered your client with Dataporten, you may enter its ID/Secret in the config file. Otherwise, you may set it manually in Flarum's Admin UI after the build/run.


```
baseUrl : "http://127.0.0.1/path_to_flarum/"
databaseConfiguration :
    host : "localhost"
    database : "flarum_database_name"
    prefix: "flarum_table_name_prefix_"
    username : "username"
    password : "password"
adminUser : 
    username : "adminuser"
    password : "adminpassword"
    password_confirmation : "adminpassword"
    email : "admin@email.com"
settings : 
    forum_title : "Flarum Site Title"
    forum_title           : "Dataporten Test Forum"
    welcome_title         : "Velkommen til Dataporten Test Forum"
    welcome_message       : "Bare for testing :)"
    uninett-auth-dataporten.client_id     : "___optional___"
    uninett-auth-dataporten.client_secret : "___optional____"
    mail_from             : "noreply@dataporten.dev"
    default_locale        : "no"
    theme_colored_header  : 1
    theme_primary_color   : "#ed1b34"
    theme_secondary_color : "#010777"
```

### 2. Build

Run the following from the directory in which you cloned the repo:

> docker build -t uninettno/dataporten-flarum-docker .

* The config file will be copied into the container, used by Flarum installation, then deleted again.


## After Build

### Run

E.g. on port 80, like this:

	> docker run -d -p 80:80 --name flarum uninettno/dataporten-flarum-docker

* Remember to disable any web-servers running on host (to free port 80, if that's what you're using).

### Enable Dataporten

Log on to Flarum with the `adminUser` credentials (set in config.yml) and enable Dataporten extension in Flarum's Admin UI.

a) If you already entered the client's ID/Secret in the install-config, the extension will now work.
b) otherwise, use the extenstions `settings` button and enter ID/Secret manually.

*Add `/auth/dataporten` at the end of your Dataporten client's Redirect URI.*

More info about Dataporten in the [Dataporten extension readme on GitHub](https://github.com/skrodal/flarum-ext-auth-dataporten).

### Debug

	> docker exec -ti flarum bash

### Stop
	
	> docker stop flarum && docker rm flarum

## Issues/todo


**Avatars** 

Avatars (profile photo) are stored locally (in the container) and will thus be lost on Docker restart (as it is downloaded and saved locally within the image). 

Using external image links, to make avatars persistent, [is not supported by Flarum](https://discuss.flarum.org/d/3041-upload-avatar-to-imgur) (yet). 

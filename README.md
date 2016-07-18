
** 1st release -- do not use! **

# Flarum

Adapted to higher education in Norway, which includes an extension for authentication via Dataporten.

Built to work with Flarum `v0.1.0-beta.5`.

## Install

As you will need to populate a Flarum configuration file before build, first clone this repository.

### 1. etc/flarum/config.yml

Populate Flarum's config-file used for command-line install, which will be used by Docker build. 

The config file will attempt to enable the Dataporten (and 3rd party language) extension. You will therefore need to register your client with Dataporten and populate this config file **prior** to running the Docker build.


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
    uninett-auth-dataporten.client_id     : "___ENTER____"
    uninett-auth-dataporten.client_secret : "___ENTER____"
    mail_from             : "noreply@dataporten.dev"
    default_locale        : "no"
    extensions_enabled    : "uninett-auth-dataporten"
    extensions_enabled    : "pladask-norwegian-bokmal"
    theme_colored_header  : 1
    theme_primary_color   : "#ed1b34"
    theme_secondary_color : "#010777"
```

### 2. Build

Run the following from the directory in which you cloned the repo:

> docker build -t uninettno/dataporten-flarum-docker .


## After Build

### Run

On port 80:

	> docker run -d -p 80:80 --name flarum uninettno/dataporten-flarum-docker

Remember to disable any web-servers running on host (to free port 80).

### Enable Dataporten

Log on to Flarum with the `adminUser` credentials (set in config.yml) and enable Dataporten extension in Flarum's Admin UI.

More info about Dataporten in the [Dataporten extension readme on GitHub](https://github.com/skrodal/flarum-ext-auth-dataporten).

### Debug

	> docker exec -ti flarum bash

### Stop
	
	> docker stop flarum && docker rm flarum

## Issues/todo

Avatars (profile photo) will be lost on Docker restart (as it is downloaded and saved locally within the image). Using external image links [is not supported by Flarum](https://discuss.flarum.org/d/3041-upload-avatar-to-imgur)

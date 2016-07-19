
Note: Built to work with Flarum `v0.1.0-beta.5`.

# Flarum & Dataporten Docker Image

This image features PHP/Nginx/Flarum, with:

- The [Dataporten extension] (https://github.com/skrodal/flarum-ext-auth-dataporten), which allows users to login using Dataporten from UNINETT
- Command-line installation of Flarum with YAML config file (see below) 
- Uses an external DB (making Flarum content persistent) 
- No need to create DB ahead of time (the config file takes care of that)

Although the image is adapted to suit higher education in Norway, the workflow (and Dataporten OAuth extension) may be useful to others wanting to create something similar.

## Installation

Follow the steps below to install and configure the Flarum Docker Image:


### 1. Clone repository

    git clone https://github.com/skrodal/dataporten-flarum-docker.git

### 2. Edit install config

After clone, you will find the config file here `dataporten-flarum-docker/etc/flarum/config.yml`. It looks something like this:

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

- This Docker Image is created to use an external Database for Flarum. Edit the config file's `databaseConfiguration` section according to your setup.

- The section `adminUser` defines Flarum's administrator account.

- Use section `settings` to change some of Flarum's defaults. Here, you may also enter your client's `Dataporten` OAuth ID and Secret (if you have already registered your client with Dataporten).

### 3. Build the Docker image

Make sure your working directory is the root of the cloned repository and run:

    docker build -t uninettno/dataporten-flarum-docker .

_The config file will be copied into the image, then used by Flarum's installation script and then deleted._

...the build will take a while, but when finished you should have a working image. If not (e.g. problems connecting to DB), observe any error messages from the build, address these (e.g. update your config file) and re-run the above build command.

### 4. Run/debug/stop the image

Run the _image_ on port 80 (alt. choose your own port), like this:

    docker run -d -p 80:80 --name flarum uninettno/dataporten-flarum-docker

_Remember to disable any web-servers running on host (to free port 80, if that's what you're using)._

Enter the running container:
    
    docker exec -ti flarum bash

Stop [and remove] the container:
    
    docker stop flarum [&& docker rm flarum]

Start a stopped container named 'flarum':
    
    docker start flarum

### 5. Go to your site!

Go to the URL of your Flarum installation and log in as admin with the `adminUser` credentials you set in the config file, then:

- Menu `Admin->Administration`
- Page `Extensions`
- Enable extension Dataporten (and optionally Norwegian translation extension)
    - Double-check that the Client ID and Client Secret are entered in the extension's Settings
- Log out and back in again - notice that Dataporten Login now is an option in the login window :)

#### Dataporten-Specific

The redirect URI for your client should be the URI to your Flarum site, plus `/auth/dataporten`.

More info about Dataporten in the [Dataporten extension readme on GitHub](https://github.com/skrodal/flarum-ext-auth-dataporten).


## Issues/todo


**Uploaded content (e.g. avatars)** 

The Dataporten extension automatically adds the profile photo ('avatar') upon first auth/signup. Flarum downloads any added media and stores it locally, meaning that this type of data is not persitent (i.e. lost on a Docker image update). 

- Using external image links, i.e. to make avatars persistent, [is not supported by Flarum](https://discuss.flarum.org/d/3041-upload-avatar-to-imgur) (yet). 
- As a workaround, you can use a Docker Volume/Data Container to achieve persistency.

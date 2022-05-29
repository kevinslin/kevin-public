---
id: chwe5ewde3wfh09we9n1rfi
title: Private Images
desc: ''
updated: 1653785858431
created: 1653784733343
---

All my images are stored in my assets folder in Dropobx.
```
$HOME/Dropbox/assets/images
```

I serve them via localhost using nginx.

I can access any image using the following url: `http://localhost:$NGINX_PORT/img/<image-name>`

Benefits of this system - it keeps the backend flexible. If I decide to drop dropbox (which I'm increasingly tempted to do due to the agressive upselling, I can do so without breaking my links. Also, many markdown editors are unable to render images that are based on absolute paths.

A future project I have in mind is to combine this with a VPN like [tailscale](https://tailscale.com/) so that I can access images on the go as well. 

## Setup Instructions 
- these instructions are for mac and true as of 2022-05-28

1. install nginx
    ```
    brew install nginx
    ```
1. symlink image folder
    ```
    cd /opt/homebrew/var/www
    ln -s {PATH_TO_WHERE_YOUR_IMAGES_ARE} img
    ```
1. optional: change the port 
    - if you don't want to use the default (`8080`), you can change it in `/opt/homebrew/etc/nginx/nginx.conf`
    - afterwards, reload nginx
    ```
    brew services reload nginx
    ```
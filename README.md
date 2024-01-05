# Drupal training - Custom Theming

- Add ddev configurations
- Use the following .ddev/config.yaml

```bash
name: drupaltraining
type: drupal8
docroot: web
php_version: "7.3"
webserver_type: nginx-fpm
router_http_port: "80"
router_https_port: "443"
xdebug_enabled: false
additional_hostnames: []
additional_fqdns: []
provider: default
use_dns_when_possible: true
nfs_mount_enabled: true

hooks:
  post-start:
    - composer: install
    - exec: /var/www/html/db/import-db.sh
    - exec: drush updb -y
    - exec-host: cp web/sites/example.development.services.yml web/sites/development.services.yml
    - exec-host: mkdir -p web/sites/default/files/images
    - exec-host: chmod -R ugo+w web/sites/default/files/images/
    - exec-host: cp -rf web/assets/images/* web/sites/default/files/images/
    - exec: drush cr
    - exec-host: ddev launch /user
```

- Fix the theme template
    - Drupal theming with components using pattern lab
    - https://youtube.com/playlist?list=PLu-MxhbnjI9oEF9uR59Sq66_7Pf4guwG1&si=B0wZqO1L93T_eLiV
- Update the php version to 8.x
- Update the Drupal version to 9.x

## Let's start

### 1. Software requirements
* Install [Docker & DDEV-Local](https://ddev.readthedocs.io/en/stable/#installation)

### 2. Clone the repo:
```
git clone <this repo>
```

### 3. Build the project:
Ensure Docker is running before proceeding.

```
cd <repo>
```


---
### Login to Drupal
http://<project-name>.ddev.site/user/

Username: `admin`, password: `admin`

## Working with the theme
The custom Drupal theme, `training_theme`, can be found in `web/themes/custom/`.  Commands to interact with the theme can be ran from anywhere whithin this project.

### Install theme dependencies
```bash
ddev nvm install && ddev nvm use
```
* This will install version of node declared in `.nvmrc` and will set the theme to use it.

```bash
ddev npm install
```
* This command will install all of the theme's node dependencies (Gulp, Pattern Lab, Autoprefixer, Browsersync, eslint, sass-lint, and others).
* These last two commands above may only be need to run once.

### Build/Compile the theme
```bash
ddev npm run build
```
* This command builds the entire codebase for the theme.
* If you encountered npm errors, run `ddev npm rebuild node-sass`, and try the build command again.

### Clear Drupal caches
To ensure Drupal is able to access all the theme's assets after building the
theme above, clear Drupal's caches.
```bash
ddev drush cr
```

### Run the watch task to access Pattern Lab
```bash
ddev npm run watch
```
* In addition to compiling the theme, this tasks stays running to watch for new code changes within the theme, then automatically compiles them.
* While the watch task is running, you can access Pattern Lab by going to `https://<project-name>.ddev.site:3000`, or port 3001 if using `http`.

## Interacting with DDev
Official <a target="_blank" href="https://ddev.readthedocs.io/en/stable/">DDEV-Local docs</a>.

```bash
ddev poweroff
```
_Stop DDev if not longer using_.

```bash
ddev start
```
_Start DDev to work on project_.

```bash
ddev delete --omit-snapshot --yes
```
_If project is no longer needed, this will wipe everything out including database.  Use `ddev start` to rebuild project from scratch_.

### Running Drush commands
```bash
ddev drush <command>
```
example: `ddev drush cr`, `ddev drush updb -y`, etc.

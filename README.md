# Quick start for Rails 6 + Webpacker + PostgreSQL on Docker

> The repository is based on the article [Ruby on Whales: Dockerizing Ruby and Rails development — Martian Chronicles, Evil Martians’ team blog](https://evilmartians.com/chronicles/ruby-on-whales-docker-for-ruby-rails-development). Note that the article will be continuously updated.

The docker-compose.yml is for building a minimum and configurable development environment for Rails 6 with the following, in a very short time.

- Webpacker
- PostgreSQL

You don't need to tweak the Dockerfile to update gems, packs, databases.

Note that current config is just for building a dev env, not for production container.

Assumed that bundler is included within Ruby 2.7 or later. If you want to specify other version of bundler, uncomment the following:

- `BUNDLER_VERSION` in docker-compose.yml
- `&& gem install bundler:$BUNDLER_VERSION` in .dockerdev/Dockerfile.

## Requirement

- Docker and Docker Compose
- [dip](https://github.com/bibendi/dip) for managing docker-compose.yml

## Step

### Preparation

- Clone this repository to your local environment.
- You can `rm -rf .git` to recreate the repository for your own usage.
- Adjust the values at the top of docker-compose.yml if needed:

```yaml
x-var: &APP_IMAGE_TAG
  "my_app:1.0.0"
x-var: &RUBY_VERSION
  "2.7.0-slim-buster"
x-var: &PG_MAJOR
  12
x-var: &POSTGRES
  "postgres:12"
x-var: &NODE_MAJOR
  12
x-var: &YARN_VERSION
  1.21.1
x-var: &BUNDLER_VERSION
  2.1.2
```

Assumed that bundler is included within Ruby 2.7 or later. If you want to specify other version of bundler, uncomment the following as well:

- `BUNDLER_VERSION` in docker-compose.yml
- `&& gem install bundler:$BUNDLER_VERSION` in .dockerdev/Dockerfile.

### Install

### A. Quick Install

- `dip provision`

> Note: `--skip-listen` option is specified in the command to avoid the issue on macOS:
> ref: [Code is not reloaded in dev with Docker on OS X · Issue \#25186 · rails/rails](https://github.com/rails/rails/issues/25186). Perhaps you can remove the option for Linux environments.

### B. Custom Install

- `dip compose build` to build a container.
- `dip bundle install` to install gems for Rails.
- `dip bundle exec rails new . --webpacker <options as you like>`.
  - To macOS user: add `--skip-listen`
- `dip yarn install` to install yarn.
- Perform the following manually to activate local access via Docker:

```sh
dip sh -c "sed -i -e \"3i\   config.hosts << 'localhost'\" config/environments/development.rb"
dip sh -c "sed -i -e \"4i\   config.web_console.whitelisted_ips = '0.0.0.0/0'\" config/environments/development.rb"
```

- Then manually create databases:

```sh
dip sh -c "rails db:prepare 2> /dev/null; exit 0 && rails db:prepare"
dip sh -c "RAILS_ENV=test rails db:prepare"
```

> Known issue: currently, running `db:prepare` twice is needed for establishing the initial database connection.

--------

That's all. Now you can run `rails s` command via `dip rails s`. You don't need to add `bundle exec` any more.

### Misc

- You can see the available dip commands via `dip ls`.
- .vscode contains a minimum set of conf and extensions. You can discard.
- If you encounter any issues around caching, try checking bootsnap and spring gem.

## Webpacker + Bootstrap + font-awesome

You can configure Bootstrap 4 and font-awesome on Webpacker by running the following script:

```sh
dip yarn add bootstrap jquery popper.js @fortawesome/fontawesome-free

mkdir app/javascript/src
mkdir app/javascript/images

echo "@import '~bootstrap/scss/bootstrap';" > app/javascript/src/application.sass
echo "@import '~@fortawesome/fontawesome-free/scss/fontawesome';" >> app/javascript/src/application.sass

dip sh -c 'sed -i -e "s/stylesheet_link_tag/stylesheet_pack_tag/g" app/views/layouts/application.html.erb'

dip sh -c "sed -i -e \"10i\import 'bootstrap';\" app/javascript/packs/application.js"
dip sh -c "sed -i -e \"11i\import '../src/application.sass';\" app/javascript/packs/application.js"
dip sh -c "sed -i -e \"12i\import '@fortawesome/fontawesome-free/js/all';\" app/javascript/packs/application.js"
```

Then you can remove app/assets and deactivate Sprockets if unnecessary.


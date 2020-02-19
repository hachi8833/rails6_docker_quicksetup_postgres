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

## Quick install

- `dip provision`

> Note: `--skip-listen` option is specified in the command to avoid the issue on macOS:
> ref: [Code is not reloaded in dev with Docker on OS X · Issue \#25186 · rails/rails](https://github.com/rails/rails/issues/25186). Perhaps you can remove the option for Linux environments.

Known isue: currently, running `db:prepare` twice is needed for establishing the initial database connection.

--------

That's all. Now you can run `rails s` command via `dip rails s`. You don't need to add `bundle exec` any more.

### Misc

- You can see the available dip commands via `dip ls`.
- .vscode contains a minimum set of conf and extensions. You can discard.
- If you encounter any issues around caching, try checking bootsnap and spring gem.

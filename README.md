# Campfire

Campfire is web-based chat application. It supports many of the features you'd
expect, including:

- Multiple rooms, with access controls
- Direct messages
- File attachments with previews
- Search
- Notifications (via Web Push)
- @mentions
- API, with support for bot integrations

Campfire is single-tenant: any rooms designated "public" will be accessible by
all users in the system. To support entirely distinct groups of customers, you
would deploy multiple instances of the application.

## Installation
- install your ruby version manager of choice (rbenv or chruby)
- install the version of ruby in the [.ruby-version file](/.ruby-version)
- clone the repo and ensure that `ruby -v` returns the version in the file above
- you will probably need to run `brew install coreutils` or install [GNU Coreutils](https://www.gnu.org/software/coreutils/) some other way
- run `bin/bundle install` to install dependencies
- run `bin/setup` to setup the DB ([this is what it runs](/bin/setup))
- run `bin/dev` to start the server ([this is what it calls](/bin/dev))

> [!Note]
> In case you're unfamiliar with Ruby, we run `bin/{command}` to use the specific version of the command specified in this project's Gemfile rather than any other older versions of the library you might have installed. Generally, always run `bin/rails` or `bin/bundle`

To start the server at any time in development, run `bin/dev` again

## Deploying with Docker

Campfire's Docker image contains everything needed for a fully-functional,
single-machine deployment. This includes the web app, background jobs, caching,
file serving, and SSL.

To persist storage of the database and file attachments, map a volume to `/rails/storage`.

To configure additional features, you can set the following environment variables:

- `SSL_DOMAIN` - enable automatic SSL via Let's Encrypt for the given domain name
- `DISABLE_SSL` - alternatively, set `DISABLE_SSL` to serve over plain HTTP
- `VAPID_PUBLIC_KEY`/`VAPID_PRIVATE_KEY` - set these to a valid keypair to
  allow sending Web Push notifications. You can generate a new keypair by running
  `/script/admin/create-vapid-key`
- `SENTRY_DSN` - to enable error reporting to sentry in production, supply your
  DSN here

For example:

    docker build -t campfire .

    docker run \
      --publish 80:80 --publish 443:443 \
      --restart unless-stopped \
      --volume campfire:/rails/storage \
      --env SECRET_KEY_BASE=$YOUR_SECRET_KEY_BASE \
      --env VAPID_PUBLIC_KEY=$YOUR_PUBLIC_KEY \
      --env VAPID_PRIVATE_KEY=$YOUR_PRIVATE_KEY \
      --env SSL_DOMAIN=chat.example.com \
      campfire

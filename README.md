# Gollum::Auth - Authentication Middleware for Gollum Wiki

[![Gem Version](https://badge.fury.io/rb/gollum-auth.svg)](https://badge.fury.io/rb/gollum-auth)
[![Build Status](https://travis-ci.org/bjoernalbers/gollum-auth.svg?branch=master)](https://travis-ci.org/bjoernalbers/gollum-auth)

[gollum](https://github.com/gollum/gollum)
is an excellent Wiki-software.
But it does not include user authentication (on purpose).

[gollum-auth](https://github.com/bjoernalbers/gollum-auth) adds
[HTTP Basic Authentication](https://en.wikipedia.org/wiki/Basic_access_authentication)
to gollum so that only authenticated users have access to your wiki.
Optionally you can allow readonly-access for unauthenticated guests.
Also the current user's name and e-mail are passed to gollum (via session key
`gollum.author`) to see who changed what.
It works with Gollum 4 and Gollum 5.


## Installation

Add this line to your application's Gemfile:

```ruby
gem 'gollum-auth'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install gollum-auth


## Usage

You have to run
[Gollum via Rack](https://github.com/gollum/gollum/wiki/Gollum-via-Rack)
and use this middleware *before* gollum.
Here is a sample `config.ru`:

```ruby
#!/usr/bin/env ruby
require 'rubygems'
require 'gollum/auth' # Don't forget to load the gem!
require 'gollum/app'

# Define list of authorized users.
# Each user must have a username, password, name and email.
#
# Instead of a password you can also define a password_digest, which is the
# SHA-256 hash of a password.
#
# Example:
users = YAML.load %q{
---
- username: rick
  password: asdf754&1129-@lUZw
  name: Rick Sanchez
  email: rick@example.com
- username: morty
  password_digest: 5994471abb01112afcc18159f6cc74b4f511b99806da59b3caf5a9c173cacfc5
  name: Morty Smith
  email: morty@example.com
}

# Allow unauthenticated users to read the wiki (disabled by default).
options = { allow_unauthenticated_readonly: true }

# Allow only authenticated users to change the wiki.
# (NOTE: This must be loaded *before* Precious::App!)
use Gollum::Auth, users, options

# That's it. The rest is for gollum only.
gollum_path = File.expand_path(File.dirname(__FILE__)) # CHANGE THIS TO POINT TO YOUR OWN WIKI REPO
wiki_options = {:universal_toc => false}
Precious::App.set(:gollum_path, gollum_path)
Precious::App.set(:wiki_options, wiki_options)
run Precious::App
```


## Development

After checking out the repo, run `bin/setup` to install dependencies.
Then, run `rake spec` to run the tests. You can also run `bin/console` for an
interactive prompt that will allow you to experiment.


## Contributing

Bug reports and pull requests are welcome on the official
[GitHub Repository](https://github.com/bjoernalbers/gollum-auth).
This project is intended to be a safe, welcoming space for collaboration, and
contributors are expected to adhere to the
[Contributor Covenant](http://contributor-covenant.org) code of conduct.


## License

The gem is available as open source under the terms of the
[MIT License](LICENSE.txt).

Demonstration of a bundler bug
==============================

From a chat with Andre Arko, when bundler should prioritize gems in `vendor/cache` when resolving dependencies.

I think this project shows that this isn't happening as expected. Or its a chance for someone to show me how to get this to work.

The bug:

```
$ bundle
Fetching gem metadata from https://rubygems.org/.........
Resolving dependencies...
Bundler could not find compatible versions for gem "nokogiri":
In Gemfile:
bosh_vsphere_cpi (= 1.2797.0) ruby depends on
nokogiri (~> 1.5.10) ruby

nokogiri (1.6.5)
```

The gems
--------

In `vendor/cache` is `bosh_vsphere_cpi-1.2797.0.gem` - this is a patched/locally created version of the same gem that is on rubygems.org.

It has been modified to allow nokogiri 1.6.5.

```
$ gem spec vendor/cache/bosh_vsphere_cpi-1.2797.0.gem
...
- !ruby/object:Gem::Dependency
name: nokogiri
requirements:
- - ">="
- !ruby/object:Gem::Version
version: 1.5.10
- - "<"
- !ruby/object:Gem::Version
version: '2.0'
```

The version on rubygems is constrained to `~> 1.5.10` (see http://rubygems.org/gems/bosh_vsphere_cpi/versions/1.2797.0\)

`bundle install` *should* be using the `vendor/cache/bosh_vsphere_cpi-1.2797.0.gem` file when resolving dependencies. Instead, its seemingly using the `requirements:` from the remote `bosh_vsphere_cpi-1.2797.0.gem`.

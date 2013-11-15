Heroku et. al. buildpack that installs packages from a list of packages.

Installation
============

You probably want to compose this with other buildpack(s) using
heroku-buildpack-multi (https://github.com/ddollar/heroku-buildpack-multi):

Set your buildpack:

```
heroku config:set BUILDPACK_URL=https://github.com/ddollar/heroku-buildpack-multi
```

And add this to your `.buildpacks`, i.e.:

```
https://github.com/benalavi/buildpack-packages
https://github.com/heroku/heroku-buildpack-ruby
```

Then add a file called `.packages` that is simply a list of packages you want
to "install" (really it just unzips them and sets the PATH).

```
https://s3.amazonaws.com/buildpack-s3cmd/s3cmd-1.0.1.tar.gz
https://s3.amazonaws.com/buildpack-exiftool/exiftool-9.40.tar.gz
https://s3.amazonaws.com/buildpack-zip/zip-3.0.tar.gz
```

The above would install `s3cmd`, `exiftool`, and `zip` from S3 sources.

The script is very simple: it just downloads and uncompresses the given
packages into `/app/vendor` and then adds `/app/vendor/<package name>/bin` to
`PATH`.

The packages are expected to be compressed in a specific way: they should
uncompress to a folder named the same as the filename, with a folder called
`/bin` under it which will be put on `PATH`. For instance:

```
tar xvf zip-3.0.tar.gz
./zip-3.0/
./zip-3.0/bin/
./zip-3.0/bin/zip
./zip-3.0/bin/zipsplit
./zip-3.0/bin/zipnote
./zip-3.0/bin/zipcloak
```

Testing
=======

`ruby test/buildpack_test.rb`

There is also a `Vagrantfile` which will set up a VM with the build/test
environment ready to go:

```
vagrant up
vagrant ssh
cd /vagrant
ruby test/buildpack_test.rb
```

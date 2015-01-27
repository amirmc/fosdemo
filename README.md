[![Build Status](https://travis-ci.org/amirmc/fosdemo.svg?branch=master)](https://travis-ci.org/amirmc/fosdemo)

## Unikernel to serve 2048 game from a cubieboard.

These are the files needed to generate a [mirage unikernel][] from a
static site.  The content of the \*.ml files are taken from the
[`mirage_skeleton` repository][1], specifically, the `static_website` folder.

[Travis CI][] is used to build the unikernel for test purposes. The actual repo needs to be cloned and built on a cubieboard to work.

Since the config/dispatch files are lifted from another repo,
the following links should help to check if things have been updated upstream.

Files are from: https://github.com/mirage/mirage-skeleton/commit/1709bad5bac9484b8feb476aa6803e464d66057b

Latest versions are:
- [config.ml][]
- [dispatch.ml][]

[1]: https://github.com/mirage/mirage-skeleton

[mirage unikernel]: http://openmirage.org
[Travis CI]: https://travis-ci.org
[config.ml]: https://github.com/mirage/mirage-skeleton/commits/master/static_website/config.ml
[dispatch.ml]: https://github.com/mirage/mirage-skeleton/commits/master/static_website/dispatch.ml


***************************************

*Original README content*

Put static files into the htdocs/ directory.

For a Xen DHCP kernel, do:

$ env DHCP=true mirage configure --xen
$ make
$ make run

edit `www.xl` to add a VIF, e.g. via:

```
vif = ['bridge=xenbr0']
```

And then run the VM via `xl create -c www.xl`

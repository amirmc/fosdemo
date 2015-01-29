[![Build Status](https://travis-ci.org/amirmc/fosdemo.svg?branch=master)](https://travis-ci.org/amirmc/fosdemo)

## Unikernel to serve 2048 game from a cubieboard.

This repo contains the files needed to set up and serve the 2048 game from a
cubieboard. This repo needs to be cloned and built on a cubieboard for the
resulting unikernel to work.  The game itself is pure javascript, hence the
server only has to send the information once (the code for the game is taken
from a [prior tutorial][2048-repo]).

This repo assumes that you already have a cubieboard set up using the
[pre-built images][blobs].  From that point on, the information in
`cubie_setup.md` will help you set up the networking so that you can run the
unikernel and actually serve a page.  The files in `scripts/` will help make
it easier to set up your board in the same way (just `sudo cp` the relevant
files into the appropriate places). NB you might want to configure the board
to run `scripts/run_unikernel` on every boot, so that no manual input is
required.

#### Note about the *.ml files

These are the files needed to generate a [mirage unikernel][] from a
static site.  The content of the \*.ml files are taken from the
[`mirage_skeleton` repository][1], specifically, the `static_website` folder.

[Travis CI][] is used to build the unikernel for test purposes. 

Since the config/dispatch files are lifted from another repo,
the following links should help to check if things have been updated upstream.

Files are from: https://github.com/mirage/mirage-skeleton/commit/1709bad5bac9484b8feb476aa6803e464d66057b

Latest versions are:
- [config.ml][]
- [dispatch.ml][]

[1]: https://github.com/mirage/mirage-skeleton

[2048-repo]: https://github.com/ocamllabs/2048-tutorial/
[blobs]: http://blobs.openmirage.org

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

gtkglext for OSX
====
This is [gtkglext](http://projects.gnome.org/gtkglext/) from the Gnome project (`git HEAD`), with small fixes for compiling on Mac OSX. You'll want to have [Macports](http://www.macports.org/) installed.

Compiling gtkglext
----
Compiling gtkglext with Macports installed is very easy. Run the `./configure` script, and install anything it complains is missing through `ports`. Remember to tell `./configure` where Macports' libraries are installed.

    ./configure CPPFLAGS=-I/opt/local/include LDFLAGS=-L/opt/local/lib
    make -j3 && sudo make install

Compiling with gtkglext
----
The easiest way to compile against gtkglext is to use `pkg-config` (which you already have if you've installed gtkglext). However, if you installed gtkglext to `/usr/local` (the default), `pkg-config` won't find it.

    export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig
    pkg-config --cflags --libs gtkglext-1.0

Updates
----
I think gtkglext is dead, but if there is activity upstream, I'll do my best to merge it and make sure this repo is up to date.

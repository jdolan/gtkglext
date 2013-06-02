gtkglext for OSX
====
This is gtkglext from Gnome's Git repository, with small fixes for compiling on Mac OSX. You'll want to have Macports installed.

Compiling gtkglext
----
    ./configure CPPFLAGS=-I/opt/local/include LDFLAGS=-L/opt/local/lib
    make -j3 && sudo make install

Compiling with gtkglext
----
The easiest way to compile against gtkglext is to use `pkg-config` (which is available through Macports). However, if you installed gtkglext to `/usr/local` (default), `pkg-config` won't find it
    export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig
    pkg-config --cflags --libs gtkglext-1.0

Updates
----
I think gtkglext is dead, but if there is activity upstream, I'll do my best to merge it and make sure this repo is up to date.

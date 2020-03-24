For Packagers
=============

The first question most will ask is: how is this better than PKGBUILDs and makepkg?
alpmbuild improves on both: the build recipe and the build tool.

The Build Recipe
----------------

Let's compare two real-world GNU hello packages: one built with PKGBUILD/makepkg
and the other one built using alpmbuild spec/alpmbuild.

PKGBUILD
++++++++

.. code-block:: shell

   pkgname=hello
   pkgver=2.10
   pkgrel=1
   epoch=5
   pkgdesc="Prints Hello World and more"
   arch=(i686 x86_64)
   url='https://gnu.org'
   license=('GPL')

   source=(https://ftp.gnu.org/gnu/hello/$pkgname-$pkgver.tar.gz)
   md5sums=('6cd0ffea3884a4e79330338dcc2987d6')

   build() {
       cd "$pkgname-$pkgver"
       ./configure --prefix=/usr
       make
   }
   package() {
       cd "$pkgname-$pkgver"
       make DESTDIR="$pkgdir/" install
   }

hello.spec
++++++++++

.. code-block:: spec

   Name:    hello
   EVR:     5:2.10-1
   Summary: Hello world
   License: GPL
   URL:     https://gnu.org

   Source0: https://ftp.gnu.org/gnu/hello/%{name}-%{version}.tar.gz \
            with md5 6cd0ffea3884a4e79330338dcc2987d6

   %build
   ./configure --prefix=%{_prefix}
   make

   %install
   make DESTDIR=%{?buildroot} install

The first thing you may notice is the vastly different look.
A PKGBUILD looks like this...

.. code-block:: shell

   key=value

   section() {
      commands
   }

...while an alpmbuild spec looks like this:

.. code-block:: spec

   Key: value

   %section
   commands

alpmbuild specs utilize a syntax designed with simplicity and conciseness in mind,
whereas a PKGBUILD is just a fancy shell script sourced by makepkg to run certain
functions and to read certain variables. Many elements in PKGBUILDs can simply be replaced
by keywords or whitespace and linebreaks for a cleaner build recipe, and alpmbuild specs
do just that.

The EVR
+++++++

You may have noticed this line in the example alpmbuild spec:

.. code-block:: spec

   EVR: 5:2.10-1

This is a more concise way of writing:

.. code-block:: spec

   Epoch:   5
   Version: 2.10
   Release: 1

Sources
+++++++

This source specification may also stand out for being multiline:

.. code-block:: spec

   Source0: https://ftp.gnu.org/gnu/hello/%{name}-%{version}.tar.gz \
            with md5 6cd0ffea3884a4e79330338dcc2987d6

This makes it easier to match verification information to sources.

This is especially noticeable when you have multiple sources with many
ways of verification:

.. code-block:: shell

   source=('https://one.tarball'
           'https://two.tarball'
           'https://three.tarball'
           'https://four.tarball')

   md5sums=('6cd0ffea3884a4e79330338dcc2987d6'
            'SKIP'
            'SKIP'
            '7d0b18f1ea4d1d837e3c342c5b8135c7')
   sha1sums=('SKIP'
            '3747a903709e799860668d92eb738470d3fa9159'
            'SKIP'
            'SKIP')
   sha256sums=('SKIP'
               'SKIP'
               '10f194684834a149ced29f25f93a588c41f0fc40c930482520d0688617994b94'
               'SKIP')

versus:

.. code-block:: spec

   Source0: https://one.tarball   with md5    6cd0ffea3884a4e79330338dcc2987d6
   Source1: https://two.tarball   with sha1   3747a903709e799860668d92eb738470d3fa9159
   Source2: https://three.tarball with sha256 10f194684834a149ced29f25f93a588c41f0fc40c930482520d0688617994b94
   Source3: https://four.tarball  with md5    7d0b18f1ea4d1d837e3c342c5b8135c7

Obviously a convoluted example, but it does demonstrate how alpmbuild spec syntax can
be more concise than that of a PKGBUILD.

The Build Tools
---------------

alpmbuild prides itself one one thing more than its simple and concise build recipe
format: its error messages. alpmbuild does everything in its power to help you understand
where you went wrong in your package.

From warning about nonexistent packages and helping you find the one you're looking for...

::

   WARNING ==> Dependent package cbindge does not exist in repositories on line 11
               Recommends: cbindge
                           ^^^^^^^

               Did you mean to use cbindgen?

...to warning you about variables that didn't expand...

::

   WARNING ==> Macro not expanded on line 9: %{naame}
               Source0: https://ftp.gnu.org/gnu/hello/%{naame}-%{version}.tar.gz
                                                      ^^^^^^^^

               Did you mean to use %{name}?

...to warning you about other mistakes that you might make:

::

   ERROR ==> %{name} does not expand to a valid integer on line 11
             %if %{name} > 2

             %{name} -> hello

alpmbuild is also designed to keep your filesystem from becoming a clutter by
keeping all of its files in one place: `~/alpmbuild`.

.. container:: flex

   .. figure:: /img/indepth-small.png
      :target: ../reference/index.html
      :figclass: related-link

      :nimi:`Reference` |br|
      Read the reference for alpmbuild
      to understand everything it offers.
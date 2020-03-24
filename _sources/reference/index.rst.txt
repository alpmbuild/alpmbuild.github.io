Reference Manual
================

Key-Values
----------

Single-Value Fields
+++++++++++++++++++

Name
****

The name of your package. Corresponds to ``pkgname``.

.. code-block:: spec

   Name: my-package-name

Summary
*******

The summary of your package.

.. code-block:: spec

   Summary: A very awesome package

License
*******

The license of your package.

.. code-block:: spec

   License: GPL

URL
***

A URL to a human-readable homepage.

.. container:: flex

   .. container:: do

      .. code-block:: spec

         URL: https://phabricator.kde.org/source/discover/

      .. container:: caption

         :noblefir:`Do.`
         Link to a human-readable page, like a git forge.

   .. container:: dont

      .. code-block:: spec

         URL: https://download.kde.org/stable/plasma/%{version}/%{name}-%{version}.tar.xz

      .. container:: caption

         :iconred:`Don't.`
         Don't link to sources.

Version
*******

The version of the upstream item being packaged.

.. code-block:: spec

   Version: 2.10

Release
*******

The version of the package itself—increment this when you change the build recipe
to get a different package when there's not a new version of the thing being packaged.

.. code-block:: spec

   Release: 5

Epoch
*****

The epoch of your package. If the epoch for a package is higher than an older version,
it will be considered an upgrade, regardless of the version and release.

.. code-block:: spec

   Epoch:

Array Fields
++++++++++++

Array fields can be specified like this:

.. code-block:: spec

   Array: item
   Array: item

   # array is [item, item]

Or they can be specified like this:

.. code-block:: spec

   Array: item item

Requires
********

Indicates a dependency that needs to be installed in order to use the package.

.. code-block:: spec

   Requires: dependency

BuildRequires
*************

Indicates a dependency that needs to be installed in order to build the package.

.. code-block:: spec

   BuildRequires: dependency

CheckRequires
*************

Indicates a dependency that needs to be installed in order to run tests on the package.

.. code-block:: spec

   CheckRequires: dependency

Recommends
**********

Indicates a dependency that can be installed to provide more features for a package.
Do not use this to make parts of a package optional by breaking them with missing
dependencies. Instead, use split packages.

.. code-block:: spec

   Recommends: dependency

Provides
********

Indicates a virtual package that this package provides.

.. code-block:: spec

   Provides: capability

Conflicts
*********

Indicates a package that cannot be installed alongside this package.

.. code-block:: spec

   Conflicts: dependency

Replaces
********

Indicates that this package should replace another package. ``pacman`` will
attempt to replace the old one with this package when it becomes aware of it
during a system upgrade.

.. code-block:: spec

   Replaces: dependency

Groups
******

Groups that this package is part of.

.. code-block:: spec

   Groups: qt5

Backup
******

Configuration files to back up:

.. code-block:: spec

   Backup: /etc/pacman.conf

ExclusiveArch
*************

Indicates that this package can only be compiled on the given architectures.

.. code-block:: spec

   ExclusiveArch: x86_64

SourceX
*******

Indicates a source to build a package with.

.. code-block:: spec

   Source0: URL [with hashMethod hashValue] [with sig signatureFile] [with key pgpKey] [with keyserver keyserverURL]

PatchX
******

Indicates a patch to be applied on the package sources.

.. code-block:: spec

   Patch0: URL [with hashMethod hashValue] [with sig signatureFile] [with key pgpKey] [with keyserver keyserverURL]

Hash Methods
************

====== ======================================
Name
====== ======================================
sha1   A sha1 sum to verify the source with
sha224 A sha224 sum to verify the source with
sha256 A sha256 sum to verify the source with
sha384 A sha384 sum to verify the source with
sha512 A sha512 sum to verify the source with
md5    A md5 sum to verify the source with
====== ======================================

Directives
----------

Build Recipe
++++++++++++

Prepare
*******

Commands to prepare the package for compilation. Normally, you can omit this
directive and assume you're starting in the extracted root of Source0 if it
is a tarball and you don't need to apply patches.

.. code-block:: spec

   %prep
   commands

Build
*****

Commands to compile the package or otherwise produce the end result.

.. code-block:: spec

   %build
   commands

Install
*******

Commands to install the final package into the virtual filesystem for compressing.

.. code-block:: spec

   %install
   commands

Check
*****

Commands to run tests on the package.

.. code-block:: spec

   %check
   commands

Scriptlets
++++++++++

Small scripts that run at key points in transactions.

.. code-block:: spec

   %scriptlet
   commands

================= ========================================================= =========================================================
Scriptlet         Description                                               Arguments
================= ========================================================= =========================================================
``%pre_install``  Run before files are extracted when installing a package. ``$1``: package version.
``%post_install`` Run after files are extracted when installing a package.  ``$1``: package version.
``%pre_upgrade``  Run before files are extracted when updating a package.   ``$1``: old package version. ``$2``: new package version.
``%post_upgrade`` Run after files are extracted when updating a package.    ``$1``: old package version. ``$2``: new package version.
``%pre_remove``   Run before files are removed when removing a package.     ``$1``: package version.
``%post_remove``  Run after files are removed when removing a package.      ``$1``: package version.
================= ========================================================= =========================================================

Changelog
+++++++++

A changelog describing changes to a package.

.. code-block:: spec

   %changelog
   New in version 2.10
     - More hellos!
     - yadda yadda

Split Packages
++++++++++++++

Splitting packages.

Declaring a split package
*************************

.. code-block:: spec

   %package (-n) name

Declares a new split package called ``$PARENT_NAME-$NAME``. If ``-n`` is provided,
the name is used verbatim instead of appending it to the parent package's name.

Moving files to a split package
*******************************

.. code-block:: spec

   %files (-n) name

Specifies a files section for a split package. Like ``%package``, ``-n`` will cause
``name`` to be used verbatim. Files are listed based on their location in the parent
package, like so:

.. code-block:: spec

   %files translationfiles
   /usr/share/locale/*/LC_MESSAGES/hello.mo

Files are interpreted as regex.

The following macros are provided as shorthand for the GNU install directories:

===================== ===================
Macro                 Default Location
===================== ===================
``%{_prefix}``        ``/usr``
``%{_bindir}``        ``/usr/bin``
``%{_sbindir}``       ``/usr/sbin``
``%{_libexecdir}``    ``/usr/libexec``
``%{_datadir}``       ``/usr/share``
``%{_sysconfdir}``    ``/etc``
``%{_includedir}``    ``/usr/include``
``%{_mandir}``        ``/usr/share/man``
``%{_infodir}``       ``/usr/share/info``
===================== ===================

Scriptlets
**********

.. code-block:: spec

   %scriptlet (-n) name

Key-Values
**********

.. code-block:: spec

   %package extension
   Summary: A totally cool extension for %{name}
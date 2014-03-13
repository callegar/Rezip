Rezip
=====

A script to rewrite a zip archive, possibly changing the compression
level.

Introduction
------------

This script is meant to rewrite a zip archive, offering control on the
options used to read and rewrite the archive itself.

The intended use of the script is to change the way in which
components files are stored in the zip archive, and particularly their
compression level. Specifically, it is possible to rewrite an archive
so that its component are only stored, rather than deflated.
Alternatively, it is possible to rewrite an archive file to attain
maximum compression of its component files.

This functionality can be particularly useful when working with the
`git` revision control system.  Git, as many revision control systems,
uses a delta format to efficiently store multiple versions of the same
file. However, many software packages nowadays rely on zip based
storage formats from which it is impossible to extract deltas when
compression is used. In fact, a side effect of compression is to
spread a local change throughout a large part of the file. For
instance, this is the case with Libreoffice/Openoffice (that use the
ODF format), MS Office (when using the docx format), Java (using Jar),
SweetHome3D (using sh3d files) and more.

By using this script, compression can be removed when checking a file
into the revision control system and reintroduced on checkout.

Usage with git
--------------

In order to automatically invoke the script from git, one can rely on the
clean/smudge functionality.

For instance, one may introduce the following in the git configuration

```
[filter "opendocument"]
        clean = rezip -p ODF_UNCOMPRESS2
        smudge = rezip -p ODF_COMPRESS2
```

and then have a `.gitattributes` or a `.git/info/attributes` file with

```
*.odt filter=opendocument
*.ods filter=opendocument
*.odg filter=opendocument
*.odp filter=opendocument
*.odm filter=opendocument
```

Bugs and limitations
--------------------

The script is hackish. This is because zip has different ways of
storing file attributes (unix, fat, etc.). When unzipping and
rezipping on a given host the way in which the attributs are stored
may get changed to that corresponding to the particular host operating
system and setting.  Unfortunately, in zip/unzip there is no explicit
way to change this behavior.  To cope with this issue, some
workarounds are employed. Specifically, extended attributes are
dropped and an explicit umask setting is used.

Nonetheless, things might not be fully portable between different
linux or unix variants, and certainly are not portable to Windows.

One day, all this will be rewritten to use a proper zip library
allowing a finer control of attributes and such.
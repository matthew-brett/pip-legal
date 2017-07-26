# Distributing proprietary binaries with Pip / PyPI

Pip (see below) is a command line utility that downloads and installs software
distributions (packages) (see below) from the Python Package Index (PyPI)
website (see below) - a public archive of Python packages.

There are currently no license restrictions on the packages that a user can
upload to the PyPI archive.  It is therefore possible for the user to ask Pip to
install software using a copy-left license along with software using an
incompatible (for example, proprietary) license.

This document is to ask for advice as to when such an install represents a
violation of the GPL license terms.

Our immediate motivation is that we want to compile the Numpy Python package
using the Intel Fortran compiler, which would result in Numpy packages that
contain proprietary closed source binaries belonging to the Intel Fortran
run-time library.  We are worried that this will make it more likely that Pip
/ PyPI will cause a violation of the GPL.

## Some terms

### Package

A Python Package is an archive of files that can be installed on a user's
computer.  The Python interpreter can use (*import*) files from the Package.

### Pip

Pip is the standard command line program for installing Python packages onto a
user's computer.  Downloads of Python from Python.org will automatically
install Pip when installing Python.  Pip is the installation tool
[recommended](https://packaging.python.org/tutorials/installing-packages/#requirements-for-installing-packages)
by the [Python Packaging Authority](https://www.pypa.io) (see below).

### Python Packaging Authority - PyPA

The [Python Packaging Authority](https://www.pypa.io) is the official Python
body advising on the installation of Python packages.

### Python Package Index - PyPI

PyPI is the [Python Package Index](https://pypi.python.org/pypi).  See also
[the PyPI Wikipedia
article](https://en.wikipedia.org/wiki/Python_Package_Index).  PyPI is a
software repository, consisting of an archive of packages, and a web service
through which Pip, in particular, can search for and download packages.

## Relevant parts from the GPL

Our questions are about the situations under which we convey one or more
Python packages that together constitute a violation of the GPL.  Here are
some terms from the GPL and associated documents that are relevant to these
issues.

### GPL code run from an interpreter (like Python)

Python is an interpreter.  The Free Software Foundation holds that any
interpreted program that *uses* GPL code, must itself be released under the
GPL:

> Another similar and very common case is to provide libraries with the
> interpreter which are themselves interpreted. For instance, Perl comes with
> many Perl modules, and a Java implementation comes with many Java classes.
> These libraries and the programs that call them are always dynamically
> linked together.
>
> A consequence is that if you choose to use GPL'd Perl modules or Java
> classes in your program, you must release the program in a GPL-compatible
> way, regardless of the license used in the Perl or Java interpreter that the
> combined Perl or Java program will run on.

See [GPL and
interpreters](https://www.gnu.org/licenses/gpl-faq.html#IfInterpreterIsGPL).

### Distribute, propagate, convey

The GPL3 text has the following text:

> To "propagate" a work means to do anything with it that, without permission,
> would make you directly or secondarily liable for infringement under
> applicable copyright law, except executing it on a computer or modifying a
> private copy. Propagation includes copying, distribution (with or without
> modification), making available to the public, and in some countries other
> activities as well.
>
> To "convey" a work means any kind of propagation that enables other parties
> to make or receive copies. Mere interaction with a user through a computer
> network, with no transfer of a copy, is not conveying.

We'll use the term "convey" to cover the case of making Python packages
publicly available for distribution.

### Aggregates are allowed - what is an aggregate?

The GPL3 specifically allows "aggregates" of GPL and GPL-incompatible software
-- see the GPL [aggregate FAQ
entry](https://www.gnu.org/licenses/gpl-faq.html#MereAggregation). From that
entry: "An 'aggregate' consists of a number of separate programs, distributed
together".  The question then becomes what is a "separate program".  In the
FAQ: "If modules are designed to run linked together in a shared address
space, that almost surely means combining them into one program."

We will use the term *composite* for a collection of programs that should be
considered as one program, in this sense.  Therefore a collection of programs
may be an *aggregate* or a *composite*.

## Pip installs software from PyPI

The standard use of Pip is to install packages from the command line.

The user passes a specification of what packages they want to Pip. Call this
specification "the original specification".  Pip downloads packages matching
the original specification and, for each package P in the original
specification, Pip determines the specification of packages that P depends on
-- call this P-spec. For each package in P-spec, Pip checks whether the user
already has a package matching P-spec, and if not, it runs another download /
specification check on P-spec, continuing recursively until it has packages
matching the original specification and all missing dependent packages.
For example, here is a terminal session where the user asks Pip for the
[Scipy](https://pypi.python.org/pypi/scipy) package.  The Scipy package
depends on the [Numpy](https://pypi.python.org/pypi/numpy) package.  Pip
downloads both Scipy and Numpy, and installs them:

    $ pip install scipy
    Collecting scipy
    Downloading scipy-0.19.1-cp35-cp35m-macosx_10_6_intel.macosx_10_9_intel.macosx_10_9_x86_64.macosx_10_10_intel.macosx_10_10_x86_64.whl (16.1MB)
        100% |████████████████████████████████| 16.1MB 14.3MB/s 
    Collecting numpy>=1.8.2 (from scipy)
    Downloading numpy-1.13.1-cp35-cp35m-macosx_10_6_intel.macosx_10_9_intel.macosx_10_9_x86_64.macosx_10_10_intel.macosx_10_10_x86_64.whl (4.5MB)
        100% |████████████████████████████████| 4.5MB 6.1MB/s 
    Installing collected packages: numpy, scipy
    Successfully installed numpy-1.13.1 scipy-0.19.1

We will use the phrase "pip output" to refer to the files that get installed
on the user's machine after a single call to Pip.

A package is not required to specify all its dependencies - that is up to the
package author.  For example, package A might depend on package B in order to
run, but not specify package B in its P-spec.

## Examples to support the discussion

Imagine a Python package, hosted on PyPI, licensed under the GPL, called
`gpl_package`.

Imagine another Python package, hosted on PyPI, with a proprietary license,
called `proprietary_package`.  By "proprietary license" we mean to indicate
that the license is not [compatible with the
GPL](https://www.gnu.org/licenses/gpl-faq.html#WhatDoesCompatMean).

Imagine another Python package `composite_package` which contains the
following code:

    # Some code in "composite_package"
    import gpl_package
    import proprietary_package

    data = get_some_data()
    processed_data_1 = gpl_package.do_a_gpl_thing(data)
    processed_data_2 = proprietary_package.do_a_non_gpl_thing(processd_data_1)

`composite_package` has a GPL-compatible license, such as the MIT license.

## Questions

### Pip install of `gpl_package` with `proprietary package`

Consider the *pip output* from:

    pip install gpl_package proprietary_package

By the construction of our example, there is no code in `gpl_package` using
`proprietary_package`, and there is no code in `proprietary_package` using
`gpl_package`.

Our assumption is that the Pip output constitutes an *aggregate* of
`gpl_package` and `proprietary_package` (see above) and therefore, this
installation by Pip does not violate the GPL.  Is this assumption correct?

### Pip install of `composite_package` without dependency resolution

Imagine that PyPI hosts `composite_package`, and that the hosted version of
`composite_package` does not include `gpl_package` in its P-spec (see above).

In that circumstance:

    pip install composite_package

will install `composite_package` but not `gpl_package`. Here no GPL code has
been installed, although the Pip output cannot be used to do any useful work
until the user separately installs `gpl_package`.

We assume that the Pip install command above does not violate the GPL, because
it does not convey GPL software.  Is that correct?

### Pip install of `composite_package` with dependency resolution

Imagine that the version of `composite_package` on PyPI does specify
`gpl_package` in its P-spec. Now the Pip output of:

    pip install composite_package

includes the files for `gpl_package` and `proprietary_package` as well as
`composite_package`.  We assume that this should be considered a "composite"
install, where `composite_package`, `gpl_package` and `proprietary_package`
should all be considered part of a single larger program.  In this case, the
GPL license should be applied to the whole work, and as it cannot (by
construction) be applied to `proprietary_package`, we assume the output from
the Pip command above will violate the GPL.  Is this correct?

If it is correct, who should be held liable for this violation?

Is it the controllers of the PyPI site, that hosts the package in such a way
that such an output is possible?  Or the person who uploaded the
version of `composite_package` that required Pip (by default) to download
`gpl_package` with `composite_package` and `proprietary_package`?

### Two-step pip install of `composite_package` without dependency resolution

Return to the case where `composite_package` does not specify `gpl_package` in
its P-spec.  Now consider:

    # No installation of "gpl_package"
    pip install composite_package
    # Separately install "gpl_package"
    pip install gpl_package

If we are correct in our assumptions above, neither of the individual commands
/ Pip outputs would violate the GPL when run in isolation.  However, the two
commands together produce a combined output equivalent to the Pip install with
dependency resolution above.  Does the second of these two commands violate
the GPL?  Under what circumstances?  For example, what if these two commands
are run by two different users, or on two different days?

### Is the Intel Fortran run-time a system library?

Thus far we have discussed `proprietary_package` which has a license
incompatible with the GPL.

We assume that linking Numpy to the Intel Fortran run-time library will make
the license of the Numpy binary incompatible with the GPL (because of linked
routines compiled from closed-source code).  This section checks whether that
assumption is correct.

The GPL has a [system library
exception](https://www.gnu.org/licenses/gpl-faq.en.html#SystemLibraryException),
that allows us to convey GPL works with "system libraries" that cannot be
licensed under the GPL.  The [GPLv3
license](https://www.gnu.org/licenses/gpl-3.0.html) has the following text
defining system libraries:

> The “System Libraries” of an executable work include anything, other than
> the work as a whole, that (a) is included in the normal form of packaging a
> Major Component, but which is not part of that Major Component, and (b)
> serves only to enable use of the work with that Major Component, or to
> implement a Standard Interface for which an implementation is available to
> the public in source code form. A “Major Component”, in this context, means
> a major essential component (kernel, window system, and so on) of the
> specific operating system (if any) on which the executable work runs, or a
> compiler used to produce the work, or an object code interpreter used to run
> it.

[GPL2](https://www.gnu.org/licenses/old-licenses/gpl-2.0.en.html) has this:

> However, as a special exception, the source code distributed need not
> include anything that is normally distributed (in either source or binary
> form) with the major components (compiler, kernel, and so on) of the
> operating system on which the executable runs, unless that component itself
> accompanies the executable.

Our question is - does the closed-source run-time of Intel Fortran qualify as
a "system library" in these senses?  Specifically, if we statically link to
the Intel Fortran run-time, to build a Numpy binary package, or we dynamically
link to this run-time and ship the closed-source run-time library with the
Numpy binary package, does this qualify under the system library exception?
Is the answer the same for GPL2 and GPL3?

##################################
Building and uploading dipy wheels
##################################

We automate wheel building using this custom github repository that
builds on Github Actions (they provide x86 and x64 machines for Windows, Linux and Mac).

The Github Actions interface for the builds is
https://github.com/MacPython/dipy-wheels/actions

The driving github repository is
https://github.com/MacPython/dipy-wheels

How it works
============

The wheel-building repository:

* does a fresh build of any required C / C++ libraries;
* builds a dipy wheel, linking against these fresh builds;
* processes the wheel using delocate_ (OSX) or auditwheel_ ``repair``
  (Manylinux1_).  ``delocate`` and ``auditwheel`` copy the required dynamic
  libraries into the wheel and relinks the extension modules against the
  copied libraries;
* uploads the built wheels to a container - The container is at the following link https://anaconda.org/scipy-wheels-nightly/ for weekly uploads
and https://anaconda.org/multibuild-wheels-staging for staging wheels to PyPI.

The resulting wheels are therefore self-contained and do not need any external
dynamic libraries apart from those provided as standard by OSX / Linux as
defined by the manylinux1 standard.


Triggering a build
==================

You will likely want to edit the ``build-wheels.yml`` and ``build-wheels-windows.yml`` files to
specify the ``BUILD_COMMIT`` before triggering a build - see below.

You will need write permission to the github repository to trigger new builds
on the travis-ci interface.  Contact us on the mailing list if you need this.

You can trigger a build by:

* making a commit to the ``dipy-wheels`` repository (e.g. with ``git commit
  --allow-empty``); or
* clicking on the circular arrow icon towards the top right of the travis-ci
  page, to rerun the previous build.

In general, it is better to trigger a build with a commit, because this makes
a new set of build products and logs, keeping the old ones for reference.
Keeping the old build logs helps us keep track of previous problems and
successful builds.

Which dipy commit does the repository build?
============================================

The ``dipy-wheels`` repository will build the commit specified in the
``BUILD_COMMIT`` at the top of the ``.build-wheels.yml`` and ``build-wheels-windows.yml`` files.
This can be any naming of a commit, including branch name, tag name or commit
hash.

Uploading the built wheels to PyPI
==================================

* release container visible at
  https://anaconda.org/multibuild-wheels-staging

Be careful, this link points to a container on a distributed content delivery
network.  It can take up to 15 minutes for the new wheel file to get updated
into the containers at the links above.

When the wheels are updated, you can download them to your machine manually,
and then upload them manually to PyPI, or by using twine_.  You can also use a
script for doing this, housed at :
https://github.com/MacPython/terryfy/blob/master/wheel-uploader

For the ``wheel-uploader`` script, you'll need twine and `beautiful soup 4
<bs4>`_.

You will typically have a directory on your machine where you store wheels,
called a `wheelhouse`.   The typical call for `wheel-uploader` would then
be something like::

    VERSION=0.13.0
    CDN_URL=https://3f23b170c54c2533c070-1c8a9b3114517dc5fe17b7c3f8c63a43.ssl.cf2.rackcdn.com
    wheel-uploader -u $CDN_URL -s -v -w ~/wheelhouse -t all dipy $VERSION

where:

* ``-u`` gives the URL from which to fetch the wheels, here the https address,
  for some extra security;
* ``-s`` causes twine to sign the wheels with your GPG key;
* ``-v`` means give verbose messages;
* ``-w ~/wheelhouse`` means download the wheels from to the local directory
  ``~/wheelhouse``.

``dipy`` is the root name of the wheel(s) to download / upload, and ``0.13.0``
is the version to download / upload.

In order to upload the wheels, you will need something like this
in your ``~/.pypirc`` file::

    [distutils]
    index-servers =
        pypi

    [pypi]
    username:your_user_name
    password:your_password

So, in this case, `wheel-uploader` will download all wheels starting with
`dipy-0.13.0-` from the URL in ``$CDN_URL`` above to ``~/wheelhouse``, then
upload them to PyPI.

Of course, you will need permissions to upload to PyPI, for this to work.

.. _manylinux1: https://www.python.org/dev/peps/pep-0513
.. _twine: https://pypi.python.org/pypi/twine
.. _bs4: https://pypi.python.org/pypi/beautifulsoup4
.. _delocate: https://pypi.python.org/pypi/delocate
.. _auditwheel: https://pypi.python.org/pypi/auditwheel

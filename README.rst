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
and then upload them manually to PyPI, or by using twine_.

When the wheels are updated, you can download them manually or using the `download-wheels.py` script`,
and then upload them manually or using twine. The download-wheels.py script is run as follows:

$ python3 download-wheels.py 1.2.0 -w <path_to_wheelhouse>
$ twine upload <path_to_wheelhouse>/*.whl

Where 1.2.0 is the release version, The wheelhouse argument is optional and defaults to ./release/installers.

You will need beautifulsoup4 and urllib3 installed in order to run download-wheels.py and permissions in order to upload to PyPI.

.. _manylinux1: https://www.python.org/dev/peps/pep-0513
.. _twine: https://pypi.python.org/pypi/twine
.. _bs4: https://pypi.python.org/pypi/beautifulsoup4
.. _delocate: https://pypi.python.org/pypi/delocate
.. _auditwheel: https://pypi.python.org/pypi/auditwheel

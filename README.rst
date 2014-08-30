##################
dipy wheel builder
##################

Repository to build OSX wheels for dipy.

The usual behavior of the repo is to build the wheel corresponding to the most
recent git tag (see `git-closest-tag
<https://github.com/MacPython/terryfy/blob/master/git-closest-tag>`_.

To build a particular commit instead:

* comment out the line ``global: LATEST_TAG=1`` in the ``.travis.yml`` file.
* Update the dipy submodule the with commit you want to build:

    * cd dipy && git pull && git checkout DESIRED_COMMIT
    * cd .. && git add dipy
    * git commit

where "DESIRED_COMMIT" is a something specifycing the git revision to build
* Check minimum numpy versions to build against in ``.travis.yml`` file.  You
  need to build against the earliest numpy that dipy is compatible with; see
  `forward, backward numpy compatibility
  <http://stackoverflow.com/questions/17709641/valueerror-numpy-dtype-has-the-wrong-size-try-recompiling/18369312#18369312>`_
The wheels get uploaded to a `rackspace container
<http://a365fff413fe338398b6-1c8a9b3114517dc5fe17b7c3f8c63a43.r19.cf2.rackcdn.com>`_
to which I have the API key.  The API key is encrypted to this specific repo
in the ``.travis.yml`` file, so the upload won't work for you from another
account.  Either contact me to get set up, or use another upload service such as
github - see for example Jonathan Helmus' `sckit-image wheels builder
<https://github.com/jjhelmus/scikit-image-ci-wheel-builder>`_

I got the rackspace API key from Olivier Grisel - we might be able to share
this account across projects - again - please contact me or Olivier if you'd
like this to happen.

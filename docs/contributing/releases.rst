:title: Releases
:description: Details the Deis release process. Deis releases.
:keywords: deis, release, process, build, tag

.. _releases:

Releases
========

When the maintainers create a new Deis release, here are the steps involved:


GitHub Issues
-------------

- create next `milestone`_
- roll unfinished issues (if there are any) into next milestone
- close current release milestone


Other Repos
-----------

- TBD


Chef Repo
---------

- merge master into release branch locally
    * ``git checkout release``
    * ``git merge master``
- change chef attributes to latest tags in attributes/default.rb
    * default.deis.gitosis.revision
    * default.deis.controller.revision
    * (default.deis.build.revision for slugbuilder stays at master)
- ``knife cookbook metadata .`` to update metadata.json
- commit and push the opdemand/deis-cookbook release and tag
    * ``git commit -a -m 'Updated for vX.Y.Z release.'``
    * ``git push origin release``
    * ``git tag vX.Y.Z``
    * ``git push --tags origin vX.Y.Z``
- update opscode community cookbook
    * cd to parent dir of deis-cookbook
    * ``cp -pr deis-cookbook /tmp/deis && cd /tmp``
    * ``tar cvfz deis-cookbook-vX.Y.Z.tar.gz --exclude='deis/.*' deis``
    * log in to community.opscode.com and upload tarball
- switch master to upcoming release
    * ``git checkout master``
    * change cookbook revisions in metadata.rb to *next* version
    * ``git commit -a -m 'Switch master to vA.B.C.'`` (next version)
    * ``git push origin master``


Deis Repo
---------

- merge master into release branch locally
    * ``git checkout release``
    * ``git merge master``
- update berksfile with new release
    * ensure Berksfile is pointing to opscode community cookbook
    * ``berks update && berks install`` to update Berksfile.lock
- commit and push the opdemand/deis release and tag
    * ``git commit -a -m 'Updated for vX.Y.Z release.'``
    * ``git push origin release``
    * ``git tag vX.Y.Z``
    * ``git push --tags origin vX.Y.Z``
- publish CLI to pypi.python.org
    - ``cd client && python setup.py sdist upload``
    - use testpypi.python.org first to ensure there aren't any problems
- create CLI binaries for Windows and Mac OS X
    - ``pip install pyinstaller && make client_binary``
    - use Mac OS X 10.8 to avoid LLVM errors for others
    - upload to aws-eng S3 bucket, set as publically downloadable
- switch master to upcoming release
    * ``git checkout master``
    * update __version__ fields in Python packages to *next* version
    * switch from opscode community cookbook back to github cookbook
    * ``berks update && berks install`` to update Berksfile.lock
    * ``git commit -a -m 'Switch master to vA.B.C.'`` (next version)
    * ``git push origin master``

Docs
----
- create release notes docs
    - follow format of previous `release notes`_
    - summarize all work done
    - what's next and future directions
- publish docs to http://docs.deis.io (deis.readthedocs.org)
- visit readthedocs.org admin and add this release to published builds
    - Rebuild *all* published versions so their "Versions" index is updated
- publish docs to pythonhosted.org/deis
    - from the project root, run ``make -C docs clean zipfile``
    - zipfile will be at *docs/docs.zip*
    - log in and use web form at https://pypi.python.org/pypi/deis/
      to upload zipfile


.. _`milestone`: https://github.com/opdemand/deis/issues/milestones
.. _`release notes`: https://github.com/opdemand/deis/releases

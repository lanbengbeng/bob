.. _manpage-bob-status:

bob-status
==========

.. only:: not man

   Name
   ----

   bob-status - Show SCM status

Synopsis
--------

::

    bob status [-h] [--develop | --release] [-c CONFIGFILE] [-D DEFINES]
               [--attic] [-r] [--sandbox | --no-sandbox] [--show-clean]
               [--show-overrides] [-v]
               [packages [packages ...]]

Description
-----------

Show SCM status of existing workspaces.

This command is intended to list the status of all checkouts, especially
modifications, in a project. It can be used in two different modes. If the
command is invoked without a package then all workspaces of the project are
scanned for SCM changes. The other mode, when called with at least one
package, shows only the status of the given package(s). In this case the
``--develop`` (default) and ``--release`` options may be used to select the
workspaces. Adding ``-r`` scans the dependencies of the given package(s) too.

Options
-------

``--attic``
    Consider attic directories too.

    Normally SCMs that were moved to the attic next to the workspace are not
    checked. By using this option the attic directories that belong to a
    workspace are scanned too.

    .. attention::

       Bob versions before 0.15 did not store the locations of the attic
       directories in the project. If the project was initially built with an
       older version then the attic listing will probably be incomplete. In this
       case Bob will print a warning that you should not rely on the output.

.. include:: std-opt-cfg.rst

``--develop``
    Use developer mode. This is the default.

``--no-sandbox``
    Disable sandboxing

``-r, --recursive``
    Recursively display dependencies. Only direct dependencies are considered,
    i.e. packages that are named in the ``depends`` section of the recipe.
    Consumed tool- and sandbox- packages that were forwarded are thus not
    visited.

``--release``
    Use release mode.

``--sandbox``
    Enable sandboxing

``--show-clean``
    Show the status of a checkout even if unmodified. This includes
    ``--show-overrides``. See :ref:`manpage-bob-status-verbosity` below.

``--show-overrides``
    Show checkouts that have active :ref:`configuration-config-scmOverrides`
    (``O``) even if the SCM is unchanged. Override information is always
    displayed if a checkout is shown but a ``STATUS`` line is normally only
    emitted if the SCM was modified. See :ref:`manpage-bob-status-verbosity`
    below.

``-v, --verbose``
    Increase verbosity. May be specified multiple times.  See
    :ref:`manpage-bob-status-verbosity` below.

Output
------

The output of bob status is one line per SCM checkout. Only existing workspaces
are considered. A status line consists of one ore more status codes followed by
the SCM path.

Status codes can be interpreted as follows:

- ``?`` = missing information. The workspace was created by an older version of
  Bob that did not store enough information. The state of the SCM directory
  cannot be determined.

  .. attention::
     The directory can be in any state with respect to the original checkout.
     You should manually inspect it because it might contain unsaved changes.

- ``A`` = attic. The recipe was changed for this checkout or the checkout
  is not referenced anymore in the recipe. The SCM path will be moved to
  the attic the next time the package is built.
- ``C`` = collision. The SCM was not yet checked out but there is an existing
  file/directory at the checkout location. The next run of the checkout step
  will fail.
- ``E`` = error. The SCM state could not be determined. The checkout is
  probably messed up. Use ``-v`` to get additional information about the error.
- ``M`` = modified. Some sources have been modified and not yet committed to SCM.
- ``N`` = new. The SCM was not yet checked out. There might still be an old
  checkout at the same location. This is indicated by a simultaneous ``A`` flag
  (see above).
- ``O`` = overridden. This SCM is affected by a
  :ref:`configuration-config-scmOverrides`. Pass ``--show-overrides`` to force
  the output of a ``STATUS`` line if the SCM is otherwise unmodified.
- ``S`` = switched. The commit/tag/branch/URL is different from the recipe.
- ``U`` = unpushed commmits. Git only. Some commits were made locally to the
  configured branch but they have not yet been pushed to the remote.
- ``u`` = unpushed branch(es). Git only. At least one branch exists with commits
  that have not been pushed to a remote.

The command shows the status of the current checkout. If the recipe was changed and
the next build would move a checkout to the attic then the current information still
refers to the existing checkout.

.. _manpage-bob-status-verbosity:

Verbosity
---------

By default only modified checkouts are shown. If there is not enough
information available to determine the SCM status or if there was an error
while retrieving then the checkout is show too.

By adding one or more ``-v`` options the display of less important information
can be enabled. The following levels are available:

- ``-v`` shows a detailed description of each listed flag. In particular the
  modified files of an SCM are listed.
- ``-vv`` additionally shows the status of SCMs that are not modified, .e.g.
  without flags or only ``A``, ``N`` and ``O``. If you want to display this
  information without also enabling the detailed descriptions (see above) use
  ``--show-clean`` or ``--show-overrides``.
- ``-vvv`` additionally shows skipped workspaces that do not exist.


Athena Virtual Environment
==========================

Athena Virtual Environment, ``ave``, is a set of bash functions whose purpose is to ease the CMT pain of the day-to-day job of a ``Athena`` developers and users.

``ave`` started as a wrapper around the old AtlasLogin toolbox and is now a wrapper around AtlasSetup.

There are 4 major bash functions:

 - ``ave-login``

 - ``ave-workarea``

 - ``ave-make``

 - ``ave-brmake``

# usage example

one has first to source the ``ave_rc.sh`` file (can be done in your
.login) like so:

```sh  
$ . ave_rc.sh
```

then one can use the ``ave-*`` functions like so:

```sh
$ mkdir -p /tmp/example
$ cd /tmp/example
$ ave-login 16.0.2.3
# retrieve a few packages locally
$ pkgco.py JetRec JetEventTPCnv
# create a 'meta' package named WorkArea which depends on all
# local packages and compile all packages
$ ave-workarea
$ pwd
/tmp/example/WorkArea/run
$ athena jobo.py
```

# ave-login

``ave-login`` setups the shell to use a given Athena release or nightly.
It calls ``asetup`` under the hood with a pre-defined set of
(hopefully) sensible options.


# ave-workarea

``ave-workarea`` creates a "meta" ``WorkArea`` package which depends
on all locally checked-out packages.
This is achieved by calling the ``abootstrap-wkarea.py`` script from
the ``Tools/PyUtils`` CMT package.

As a bonus, ``abootstrap-wkarea.py`` will set things up as to put all
the directories holding binaries (e.g. ``i686-slc5-gcc43-opt``) under
``$TMPDIR/$LOGIN/aboot-<project-name>``
This should help saving precious AFS space.

Once this ``WorkArea`` package is created, a ``cmt bro make`` is
issued (via ``ave-brmake``) to compile all local packages.

# ave-make

``ave-make`` runs the usual ``cmt make`` command with a few ``ave``
default options taken from ``${AVE_MAKE_DEFAULT_OPTS}``

By default, this will run ``make`` in ``quiet`` and ``pedantic`` modes
and in parallel (usually the number of physical cores)

# ave-brmake

same as ``ave-make`` but broadcasted via ``cmt``


# ave-reco-tests

runs ``manyrecotests.sh`` in a sub-directory of the current directory.
it also runs the ``PyUtils.AthFile`` unit tests.

# ave-reco-trf-tests

runs the ``Reco_trf.py`` command in a sub-directory for ``q120``,
``q121`` and ``q122``.

# ave-valgrind-mem

wrapper command around ``athena.py`` to launch ``valgrind`` (with
sensible suppressions files tailored for Athena, ROOT and Gaudi) with
the ``memory`` checker tool (``memcheck``)
a logfile named ``valgrind.mem.log`` will be created in the current
directory.

example:

```sh
$ ave-valgrind-mem -c 'rec.doESD = False' jobo.py
```

# ave-valgrind-cpu

wrapper command around ``athena.py`` to launch ``valgrind`` (with
sensible suppressions files tailored for Athena, ROOT and Gaudi) with
the ``cpu`` profiler tool (``callgrind``)
a logfile named ``valgrind.cpu.log`` will be created in the current
directory.

example:

```sh
$ ave-valgrind-cpu -c 'rec.doESD = False' jobo.py
```


# ave-valgrind-helgrind

same as for the other commands, but running the thread data race
conditions checker tool.


# ave-voms-proxy-init

helper function to setup the grid credentials (in a subshell so the athena
environment won't be borked) so TagCollector command-line tools will work 
correctly.

# ave-gpt-cpu-profile

wrapper command around ``athena.py`` to launch ``GPerfTools``with the
``cpu`` profiler tool.
a logfile named ``ave-gpt-$PID.cpu.profile`` will be created in the
current directory and can be analysed with ``ave-gpt-analyze``.

example:
```sh
$ ave-gpt-cpu-profile AthenaRootComps/test_athena_ntuple_dumper.py
$ ave-gpt-analyze ave-gpt-42666.cpu.profile
```

# ave-gpt-mem-profile

same as for ``ave-gpt-cpu-profile`` but running the memory profiler.


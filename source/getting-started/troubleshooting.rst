===============
Troubleshooting
===============

Here are the most common problems encountered when installing and using Exegol.

.. contents::
    :local:

Unable to connect to Docker
===========================

There are multiple checks to do to make sure Docker works properly.

.. tabs::

    .. tab:: Docker service

        The Docker service must installed up and running.

        - For Windows users: Docker Desktop for Windows must be up and running.
        - For macOS users: Docker Desktop for Mac (or `OrbStack <https://orbstack.dev/>`_) must be up and running.

    .. tab:: Docker permissions

        Make sure the Docker permissions are consistent with the Exegol permissions.
        For instance, if you need ``sudo`` rights to use Docker, you'll most likely need ``sudo`` to run Exegol smoothly.

        See :ref:`the Exegol install guidance<install_exegol_privileges>` to use exegol correctly with sudo.

    .. tab:: Docker socket

        The following command can be used to see the docker socket that is used by default: ``docker context ls``.

        * For `OrbStack <https://orbstack.dev/>`_ users (on macOS), the "orb socket" must be used.
        * For Docker Desktop users (macOS/Windows), the "Docker desktop socket" must be used.
        * For Linux users, the default socket should work.

        Switching context can be done with ``docker context use <context>``.
        For instance, switching from a Docker Desktop to OrbStack could be done with ``docker context use orbstack``.

    .. tab:: Symbolic link

        The following symbolic link must exist ``/var/run/docker.sock`` and point to the correct socket. Below is an example of what it should look like.

        .. code-block::

            (Host) ~ $ ls -la /var/run/docker.sock
            lrwxr-xr-x  1 root  daemon  38 Jul 28 09:02 /var/run/docker.sock -> /Users/someuser/.orbstack/run/docker.sock

        If the link does not exist, it could be created with the following command ``ln -sf /Users/someuser/.orbstack/run/docker.sock /var/run/docker.sock``. This is an example for `OrbStack <https://orbstack.dev/>`_. The command must be adapted to the user's context.

How do I get X11 to work on a non-Linux host?
=============================================

X11, or X Window System, is a graphical windowing system that provides a framework for creating and managing graphical user interfaces (GUIs) in Unix-like operating systems.

X11 sharing between an Exegol container and a host allows a graphical application running within the container to display its GUI on the host's X11 server. This means you can run graphical applications in Exegol containers and have them appear as if they were running directly on the host machine. It enables the execution of GUI-based applications in isolated containers while interacting with them through the host's graphical interface.

For macOS users, XQuartz is needed. It's listed in the :ref:`install requirements <install_requirements>`.

.. note::

    Exegol's wrapper automatically starts XQuartz on macOS hosts when needed. But if for some reason it gets manually closed by the users while a container is running, X11 sharing will not work. Restarting the container with ``exegol restart <container>`` will restart XQuartz automatically if needed.

How to fix ``Docker download error: unauthorized: authentication required``
===========================================================================

Docker image downloads may be time-sensitive. In the case of dual-boot systems, it is common to experience time lags of a few hours.

To correct the problem, check that your computer's date and time are correct.

How to fix CRLF errors on Windows
=================================

If you have cloned the Exegol repository on Windows, you may encounter errors when launching your container, for example:

.. code-block::

    /.exegol/entrypoint.sh: line 3: trap: SIGTERM
    : invalid signal specification
    /.exegol/entrypoint.sh: line 4: $'\r': command not found
    /.exegol/entrypoint.sh: line 5: syntax error near unexpected token $'{\r''
    /.exegol/entrypoint.sh: line 5: function exegol_init() {

This is caused by the automatic addition of CRLF linefeed by Windows to ensure compatibility.
To correct this problem, simply disable this feature on the Exegol repository and reload the file of the repository:

.. code-block:: bash

    cd ./Exegol
    git config core.autocrlf false
    git rm -rf --cached .
    git reset --hard HEAD


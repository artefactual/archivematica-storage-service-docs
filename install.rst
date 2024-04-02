.. _install:

=======================================
Manually installing the Storage Service
=======================================

This page describes how to install the Storage Service manually; however, most
users install from Ansible or packages. Please see the :ref:`Archivematica
general installation <archivematica:installation>` documentation for
instructions on installing both Archivematica and the Storage Service from
Ansible or packages.

This app is designed to run in Python 3.9.

In Ubuntu 22.04 you can install Python 3.9 using the `deadsnakes ppa`_ ::

    sudo apt-get update
    sudo apt-get install -y software-properties-common
    sudo add-apt-repository -y ppa:deadsnakes/ppa
    sudo apt-get install python3.9

Install dependencies (if they are not already installed) ::

    sudo apt-get install -y \
        build-essential \
        gcc \
        git \
        gettext \
        gnupg1 \
        libffi-dev \
        libldap2-dev \
        libmysqlclient-dev \
        libsasl2-dev \
        libssl-dev \
        libxml2-dev \
        libxslt1-dev \
        libz-dev \
        nginx \
        p7zip-full \
        python3.9-dev \
        python3.9-venv \
        rclone \
        rng-tools-debian \
        rsync \
        unar

Create and configure the virtualenv, and add source code. ::

    python3.9 -m venv $HOME/ss-venv
    source $HOME/ss-venv/bin/activate
    git clone https://github.com/artefactual/archivematica-storage-service.git --branch stable/0.22.x
    cd archivematica-storage-service
    pip install -r requirements.txt

You should now have a virtual environment, with its own isolated Python
interpreter, and a Django project in the ``storage_service`` subdirectory.

If you plan on doing development work on the Storage Service, it's recommended
you use `the Docker Compose based development environment`_.

:ref:`Back to the top <install>`

.. _`deadsnakes ppa`: https://launchpad.net/~deadsnakes/+archive/ubuntu/ppa
.. _`the Docker Compose based development environment`: https://github.com/artefactual/archivematica/blob/qa/1.x/hack/README.md

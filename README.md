Ruby for DeployDock - Docker images
========================================

This repository contains the source for building various versions of
the Ruby application as a reproducible Docker image using
[source-to-image](https://github.com/Meros-io/source-to-image).
Users can choose between RHEL and CentOS based builder images.
The resulting image can be run using [Docker](http://docker.io).


Versions
---------------
Ruby versions currently provided are:
* ruby-2.0
* ruby-2.2

RHEL versions currently supported are:
* RHEL7

CentOS versions currently supported are:
* CentOS7


Installation
---------------
To build a Ruby image, choose either the CentOS or RHEL based image:
*  **RHEL based image**

    To build a RHEL based Ruby image, you need to run the build on a properly
    subscribed RHEL machine.

    ```
    $ git clone https://github.com/Meros-io/sti-ruby.git
    $ cd sti-ruby
    $ make build TARGET=rhel7 VERSION=2.0
    ```

*  **CentOS based image**

    This image is available on DockerHub. To download it run:

    ```
    $ docker pull deploydock/ruby-20-centos7
    ```

    To build a Ruby image from scratch run:

    ```
    $ git clone https://github.com/Meros-io/sti-ruby.git
    $ cd sti-ruby
    $ make build VERSION=2.0
    ```

**Notice: By omitting the `VERSION` parameter, the build/test action will be performed
on all provided versions of Ruby.**


Usage
---------------------
To build a simple [ruby-sample-app](https://github.com/Meros-io/sti-ruby/tree/master/2.0/test/puma-test-app) application
using standalone [S2I](https://github.com/Meros-io/source-to-image) and then run the
resulting image with [Docker](http://docker.io) execute:

*  **For RHEL based image**
    ```
    $ s2i build https://github.com/Meros-io/sti-ruby.git --context-dir=2.0/test/puma-test-app/ deploydock/ruby-20-rhel7 ruby-sample-app
    $ docker run -p 8080:8080 ruby-sample-app
    ```

*  **For CentOS based image**
    ```
    $ s2i build https://github.com/Meros-io/sti-ruby.git --context-dir=2.0/test/puma-test-app/ deploydock/ruby-20-centos7 ruby-sample-app
    $ docker run -p 8080:8080 ruby-sample-app
    ```

**Accessing the application:**
```
$ curl 127.0.0.1:8080
```


Test
---------------------
This repository also provides a [S2I](https://github.com/Meros-io/source-to-image) test framework,
which launches tests to check functionality of a simple Ruby application built on top of the sti-ruby image.

Users can choose between testing a Ruby test application based on a RHEL or CentOS image.

*  **RHEL based image**

    To test a RHEL7-based Ruby-2.0 image, you need to run the test on a properly
    subscribed RHEL machine.

    ```
    $ cd sti-ruby
    $ make test TARGET=rhel7 VERSION=2.0
    ```

*  **CentOS based image**

    ```
    $ cd sti-ruby
    $ make test VERSION=2.0
    ```

**Notice: By omitting the `VERSION` parameter, the build/test action will be performed
on all the provided versions of Ruby.**


Repository organization
------------------------
* **`<ruby-version>`**

    * **Dockerfile**

        CentOS based Dockerfile.

    * **Dockerfile.rhel7**

        RHEL based Dockerfile. In order to perform build or test actions on this
        Dockerfile you need to run the action on a properly subscribed RHEL machine.

    * **`s2i/bin/`**

        This folder contains scripts that are run by [S2I](https://github.com/Meros-io/source-to-image):

        *   **assemble**

            Used to install the sources into the location where the application
            will be run and prepare the application for deployment (eg. installing
            modules using bundler, etc.)

        *   **run**

            This script is responsible for running the application by using the
            application web server.

        *   **usage***

            This script prints the usage of this image.

    * **`contrib/`**

        This folder contains a file with commonly used modules.

    * **`test/`**

        This folder contains a [S2I](https://github.com/Meros-io/source-to-image)
        test framework with a simple Rack server.

        * **`puma-test-app/`**

            Simple Puma web server used for testing purposes by the [S2I](https://github.com/Meros-io/source-to-image) test framework.

        * **`rack-test-app/`**

            Simple Rack web server used for testing purposes by the [S2I](https://github.com/Meros-io/source-to-image) test framework.

        * **run**

            Script that runs the [S2I](https://github.com/Meros-io/source-to-image) test framework.

* **`hack/`**

    Folder containing scripts which are responsible for build and test actions performed by the `Makefile`.


Image name structure
------------------------
##### Structure: deploydock/1-2-3

1. Platform name (lowercase) - ruby
2. Platform version(without dots) - 20
3. Base builder image - centos7/rhel7

Examples: `deploydock/ruby-20-centos7`, `deploydock/ruby-20-rhel7`


Environment variables
---------------------

To set these environment variables, you can place them as a key value pair into a `.sti/environment`
file inside your source code repository.

* **RACK_ENV**

    This variable specifies the environment where the Ruby application will be deployed (unless overwritten) - `production`, `development`, `test`.
    Each level has different behaviors in terms of logging verbosity, error pages, ruby gem installation, etc.

    **Note**: Application assets will be compiled only if the `RACK_ENV` is set to `production`

* **DISABLE_ASSET_COMPILATION**

    This variable indicates that the asset compilation process will be skipped. Since this only takes place
    when the application is run in the `production` environment, it should only be used when assets are already compiled.

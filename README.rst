PyBDSF
======

PyBDSF (the Python **B**\ lob **D**\ etection and **S**\ ource **F**\ inder)
is a tool designed to decompose radio interferometry images into
sources and make available their properties for further use. PyBDSF can
decompose an image into a set of Gaussians, shapelets, or wavelets as
well as calculate spectral indices and polarization properties of
sources and measure the psf variation across an image. PyBDSF uses an
interactive environment based on CASA that will be familiar to most
radio astronomers. Additionally, PyBDSF may also be used in Python
scripts.

The documentation is currently hosted at https://pybdsf.readthedocs.io

Installation
------------
Installation can be done in a number of ways. In order of preference (read:
ease of use):

**Recommended: Using Conda**

The easiest way to install PyBDSF with all dependencies is using conda/mamba::

    conda install -c conda-forge pybdsf

This installs prebuilt binaries with all required system dependencies (Boost, compilers, etc.)
and avoids compilation issues, especially on RHEL/CentOS systems.

**Using pip (prebuilt wheels)**

* Install the latest release from PyPI::

    pip install bdsf

  .. note:: The interactive shell ``pybdsf`` is no longer installed by default.
    To install it you have to specify the extra ``[ishell]``. For example::

      pip install bdsf[ishell]

**Building from source**

* Install the ``master`` branch from the PyBDSF git repository::

    pip install git+https://github.com/lofar-astron/PyBDSF.git

  Or install a specific revision or release, for example ``v1.9.3``::

    pip install git+https://github.com/lofar-astron/PyBDSF.git@v1.9.3

* Install from a local source tree, e.g. after you cloned the git repository::

    pip install .

  or (to install the interactive shell as well)::

    pip install .[ishell]

If you get the error::

  RuntimeError: module compiled against API version 0xf but this version of numpy is 0xd

then please update ``numpy`` with ``pip install -U numpy``.

.. attention:: It is *not* recommend to use ``python setup.py install``. It is
  deprecated, and we do *not* support it.

**Building on RHEL 8 / CentOS 8 / Rocky Linux 8**

Building from source on RHEL8-based systems requires special care due to GLIBC compatibility.

**Option 1: Use prebuilt binaries (STRONGLY RECOMMENDED)**

The easiest and most reliable approach is to use the prebuilt conda package::

    conda create -n pybdsf python=3.10
    conda activate pybdsf
    conda install -c conda-forge pybdsf

This completely avoids compilation and GLIBC compatibility issues.

**Option 2: Build from source with system compilers**

If you must build from source, use system compilers with conda-provided Boost libraries::

    # Install system compilers and cmake
    sudo yum install gcc gcc-c++ gcc-gfortran cmake3

    # Create conda environment WITHOUT conda compilers
    conda create -n pybdsf-build python=3.10
    conda activate pybdsf-build
    # Install libraries AND build dependencies (but NOT compilers)
    conda install -c conda-forge boost-cpp numpy scipy astropy ninja cmake
    pip install scikit-build setuptools wheel setuptools_scm
    
    # Explicitly tell CMake to use system compilers
    export CC=/usr/bin/gcc
    export CXX=/usr/bin/g++
    export FC=/usr/bin/gfortran
    export CMAKE_PREFIX_PATH="${CONDA_PREFIX}/lib/cmake:${CMAKE_PREFIX_PATH}"
    export CMAKE_ARGS="-DCMAKE_C_COMPILER=/usr/bin/gcc -DCMAKE_CXX_COMPILER=/usr/bin/g++ -DCMAKE_Fortran_COMPILER=/usr/bin/gfortran"
    
    # Build without build isolation
    pip install --no-build-isolation -v .

.. warning:: Do NOT install conda compiler packages (``gxx_linux-64``, ``gfortran_linux-64``,
  ``gcc_linux-64``) on RHEL8. They require GLIBC 2.14+ which RHEL8 doesn't have.
  The CMAKE_ARGS environment variable ensures CMake uses system compilers instead of
  conda's, even if conda tools are on the PATH.

This approach uses conda's Boost libraries (including boost-numpy) while using RHEL8's
system compilers that are compatible with the system GLIBC. Scikit-learn and other
pure Python dependencies will be installed automatically as wheels.

Alternatively, if you have a previously built installation and only want to update
Python code (without recompiling C++/Fortran), you can directly copy the updated
``.py`` files to your site-packages directory or reuse a previously built wheel.

**External requirements for building from source (non-conda)**

Ubuntu/Debian packages (or similar packages in another Linux distribution):

* ``gfortran``
* ``libboost-python-dev``
* ``libboost-numpy-dev`` (Only if boost > 1.63)
* ``python-setuptools``

RHEL/CentOS packages::

    sudo yum install gcc-gfortran boost-devel python3-devel

.. note:: On RHEL8, ``boost-numpy`` may not be available in system packages.
  Using conda (as described above) is strongly recommended.

Also, a working ``numpy`` installation is required. At runtime, you will need ``scipy`` and either ``pyfits`` and ``pywcs`` or ``python-casacore`` or ``astropy``.

If you install as a user not using conda, use ``pip install --user``.
Make sure to use similar versions for gcc, g++ and gfortran
(use update-alternatives if multiple versions of gcc/g++/gfortran are present on the system).
In this case, the script ``pybdsf`` is installed in ``~/.local/bin``, so you might want to add that to your ``$PATH``.

**Installation on MacOS / OSX**

Installation on MacOS is more involved. You will need the packages mentioned above, for example
installed with Homebrew. You will need to tell the build system to use the same compiler for
Fortran as for C++. In case of problems, see https://github.com/lofar-astron/PyBDSF/issues/104#issuecomment-509267088
for some possible steps to try.

.. image:: https://github.com/lofar-astron/PyBDSF/actions/workflows/ci.yml/badge.svg?branch=master
    :target: https://github.com/lofar-astron/PyBDSF/actions/workflows/ci.yml

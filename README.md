[![Build Status](https://travis-ci.org/sns-chops/multiphonon.svg?branch=master)](https://travis-ci.org/sns-chops/multiphonon) 
[![Coverage Status](https://coveralls.io/repos/github/sns-chops/multiphonon/badge.svg?branch=master)](https://coveralls.io/github/sns-chops/multiphonon?branch=master)

# multiphonon
This is a rewrite of multiphonon (getDOS) code that was originally authored by 
Max Kresch during the [DANSE project](http://danse.us/) and
was then revised by several authors including Brandon, Chen, Jennifer, and Dipanshu
(their work were recorded as branches in this repo). 
It fixes some problems in the earlier versions of getDOS code and implemented new features.
The original requirements of this project is captured at [this ticket](https://github.com/sns-chops/multiphonon/issues/32).
And details of the features of this code can be found below.

Main functionality: **Compute phonon Density of States (DOS) from powder Inelastic Neutron Scattering (INS) spectrum**

Inelastic neutron scattering (INS) are important probes of dynamics in materials [2].  Powder spectra measured by inelastic neutron spectrometers provide information such as phonon density of states (DOS), a fundamental property of a solid.
The measured spectra, however, are two-dimensional in axes of Q (momentum transfer) and E (energy transfer).
This code converts a S(Q,E) INS spectrum to DOS.

## Features

* The full I(Q,E) dynamic range measured is utilized, leading to better statistics
* Can be used with data measured at multiple incident energies to progressivley obtain better density of states
* Intermediate results are saved for further investigation
* Handle inputs in nxs and nxspe files for sample and empty can measurements

## Installation

**NOTE:** SNS users can skip this step and use this software package directly at SNS analysis cluster (see below). 

The multiphonon package can be installed using conda (python 2) on a recent 64bit linux (ubuntu/fedora/centos) distribution:

      $ conda config --add channels conda-forge
      $ conda config --add channels mcvine
      $ conda install multiphonon

Information on dependencies of this code can be found at [the conda recipe](/conda-recipe/meta.yaml)

The multiphonon package converts a I(Q,E) spectrum to DOS.
Experimental data obtained from direct-geometry inelastic neutron spectrometers first need to
be converted to I(Q,E) spectra.
Those experimental data are usually in the form of nexus files
(including event mode nexus files, histogram mode nexus files,
and nxspe files) and can be reduced to I(Q,E) spectra using [mantid](http://mantidproject.org).
Convenient methods and example notebooks exist in multiphonon for this preprocessing step.
For those methods and notebooks to work, the following installation is needed

      $ conda config --add channels mantid
      $ conda install mantid-framework

Finally, example jupyter notebooks need [ipywe](https://github.com/scikit-beam/ipywe)

      $ conda config --add channels neutrons
      $ conda install ipywe

### Why not PyPI installation
Some dependencies of multiphonon contain sophistated C++ libraries so it is much easier to rely
on the conda environment for installation. However, multiphonon package itself is a pure python package,
and can be installed by

    $ python setup.py install
    
if all dependecies are already installed.

## Usage

### GetDOS at SNS analysis cluster using jupyter 
For SNS users, GetDOS can be performed at SNS analysis cluster through the jupyter.sns.gov website.
* First log into SNS analysis cluster using thinlinc. The instructions for that can be found at https://analysis.sns.gov/
* Inside the thinlinc session of analysis.sns.gov, open a browser window and connect to https://jupyter.sns.gov, and login with your XCAMS/UCAMS account credentials
* Follow the tutorial videos below:
  - [For the first time users, a setup is necessary](https://www.youtube.com/embed/5XOX8RdHBnQ?start=0&end=36&version=3)
  - [Run GetDOS2](https://www.youtube.com/embed/uTEEyifpG-k) (this works for SNS data only)

### GetDOS using local installation
For any user, GetDOS can be performed with a local installation of multiphonon.
Examples and instructions can be found [here](/examples)

## Community guidelines

**How to contribute**

Please clone the repository, make changes and make a pull request.

**How to report issues**

Please use [the github issues](https://github.com/sns-chops/multiphonon/issues) to report issues or bug reports.

**Support**

Please either use [the github issues](https://github.com/sns-chops/multiphonon/issues) to ask for support, or contact the authors directly using email.


## Algorithm
The core algorithm is an iterative procedure:
- Start with the input SQE and an initial guess of DOS
- Calculate SQE of multiphonon scattering (MP)
- Calculate SQE of multiple scattering (MS) using C\_ms and multiphonon scattering SQE
- Subtract MS and MP SQE from the experimental SQE to obtain an approximation of the single-phonon SQE
- Compute a new DOS from the single-phonon SQE
- Compare the new DOS to the initial guess and calculate the difference
- If difference is large, continue the iteration using the new DOS as the initial guess. Otherwise, output the new DOS and stop iteration

For more details of the basic principles of SQE->DOS conversion, please refer to Appendix of [1] and Section 6.5 "Calculation of Multiphonon Scattering" of [2].

## API doc
See http://sns-chops.github.io/multiphonon/

## Run tests
Tests are run automatically at [travis CI](https://travis-ci.org/sns-chops/multiphonon/builds). 

To manually run the minimal test suite, install multiphonon, clone this repository, and run

    $ cd multiphonon/tests && py.test -m "not needs_mantid and not needs_ipywe"

If mantid and ipywe were installed, you can run all tests by

    $ py.test

## References
[1] Max Kresch et al., https://journals.aps.org/prb/abstract/10.1103/PhysRevB.75.104301

[2] Brent Fultz et al., http://www.cacr.caltech.edu/projects/danse/doc/Inelastic_Book.pdf

## History
[Releases](https://github.com/sns-chops/multiphonon/releases)

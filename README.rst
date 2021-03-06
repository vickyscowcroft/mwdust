mwdust
======

**Dust in 3D in the Milky Way**

.. image:: https://travis-ci.org/jobovy/mwdust.svg?branch=master
   :target: http://travis-ci.org/jobovy/mwdust

.. image:: http://img.shields.io/pypi/v/mwdust.svg
   :target: https://pypi.python.org/pypi/mwdust/

.. image:: http://img.shields.io/badge/license-New%20BSD-brightgreen.svg
   :target: https://github.com/jobovy/mwdust/blob/master/LICENSE

Installation
-------------

Please define an environment variable ``DUST_DIR`` before installing
the code; this is a directory that will contain the dust data.

Standard python setup.py build/install

Either

``sudo python setup.py install``

or 

``python setup.py install --prefix=/some/directory/``

The installation automatically downloads the relevant dust data. You
might have to define an environment variable ``SUDO_USER`` if not
installing with sudo.

Dust Data
---------

The code can automatically download all of the necessary data (use the
installation option ``--no-downloads`` to turn this off). These data
are put in subdirectories of a directory ``DUST_DIR``, with roughly
the following lay-out::

    $DUST_DIR/
       combined15/
          dust-map-3d.h5
       green15/
          dust-map-3d.h5
       maps/
          SFD_dust_4096_ngp.fits
	  SFD_dust_4096_sgp.fits
       marshall06/
          ReadMe
	  table1.dat
       sale14/
          Amap.dat
          ReadMe

The data for the Drimmel et al. (2003) map is installed in the code
directory, because it is not very large.

Usage
------

All of the maps can be initialized similar to::

    import mwdust
    drimmel= mwdust.Drimmel03(filter='2MASS H')
    sfd= mwdust.SFD(filter='2MASS H')

which sets up a Drimmel et al. (2003) map for the *H*-band filter. The
maps can be evaluate for a given Galactic longitude *l*, Galactic
latitude *b*, and an array (or scalar) of distances *D*::

	 drimmel(60.,0.,3.) # inputs are (l,b,D)
	 array([ 0.42794197])
	 drimmel(30.,3.,numpy.array([1.,2.,3.,10.]))
	 array([ 0.24911393,  0.53050198,  0.78045575,  1.14657304])
	 # SFD is just the constant SFD extinction
	 sfd(30.,3.,numpy.array([1.,2.,3.]))
	 array([ 1.19977335,  1.19977335,  1.19977335])

and they can be plotted as::

    drimmel.plot(55.,0.5) # inputs are (l,b)

(plot not shown). 

Supported bandpasses
---------------------

Currently only a few filters are supported; if no filter is supplied,
*E(B-V)* is returned on the SFD scale if the object is initialized
with ``sf10=True`` (which tells the code to use re-scalings from
`Schlafly & Finkbeiner 2011
<http://adsabs.harvard.edu/abs/2011ApJ...737..103S>`__). ``sf10=True``
is the default initialization for every map, so be careful in
interpreting the raw *E(B-V)* that come out of the code. *Only use*
``sf10=False`` *when you have an extinction map in true E(B-V)*, **not**
*SFD E(B-V)*. No map currently included in this package is in this
situation, so using ``sf10=False`` is never recommended.

To check what bandpasses are supported on the ``sf10=True`` scale do
(these are all the bandpasses from Table 6 in `Schlafly & Finkbeiner
2011 <http://adsabs.harvard.edu/abs/2011ApJ...737..103S>`__)::

   from mwdust.util import extCurves  
   extCurves.avebvsf.keys()

which gives::

      ['Stromgren u',
       'Stromgren v',
       'ACS clear',
       'CTIO R',
       'CTIO V',
       'CTIO U',
       'CTIO I',
       ...]

To check the bandpasses that are supported on the old SFD scale (``sf10=False``), do::

   numpy.array(extCurves.avebv.keys())[True-numpy.isnan(extCurves.avebv.values())]

which gives::

      array(['CTIO R', 'CTIO V', 'CTIO U', 'CTIO I', 'CTIO B', 'DSS-II i',
       'DSS-II g', 'WISE-1', 'WISE-2', 'DSS-II r', 'UKIRT H', 'UKIRT J',
       'UKIRT K', 'IRAC-1', 'IRAC-2', 'IRAC-3', 'IRAC-4', '2MASS H',
       'SDSS r', 'SDSS u', 'SDSS z', 'SDSS g', 'SDSS i', '2MASS Ks',
       '2MASS J'], 
      dtype='|S14'

Acknowledgements
-----------------

When making use of this code in a publication, please cite `Bovy et
al. (2015a) <http://arxiv.org/abs/1509.06751>`__. Also cite the relevant papers for the dust
map that you use:

* **mwdust.SFD**: `Schlegel et al. (1998) <http://adsabs.harvard.edu/abs/1998ApJ...500..525S>`__

* **mwdust.Drimmel03**: `Drimmel et al. (2003) <http://adsabs.harvard.edu/abs/2003A%26A...409..205D>`__

* **mwdust.Marshall06**: `Marshall et al. (2006) <http://adsabs.harvard.edu/abs/2006A%26A...453..635M>`__

* **mwdust.Sale14**: `Sale et al. (2014) <http://adsabs.harvard.edu/abs/2014MNRAS.443.2907S>`__

* **mwdust.Green15**: `Green et al. (2015) <http://adsabs.harvard.edu/abs/2015arXiv150701005G>`__

* **mwdust.Combined15**: Combination of `Marshall et al. (2006) <http://adsabs.harvard.edu/abs/2006A%26A...453..635M>`__, `Green et al. (2015) <http://adsabs.harvard.edu/abs/2015arXiv150701005G>`__, and `Drimmel et al. (2003) <http://adsabs.harvard.edu/abs/2003A%26A...409..205D>`__; see `Bovy et al. (2015a) <http://adsabs.harvard.edu/abs/2015arXiv150906751B>`__

* **mwdust.Zero**: `Bovy et al. (2015b) <http://adsabs.harvard.edu/abs/2015arXiv150905796B>`__ :smirk:

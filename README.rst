=====
PyPDS
=====

- Overview_
- Documentation_
- Installation_
- Examples_
- CommandLineTools_

This python package is suitable for working with Planetary Data System (PDS) data products in your own projects.

Several command line tools are also available for viewing labels and image contents as well as image conversion.

.. _Overview:

Overview
========

PyPDS is a python interface to `Planetary Data System <http://pds.jpl.nasa.gov/>`_ (PDS) data products.
For more information please visit the `PyPDS Wiki <http://wiki.github.com/RyanBalfanz/PyPDS/>`_ and the Sphinx documentation included in the source.

.. _Documentation:

Documentation
=============

The `latest documentation <http://readthedocs.org/docs/pypds/en/latest/>`_ is available on Read the Docs.

.. _Installation:

Installation
============

#. Install PIL

#. Install PyPDS as a development (local) branch. It is highly recommended you install PyPDS as a local branch.
   This will allow you to take advantage of updates and bugfixes to the code by simply downloading the latest
   code updates from the git repo. This way you do not have to uninstall then reinstall the module to incorporate
   the latest code.

* First clone the github repository

		git clone https://github.com/RayEspiritu/PyPDS.git

	* To install as a local branch:

        pip install -e <path/to/your/local/darttk/package/directory containing setup.py>

        NOTE: This is not a path to setup.py! This is the path to the root directory containing setup.py!
        As an example, if you are user werkzeug and you want to use your copy of the PyPDS package
        that you put in /homes/werkzeug/git/pypds/PyPDS, you would do:
        pip install -e /homes/werkzeug/git/pypds/PyPDS

If you want to contribute, consider creating a new branch within this fork and submitting a pull request.

.. _Examples:

Examples
========

Please note, test.py will still fail with errors because I have not fully ported all the code to Python 3.
Enough of the code has been ported to let you do the following.

>>> pds_parser = Parser()
>>> label = pds_parser.parse(open_pds(input_file))

Disregard the followingg until I completely port the code to Python 3
First, you may want to run tests.py to verify that things seem to be working. That files was based on the interacitve examples below. There are more examples in the documentation.

Working with labels
-------------------

Before we may do anything, let's import some modules.

>>> import pprint
>>> from pds.core.common import open_pds
>>> from pds.core.parser import Parser

If we are to get all the labels from a PDS file, we'll need a Parser object.

>>> parser = Parser()

Now we use use it's ``parse`` method on a file. The file should first be opened with ``open_pds``.

>>> labels = parser.parse(open_pds("../test_data/pds.img"))

The return object is just a dictionary. Let's take a look.

>>> pprint.pprint(labels.keys()) # Inspect the top-level labels.
['IMAGE_HISTOGRAM',
 'IMAGE_MAP_PROJECTION_CATALOG',
 'FILE_RECORDS',
 'IMAGE_ID',
 'TARGET_NAME',
 'SOURCE_IMAGE_ID',
 'IMAGE',
 'LABEL_RECORDS',
 'SPACECRAFT_NAME',
 'NOTE',
 'RECORD_TYPE',
 'CCSD3ZF0000100000001NJPL3IF0PDS200000001',
 '^IMAGE',
 'INSTRUMENT_NAME',
 '^IMAGE_HISTOGRAM',
 'DATA_SET_ID',
 'RECORD_BYTES']

Some of the labels contain other labels, which are also dictionary objects.

>>> pprint.pprint(labels["IMAGE"]) # Inspect the image labels.
{'CHECKSUM': '12054227',
 'LINES': '320',
 'LINE_SAMPLES': '306',
 'SAMPLE_BITS': '8',
 'SAMPLE_BIT_MASK': '2#11111111#',
 'SAMPLE_TYPE': 'UNSIGNED_INTEGER'}

Working with the image
----------------------

PyPDS also takes care of the details about creating image objects. Behind the scenes all images are instances of PIL's Image class.

To get an image from a PDS file, create an ``ImageExtractor`` object and use its ``extract`` method. Don't forget to first open the PDS file.

>>> from pds.core.common import open_pds
>>> from pds.imageextractor import ImageExtractor
>>> ie = ImageExtractor()
>>> img, labels = ie.extract(open_pds("../test_data/pds.img"))

The ``extract`` method first parses the file, then creates the image. Since it goes to the trouble of doing so anyway, it provides the label as a freebie, along with the image.

Here, ``img`` is an instance of PIL's Image class. Do whatever you want to it.

>>> print (img.mode, img.size)
('L', (306, 320))
>>> img.show() # Open the image in the default viewer.
>>> img.save("pds.img.jpeg") # Write the image to disk in JPEG format.

Verify that the returned image has the proper dimensions.

>>> imageSize = map(int, \
... (labels["IMAGE"]["LINE_SAMPLES"], \
... labels["IMAGE"]["LINES"])) # Save the image dimensions with integers.
>>> tuple(imageSize) == img.size # The built-in map returns a list, but Image.size is a tuple.
True

By the way, an ``Image`` has a ``show`` method which should happily open the image in your default viewer.

.. _CommandLineTools:

Command Line Tools
==================

Sometimes you might not want to interact with PDS files programmatically. PyPDS also comes with several tools which are handy for working with PDS files at the command line.

Each has several options. For detailed information use ``--help``.

pds-convert.py
	Convert images to the specified format. 
	
pds-image.py
	Like pds-convert.py but dump to standard output.
	
pds-labels.py	
	Dump the labels to standard output.
	
pds-view.py
	View an image contained in a PDS file in the default viewer.
	

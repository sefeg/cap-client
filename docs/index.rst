.. cap-client documentation master file, created by
sphinx-quickstart on Fri Aug 25 14:52:59 2017.
You can adapt this file completely to your liking, but it should at least
contain the root `toctree` directive.

Welcome to cap-client's documentation!
======================================

Installation
======

This is to detail the cap-client installation with the package manager pip. Please refer to the `pip installation instructions <https://pip.pypa.io/en/stable/installing/>`_ if you do not yet have the package management system installed.

To install cap-client:

.. code-block:: console

	$ pip install cap-client


Usage
=====

This guide assumes that you have successfully installed cap-client package already. If not, please follow the  :doc:`installation` instructions first.

The cap-client is designed to communicate with a CERN Analysis Preservation (CAP) server instance. You can use the `CERN Production server <https://analysispreservation.cern.ch/>`_, which comes with the most stable version of CAP. All further descriptions and references link to this production instance. 

In order to communicate with the server, you first need to generate a personal access token `here <https://analysispreservation.cern.ch/profile/applications>`_.

Afterwards, set the required environment variables for the cap-client. If you like to select a CAP server different than the production instance, you can change the URL here.

.. code-block:: console

	$ export CAP_SERVER_URL=https://analysispreservation.cern.ch/
	$ export CAP_ACCESS_TOKEN=<your generated access token from server>

Note that CAP_ACCESS_TOKEN can also be passed as an argument in the command line interface.

.. _types:

Retrieve current user information
----------

.. code-block:: console

    $ cap-client me

    {
        "collaborations": [
                    "ATLAS",
                    "LHCb",
                    "CMS",
                    "ALICE"
                ],
        "id": 1,
        "email": "user@cern.ch"
    }


Get all types of analyses available
----------

.. code-block:: console

	$ cap-client types

	Available types:
		atlas-workflows
		alice-analysis
		atlas-analysis
		lhcb
		cms-questionnaire
		cms-analysis


Get analysis with given PID
----------

You can retrieve analysis details if you have read or write access to the analysis.

You need to specify: 
 
	--pid  the PID of an analysis.

.. code-block:: console

	$ cap-client get --pid/-p <existing pid>

	E.g $ cap-client get --pid 883090d3c1784aeabe9e23412a81239e

	{   
		"pid": "883090d3c1784aeabe9e23412a81239e",
	    "basic_info": {
	        "abstract": "Example abstract",
	        "people_info": [
	            {
	                "name": "John doe"
	            },
	            {
	                "name": "J doe"
	            }
	        ],
	        "analysis_number": "test"
	    }
	}


Create analysis
----------

You can create a new analysis by specifing:

	--file  a file with the json data corresponding to the analysis JSON Schema.
	--type  the type of analysis you want to create. Refer to the :ref:`analysis type section <types>` to see an overview of all the options.

You can create analyses that correspond to your affiliation with a collaboration. For example: if you are a member of the CMS collaboration, you can create analyses with type cms-analysis or cms-questionnaire.

.. code-block:: console

	$ cap-client create --file/-f <file with JSON data>  --type/-t <type of analysis> 

	E.g $ cap-client create --file test.json --type cms-analysis

	{
		'status': 200, 
		'data': {   
			"pid": "883090d3c1784aeabe9e23412a81239e",
		    "basic_info": {
		        "abstract": "Example abstract",
		        "people_info": [
		            {
		                "name": "John doe"
		            },
		            {
		                "name": "J doe"
		            }
		        ],
		        "analysis_number": "test"
		    }
		}
	}
		


Delete analysis
----------

You can delete an existing analysis by specifing:

	--pid  the PID as a parameter.

	.. code-block:: console

		$ cap-client delete --pid/-p <existing pid>

		E.g $ cap-client delete --pid 4c734c3ae5b14a2195e3b17dc9ff63ae

		Server response:
			{
				'status': 204, 
			 	'data': None
			}


Patch analysis
----------

You can patch an existing analysis by specifing

	--pid  the PID as a parameter.
	--file  a file with the changes in `JSON patch format <http://jsonpatch.com/>`_.

Example changes in JSON patch format:

	.. code-block:: javascript
		[ { "op": "add", "path": "/basic_info/analysis_number", "value": "22" }]

.. code-block:: console

	$ cap-client patch --pid/-p <existing pid> --file/-f <file with JSON data>

	E.g $ cap-client patch --pid 883090d3c1784aeabe9e23412a81239e --file test.json
	
	{
	 	'status': 200, 
	 	'data': {   
			"pid": "883090d3c1784aeabe9e23412a81239e",
		    "basic_info": {
		        "abstract": "Example abstract",
		        "people_info": [
		            {
		                "name": "John doe"
		            },
		            {
		                "name": "J doe"
		            }
		        ],
		        "analysis_number": "test"
		    }
		}
	}


Publish analysis
----------------

You can publish an existing analysis by specifing

    --pid  the PID of the analysis you want to share.

.. code-block:: console

    $ cap-client publish --pid/-p <existing pid>

    E.g cap-client publish -p a85dc95be2a04d70973de8a39065fc8d

    {
        "updated": "2018-02-16T13:25:45.999349+00:00",
        "metadata": {
            "$schema": "https://ioanniss-mbp.dyndns.cern.ch:5000/schemas/deposits/records/lhcb-v0.0.1.json",
            "user_analysis": {
                "basic_script": {
                    "source": {
                        "preserved": true
                    }
                },
                "gitlab_link": {
                    "source": {
                        "preserved": true
                    }
                }
            },
            "general_title": "LHCb Analysis 16/02/2018, 14:21:00",
            "control_number": "2"
        },
        "pid": "a85dc95be2a04d70973de8a39065fc8d",
        "created": "2018-02-16T13:21:10.968585+00:00"
    }


Clone analysis
----------------

You can clone an existing analysis by specifing

    --pid  the PID of the analysis you want to share.

.. code-block:: console

    $ cap-client clone --pid/-p <existing pid>

    E.g cap-client clone -p 046ee5e83d084241a7b0767432e9682c

    {
        "updated": "2018-02-16T13:32:23.749106+00:00",
        "metadata": {
            "$schema": "https://ioanniss-mbp.dyndns.cern.ch:5000/schemas/deposits/records/atlas-analysis-v0.0.1.json",
            "general_title": "ATLAS Analysis 16/02/2018, 14:31:20",
            "basic_info": {
                "analysis_title": "testing",
                "glance_id": "123"
            }
        },
        "pid": "046ee5e83d084241a7b0767432e9682c",
        "created": "2018-02-16T13:32:23.691479+00:00"
    }






Metadata
========

Get Metadata
--------

You can get existing analysis metadata only if you have at least read access to it.

You need to specify

	--pid  the PID of an analysis.

.. code-block:: console

    $ cap-client metadata get <field> --pid/-p <existing pid>

    E.g $ cap-client metadata get basic_info.description --pid 4b2924db6c32467bb2de6221f4faf167

    "Very Interesting Description"


Edit Metadata
-------------

You can edit and change existing metadata details if you have at least read access to it.

You need to specify

    --pid  the PID of an analysis.


.. code-block:: console

    $ cap-client metadata set <field> <new value> --pid/p <existing pid>

    E.g $ cap-client metadata set basic_info.description "Very Interesting Description" --pid 4b2924db6c32467bb2de6221f4faf167

    {
        "$ana_type": "lhcb",
        "$schema": "https://macbook-trzcinska.cern.ch:5000/schemas/deposits/records/lhcb-v0.0.1.json",
            "basic_info": {
                "description": "Very Interesting Description"
            }
    }

    $ cap-client --verbose metadata append basic_info.my_array "New element" --pid 0af85220ef0c492889658539d8b3d4e2

    {
        "$ana_type": "lhcb",
        "$schema": "https://macbook-trzcinska.cern.ch:5000/schemas/deposits/records/lhcb-v0.0.1.json",
        "basic_info": {
            "my_array": [
                "New element"
            ],
            "description": "Very Interesting Description"
        }
    }

Remove Metadata
-------------

You can remove existing metadata details if you have at least read access to it.

You need to specify

    --pid  the PID of an analysis.

.. code-block:: console

    $ cap-client metadata remove <field> -p 0af85220ef0c492889658539d8b3d4e2

    E.g $ cap-client metadata remove basic_info.my_array.0 -p 0af85220ef0c492889658539d8b3d4e2
    {
        "$ana_type": "lhcb",
        "$schema": "https://macbook-trzcinska.cern.ch:5000/schemas/deposits/records/lhcb-v0.0.1.json",
        "basic_info": {
            "my_array": [],
            "description": "Very Interesting Description"
        }
    }



Permissions
===========


Get permissions
-----------

You can get existing analysis user permissions only if you have at least read access to it.

You need to specify

    --pid  the PID of an analysis.

.. code-block:: console

    $ cap-client permissions get --pid/p <existing pid>

    {
        "updated": "2018-02-12T15:57:31.824619+00:00",
        "metadata": {
            "deposit-admin": {
                "user": [],
                "roles": []
            },
            "deposit-update": {
                "user": [],
                "roles": []
            },
            "deposit-read": {
                "user": [
                    "alice@inveniosoftware.org"
                ],
                "roles": []
                }
            },
        "created": "2018-02-12T15:15:40.697516+00:00"
    }


Set permissions
-----------

You can set existing analysis user permissions only if you have at least read access to it.

You need to specify

    --rights  the permission rights. You can choose between read, update and admin.
    --user  the email of the user to grant permissions.
    --pid  the PID of an analysis you want to set permissions.

.. code-block:: console

    $ cap-client permissions add --rights/-r [read | update | admin] --user/-u <email> --pid/p <existing pid>

    E.g $ cap-client permissions add -r update -u alice@inveniosoftware.org -p 0af85220ef0c492889658539d8b3d4e2

    {
        "updated": "2018-02-12T15:57:31.824619+00:00",
        "metadata": {
            "deposit-admin": {
                "user": [],
                "roles": []
            },
            "deposit-update": {
                "user": [
                    "alice@inveniosoftware.org"
                ],
                "roles": []
            },
            "deposit-read": {
                "user": [
                    "alice@inveniosoftware.org"
                ],
                "roles": []
                }
            },
        "created": "2018-02-12T15:15:40.697516+00:00"
    }

Remove permissions
-----------

You can remove existing analysis user permissions only if you have at least read access to it.

You need to specify

    --rights  the permission rights. You can choose between read, update and admin.
    --user  the email of the user to grant permissions.
    --pid  the PID of an analysis you want to remove permissions.

.. code-block:: console

    $ cap-client permissions remove --rights/-r [read | update | admin] --user/-u <email> --pid/p <existing pid>

    E.g $ cap-client permissions remove -r update -u alice@inveniosoftware.org -p 0af85220ef0c492889658539d8b3d4e2

    {
        "updated": "2018-02-12T15:57:31.824619+00:00",
        "metadata": {
            "deposit-admin": {
                "user": [],
                "roles": []
            },
            "deposit-update": {
                "user": [],
                "roles": []
            },
            "deposit-read": {
                "user": [
                    "alice@inveniosoftware.org"
                ],
                "roles": []
                }
            },
        "created": "2018-02-12T15:15:40.697516+00:00"
    }


Files
===========

List files
----------

You can list all the files from an analysis only if you have at least read access to it.

You need to specify

    --pid  the PID of an analysis you want to list all the contained files.

.. code-block:: console

    $ cap-client files list --pid/-p <existing pid>

    $ cap-client files list -p 89b593c498874ec8bcafc88944c458a7

    [
        {
            "checksum": "md5:f0428126e7cf7b0d4af7091c68ae2a9f",
            "filename": "file.json",
            "filesize": 25,
            "id": "25852e50-be6d-47a5-897b-1f3df015fac7"
        },
        {
            "checksum": "md5:926fb9c44251d70614ee42d34c5365b6",
            "filename": "Receipt.pdf",
            "filesize": 160898,
            "id": "89743c9b-106d-4235-8e96-23a164c7b1f4"
        }
    ]



Upload file
-----------

You can upload a file to an analysis only if you have at least read access to it.

You need to specify

    --pid  the PID of an analysis you want to set permissions.

.. code-block:: console

    $ cap-client files upload <file path> --pid/-p <existing pid>

    $ cap-client files upload file.json -p 89b593c498874ec8bcafc88944c458a7

    File uploaded successfully.


Download file
-----------

You can download a file to an analysis only if you have at least read access to it.

You need to specify

    --pid  the PID of an analysis you want to set permissions.
    --output-file  save the downloaded file as <desired file name>.

.. code-block:: console

    $ cap-client files download <file key> --output-file/-o <file name> --pid/-p <existing pid>

    $ cap-client files download file.json -o local_file.json -p 89b593c498874ec8bcafc88944c458a7

    File saved as local_file.json




Remove file
-----------

You can remove a file to an analysis only if you have at least read access to it.

You need to specify

    --pid  the PID of an analysis you want to set permissions.

.. code-block:: console

    $ cap-client files remove <file path> --pid/-p <existing pid>

    $ cap-client files upload file.json -p 89b593c498874ec8bcafc88944c458a7

    File file.json removed.


Shared records
==============

You can get one or all the shared records only if you have at least read access to it.

You need to specify

    --pid  the PID of the shared analysis you want to fetch.
    --all  flag to fetch all the shared analysis you have access to.


.. code-block:: console

    $ cap-client get-shared --all

    $ cap-client get-shared --pid 1

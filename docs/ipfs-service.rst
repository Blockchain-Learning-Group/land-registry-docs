======================
IPFS Microservice
======================

Description
***********
Enables the interaction with the IPFS service.
The service provides the funtionality of being able to encrypt uploaded pdfs to a decentralized storage like IPFS, and decrypt / download back from the same location using an IPFS hash.

====

API
***

1. `Encrypt <#encrypt>`_
2. `Decrypt <#decrypt>`_


Encrypt
-------
Adds a PDF file with contents to the decentralized storage of IPFS.

- URL:
``/ipfs/encrypt``

- Method:
``POST``

- URL Params

``None``

- Body
**Required**

.. code-block:: console

  {
    file          =[File],
    password      =[string],
    fileName      =[string]
  }

- Success Response

.. code-block:: console

  statusCode: 200
  body: { ipfsHash: 'QmPQWExfz6bkWgQkdrVz3qhpXsZsAegMYDbMuj9BBk6Gg3'}

- Error Response

.. code-block:: console

  TODO

- Sample Call
Call must be sent as a multi-part form with the file name in the form and as a PDF.

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: `localhost:3003/encrypt`,
    method: 'POST',
    json: true,
    resolveWithFullResponse: true,
    simple: true,
    formData: {
      password: password,
      file: {
          value: File (e.g. "..."),
          options: {
              filename: "fileName.pdf",
              contentType: 'application/pdf'
          }
      }
    }
  });


Decrypt
---------------
Returns a downloaded PDF from IPFS.

- URL:

``/decrypt``

- Method:
``POST``

- URL Params

- Body
**Required**

.. code-block:: console

  {
    password      =[string],
    ipfsHash      =[string]
  }

- Success Response

.. code-block:: console

  statusCode: 200
  body: {
          file:    '...',    // File
        }


- Error Response
TODO

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: `localhost:3003/decrypt`,
    method,
    json: true,
    resolveWithFullResponse: true,
    body: {
      password: "...
      ipfsHash: "..."
    },
    simple: true,
  })

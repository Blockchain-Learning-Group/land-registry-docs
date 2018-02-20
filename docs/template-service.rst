======================
<service> Microservice
======================

Description
***********
- what is the service?
- what functionality does the service provide?
- etc.

====

API
***

- See REST api documentation reference `here <https://gist.github.com/iros/3426278>`_

- Examples(POST and GET):

Titles
======

Add Title
---------
Adds a new title to the land registry contract. Returns the transaction hash.

- URL:
``/titles``

- Method:
``POST``

- URL Params
**Required**

``None``

- Body
**Required**

.. code-block:: console

  {
    titleNumber=[integer],
    owner      =[address],
    dataHash   =[string]
  }

- Success Response

.. code-block:: console

  statusCode: 200
  body: {
          txHash: '0x7f480f9b52ae4cfe8ff8b607c46c795482f9543264c1b61d7032715b1e1eb66e',
        }

- Error Response

.. code-block:: console

  TODO

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/titles',
    'POST',
    json: true,
    body: {
      titleNumber: 1,
      owner:       '0x26006236eab6409d9fdecb16ed841033d6b4a6bc',
      dataHash:    'QmSsV8J2KgynQ5DK1nRcKsTuxojFvEHmgmJwrU9vHWeyEL'
    }
    simple: true
  })

Get Title By Id
---------------
Returns all on-chain information about a specific title.  The id for a title is an integer and also referred to as titleNumber or UID.

- URL:

``/titles/:uid``

- Method:
``GET``

- URL Params
**Required**

``uid=[integer]``

- Success Response

.. code-block:: console

  statusCode: 200
  body: {
          owner:    '0x26006236eab6409d9fdecb16ed841033d6b4a6bc',    // String
          dataHash: 'QmSsV8J2KgynQ5DK1nRcKsTuxojFvEHmgmJwrU9vHWeyEL' // String
        }


- Error Response

.. code-block:: console

  statusCode: 404
  error: {
            code:    'NotFound',
            message: 'Title does not exist.'
         }

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/titles/1',
    'GET',
    json: true,
    simple: true
  })

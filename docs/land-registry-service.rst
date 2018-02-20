==========================
Land Registry Microservice
==========================

Description
***********
This is a top level service to interact direct with the front end.  The service enables a single entry pointfor the front end to interact across all microservices.
For example this service facilitates functionality that requires interaction across several microservice.

====
Table of Contents
*****************
1. `API <#api>`_

  - `Sale Deeds <#sale-deeds>`_

    - `Add Sale Deed <#add-sale-deed>`_

API
***

Sale Deeds
==========

Add Sale Deed
-------------
Adds a new sale deed to the land registry.

- URL:
``/saleDeeds``

- Method:
``POST``

- Body
**Required**

.. code-block:: javascript

  /**
   * @param {Object}  req.body.buyer          Party buying the property.
   * @param {String}  req.body.buyer.name     Legal name of the buyer.
   * @param {Address} req.body.buyer.id       Real world Id of the buyer, Aadhaar Number in future, currently ETH address.
   * @param {String}  req.body.buyer.contact  Contact info for the buyer, email, phone, etc.
   * @param {Object}  req.body.seller         Party selling the property.
   * @param {String}  req.body.seller.name    Legal name of the seller.
   * @param {Address} req.body.seller.id      Real world Id of the seller, Aadhaar Number in future, currently ETH address.
   * @param {String}  req.body.seller.contact Contact info for the buyer, email, phone, etc.
   * @param {Address} req.body.authority      Party required to approve sale deed.
   * @param {Integer} req.body.propertyId     Id of an existing property this sale deed is associated with.
   * @param {String}  req.body.city           City the property is located in.
   * @param {String}  req.body.address        Street address of the property.
   */

- Success Response

.. code-block:: javascript

  /**
   * @returns {Object} Transaction receipt, rawRecipt and human readable logs.
   */

.. code-block:: console

  statusCode: 200
  body: {
         rawReceipt:
           { transactionHash: '0x9c37cf50d7f0ebbc80247acb1a1d0e363321f9acd96da7d5661740b1fb5d2e80',
             transactionIndex: 0,
             blockHash: '0x58105c1d41b8d9f26abfbb9b7d3f9f15d2127435c7e13410667f17c0171ab70c',
             blockNumber: 13,
             gasUsed: 134205,
             cumulativeGasUsed: 134205,
             contractAddress: null,
             logs: [ [Object] ],
             status: '0x01',
             logsBloom: '0x00000000000000000080000000000000000000000000000000000000000000008000000000000000000000000000000000001000000000000000000000040000000020000000000000000000000000000000000000040000000000000000000000000000000000008000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000010000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000020000000000000100000040000000000000000000000000000000000000000000000000040000000000000000' },
          logs:
           [ { logIndex: 0,
               transactionIndex: 0,
               transactionHash: '0x9c37cf50d7f0ebbc80247acb1a1d0e363321f9acd96da7d5661740b1fb5d2e80',
               blockHash: '0x58105c1d41b8d9f26abfbb9b7d3f9f15d2127435c7e13410667f17c0171ab70c',
               blockNumber: 13,
               address: '0xf7bc3030ef36eb38016e39c2c06825d8c14b324c',
               type: 'mined',
               event: 'LogSaleDeedAdded',
               args: [Object] } ]
         }

- Error Response
**Error event emitted! Note than are many possible messages based on response from the `HaryanaLandRegistryProxy contract`**

.. code-block:: console

  statusCode: 412
  error: {
            code: 'PreconditionFailed',
            message: 'Not a valid authority, HaryanaLandRegistryProxy.addSaleDeed()'
         }


**Transaction failed to be executed.**

.. code-block:: console

  statusCode: 400
  error: {
            code: 'BadRequestError',
            message: 'Transaction failed... 0x9c37cf50d7f0ebbc80247acb1a1d0e363321f9acd96da7d5661740b1fb5d2e80'
         }

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/saleDeeds',
    'POST',
    json: true,
    body: {
            buyer: {
                id: '0x04175fac22db2a28784d9b96f95acf619fffb42a',
                name: 'buyer',
                contact: 'buyer@buyer.com'
            },
            seller: {
                id: '0x3d269a888baf589cbe6e54c8ec8e73e8d615064c',
                name: 'seller',
                contact: 'seller@seller.com'
            },
            authority: '0xdb02ff081b2171241088a83b4db8444d3d39151a',
            propertyId: 7,
            city: 'Chandigarh, Haryana, India',
            address: 'address'
    }
    simple: true
  })

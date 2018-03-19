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

    - `Approve Sale Deed <#approve-sale-deed>`_

    - `Sign Sale Deed <#sign-sale-deed>`_

    - `Get Sale Deeds By Authority <#get-sale-deeds-by-authority>`_

    - `Upload Sale Deed <#upload-sale-deed>`_

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

Approve Sale Deed
-------------
Approve a sale deed that exists.

- URL:
``/approveSaleDeed``

- Method:
``PUT``

- Body
**Required**

.. code-block:: javascript

  /**
   * @param {Address} req.body.signer Id of party signing the deed.
   * @param {Integer} req.body.deedId id of the deed being approved.
   * @param {String}  req.body.salt   Pseudo-randomness to generate unique sig. BigNumber.toFixed().
   * @param {Integer} req.body.v      Ethereum ecdsa signature variable, generally 27 or 28.
   * @param {String}  req.body.r      Standard ecdsa r variable, byte string, 32 bytes.
   * @param {String}  req.body.s      Standard ecdsa s variable, byte string, 32 bytes.
   */

- Success Response

.. code-block:: javascript

  /**
  * @returns {Object} Transaction receipt.
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
               event: 'LogSaleDeedApproved',
               args: [Object] } ]
         }

- Error Response

.. code-block:: console

  TODO

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/approveSaleDeed',
    'PUT',
    json: true,
    body: {
            signer: '0xa8d5f39f3ccd4795b0e38feacb4f2ee22486ca44',
            deedId: 4,
            salt:   '65073403076894699546208128805910860907947257258881689808941649510475988244552',
            v:      27,
            r:      '0xc02d019c08d5fb700ac7eab54d4ecffe3b02d3253a7c34c2a6ac22e7aee65ebb',
            s:      '0x51ef8a7292b73d0742fadb3a2e42b4a1c17b03be14c8ec3dd5b2c458c9bb36ae'
          }
    simple: true
  })

Sign Sale Deed
-------------
Sign a sale deed that exists.

- URL:
``/signSaleDeed``

- Method:
``PUT``

- Body
**Required**

.. code-block:: javascript

  /**
   * @param {Address} req.body.signer Id of party signing the deed.
   * @param {Integer} req.body.deedId id of the deed being approved.
   * @param {String}  req.body.salt   Pseudo-randomness to generate unique sig. BigNumber.toFixed().
   * @param {Integer} req.body.v      Ethereum ecdsa signature variable, generally 27 or 28.
   * @param {String}  req.body.r      Standard ecdsa r variable, byte string, 32 bytes.
   * @param {String}  req.body.s      Standard ecdsa s variable, byte string, 32 bytes.
   */

- Success Response

.. code-block:: javascript

  /**
  * @returns {Object} Transaction receipt.
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
               event: 'LogSaleDeedSigned',
               args: [Object] } ]
         }

- Error Response

.. code-block:: console

  TODO

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/signSaleDeed',
    'PUT',
    json: true,
    body: {
            signer: '0xa8d5f39f3ccd4795b0e38feacb4f2ee22486ca44',
            deedId: 4,
            salt:   '65073403076894699546208128805910860907947257258881689808941649510475988244552',
            v:      27,
            r:      '0xc02d019c08d5fb700ac7eab54d4ecffe3b02d3253a7c34c2a6ac22e7aee65ebb',
            s:      '0x51ef8a7292b73d0742fadb3a2e42b4a1c17b03be14c8ec3dd5b2c458c9bb36ae'
          }
    simple: true
  })


Get Sale Deeds By Authority
---------------------------
Get all sale deed data for a given authority.

- URL:
``/getSaleDeedsByAuthority``

- Method:
``GET``

- URL PARAMS

.. code-block:: javascript

  /**
   * Return all sale deeds associated with an authority.
   * @param {Address}  req.params.authorityId ID of the authoirty.
   */

- Success Response

.. code-block:: javascript

  /**
   * @returns {Object} All deeds indexed by id.
   */

.. code-block:: console

  statusCode: 200
  body: { '10':
   { signOff: 0,
     buyer: '0x840cf0e3d1cca724a6d5dc274b06921b6cc065b4',
     seller: '0x776220e6ef98f8c58a71246135e2725490690cc3',
     authority: '0x592c0e099e4d36eece9149f0da602d292ea05a06',
     propertyId: 7,
     dataHash: '',
     state: 0,
     createdAt: '2018-03-11T23:32:12.445995Z',
     stampDuty: '33000',
     value: '660000',
     address: 'address',
     city: 'Chandigarh, Haryana, India' } }

- Error Response

.. code-block:: console

  TODO

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/getSaleDeedsByAuthority/0x592c0e099e4d36eece9149f0da602d292ea05a06',
    'GET',
    json: true,
    simple: true
  })

Upload Sale Deed
----------------
Upload a new

- URL:
``/uploadSaleDeed``

- Method:
``PUT``

- Body
**Required**

.. code-block:: javascript

  /**
   * @param {Number}  req.body.deedId   ID of the deed.
   * @param {String}  req.body.password Password to encrypt the file with.
   * @param {String}  req.body.file     File to encrypt and upload.
   */

- Success Response

.. code-block:: javascript

  /**
   * @returns {Object} Receipt of the transaction as well as the ipfs hash.
   */

.. code-block:: console

  statusCode: 200
  body: { receipt:
   { rawReceipt:
      { transactionHash: '0x7eca07587d9cfd57ab31ca828327b525139f185dc3d1f074eb7916fab4aca3f2',
        transactionIndex: 0,
        blockHash: '0x3e58edbc8315e4de9f4cb792cb569c1ac77d258bec81407852b021fb66091c59',
        blockNumber: 10,
        gasUsed: 88948,
        cumulativeGasUsed: 88948,
        contractAddress: null,
        logs: [Array],
        status: '0x01',
        logsBloom: '0x00000000000000000000000000000000000000000000000000000000000000000000100000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000002000000000000000000000000000000000000000000000000000000000000000000000010000000000000040000000000000000000000000000000000000000000000000000000000000000000000000000000040000000000000000000000000000040000400000000000000000000000000000000000000000000000000000400000000000000000000000000000000000000000000000000000080' },
     logs: [ [Object] ],
     dataHash: 'QmbH6ikUjb7KJ47iFsemmHCfmhPKQxc8BXjK2xCsVab1CZ' } }

- Error Response

.. code-block:: console

  TODO

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const file = await readFile(path.join(__dirname + '/../fixtures/Sale-Deed-1.pdf'));
  const response = await rp({
    url: http://localhost:8080/uploadSaleDeed/,
    method: 'PUT',
    json: true,
    resolveWithFullResponse: true,
    simple: true,
    formData: {
      deedId: deedId,
      password: password,
      file: {
          value: file,
          options: {
              filename: fileName,
              contentType: 'application/pdf'
          }
      }
    }
  }

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
1. API_

  - `Sale Deeds <#sale-deeds>`_

    - `Add Sale Deed <#add-sale-deed>`_

    - `Approve Sale Deed <#approve-sale-deed>`_

    - `Sign Sale Deed <#sign-sale-deed>`_

    - `Get All Sale Deeds <#get-all-sale-deeds>`_

    - `Get Sale Deeds By Authority <#get-sale-deeds-by-authority>`_

    - `Get Sale Deed By ID <#get-sale-deed-by-id>`_

    - `Upload Sale Deed <#upload-sale-deed>`_

  - `Properties <#properties>`_

    - `Get Property History <#get-property-history>`_

    - `Get All Properties <#get-all-properties>`_

.. _api:

API
***

.. _sale-deeds:

Sale Deeds
==========

.. _add-sale-deed:

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
   * @param {Address} req.body.authority      ID of party required to approve sale deed.
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
         receipt: {
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
          logs: [{
              logIndex: 0,
              transactionIndex: 0,
               transactionHash: '0xf33c69c7aef2482dc51d83b95cf89acf86710fbc2077b458299e85dba4badb0b',
               blockHash: '0xbd2c3dc293575f13040b8917c8782f316dc76eb710b439ef1b56c5e5da569049',
               blockNumber: 9,
               address: '0x4aa54ce27902e8d097b6116ac6598beb045bbd2b',
               type: 'mined',
               event: 'LogSaleDeedAdded',
               args: {
                  deedId: '4',
                  buyer: '0x840cf0e3d1cca724a6d5dc274b06921b6cc065b4',
                  seller: '0x776220e6ef98f8c58a71246135e2725490690cc3',
                  authority: '0xb386ee3160c5d84a5455f6aea2ca21f68811ffc6',
                  propertyId: '7',
                  dataHash: '0x'
              }
          }]
         }
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
    },
    simple: true
  })

.. _approve-sale-deed:

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
          receipt: {
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
                 args: {
                   deedId: '4',
                 }
             } ]
         }
       }

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
    },
    simple: true
  })

.. _sign-sale-deed:

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
         receipt : {
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
                 args: {
                  deedId: '4',
                  signer: '0x840cf0e3d1cca724a6d5dc274b06921b6cc065b4',
                 }
             } ]
         }
       }

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
    },
    simple: true
  })

.. _get-all-sale-deeds:

Get All Sale Deeds
------------------
Get all sale deeds.

- URL:
``/getAllSaleDeeds``

- Method:
``GET``

- Success Response

.. code-block:: javascript

  /**
   * @returns {Object} All deeds indexed by id.
   */

.. code-block:: console

  statusCode: 200
  body: {
    deeds: {
      '3': {
        id: '3',
        onChain:{
          signOff: 0,
          buyer: '0x840cf0e3d1cca724a6d5dc274b06921b6cc065b4',
          seller: '0x776220e6ef98f8c58a71246135e2725490690cc3',
          authority: '0x592c0e099e4d36eece9149f0da602d292ea05a06',
          propertyId: 7,
          dataHash: '',
          state: 0,
          logData: {
            addedLogs: {
              transaction: '0x12a6db8340be6cf550255459476802ed18d7f2080f1d5b21fadf322709e33067',
              block: 9,
              timestamp: 1523307993
            },
            sigLogs: {
              buyerSigTransaction: '0x12a6db8340be6cf550255459476802ed18d7f2080f1d5b21fadf322709e33067',
              buyerSigBlock: 11,
              buyerSigTimestamp: 1523307993,
              sellerSigTransaction: '0x12a6db8340be6cf550255459476802ed18d7f2080f1d5b21fadf322709e33067',
              sellerSigBlock: 10,
              sellerSigTimestamp: 1523307993
            },
            approvalLogs: {
              transaction: '0x12a6db8340be6cf550255459476802ed18d7f2080f1d5b21fadf322709e33067',
              block: 9,
              timestamp: 1523307993
            },
            executedLogs: {
              transaction: '0x12a6db8340be6cf550255459476802ed18d7f2080f1d5b21fadf322709e33067',
              block: 9,
              timestamp: 1523307993
            }
          },
        },
        metadata: {
          createdAt: '2018-03-30T22:05:42.243139Z',
          stampDuty: '33000',
          value: '660000',
          address: 'address',
          city: 'Chandigarh, Haryana, India',
          seller: {
            id: '0x776220e6ef98f8c58a71246135e2725490690cc3',
            created_at: '2018-03-30T22:04:27.634754Z',
            name: 'seller',
            contact: 'seller@seller.com'
          },
          buyer: {
            id: '0x840cf0e3d1cca724a6d5dc274b06921b6cc065b4',
            created_at: '2018-03-30T22:04:27.582874Z',
            name: 'buyer',
            contact: 'buyer@buyer.com'
          }
        },
        createdAt: '2018-03-30T22:05:42.243139Z',
        stampDuty: '33000',
        value: '660000',
        address: 'address',
        city: 'Chandigarh, Haryana, India'
      },
    }
  }

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/getAllSaleDeeds'
    'GET',
    json: true,
    simple: true
  })

.. _get-sale-deeds-by-authority:

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
  body: {
    deeds: {
      '3': {
        id: '3',
        onChain:{
          signOff: 0,
          buyer: '0x840cf0e3d1cca724a6d5dc274b06921b6cc065b4',
          seller: '0x776220e6ef98f8c58a71246135e2725490690cc3',
          authority: '0x592c0e099e4d36eece9149f0da602d292ea05a06',
          propertyId: 7,
          dataHash: '',
          state: 0,
          logData: {
            addedLogs: {
              transaction: '0x12a6db8340be6cf550255459476802ed18d7f2080f1d5b21fadf322709e33067',
              block: 9,
              timestamp: 1523307993
            },
            sigLogs: {
              buyerSigTransaction: '0x12a6db8340be6cf550255459476802ed18d7f2080f1d5b21fadf322709e33067',
              buyerSigBlock: 11,
              buyerSigTimestamp: 1523307993,
              sellerSigTransaction: '0x12a6db8340be6cf550255459476802ed18d7f2080f1d5b21fadf322709e33067',
              sellerSigBlock: 10,
              sellerSigTimestamp: 1523307993
            },
            approvalLogs: {
              transaction: '0x12a6db8340be6cf550255459476802ed18d7f2080f1d5b21fadf322709e33067',
              block: 9,
              timestamp: 1523307993
            },
            executedLogs: {
              transaction: '0x12a6db8340be6cf550255459476802ed18d7f2080f1d5b21fadf322709e33067',
              block: 9,
              timestamp: 1523307993
            }
          },
        },
        metadata: {
          createdAt: '2018-03-30T22:05:42.243139Z',
          stampDuty: '33000',
          value: '660000',
          address: 'address',
          city: 'Chandigarh, Haryana, India',
          seller: {
            id: '0x776220e6ef98f8c58a71246135e2725490690cc3',
            created_at: '2018-03-30T22:04:27.634754Z',
            name: 'seller',
            contact: 'seller@seller.com'
          },
          buyer: {
            id: '0x840cf0e3d1cca724a6d5dc274b06921b6cc065b4',
            created_at: '2018-03-30T22:04:27.582874Z',
            name: 'buyer',
            contact: 'buyer@buyer.com'
          }
        },
        createdAt: '2018-03-30T22:05:42.243139Z',
        stampDuty: '33000',
        value: '660000',
        address: 'address',
        city: 'Chandigarh, Haryana, India'
      },
    }
  }

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/getSaleDeedsByAuthority/0x592c0e099e4d36eece9149f0da602d292ea05a06',
    'GET',
    json: true,
    simple: true
  })

.. _get-sale-deed-by-id:

Get Sale Deed By ID
---------------------------
Get all data for a sale deed.

- URL:
``/getSaleDeedById``

- Method:
``GET``

- URL PARAMS

.. code-block:: javascript

  /**
   * @param {Number} req.params.deedId ID of the deed.
   */

- Success Response

.. code-block:: javascript

  /**
   * @returns {Object} All deed data onchain and metadata.
   */

.. code-block:: console

  statusCode: 200
  body: {
    deed: {
      '1': {
        id: '1',
        onChain:{
          signOff: 0,
          buyer: '0x840cf0e3d1cca724a6d5dc274b06921b6cc065b4',
          seller: '0x776220e6ef98f8c58a71246135e2725490690cc3',
          authority: '0x592c0e099e4d36eece9149f0da602d292ea05a06',
          propertyId: 7,
          dataHash: '',
          state: 0,
          logData: {
            addedLogs: {
              transaction: '0x12a6db8340be6cf550255459476802ed18d7f2080f1d5b21fadf322709e33067',
              block: 9,
              timestamp: 1523307993
            },
            sigLogs: {
              buyerSigTransaction: '0x12a6db8340be6cf550255459476802ed18d7f2080f1d5b21fadf322709e33067',
              buyerSigBlock: 11,
              buyerSigTimestamp: 1523307993,
              sellerSigTransaction: '0x12a6db8340be6cf550255459476802ed18d7f2080f1d5b21fadf322709e33067',
              sellerSigBlock: 10,
              sellerSigTimestamp: 1523307993
            },
            approvalLogs: {
              transaction: '0x12a6db8340be6cf550255459476802ed18d7f2080f1d5b21fadf322709e33067',
              block: 9,
              timestamp: 1523307993
            },
            executedLogs: {
              transaction: '0x12a6db8340be6cf550255459476802ed18d7f2080f1d5b21fadf322709e33067',
              block: 9,
              timestamp: 1523307993
            }
          },
        },
        metadata: {
          createdAt: '2018-03-30T22:05:42.243139Z',
          stampDuty: '33000',
          value: '660000',
          address: 'address',
          city: 'Chandigarh, Haryana, India',
          seller: {
            id: '0x776220e6ef98f8c58a71246135e2725490690cc3',
            created_at: '2018-03-30T22:04:27.634754Z',
            name: 'seller',
            contact: 'seller@seller.com'
          },
          buyer: {
            id: '0x840cf0e3d1cca724a6d5dc274b06921b6cc065b4',
            created_at: '2018-03-30T22:04:27.582874Z',
            name: 'buyer',
            contact: 'buyer@buyer.com'
          }
        },
        createdAt: '2018-03-30T22:05:42.243139Z',
        stampDuty: '33000',
        value: '660000',
        address: 'address',
        city: 'Chandigarh, Haryana, India'
      },
    }
  }

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/getSaleDeedById/1',
    'GET',
    json: true,
    simple: true
  })

.. _upload-sale-deed:

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
  body: {
    receipt: {
      rawReceipt: {
        transactionHash: '0x7eca07587d9cfd57ab31ca828327b525139f185dc3d1f074eb7916fab4aca3f2',
        transactionIndex: 0,
        blockHash: '0x3e58edbc8315e4de9f4cb792cb569c1ac77d258bec81407852b021fb66091c59',
        blockNumber: 10,
        gasUsed: 88948,
        cumulativeGasUsed: 88948,
        contractAddress: null,
        logs: [Array],
        status: '0x01',
        logsBloom: '0x00000000000000000000000000000000000000000000000000000000000000000000100000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000002000000000000000000000000000000000000000000000000000000000000000000000010000000000000040000000000000000000000000000000000000000000000000000000000000000000000000000000040000000000000000000000000000040000400000000000000000000000000000000000000000000000000000400000000000000000000000000000000000000000000000000000080'
      },
      logs: [{
        logIndex: 0,
        transactionIndex: 0,
        transactionHash: '0xa346a211684c3d6831906f003307df5d083a85b2a8f080e5ba6d01b651887d98',
        blockHash: '0x7010e4a6fb3f5c8cd8c24c4625006dc5b22f6f584840e3a09a1923c7ed12a587',
        blockNumber: 10,
        address: '0x4aa54ce27902e8d097b6116ac6598beb045bbd2b',
        type: 'mined',
        event: 'LogSetDeedMetaData',
        args: {
          deedId: '4',
          dataHash: '0x516d624836696b556a62374b4a3437694673656d6d4843666d68504b5178633842586a4b3278437356616231435a'
        }
      }],
     dataHash: 'QmbH6ikUjb7KJ47iFsemmHCfmhPKQxc8BXjK2xCsVab1CZ'
   }
 }

- Error Response

.. code-block:: console

  statusCode: 412
  error: {
    code: 'PreconditionFailed',
    message: 'Deed does not exist, HaryanaLandRegistryProxy.setDeedMetaData()'
  }

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const file = await readFile(path.join(__dirname + '/../fixtures/Sale-Deed-1.pdf'));
  const response = await rp({
    url: http://localhost:8080/uploadSaleDeed/,
    method: 'PUT',
    json: true,
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
  })

---

.. _properties:

Properties
==========

.. _get-property-history:

Get Property History
--------------------
Get the history of a property

- URL:
``/getPropertyHistory``

- Method:
``GET``

.. code-block:: javascript

  /**
   * @param {Number} req.params.propertyId ID of the property.
   */

- Success Response

.. code-block:: javascript

  /**
   * @returns {Object} All historical deeds indexed by id.
   */

.. code-block:: console

  statusCode: 200
  body: {
    history: {
      owner: {
        id: '0x776220e6ef98f8c58a71246135e2725490690cc3',
        created_at: '2018-04-10T15:13:07.510067Z',
        name: 'seller',
        contact: 'seller@seller.com'
      },
      dataHash: '',
      deeds: {
        '1': {
          id: '1',
          onChain:{
            signOff: 0,
            buyer: '0x840cf0e3d1cca724a6d5dc274b06921b6cc065b4',
            seller: '0x776220e6ef98f8c58a71246135e2725490690cc3',
            authority: '0x592c0e099e4d36eece9149f0da602d292ea05a06',
            propertyId: 7,
            dataHash: '',
            state: 0,
            logData: {
              addedLogs: {
                transaction: '0x12a6db8340be6cf550255459476802ed18d7f2080f1d5b21fadf322709e33067',
                block: 9,
                timestamp: 1523307993
              },
              sigLogs: {
                buyerSigTransaction: '0x12a6db8340be6cf550255459476802ed18d7f2080f1d5b21fadf322709e33067',
                buyerSigBlock: 11,
                buyerSigTimestamp: 1523307993,
                sellerSigTransaction: '0x12a6db8340be6cf550255459476802ed18d7f2080f1d5b21fadf322709e33067',
                sellerSigBlock: 10,
                sellerSigTimestamp: 1523307993
              },
              approvalLogs: {
                transaction: '0x12a6db8340be6cf550255459476802ed18d7f2080f1d5b21fadf322709e33067',
                block: 9,
                timestamp: 1523307993
              },
              executedLogs: {
                transaction: '0x12a6db8340be6cf550255459476802ed18d7f2080f1d5b21fadf322709e33067',
                block: 9,
                timestamp: 1523307993
              }
            },
          },
          metadata: {
            createdAt: '2018-03-30T22:05:42.243139Z',
            stampDuty: '33000',
            value: '660000',
            address: 'address',
            city: 'Chandigarh, Haryana, India',
            seller: {
              id: '0x776220e6ef98f8c58a71246135e2725490690cc3',
              created_at: '2018-03-30T22:04:27.634754Z',
              name: 'seller',
              contact: 'seller@seller.com'
            },
            buyer: {
              id: '0x840cf0e3d1cca724a6d5dc274b06921b6cc065b4',
              created_at: '2018-03-30T22:04:27.582874Z',
              name: 'buyer',
              contact: 'buyer@buyer.com'
            }
          },
          createdAt: '2018-03-30T22:05:42.243139Z',
          stampDuty: '33000',
          value: '660000',
          address: 'address',
          city: 'Chandigarh, Haryana, India'
        },
      }
    }
  }

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/getPropertyHistory/7'
    'GET',
    json: true,
    simple: true
  })

.. _get-all-properties:

Get All Properties
------------------
Get the all the properties

- URL:
``/getAllProperties``

- Method:
``GET``

- Success Response

.. code-block:: javascript

  /**
   * @returns {Object} All properties indexed by id.
   */

.. code-block:: console

  statusCode: 200
  body: {
    properties: {
      '7': {
        owner: 'seller',
        dataHash: 'hashash'
      },
    }
  }

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/getAllProperties'
    'GET',
    json: true,
    simple: true
  })

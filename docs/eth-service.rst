=====================
Ethereum Microservice
=====================

Description
***********
Enables interaction with the Ethereum network.
This service interacts directly with the network as well as the ``HaryanaLandRegistryProxy`` and ``StandardAssetRegistry`` contracts.  All interaction with either is routed through this service.

Table of Contents
*****************
1. `Terminology <#terminology>`_

2. `API <#api>`_

  - `Accounts <#accounts>`_

    - `Create Account <#create-account>`_: ``POST /accounts/{data}``
    - `Get Account by Party Id <#get-account-by-party-id>`_: ``GET /accounts/:partyId``

  - `Assets <#assets>`_

    - `Add Asset <#add-asset>`_: ``POST /assets/{data}``
    - `Get Asset By Id <#get-asset-by-id>`_: ``GET /assets/:assetId``
    - `Get All Assets <#get-all-assets>`_: ``GET /assets``
    - `Asset Exists <#asset-exists>`_: ``GET /assetExists/:assetId``

  - `Authorities <#authorities>`_

    - `Add Authority <#add-authority>`_: ``POST /authorities/{data}``

  - `Deeds <#deeds>`_

    - `Add Deed <#add-deed>`_: ``POST /deeds/{data}``
    - `Approve Deed <#approve-deed>`_: ``PUT /approveDeed/{data}``
    - `Get Deed By Id <#get-deed-by-id>`_: ``GET /deeds/:deedId``
    - `Get Deeds By Property Id <#get-beeds-by-property-id>`_: ``GET /getDeedsByProperty/:propertyId``
    - `Sign Deed <#sign-deed>`_: ``PUT /signDeed/{data}``
    - `Set Deed Meta Data <#set-deed-metadata>`_: ``PUT /setDeedMetaData/:deedId/:dataHash``

  - `Utils <#utils>`_

    - `Get Transaction Receipt After Confrimations <#get-transaction-receipt-after-confirmations>`_: ``GET transactionReceipt/:txHash/:confirmations``

Terminology
***********
- ``Party``: A party refers to a real world human being, buyers and sellers associed with sale deeds are most commonly referred to as parties.

  - Where ``partyId`` is their real-world identity. This identity may be their Aadhaar number an estoian EID and others that support digital signatures in order to prove owernship of such an id.

- ``Asset``: A physical asset, held as a ``Non-Fungible Token or NFT`` in the underlying `StandardAssetRegistry` contract. A property or piece or land is interpreted as an asset within the registry.

- ``Authority``: A party that has signing power of sale deeds. Every sale deed must posses a valid authority to approve the deed.

- ``Property``: A physical piece of land.  A plot of land will be referred to as a property at the application level and generically as an asset within the `StandardAssetRegistry`.

- ``Deed``: A sale deed that is associated with a specific property. A completely executed and approved sale deed is required in order to transfer pwnership of a property.

====

API
***

====

Accounts
========

Create Account
--------------
Create an account for a party. Creating a new digital account for the party, which is in fact a smart contract that will serve to interact directly with their assets held in the underlying asset registry.

- URL:
``/accounts``

- Method:
``POST``

- URL Params
**Required**

``None``

- Body
**Required**

.. code-block:: javascript

 /**
  * @param {Address} partyId Party to create an account for. This is the
  *                          person's real world Id, ie. Aadhaar number or estonian EID.
  */

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
    url: 'http://localhost:8080/accounts',
    'POST',
    json: true,
    body: {
      partyId: '0x26006236eab6409d9fdecb16ed841033d6b4a6bc',
    }
    simple: true
  })


Get Account by Party Id
-----------------------
Get the address of the on-chain account associated with a party, to be concrete this is the address of the party's ``Account`` smart contract.

- URL:
``/accounts/:partyId``

- Method:
``POST``

- URL Params
**Required**

.. code-block:: javascript

 /**
  * @param {Address} partyId Person's real world Id, ie. Aadhaar number or estonian EID, ethereum address at this time however for the PoC.
  */

- Success Response

.. code-block:: javascript

 /**
  * @return {Address} The party's on-chain Account address.
  */

.. code-block:: console

  statusCode: 200
  body: {
          account: '0xfc2b81a6f9dde1d20732a4651cc3d1526c17de1a'
        }

- Error Response

.. code-block:: console

  TODO

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/accounts/0x26006236eab6409d9fdecb16ed841033d6b4a6bc',
    'GET',
    json: true,
    simple: true
  })


Assets
======

Add Asset
---------
Adds a new asset to the ``StandardAssetRegistry`` contract.

- URL:
``/assets``

- Method:
``POST``

- Body
**Required**

.. code-block:: javascript

 /**
  * @param {Integer} assetId  Id of the new asset.
  * @param {Address} owner    Current owner of the asset, Account address.
  * @param {String}  dataHash File storage hash to look up raw data regarding the asset,
  */

- Success Response

.. code-block:: javascript

 /**
  * @returns {String} Transaction hash.
  */

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
    url: 'http://localhost:8080/assets',
    'POST',
    json: true,
    body: {
      assetId:  1,
      owner:    '0x26006236eab6409d9fdecb16ed841033d6b4a6bc',
      dataHash: 'QmSsV8J2KgynQ5DK1nRcKsTuxojFvEHmgmJwrU9vHWeyEL'
    }
    simple: true
  })

Get Asset By Id
---------------
Returns all on-chain information about a specific asset.

- URL:

``/assets/:assetId``

- Method:
``GET``

- URL Params
**Required**

.. code-block:: javascript

  /**
   * @param {Integer} assetId Id of the asset.
   */

- Success Response

.. code-block:: javascript

  /**
   * @returns {Object} All on-chain fields of the asset, owner and dataHash.
   */

.. code-block:: console

  statusCode: 200
  body: {
          owner:    '0x26006236eab6409d9fdecb16ed841033d6b4a6bc',
          dataHash: 'QmSsV8J2KgynQ5DK1nRcKsTuxojFvEHmgmJwrU9vHWeyEL'
        }

- Error Response

.. code-block:: console

  statusCode: 404
  error: {
            code:    'NotFound',
            message: 'Assets does not exist.'
         }

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/assets/1',
    'GET',
    json: true,
    simple: true
  })


Get All Assets
--------------
Returns all on-chain information about all existing assets in the ```StandardAssetRegistry`.

- URL:
``/assets``

- Method:
``GET``

- Success Response

.. code-block:: javascript

  /**
   * @returns {Object} All existing assets' on-chain fields.
   */

.. code-block:: console

  statusCode: 200
  body: {
          '2': {
                  owner:    '0x26006236eab6409d9fdecb16ed841033d6b4a6bc',
                  dataHash: 'QmSsV8J2KgynQ5DK1nRcKsTuxojFvEHmgmJwrU9vHWeyEL'
               },
          '3': {
                  owner:    '0xa8d5f39f3ccd4795b0e38feacb4f2ee22486ca44',
                  dataHash: 'QmSsV8J2KgynQ5DK1nRcKsTuxojFvEHmgmJwrU9vHWeyEL'
                }
          }

- Error Response

.. code-block:: console

  TODO

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/assets',
    'GET',
    json: true,
    simple: true
  })

Assets Exists
---------------
Returns bool if a specific asset exists within the ```StandardAssetRegistry`.

- URL:

``/assetExists/:assetId``

- Method:
``GET``

- URL Params
**Required**

.. code-block:: javascript

  /**
   * @param {Integer} assetId Id of the asset.
   */

- Success Response

.. code-block:: javascript

  /**
   * @returns {Boolean} If the asset exists.
   */

.. code-block:: console

  statusCode: 200
  body: {
          exists: true
        }

- Error Response

.. code-block:: console

  TODO

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/assetExists/1',
    'GET',
    json: true,
    simple: true
  })

====

Authorities
===========

Add Authority
-------------
Adds a new authority to the ``HaryanaLandRegistryProxy`` contract.

- URL:
``/authorities``

- Method:
``POST``

- Body
**Required**

.. code-block:: javascript

  /**
   * @param {Address} partyId Id of the party to add as an authority.
   */

- Success Response

.. code-block:: javascript

  /**
   * @returns {String} Transaction hash.
   */

.. code-block:: console

  statusCode: 200
  body: {
          txHash: '0x7f480f9b52ae4cfe8ff8b607c46c795482f9543264c1b61d7032715b1e1eb66e'
        }

- Error Response

.. code-block:: console

  TODO

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/authorities',
    'POST',
    json: true,
    body: {
      partyId: '0x627306090abab3a6e1400e9345bc60c78a8bef57',
    }
    simple: true
  })

====

Deeds
=====

Add Deed
---------
Adds a new deed to the ``HaryanaLandRegistryProxy`` contract.

- URL:
``/deeds``

- Method:
``POST``

- Body
**Required**

.. code-block:: javascript

  /**
   * @param {Integer} deedId     Id of the new deed.
   * @param {Address} buyer      Party buying the property.
   * @param {Address} seller     Party selling the property.
   * @param {Address} authority  Party required to approve sale deed.
   * @param {Integer} propertyId Id of an existing property this sale deed is associated with.
   * @param {String}  dataHash   File storage hash to look up raw data regarding the deed.
   */

- Success Response

.. code-block:: javascript

  /**
   * @returns {String} Transaction hash.
   */

.. code-block:: console

  statusCode: 200
  body: {
          txHash: '0x7f480f9b52ae4cfe8ff8b607c46c795482f9543264c1b61d7032715b1e1eb66e'
        }

- Error Response

.. code-block:: console

  TODO

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/deeds',
    'POST',
    json: true,
    body: {
      deedId:    1,
      buyer:     '0x627306090abab3a6e1400e9345bc60c78a8bef57',
      seller:    '0xf17f52151ebef6c7334fad080c5704d77216b732',
      authority: '0x26006236eab6409d9fdecb16ed841033d6b4a6bc',
      assetId:   1,
      dataHash:  'QmSsV8J2KgynQ5DK1nRcKsTuxojFvEHmgmJwrU9vHWeyEL'
    }
    simple: true
  })

Approve Deed
------------
Approve a deed that exists in the ``HaryanaLandRegistryProxy`` contract.

- URL:
``/aproveDeed``

- Method:
``PUT``

- Body
**Required**

.. code-block:: javascript

  /**
   * @param {Address} signer Id of party approving the deed, must be the authority associated with the deed.
   * @param {Integer} deedId Id of the deed being signed.
   * @param {String}  salt   Pseudo-randomness to generate unique sig. BigNumber.toFixed().
   * @param {Integer} v      Ethereum ecdsa signature variable, generally 27 or 28.
   * @param {String}  r      Standard ecdsa r variable, byte string, 32 bytes.
   * @param {String}  s      Standard ecdsa s variable, byte string, 32 bytes.
   */

- Success Response

.. code-block:: javascript

  /**
   * @returns {String} Transaction hash.
   */

.. code-block:: console

  statusCode: 200
  body: {
          txHash: '0x7f480f9b52ae4cfe8ff8b607c46c795482f9543264c1b61d7032715b1e1eb66e'
        }

- Error Response

.. code-block:: console

  TODO

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/approveDeed',
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

Get Deed By Id
---------------
Returns all on-chain information about a specific deed.

- URL:
``/deeds/:deedId``

- Method:
``GET``

- URL Params
**Required**

.. code-block:: javascript

  /**
   * @param {Integer} deedId Id of the deed.
   */

- Success Response

.. code-block:: javascript

  /**
   * @returns {Object} All on-chain fields of the deed struct.
   */

.. code-block:: console

  statusCode: 200
  body: {
          signOff:   0,
          buyer:     '0x627306090abab3a6e1400e9345bc60c78a8bef57',
          seller:    '0xf17f52151ebef6c7334fad080c5704d77216b732',
          authority: '0xc5fdf4076b8f3a5357c5e395ab970b5b54098fef',
          assetId:   1,
          dataHash:  'QmSsV8J2KgynQ5DK1nRcKsTuxojFvEHmgmJwrU9vHWeyEL',
          state:     0,
        }


- Error Response

.. code-block:: console

  statusCode: 404
  error: {
            code:    'NotFound',
            message: 'Deed does not exist.'
         }

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/deeds/1',
    'GET',
    json: true,
    simple: true
  })

Get Deed By Property Id
-----------------------
Returns all on-chain deed information associated with the given property.

- URL:
``/getDeedsByProperty/:propertyId'``

- Method:
``GET``

- URL Params
**Required**

.. code-block:: javascript

  /**
   * @param {Integer} propertyId Id of the property.
   */

- Success Response

.. code-block:: javascript

  /**
   * @returns {Array} Array of objects containing all on-chain data for all associated deeds.
   */

.. code-block:: console

  statusCode: 200
  body: {
          '5': {
                  signOff:   0,
                  buyer:     '0xa8d5f39f3ccd4795b0e38feacb4f2ee22486ca44',
                  seller:    '0x3596ddf5181c9f6aa1bce87d967bf227dde70ddf',
                  authority: '0x26006236eab6409d9fdecb16ed841033d6b4a6bc',
                  assetId:   5,
                  dataHash:  'QmSsV8J2KgynQ5DK1nRcKsTuxojFvEHmgmJwrU9vHWeyEL',
                  state:     0
                },
        }


- Error Response

.. code-block:: console

  TODO

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/getDeedsByProperty/5',
    'GET',
    json: true,
    simple: true
  })

Sign Deed
---------
Sign a deed that exists in the ``HaryanaLandRegistryProxy`` contract.

- URL:
``/signDeed``

- Method:
``PUT``

- Body
**Required**

.. code-block:: javascript

  /**
   * @param {Address} signer Id of party signing the deed.
   * @param {Integer} deedId Id of the deed being signed.
   * @param {String}  salt   Pseudo-randomness to generate unique sig. BigNumber.toFixed().
   * @param {Integer} v      Ethereum ecdsa signature variable, generally 27 or 28.
   * @param {String}  r      Standard ecdsa r variable, byte string, 32 bytes.
   * @param {String}  s      Standard ecdsa s variable, byte string, 32 bytes.
   */

- Success Response

.. code-block:: javascript

  /**
   * @returns {String} Transaction hash.
   */

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
    url: 'http://localhost:8080/signDeed',
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

Set Metadata of Deed
---------
Set the meta data of a deed within the ``HaryanaLandRegistryProxy`` contract.

- URL:
``/setDeedMetaData/:deedId/:dataHash``

- Method:
``PUT``

- Body
**Required**

.. code-block:: javascript

  /**
   * @param {String}  deedId      Id of the deed which the datahash is being assigned to.
   * @param {String}  dataHash    Datahash that points to the encrypted IPFS artifact.
   */

- Success Response

.. code-block:: javascript

  /**
   * @returns {String} Transaction hash.
   */

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
    url: 'http://localhost:8080/setDeedMetaData/7/QmSsV8J2KgynQ5DK1nRcKsTuxojFvEHmgmJwrU9vHWeyEL',
    'PUT',
    json: true,
    simple: true
  })

====

Utils
=====

Get Transaction Receipt After Confirmations
-------------------------------------------
Returns the transaction receipt after waiting a specified amount of confirmations.

- URL:
``/transactionReceipt/:txHash/:confirmations``

- Method:
``GET``

- URL Params
**Required**

.. code-block:: javascript

  /**
   * @param {String}  txHash         Hash of the transaction.
   * @param {Integer} confirmations  Number of confirmations to wait for before returning.
   */

- Success Response

.. code-block:: javascript

  /**
   * @returns {Object} Transaction receipt, rawRecipt and human readable logs.
   */

.. code-block:: console

  statusCode: 200
  body: receipt {
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

**Transaction does not exist.**

.. code-block:: console

  statusCode: 404
  error: {
            code:    'NotFound',
            message: 'The txHash: 0x44e399febf4e83ee4e8b3ea740a59bc2f6d7f8855c3c01120c07d7a64b65256f does not exist.'
         }

**Transaction was re-orged out.**

.. code-block:: console

  statusCode: 404
  error: {
            code:    'NotFound',
            message: 'The txHash: 0x44e399febf4e83ee4e8b3ea740a59bc2f6d7f8855c3c01120c07d7a64b65256f has been re-orged out and is no longer included.'
         }

- Sample Call

.. code-block:: javascript

  const rp = require('request-promise')
  const respone = await rp({
    url: 'http://localhost:8080/transactionReceipt/0x44e399febf4e83ee4e8b3ea740a59bc2f6d7f8855c3c01120c07d7a64b65256f/12',
    'GET',
    json: true,
    simple: true
  })

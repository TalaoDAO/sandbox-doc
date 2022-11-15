Beacon integration
==================

Overview
--------

Beacon is an awasome protocol to allow wallets and dApps to exchange data and sign transactions. All you need about Beacon is available there : https://docs.walletbeacon.io/

Integration with Beacon is extremely simple and you can test it in minutes. To initiate the protocol, it suffices to send a payload of RAW type to the signature of the wallet.

This payload will contain a message to your liking and an URL that you will obtain on the Altme Sandbox platform : https://talao.co

The URL is given just after the fragment "#" with no space. Example with a RAW message :

.. code-block:: javascript

    'Get your Welcome card ! #https://talao.co/sandbox/op/beacon/ormmcdomjv'
               

User will be asked to sign the payload as usual but just after Altme wallet will start a process to request a credential to an issuer or present a credential to a verifier. 
User will be asked to accept/reject the credential or select credentials. Those verifiable credentials (https://www.w3.org/TR/vc-data-model/) are json signed file stored in the smartphone. 
A verifiable credential of a natural person (user) is stored off-chain as it is personal data. The subject of the credential is the Decentralized Identity attached to the wallet.
This identity is protected by a private key stored in the wallet aside the crypto privates keys.

Basically Beacon is used to initiate a Self Sovereign Identity standard protocol to request or present verifiable credentials to issuers or verifiers. Other protocols like OpenId 4 SSI or WACI exist.



Verify the age of your users (+13, +18) in a dApp
-------------------------------------------------


Access to NFT marketplaces is legitimately limited to children. Controlling the age of your users is fundamental. With Altme you have a quick solution that is easy to set up.

Thuse "cards" are verifiable credentials and you will need to setup a Verifier.

For that exemple we are going to use the Verifier Over 13 and Over 18 which are available on the Sandbox platform (Beacon Verifier).

After pairing with the wallet, the dApp code to launch that request is simple : 


.. image:: over18-13.png



Here are the calls to integrate in your dApp through a payload request for signature (both examples available on https://talao.co):


Example of an Over13 check  :

* verifier id : tuaitvcrkl 
* verifier secret : d461d33c-550f-11ed-90f5-0a1628958560

.. code-block:: javascript

    const signature = await client.requestSignPayload({
          signingType: beacon.SigningType.Raw,
          payload: 'I am over 13 years old#https://talao.co/sandbox/op/beacon/verifier/tuaitvcrkl'
               })


Example of an Over18 check 


* verifier id : jvlfopeogt
* verifier secret : c8f90f24-5506-11ed-b15e-0a1628958560


.. code-block:: javascript

    const signature = await client.requestSignPayload({
          signingType: beacon.SigningType.RAW,
          payload: 'I am over 18 years old#https://talao.co/sandbox/op/beacon/verifier/jvlfopeogt'
               })


The user will be asked to prove their age with a credential.

You can set up your own verifiers to receive data or verify your users' credentials.


Receive the Verifier data with a webhook in your backend
--------------------------------------------------------- 

If you want to receive the data in your backend, create a webhook and copy the URL of the webhook in the page ("Webhook URL of your application").

Below an example of a webhook code in python :


.. code-block:: python

    from flask import Flask, jsonify, request

    app = Flask(__name__)
    app.config.update(SECRET_KEY = "abcdefgh") # Flask key
    verifier_secret = 'c8f90f24-5506-11ed-b15e-0a1628958560' # take the client_secret from the platform https://talao.co
    
    @app.route('/webhook', methods=['POST'])
    def dapp_webhook() :
        if request.headers.get('key') != verifier_secret :
            return jsonify('Forbidden'), 403
        data = request.get_json()
        if data['event'] == 'VERIFICATION' :
            print(data)
            return jsonify('ok')
    
    if __name__ == '__main__': #  use Gunicordn for production
        IP = "127.0.0.1"
        app.run( host = IP, port=4000, debug =True)


The webhook function tests the request key against the verifier_secret and gets the json data transfered by the verifier with the event 'VERIFICATION'.


Verify other data with other credentials
----------------------------------------

Many credentials are today available for user onboardings :

* over 13, over 18
* Age range
* Nationality
* ID card
* Passport number (hash)
* Email proof
* Phone proof
* Company pass


Issue a Welcome card in a dApp
------------------------------

You will need to create an Issuer.  

That card is a verifiable credential and you will need to get an Issuer. This card can be used as a Pass or to gives advantages ans discounts to your users as for instance a loyalty card.

For that exemple we are going to use the Issuer "Example 1" which is available on the Sandbox platform.

After pairing with the wallet, the dApp code to launch that request is simple : 

.. code-block:: javascript

    const signature = await client.requestSignPayload({
          signingType: beacon.SigningType.RAW,
          payload: 'Get your Welcome card ! #https://talao.co/sandbox/op/beacon/ormmcdomjv'
               })


Example of a Welcome credential card received in Altme :


.. image:: welcome_card.jpg
      :width: 200
    

You can find the Welcome card parameters (payload and client_secret) on the platform https://talao.co/sandbox. After login, choose the Beacon Issuer integration option.

Make a copy of this issuer and generates your own issuer. You can check the process and design of the credential card with the test button.


.. image:: sandbox_2.png

Check user data of your Issuer (no code)
----------------------------------------

You can have a look at all connexions data to your issuer with no code : click on the Activity button bottom down to get the list of connexions and the data sent by users.
You will get the address of your users.

Check user data of your Issuer with a webhook
---------------------------------------------- 

If you want to receive the data in your backend, create a webhook and copy the URL of the webhook in the page ("Webhook URL of your application").

Below an example of a webhook code in python :


.. code-block:: python

    from flask import Flask, jsonify, request

    app = Flask(__name__)
    app.config.update(SECRET_KEY = "abcdefgh")
    issuer_secret = '5be650e6-5226-11ed-8298-0a1628958560'
    
    @app.route('/webhook', methods=['POST'])
    def dapp_webhook() :
        if request.headers.get('key') != issuer_secret :
            return jsonify('Forbidden'), 403
        data = request.get_json()
        if data['event'] == 'ISSUANCE' :
            print(data)
            return jsonify('ok')
    
    if __name__ == '__main__':  # use Gunicorn for production
        IP = "127.0.0.1"
        app.run( host = IP, port=4000, debug =True)


The webhook tests the request against the issuer secret and gets the data transfered by the user as a json strucure with the event 'ISSUANCE'


Under the hood : the process flow of a Beacon Verifier
------------------------------------------------------

This is the most common use case because most web3 applications already have centralized management of their users.
It is likely that the application also keeps track of users' data in its local database for later use of the data (CRM).

Application is client/server with dApp features as SPA

There are 3 protocols that are used in these interactions:

* Wallet -Application: it is the synchronization between a crypto wallet and a dApp. We use Beacon in this example.   
* Wallet - Verifier: this is the protocol that makes it possible to transfer a credential from the wallet to a verifier. We use verifiable presentation request.  
* Verifier - dApp : As the dApp has a local server,  we use an application webhook.  



.. image:: hybrid_onboard_user_with_beacon.png

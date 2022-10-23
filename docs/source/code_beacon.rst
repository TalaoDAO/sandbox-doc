Integrate an issuer with Beacon 
===============================

Overview
--------

Beacon is an awasome protocol to allow wallets and dApps to exchange data and sign transactions. All you need about Beacon is available there : https://docs.walletbeacon.io/

Integration with Beacon is extremely simple and you can test it in minutes. To initiate the protocol, it suffices to send a payload of RAW type to the signature of the wallet.
This payload will contain a message to your liking and a URL that you will obtain on the Altme Sandbox platform. 

The URL is given after the fragment (#). 

Example of a payload :


.. code-block:: javascript

    'Get your Welcome card ! #https://talao.co/sandbox/op/beacon/ormmcdomjv?issuer=did:ethr:0x64098e894fea5b83e7e4c52a30d70b98e25bd9d5'

The message is your choice, you will see it on the wallet screen when user is asked to confirm and sign the message.


Example of a Welcome credential card received in Altme :


.. image:: welcome_card.jpg
      :width: 200


The dApp code to launch the issuer : 

.. code-block:: javascript

    const signature = await client.requestSignPayload({
          signingType: beacon.SigningType.RAW,
          payload: 'Get your Welcome card ! #https://talao.co/sandbox/op/beacon/ormmcdomjv?issuer=did:ethr:0x64098e894fea5b83e7e4c52a30d70b98e25bd9d5'
               })
    

You can find the Welcome card parameters on the platform https://talao.co/sandbox. After login, choose the Beacon integration option.

Make a copy of this issuer and generates your own issuer of verifiable credential.  

You can check the design of the credential card with the test button.




.. image:: sandbox_1.png

Get user data with no code
--------------------------

With no code you can have a look at all connexions : click on the Activity button bottom down to get the list of connexions and the data sent by the user. You will get the address of your users.



Get user data with a webhook
----------------------------- 

If you want to receive the data in you rbackend, open a webhook and copy the URL of the webhook in the page.

Below an example of a webhook in python with flask :


.. code-block:: python

    from flask import Flask, jsonify, render_template, request

    app = Flask(__name__)
    app.config.update(SECRET_KEY = "abcdefgh")

    def dapp_wallet():
        return render_template('dapp.html')
    
    def dapp_webhook() :
        if request.headers.get("key") != client_secret :
            return jsonify("Forbidden"), 403
        data = request.get_json()
        if data['event'] == 'ISSUANCE' :
            print(data)
            return jsonify('ok')
    
    if __name__ == '__main__':
        IP = "127.0.0.1"
        app.run( host = IP, port=4000, debug =True)


Lets explain , the first function display the dApp code. The second tests the request agauinst the client_secret and gets the data transfered by the user !



Check the age of your users (+13, +18)
-------------------------------------

Access to NFT marketplaces is legitimately limited to children. Controlling the age of your users is fundamental. With Altme you have a quick solution that is easy to set up.

Here is the call to integrate in your dApp :


.. code-block:: javascript

    const signature = await client.requestSignPayload({
          signingType: beacon.SigningType.RAW,
          payload: ' I am over 13 #https://talao.co/sandbox/op/beacon/fzwiygzgii?issuer=did:tz:tz1NyjrTUNxDpPaqNZ84ipGELAcTWYg6s5Du
               })


User will be requested to prove is age. If user has the over13 credential he will be able to get the credential card and your backend webhook will receive a call with his address as verified.



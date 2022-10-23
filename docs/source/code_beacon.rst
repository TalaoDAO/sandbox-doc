Integrate an issuer with Beacon 
===============================

Overview
--------

Integration with Beacon is extremely simple and you can test it in minutes. To initiate the protocol, it suffices to send a payload of RAW type to the signature of the wallet.
This payload will contain a message to your liking and a URL that you will obtain on the Altme Sandbo platform. The URL is given after the fragment (#).

The message is your choice, you will see it on the wallet screen.


Example of a dApp code :


.. code-block:: javascript

    const signature = await client.requestSignPayload({
          signingType: beacon.SigningType.RAW,
          payload: 'Get your Welcome card ! #https://talao.co/sandbox/op/beacon/ormmcdomjv?issuer=did:ethr:0x64098e894fea5b83e7e4c52a30d70b98e25bd9d5'
               })
    

You can find the Welcome card parameters on the platform https://talao.co/sandbox. After login, choose the Beacon integration option.

Make copy of this issuer and generates your own verifiable credential.



.. image:: sandbox_1.png

Get the data from the user with no code
---------------------------------------

With no code you can have a look at the connexion, click on teh Activity button bottom down to get the list of connexions and eth date sent by the user.



Get the data from the user with a webhook
------------------------------------------ 

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

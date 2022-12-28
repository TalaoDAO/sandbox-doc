
.. _openid:

Altme Saas : OpenID integration
================================


Overview
--------

The best way to integrate a powerful verifier to your code is to use an OpenID Connect lib. A list of the most know libs are there https://openid.net/developers/certified/.

The Almte platform uses an authentication code flow or an implicit code flow. For dApp you can setup the PKCE option.


Example 1 with an "implicit flow"  with no code
-------------------------------------------------

This is simple integration for a CMS like Webflow or a Single Page Application in JS or a dApp.
For this first example, you need the Altme Wallet and an Email Proof as a verifiable credential.

Later you could check the verifier data and change the parameters and landing page style in minutes on https://talao.co. The example below is the verifier named "Example 1" in the select list.
Make a copy of that verifier if you want to change the parameters.

So let's try this verifier. 

You can call it from a button link with a standard <a href=...> balise or you can just copy this link in your browser :

https://talao.co/sandbox/op/authorize?client_id=xgdfdbzwri&response_type=id_token&redirect_uri=https://altme.io

Replace if needed the redirect_uri argument by your own website.

You will be requested to present an email proof and then you will received an id_token like that one below in your browser (id_token=eyJhbGciO.....): 

eyJhbGciOiJSUzI1NiIsImtpZCI6IjEyMyIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJ4Z2RmZGJ6d3JpIiwiZW1haWwiOiJ0aGllcnJ5QGFsdG1lLmlvIiwiZXhwIjoxNjY2MTk4M
jczLjY2ODQ5MiwiaWF0IjoxNjY2MTk3MjczLjY2ODQ4NCwiaXNzIjoiaHR0cHM6Ly90YWxhby5jby9zYW5kYm94L29wIiwibm9uY2UiOm51bGwsInN1YiI6ImRpZDprZXk6ejZN
a3R1d0x2U1VZZUplV0pnRHZKNlJmdFR2eVJTb05SR1h4Y2tlRTQ0cVRLa0tWIiwidXBkYXRlZF9hdCI6MTY2NTg0Mjc1MS4wfQ.MdWtxU8CjgSHfRgwUQWMMvhCLdgHzwQolWFwhtx
-i3WJpZowwtpKSxh4smGPPhtBr4JPGYFAhjklZ2ojbW64VS8S07TNh3Q75TrgixAVguYnvsWCh5CLcxBRlcFW7iyWFm1WZ9VtNqXvlIL50tbfSQnpkcGVKbg2dmUKmuvUVDE1
qoJDedz7YKL3qWobkxzsrUlDGTKLvOHKdU3kaXnkGmiVxpZtwbIhE3pa27CjGFKAMX4rXOuiJ5N81LDX06GjV9KU4a4T5J2Bd2tgfvafjhJuTQD-1MaTWJMu_ZTiHEjr4PTqYe_C
olYS2a_Ougwk2FTQ6dKIQWClwhCZ-QfWuw

As you will see in an implicit flow, the id_token is passed as a fragment (#), you can intercept it with JS in your page.

Copy this id_token and go to https://jwt.io , paste the id_token value in the left side and you will get the following json data decrypted  :


.. code-block:: JSON

    {
        "aud": "xgdfdbzwri",
        "email": "thierry@altme.io",
        "exp": 1666198273.668492,
        "iat": 1666197273.668484,
        "iss": "https://talao.co/sandbox/op",
        "nonce": null,
        "sub": "did:key:z6MktuwLvSUYeJeWJgDvJ6RftTvyRSoNRGXxckeE44qTKkKV",
        "updated_at": 1665842751
    }

* "sub" is the subject ID, in our case it is the DID of the user (walletr Decentralized IDentifier). You can check that DID in your wallet settiongs !
* "aud" is the issuer URL in the OpenID format
* "email" is the main data of your verifiable credential


Based on that  example you can request different types of credentials to authenticate your users as :

* Over 13
* Over 18
* Phone proof
* Gender proof
* Nationality proof
* Blockchain address
* ID card
* Custom credentials...

To make your own verifier, you just need to go to the plateform https://talao.co and to copy the example and configure the landing pasge.

More secured options are available with an OpenID authorization code flow.

Example 1 with an "authorization code flow" in Python
------------------------------------------------------

This a typical integration with a client-server application.

We use the flask-pyoic lib , https://flask-pyoidc.readthedocs.io/en/latest/quickstart.html

Install with pip install Flask-pyoidc==3.11.0

This verifier parameters for an authorization code flow are :

* issuer : https://talao.co/sandbox/op  
* client_id : xgdfdbzwri  
* client_secret : 0b80ec35-1941-11ed-a869-0a1628958560  

Let's see the code.

NB : you may need to setup ngrok to get the callback if you cannot install it on a internet web server.

.. code-block:: python

    import flask
    from flask import Flask, jsonify
    from flask_pyoidc import OIDCAuthentication
    from flask_pyoidc.provider_configuration import ProviderConfiguration, ClientMetadata
    from flask_pyoidc.user_session import UserSession

    # Init Flask
    app = Flask(__name__)
    app.config.update(
        OIDC_REDIRECT_URI = 'http://127.0.0.1:4000/callback', # your application redirect uri. Must be replaced by ngrok route if local to your desktop
        SECRET_KEY = "lkjhlkjh" # your application secret code for session, random
    )

    """
    Init OpenID Connect client PYOIDC with teh 3 bridge parameters :  client_id, client_secret and issuer URL
    """
    client_metadata = ClientMetadata(
        client_id='xgdfdbzwri',
        client_secret= '0b80ec35-1941-11ed-a869-0a1628958560',
        post_logout_redirect_uris=['http://127.0.0.1:4000/logout']) # your post logout uri (optional)

    provider_config = ProviderConfiguration(issuer='https://talao.co/sandbox/op',
                                        client_metadata=client_metadata)

    auth = OIDCAuthentication({'default': provider_config}, app)

    """ 
    Verifiable Credential presented by user is transfered through vp_token in OAuth2 userinfo endpoint

    """
    @app.route('/')
    @auth.oidc_auth('default')
    def index():
        user_session = UserSession(flask.session)
        return jsonify(access_token=user_session.access_token,
                   id_token=user_session.id_token,
                   userinfo=user_session.userinfo) # this is the user credential

    # use with ngrok
    if __name__ == '__main__':
        IP = "127.0.0.1"
        app.run( host = IP, port=4000, debug =True)9692-0a1628958560 



NB : with that lib, you do not have to manage the id_token, user info and the signature check.
All metada needed by the lib is available in the openid config :
https://talao.co/sandbox/op/.well-known/openid-configuration  



    
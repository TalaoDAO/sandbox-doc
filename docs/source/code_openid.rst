OpenID integration
==================


Overview
--------

The best way to integrate a powerful verifier to your code is to use an OpenID Connect lib. A list of the most know libs are there https://openid.net/developers/certified/.

The Almte platform uses an authentication code flow or an implicit code flow. For dApp you can setup the PKCE option.


Example 1 with implicit flow with no code 
-----------------------------------------

This is simple integration for a CMS like Webflow or a Single Page Application like dApp.

For this first example, you need the Altme wallet and an email proof as a verifiable credential.
You can check the verifier data and change the parameters and landing page style in minutes on https://talao.co. THe example below is the verifier named "Example 1" in the select list.
Make a copy of that verifier if you want to change the parameters.

Let's try the verifier you can call from a button link, we are going to use the implicit flow to get an ID token in the browser with no code.
That type of integration is ok for CMS as webflow for instance :

https://talao.co/sandbox/op/authorize?client_id=xgdfdbzwri&response_type=id_token&redirect_uri=https://altme.io

NB : This URL is available in the menu "Webflow" of the verifier configuration page.

You will be requested to present an email proof and then you will received an id_token like that one in your browser (id_token=eyJhbGciO.....): 

eyJhbGciOiJSUzI1NiIsImtpZCI6IjEyMyIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJ4Z2RmZGJ6d3JpIiwiZW1haWwiOiJ0aGllcnJ5QGFsdG1lLmlvIiwiZXhwIjoxNjY2MTk4M
jczLjY2ODQ5MiwiaWF0IjoxNjY2MTk3MjczLjY2ODQ4NCwiaXNzIjoiaHR0cHM6Ly90YWxhby5jby9zYW5kYm94L29wIiwibm9uY2UiOm51bGwsInN1YiI6ImRpZDprZXk6ejZN
a3R1d0x2U1VZZUplV0pnRHZKNlJmdFR2eVJTb05SR1h4Y2tlRTQ0cVRLa0tWIiwidXBkYXRlZF9hdCI6MTY2NTg0Mjc1MS4wfQ.MdWtxU8CjgSHfRgwUQWMMvhCLdgHzwQolWFwhtx
-i3WJpZowwtpKSxh4smGPPhtBr4JPGYFAhjklZ2ojbW64VS8S07TNh3Q75TrgixAVguYnvsWCh5CLcxBRlcFW7iyWFm1WZ9VtNqXvlIL50tbfSQnpkcGVKbg2dmUKmuvUVDE1
qoJDedz7YKL3qWobkxzsrUlDGTKLvOHKdU3kaXnkGmiVxpZtwbIhE3pa27CjGFKAMX4rXOuiJ5N81LDX06GjV9KU4a4T5J2Bd2tgfvafjhJuTQD-1MaTWJMu_ZTiHEjr4PTqYe_C
olYS2a_Ougwk2FTQ6dKIQWClwhCZ-QfWuw

Go to https://jwt.io and paste the id_token value in the left side and you will get the following json data  :


.. code-block:: json

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

* "sub" is the subject ID, in our case it is the DID of the user (walletr Decentralized IDentifier). You can check that DID in your wallet settiongs.
* "aud" is the issuer URL in the OpenID format
* "email" is the main data of your verifiable credential


Based on that  example you can request different types of credentials to authenticate your users as :

- Over 13/18 yo proof
- Phone proof
- Gender proof
- Nationality proof
- Blockchain address
- KYC 
- etc...

To make your own verifier, you just need to go to the plateform https://talao.co and to copy the example and configure the landing pasge.


Example 1 with authorization code flow in Python
------------------------------------------------

TYpical integration with an application which has a backend.

We use the flask-pyoic lib , https://flask-pyoidc.readthedocs.io/en/latest/quickstart.html

Install with pip install Flask-pyoidc==3.11.0

Parameters for an authorization code flow are :

* issuer : https://talao.co/sandbox/op  
* client_id : xgdfdbzwri  
* client_secret : 0b80ec35-1941-11ed-a869-0a1628958560  


.. code-block:: python

    import flask
    from flask import Flask, jsonify
    from flask_pyoidc import OIDCAuthentication
    from flask_pyoidc.provider_configuration import ProviderConfiguration, ClientMetadata
    from flask_pyoidc.user_session import UserSession

    # Init Flask
    app = Flask(__name__)
    app.config.update(
        OIDC_REDIRECT_URI = 'http://127.0.0.1:4000/callback', # your application redirect uri. Must not be used in your code
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

    if __name__ == '__main__':
        IP = "127.0.0.1"
        app.run( host = IP, port=4000, debug =True)9692-0a1628958560 



NB : with that lib, you do not have to manage the id_token, user info and the signature check.
All metada needed by the lib is available in the openid config :
https://talao.co/sandbox/op/.well-known/openid-configuration  



    
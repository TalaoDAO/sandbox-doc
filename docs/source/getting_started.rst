Quick start
===========

You are now going to create your own Decentralized Identity and first SSI verifier for your web application.

Download Altme Wallet from Apple or Google store
--------------------------------------------------

To set your Decentralized Identity, you first need to use the AltMe Wallet which supports Identity and crypto features. At the end of this process you will have your own Decentralized Identity (DID).

 1) Go to `Apple store <https://apps.apple.com/fr/app/altme/id1633216869>`_ or `Google store <https://play.google.com/store/apps/details?id=co.altme.alt.me.altme>`_ to get the app installed on your smartphone.
 2) Launch the app, you can create a wallet as a crypto wallet setup with a new passphrase or new addresses or you can create a wallet through a passphrase import from your prefered crypto wallet. If you do so you should see all your cryptos and NFTs as any standard crypto wallet.
 3) After having installed and setup your wallet, on the Discover screen click to get an Email Proof blue card.
 4) Enter your email, enter the secret code received in your email box and follow the instructions on the mobile wallet screens. 

You have now recieved your first verifiable credential.

You can also display your own Decentralized Identifier (DID) in the menu Settings/Show Decentralized ID. It starts with "did:key". Your DID is attached to a private key. 

Get more credentials in your wallet
------------------------------------

Go to the Discover page and you will have a list of credntials you can collect. Some of them needs a KYC process with a liveness test and a check of your documents.
Those data are only stored in your phone, no data are on chain. Like your wallet in your pocket you own your data and assets all associated to your private keys.

You have now one private key for your decentralized identity and private keys for your crypto accounts. To keep it simple they are all attached to the same "passphrase" (12 words) so you just need
to copy this passphrase on a paper sheet for safety. Do it as soon as you can ! 

Connect to the Altme Saas platform to setup a SSI verifier
-----------------------------------------------------------

 1) Go to https://talao.co and click on "Sign-up" in the navigation bar.
 2) Open your Altme Wallet and scan the QR code with the scanner of the mobile app Altme,  you will be requested to present your Email Proof.
 3) Choose the option "Create a verifier with OpenID".
 4) You can now create a new Verifier , click on "Create" in the navigation bar or "Copy" an existing verifier.

Your first verifier has been setup for you.

Integration in your app
-------------------------

To add a Login buttonn in your app to onboard users with a verifiable credential
go to the OpenID Integration section :ref:`openid`
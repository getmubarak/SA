
![alt text](https://github.com/getmubarak/SA/blob/master/views/Security%20View/Authentication/Authentication.png)

# 5 Factors of Authentication
- Something you know factor (pwd, key, pin, secret) 
- Something you have factor (otp, cert, email, RSA token)
- Something you are factor  (retina, voice, finger print, face, dna)
- Somewhere the user is factor (Location Factor)
- Something You DO factor (behavioral characteristics)

# Two factor/ Multi factor :  
- using a smart card and a PIN
- if a user were required to enter a password and a PIN, it would not be multifactor authentication since both methods are from the same factor

# Decision
- If you are writing a new application: use OpenID Connect
- * OpenID Connect is a rewrite of SAML using OAuth 2.0.  ODIC is designed with web and mobile applications in mind. 

- If the application already supports SAML: use SAML.
- * SAML has a long track record of providing a secure means of identity data exchange, so it is trusted by many organizations. 

- If you need rich Identity features: use SAML
- * SAML is very feature-rich, covering a wide range of identity requirements.OIDC, being newer and evolving (especially in the European banking sector, with Open Banking), is still lagging behind SAML in terms of features.

# Oauth2 vs OpenID connect
- OAuth2 is not for authorization. 
- OAuth2 is not for authentication
- OAuth2 is also not for federation.
- OAuth2 is an delegation protocol
- OpenID Connect is an authentication protocol 


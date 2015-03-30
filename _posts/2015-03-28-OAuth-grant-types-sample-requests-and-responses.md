---
layout: post
title: OAuth Grant Types - Sample Requests and Responses
tags: identity, OAuth, API, Security

---
This post briefly explains what messages being passed as requests and responses when interacting with different OAuth grant types.The main intention of this post is not to give in depth knowledge about what is going beneath the OAuth grant types theoretically but to provide a reference on message formats requires for the interaction.

<h2>1. Authorization Code</h2>
An agent (eg:- browser) should be present for this grant type because it is based on redirections and obtaining access token consists of two steps in this grant type. Here is the flow,

Get the Authorization Code
               
In order to get the authorization code browser redirection should be done to the AUTHORIZE endpoint url with following query parameters.

scope=openid
response_type=code
redirect_uri=<Redirect URL registerd with IDP>
client_id=<ClientID registered for the application>
Sample Request :- 

https://identity-stg.idp.com/i/oauth2/authorize?scope=openid&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%3A8080%2Fplayground%2Foauth2client&client_id=WCD9DsSDxBSZDsfTkSR2rPAQqWIa

One this is being hit the user’s agent (browser) will be redirected to the login screen based on the login mechanism configured. Upon successfully providing the username and password user will be reclient_iddirect back to the redirect url given in above url with the authorization code.

Sample Response :- 

http://localhost:8080/playground/oauth2client?code=6fb9f63887e3f11dde5becb954afa013


2. Get the access token 

To obtain the access token hit the token endpoint with a POST request.


Headers :- 
    Authorization : Basic <Base64 encoded “ClientID:ClientSecret”>
	   Accept: application/json

Post Parameters :- 
	  grant_type=authorization_code // This a static value
	  code=<authrization_code>
	  redirect_uri=<Redirect URL registerd with IDP>
	  tenantDomain=idp.com // This a static value

Sample Request

POST /i/oauth2/token HTTP/1.1
Host: identity-dev1.idp.com
Authorization: Basic TE9pUmRvcDdma042V3FzNnJZREJEWFhGREtNYTpuX1hkbko1RjI2VUY5OWI2UFVRU1ZTOHRGSEFh
Content-Type: application/x-www-form-urlencoded
Accept: application/json
Cache-Control: no-cache

grant_type=authorization_code&code=e5fcb0b9076cbc23b52e4112bf23025&redirect_uri=http://localhost:8080/playground/oauth2client&tenantDomain=idp.com

A JSON response will be received including following main components,
access_token, refresh_token, expires_in, id_token (JWT token)

Sample Response :-

{"scope":"openid","token_type":"bearer","expires_in":50400,"refresh_token":"9b7efad9816cf417891c79d8bc599cc9","id_token":"eyJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJzaGFtaWthMDAxQHRyaW1ibGUuY29tIiwiYXpwIjoiSFlqbmwwcFZmZ2dtNWZiTkhQNnV5TDVNYmE0YSIsImh0dHA6XC9cL3dzbzIub3JnXC9jbGFpbXNcL2xvY2FsaXR5IjoiZW4iLCJodHRwOlwvXC93c28yLm9yZ1wvY2xhaW1zXC9maXJzdG5hbWUiOiJBa2lsYTIiLCJhdF9oYXNoIjoiWTJGa05qTmtObVl5WmpZek9ETTFaREJsTmpJeFl6Qm1ZakkwTm1JMll3PT0iLCJodHRwOlwvXC93c28yLm9yZ1wvY2xhaW1zXC9lbnRlcnByaXNlZmVkZXJhdGVkc291cmNlIjoiZ29vZ2xlLmNvbSIsImlzcyI6Imh0dHBzOlwvXC9pZGVudGl0eS1kZXYxLnRyaW1ibGUuY29tXC90b2tlbiIsImh0dHA6XC9cL3dzbzIub3JnXC9jbGFpbXNcL3RlbGVwaG9uZSI6IjgxMjYwNjM0MzEiLCJodHRwOlwvXC93c28yLm9yZ1wvY2xhaW1zXC9sYXN0bmFtZSI6IkJhbGFzb29yaXlhIiwiaHR0cDpcL1wvd3NvMi5vcmdcL2NsYWltc1wvdXVpZCI6ImQ3MmIzZDFjLTg4Y2MtNGVmNy04NGVlLWQ2OGM4NmVjYmY1ZiIsImlhdCI6MTQyNzc0NzU4NTA1NiwiaHR0cDpcL1wvd3NvMi5vcmdcL2NsYWltc1wvc3RhdGVvcnByb3ZpbmNlIjoiSW5kaWFuYSIsImh0dHA6XC9cL3dzbzIub3JnXC9jbGFpbXNcL2Z1bGxuYW1lIjoiU2hhbWlrYSIsImh0dHA6XC9cL3dzbzIub3JnXC9jbGFpbXNcL2dpdmVubmFtZSI6IkFraWxhMiIsImF1dGhfdGltZSI6MTQyNzc0NzU4NTA1MiwiZXhwIjoxNDI3NzUxMTg1MDU2LCJodHRwOlwvXC93c28yLm9yZ1wvY2xhaW1zXC9nZW5kZXIiOiJNYWxlIiwiaHR0cDpcL1wvd3NvMi5vcmdcL2NsYWltc1wvc3RyZWV0YWRkcmVzcyI6IjExNSIsImh0dHA6XC9cL3dzbzIub3JnXC9jbGFpbXNcL2NvdW50cnkiOiJVbml0ZWQgU3RhdGVzIiwiYXVkIjpbIkhZam5sMHBWZmdnbTVmYk5IUDZ1eUw1TWJhNGEiXSwiaHR0cDpcL1wvd3NvMi5vcmdcL2NsYWltc1wvdGl0bGUiOiJtciIsImh0dHA6XC9cL3dzbzIub3JnXC9jbGFpbXNcL3JvbGUiOiJEZXZvcHNBZG1pbixJbnRlcm5hbFwvZXZlcnlvbmUiLCJodHRwOlwvXC93c28yLm9yZ1wvY2xhaW1zXC9lbWFpbGFkZHJlc3MiOiJ1c2hpMzI0QGdtYWlsLmNvbSIsImh0dHA6XC9cL3dzbzIub3JnXC9jbGFpbXNcL29yZ2FuaXphdGlvbiI6IldTTzIifQ.XTrN7gGqGgTZsHtIelFu2WxJyQtS90EQhVleiScWYo8vpXGmAfcqZj4o-bAmV3Z4I4v3zOq-ZyYuU3V0u3AxS472LQYBtrkFFN6Hs66LkftFKvGOyx5vuSQfpw_-MTDy5cj2tinx7txmFW5UkkphYg4Ukz6JZH2esufJ76YwTtw","access_token":"cad63d6f2f63835d0e621c0fb246b6c"}

Access token received with this can be used for further API calls.

3. Obtain profile values 

Note :- id_token came above is in the form of URL encoded. So first we have to convert it from Base64URl to Base64 then it will be the real “JWT token”.

JWT token comes with the response has three parts separated by the period (“.”).  
Those are of following structure.

<base64-encoded header>.<base64-encoded claims>.<base64-encoded signature>

For an example if you separate the id_token received above by the period and base64 decode them you will receive following three sections.

Signature algorithm - {"alg":"RS256"}
User Claims -
	
{  
   "sub":"shamika001@idp.com",
   "azp":"HYjnl0pVfggm5fbNHP6uyL5Mba4a",
   "http:\/\/wso2.org\/claims\/locality":"en",
   "http:\/\/wso2.org\/claims\/firstname":"Akila2",
   "at_hash":"Y2FkNjNkNmYyZjYzODM1ZDBlNjIxYzBmYjI0NmI2Yw==",
   "http:\/\/wso2.org\/claims\/enterprisefederatedsource":"google.com",
   "iss":"https:\/\/identity-dev1.idp.com\/token",
   "http:\/\/wso2.org\/claims\/telephone":"8126063431",
   "http:\/\/wso2.org\/claims\/lastname":"Balasooriya",
   "http:\/\/wso2.org\/claims\/uuid":"d72b3d1c-88cc-4ef7-84ee-d68c86ecbf5f",
   "iat":1427747585056,
   "http:\/\/wso2.org\/claims\/stateorprovince":"Indiana",
   "http:\/\/wso2.org\/claims\/fullname":"Shamika",
   "http:\/\/wso2.org\/claims\/givenname":"Akila2",
   "auth_time":1427747585052,
   "exp":1427751185056,
   "http:\/\/wso2.org\/claims\/gender":"Male",
   "http:\/\/wso2.org\/claims\/streetaddress":"115",
   "http:\/\/wso2.org\/claims\/country":"United States",
   "aud":[  
      "HYjnl0pVfggm5fbNHP6uyL5Mba4a"
   ],
   "http:\/\/wso2.org\/claims\/title":"mr",
   "http:\/\/wso2.org\/claims\/role":"DevopsAdmin,Internal\/everyone",
   "http:\/\/wso2.org\/claims\/emailaddress":"ushi324@gmail.com",
   "http:\/\/wso2.org\/claims\/organization":"WSO2"
}

This Json contains users profile information.

If you need more explanation about the common values represented here please refer this
http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html

Signature - XTrN7gGqGgTZsHtIelFu2WxJyQtS90EQhVleiScWYo8vpXGmAfcqZj4o+bAmV3Z4I4v3zOq+ZyYuU3V0u3AxS472LQYBtrkFFN6Hs66LkftFKvGOyx5vuSQfpw/+MTDy5cj2tinx7txmFW5UkkphYg4Ukz6JZH2esufJ76YwTtw=

4. Signature Validation

Obtain the signinginput by merging the first two parts of the JWT token by period.
	<base64-encoded header>.<base64-encoded claims>

Take this as the signing input and verify the signature (3rd part of the JWT token) using the public key of the idp.com provided.

Sample JAVA code of signature validation is as follows,
package org.shamika.samples;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.UnsupportedEncodingException;
import java.security.GeneralSecurityException;
import java.security.PublicKey;
import java.security.Signature;
import java.security.cert.Certificate;
import java.security.cert.CertificateException;
import java.security.cert.CertificateFactory;

import org.apache.commons.codec.binary.Base64;


public class Verifyier {

	public static void main(String[] args) {

		// <base64-encoded header>.<base64-encoded claims>
		Verifyier v = new Verifyier();
		try {

			String signingInput =
			                      "eyJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJzaGFtaWthMDAxQHRyaW1ibGUuY29tIiwiYXpwIjoiSFlqbmwwcFZmZ2dtNWZiTkhQNnV5TDVNYmE0YSIsImh0dHA6XC9cL3dzbzIub3JnXC9jbGFpbXNcL2xvY2FsaXR5IjoiZW4iLCJodHRwOlwvXC93c28yLm9yZ1wvY2xhaW1zXC9maXJzdG5hbWUiOiJBa2lsYTIiLCJhdF9oYXNoIjoiWTJGa05qTmtObVl5WmpZek9ETTFaREJsTmpJeFl6Qm1ZakkwTm1JMll3PT0iLCJodHRwOlwvXC93c28yLm9yZ1wvY2xhaW1zXC9lbnRlcnByaXNlZmVkZXJhdGVkc291cmNlIjoiZ29vZ2xlLmNvbSIsImlzcyI6Imh0dHBzOlwvXC9pZGVudGl0eS1kZXYxLnRyaW1ibGUuY29tXC90b2tlbiIsImh0dHA6XC9cL3dzbzIub3JnXC9jbGFpbXNcL3RlbGVwaG9uZSI6IjgxMjYwNjM0MzEiLCJodHRwOlwvXC93c28yLm9yZ1wvY2xhaW1zXC9sYXN0bmFtZSI6IkJhbGFzb29yaXlhIiwiaHR0cDpcL1wvd3NvMi5vcmdcL2NsYWltc1wvdXVpZCI6ImQ3MmIzZDFjLTg4Y2MtNGVmNy04NGVlLWQ2OGM4NmVjYmY1ZiIsImlhdCI6MTQyNzc0NzU4NTA1NiwiaHR0cDpcL1wvd3NvMi5vcmdcL2NsYWltc1wvc3RhdGVvcnByb3ZpbmNlIjoiSW5kaWFuYSIsImh0dHA6XC9cL3dzbzIub3JnXC9jbGFpbXNcL2Z1bGxuYW1lIjoiU2hhbWlrYSIsImh0dHA6XC9cL3dzbzIub3JnXC9jbGFpbXNcL2dpdmVubmFtZSI6IkFraWxhMiIsImF1dGhfdGltZSI6MTQyNzc0NzU4NTA1MiwiZXhwIjoxNDI3NzUxMTg1MDU2LCJodHRwOlwvXC93c28yLm9yZ1wvY2xhaW1zXC9nZW5kZXIiOiJNYWxlIiwiaHR0cDpcL1wvd3NvMi5vcmdcL2NsYWltc1wvc3RyZWV0YWRkcmVzcyI6IjExNSIsImh0dHA6XC9cL3dzbzIub3JnXC9jbGFpbXNcL2NvdW50cnkiOiJVbml0ZWQgU3RhdGVzIiwiYXVkIjpbIkhZam5sMHBWZmdnbTVmYk5IUDZ1eUw1TWJhNGEiXSwiaHR0cDpcL1wvd3NvMi5vcmdcL2NsYWltc1wvdGl0bGUiOiJtciIsImh0dHA6XC9cL3dzbzIub3JnXC9jbGFpbXNcL3JvbGUiOiJEZXZvcHNBZG1pbixJbnRlcm5hbFwvZXZlcnlvbmUiLCJodHRwOlwvXC93c28yLm9yZ1wvY2xhaW1zXC9lbWFpbGFkZHJlc3MiOiJ1c2hpMzI0QGdtYWlsLmNvbSIsImh0dHA6XC9cL3dzbzIub3JnXC9jbGFpbXNcL29yZ2FuaXphdGlvbiI6IldTTzIifQ";

			String signatureStr =
			                      "XTrN7gGqGgTZsHtIelFu2WxJyQtS90EQhVleiScWYo8vpXGmAfcqZj4o+bAmV3Z4I4v3zOq+ZyYuU3V0u3AxS472LQYBtrkFFN6Hs66LkftFKvGOyx5vuSQfpw/+MTDy5cj2tinx7txmFW5UkkphYg4Ukz6JZH2esufJ76YwTtw=";

			String publicKeyFileName = "idp-com-64366.cert";

			boolean isValid = v.validateSignature(signingInput, signatureStr, publicKeyFileName);
			System.out.println("Signature validation result - " + isValid);

		} catch (FileNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (UnsupportedEncodingException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (GeneralSecurityException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}

	}

	private boolean validateSignature(String signingInput, String signatureStr,
	                                  String publicKeyFileName) throws FileNotFoundException,
	                                                           UnsupportedEncodingException,
	                                                           GeneralSecurityException {

		ClassLoader classLoader = getClass().getClassLoader();
		File file = new File(classLoader.getResource(publicKeyFileName).getFile());

		CertificateFactory fact = CertificateFactory.getInstance("X.509");
		FileInputStream is = new FileInputStream(file);
		Certificate cer = fact.generateCertificate(is);
		PublicKey publicKey = cer.getPublicKey();

		byte[] data = signingInput.getBytes("utf-8");
		byte[] signature = Base64.decodeBase64(signatureStr);

		return verify(data, signature, publicKey, "SHA256withRSA");
	}

	/* Verify JWT token with signature */
	private static boolean verify(byte[] data, byte[] signature, PublicKey publicKey,
	                              String signatureAlgo) throws GeneralSecurityException {
		Signature sig = Signature.getInstance(signatureAlgo);
		sig.initVerify(publicKey);
		sig.update(data);
		return sig.verify(signature);
	}
}


 
<h2>2. Resource Owner Password Credentials </h2>

Make a POST request to the TOKEN endpoint with following params. 

Headers :- 
     Authorization : Basic <Base64 encoded “ClientID:ClientSecret”>
	     Accept: application/json

POST parameters :-
	    grant_type=password
    username=<username>
    password=<password>
Sample Request

POST /i/oauth2/token HTTP/1.1
Host: identity-dev1.idp.com
Authorization: Basic TE9pUmRvcDdma042V3FzNnJZREJEWFhGREtNYTpuX1hkbko1RjI2VUY5OWI2UFVRU1ZTOHRGSEFh
Content-Type: application/x-www-form-urlencoded
Accept: application/json
Cache-Control: no-cache

grant_type=password&username=testuser@cool.com&password=testUser@123

Sample response

{
token_type: "bearer"
expires_in: 49955
refresh_token: "b258f8961d3bf9c41b77ca328e3beace"
access_token: "cfd521fd5df77c43e3ab283d4bea2"
}

<h2>3. Implicit</h2>

An agent (eg:- browser) should be present for this grant type because it is based on redirections.

Browser redirection should be sent to AUTHORIZE endpoint with following query params,

scope=openid
response_type=token
redirect_uri=<Redirect URL registerd with IDP>
client_id=<ClientID registered for the application>

Sample request

https://identity-dev1.idp.com/i/oauth2/authorize?scope=openid&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%3A8080%2Fplayground%2Foauth2client&client_id=HYjnl0pVfggm5fbNHP6uyL5Mba4a

User will be redirected to a login page and upon successfully login, then redirected back to the callback url given with access token as a URI fragment.

 Sample response

http://localhost:8080/playground/oauth2client#access_token=717ba28b29ebb484c4726762d360f00&expires_in=45833


<h2>4. Client Credentials</h2>

Here the client itself assumed to be the resource owner, Uses for server to server communication.

Call the TOKEN endpoint with following POST param,

Headers :- 
     Authorization : Basic <Base64 encoded “ClientID:ClientSecret”>
	     Accept: application/json

POST parameters :-
    grant_type=client_credentials
Sample request :-

POST /i/oauth2/token HTTP/1.1
Host: identity-dev1.idp.com
Authorization: Basic TE9pUmRvcDdma042V3FzNnJZREJEWFhGREtNYTpuX1hkbko1RjI2VUY5OWI2UFVRU1ZTOHRGSEFh
Content-Type: application/x-www-form-urlencoded
Accept: application/json
Cache-Control: no-cache

grant_type=client_credentials
	
Sample response :-
{
token_type: "bearer"
expires_in: 47928
access_token: "cfd521fd5df77c43e3ab283d4bea2"
}

<h2>5. SAML2 Bearer Token</h2>

After successfully,  authenticated using SAML, the application would be able to get the access token by passing the SAML assertion to TOKEN endpoint.

Headers :- 
     Authorization : Basic <Base64 encoded “ClientID:ClientSecret”>
	     Accept: application/json

POST parameters :-
	    grant_type=urn:ietf:params:oauth:grant-type:saml2-bearer
    assertion=<SAML2_Encoded Assertion>
    
Sample Request

POST /i/oauth2/token HTTP/1.1
Host: identity-dev1.idp.com
Authorization: Basic TE9pUmRvcDdma042V3FzNnJZREJEWFhGREtNYTpuX1hkbko1RjI2VUY5OWI2UFVRU1ZTOHRGSEFh
Content-Type: application/x-www-form-urlencoded
Accept: application/json
Cache-Control: no-cache

grant_type=urn:ietf:params:oauth:grant-type:saml2-bearer&assertion=<SAML2_Encoded Assertion>&scope=production
 
Sample response :-
{
token_type: "bearer"
expires_in: 47928
access_token: "cfd521fd5df77c43e3ab283d4bea2"
}




















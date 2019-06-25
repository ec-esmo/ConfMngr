

# ConfManager
**Configuration files**:

A keystore.jks is provided in the **/resources/testKeys** directory.

The **microservices** configuration json file are allocated at **/resources**. Find msMetadataList.json as an example. Other files related to **EWP management** can be found in this directory too: *ewp-manifest.xml*, esmo-manifest.json (this one generated automatically).


The json files describing the different **attribute schemas**, are put at **/resources/attributeLists**. Examples provided with the deployment are: eduOrg.json, eduPerson.json, eIDAS.json, schac.json.

The json files with the **external entities** are found at **/resources/externalEntities**. Some examples are deployed: APmetadata.json, IdPmetadata.json, SPmetadata.json. The rGWmetadata.json and rAPmetadata.json are generated periodically from the **esmo hosts** published in the EWP.

The json file with the **internal** configuration can be found at **/resources/internal**. A LGW_config.json is deployed. The signing public key specified in this file (also present in the ewp-manifest.xml), is used to sign the calls to other esmo nodes. Besides, both public keys (signing and encrypting) are used in communication between Esmo Gateways through the *ESMOtoken* interchanging.


Customize the above files before starting your CM. If the CM is running already, to change those data:
```
sudo docker cp /yourDirectory/yourConfData/msMetadataList.json confMngr_ps_num:/resources/
sudo docker cp /yourDirectory/yourConfData/SPmetadata.json confMngr_ps_num:/resources/externalEntities
etc.
```


**Docker container** 

Find the related image at DockerHub: mvjatos/cm (check for the current version)

The following versions are available:

|**Tag**|**Description**|
| ------ | ------ |
| **1.0.0**| Published in GitHub |




**docker-compose example**:

```
ConfManager:
      image: mvjatos/cm:1.0.0 
      environment:
            - KEYSTORE_PATH=/resources/testKeys/keystore.jks
            - KEY_PASS=selfsignedpass
            - STORE_PASS=keystorepass
            - HTTPSIG_CERT_ALIAS=1
            - SIGNING_SECRET=QjG+wP1CbAH2z4PWlWIDkxP4oRlgK2vos5/jXFfeBw8=
            - ASYNC_SIGNATURE=true
            - SSL_KEYSTORE_PATH=/resources/keystoreatos.jks
            - SSL_STORE_PASS=AtosCert1
            - SSL_KEY_PASS=AtosCert1
            - SSL_CERT_ALIAS=atoscert
        volumes:
            - /ESMO/CM/resources:/resources
        ports:
            - 8083:8083
```

# TODO
Move to Wiki pages.

# ms Registry configuration
- In this release of the ESMO Gateway the microservices will be registered by manual configuration. 
- Two ms registry config files are needed as described further below: AP config file and the Gateway Config file. 
- The AP config file interface will be made available to the ACM ms only, and will prode access via the APconf method call.
- The  Gateway Config file interface is available to all ms, and will provide access via the GWconf method call.
- The  Conf Mngr primary address will have to be configurable on each of the ms. 
- SSL to ensure the server identity  
- **HTTP Signatures** to authenticate the calling ms 
- Each ms will provision their own public and private keys indside their ms. 

## HTTP Signatures
Signatures are used as a means to authenticate clients. Putting signatures in HTTP headers is a more robust solution compared to using client TLS certificates, which can be stripped away by intermediate proxies, load balancers, etc. before reaching the server. The solution is based on an [Internet Draft](https://tools.ietf.org/html/draft-cavage-http-signatures-10) (on its way to become an RFC) which is already being used a lot.

Since ESMO will be communicating with EWP, we should follow the EWP guidelines for HTTP signatures as much as possible. The EWP documentation related to the topic can be found here:

[Authenticating Servers with HTTP Signature](https://github.com/erasmus-without-paper/ewp-specs-sec-srvauth-httpsig)

[Authenticating Clients with HTTP Signature](https://github.com/erasmus-without-paper/ewp-specs-sec-cliauth-httpsig)

The implementation we will be using is based on [Tomitribe's library](https://github.com/tomitribe/http-signatures-java), where the version 1.1 includes our code for verification of signatures.

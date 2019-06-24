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


**Docker container:** mvjatos/cm (check for the current version)

The following versions are available:

|**Tag**|**Description**|
| ------ | ------ |
| **0.0.6**| CM-CM enhanced. |
| 0.0.6ssl| Pending to be published at DockerHub. (*)|


(*) *To update the docker-compose file too.*

**docker-compose example**:

```
ConfManager:
      image: mvjatos/cm:0.0.6 
      environment:
            - KEYSTORE_PATH=/resources/testKeys/keystore.jks
            - KEY_PASS=selfsignedpass
            - STORE_PASS=keystorepass
            - HTTPSIG_CERT_ALIAS=1
            - SIGNING_SECRET=QjG+wP1CbAH2z4PWlWIDkxP4oRlgK2vos5/jXFfeBw8=
            - ASYNC_SIGNATURE=true
        volumes:
            - /ESMO/CM/resources:/resources
        ports:
            - 8080:8080

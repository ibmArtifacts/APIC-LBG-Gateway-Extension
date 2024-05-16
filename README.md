# APIC Load Balancer Group (LBG) Gateway-Extension  

![image](https://github.com/ibmArtifacts/APIC-LBG-Gateway-Extension/assets/66093865/98fa2cde-1f5f-418f-bc1e-c92e13bdbfd8)  

  
Since there's not an out-of-the-box configuration for a DataPower Load Balancer Group (LBG) on APIC, this article showcases how to create an [APIC gateway-extension](https://www.ibm.com/docs/en/api-connect/10.0.5.x_lts?topic=environment-extending-gateway-server-behavior) to extend a DataPower LBG on APIC, and use it from an API.  
NOTE: This will not work for a v5c extension, this is only for the native api gateway.  
WARNING: Deploying this gateway-extension may cause a short outage. The API Connect Gateway Service will required to be disabled, then re-enabled.  
  
[What is a DataPower Load Balancer Group (LBG)?](https://www.ibm.com/docs/en/datapower-gateway/10.5.0?topic=administration-load-balancer-groups)  
  
**Requirements:**  
- Access to DataPower to create the LBG.  
- Access to the APIC Cloud Manager (CM) to upload the gateway-extension to the gateway in the Topology.  
- Access to APIC API Manager (APIM) to create an api and invoke the LBG.  
  
**Files to be created for the LBG gateway-extension:**  
- manifest.json: The file that APIC will read to import the DataPower LBG export and add it to the apigw object in the apiconnect domain.  
[More details about the manifest definition may be found in the IBM APIC documentations.](https://www.ibm.com/docs/en/api-connect/10.0.5.x_lts?topic=gateway-extensions-manifest)
- lbg.zip: The DataPower LBG export.  
- lbg-add-on-apigw.json: The file that APIC will update the APIC gateway configuration to include the LBG to be used later.  
[More details about overriding the configurations of the API Gateway may be found in the IBM APIC documentations.](https://www.ibm.com/docs/en/api-connect/10.0.5.x_lts?topic=type-apigw)  
****  

**Here is a completed sample gateway-extension to (1) download, (2) unzip the lbg-add-on-apigw.zip, (3) unzip the lbg.zip to modify the export.xml, (4) re-zip the export, and (5) re-zipping the  lbg-add-on-apigw.zip to upload to APIC: [lbg-add-on-apigw.zip](https://github.com/ibmArtifacts/APIC-LBG-Gateway-Extension/blob/main/lbg-add-on-apigw.zip)**  

The next few steps will walk through configuring the LBG gateway-extension.  
  
## Create DataPower LBG  
1. Log into DataPower to create a temporary LBG or navigate to the LBG you would like to import into the APIC domain to use.
![image](https://github.com/ibmArtifacts/APIC-LBG-Gateway-Extension/assets/66093865/47eea8e2-4567-4f9b-8896-583dbcf51829)

## Create the lbg-add-on-apigw.json  
The lbg-add-on-apigw.json tells APIC to use the LBG object in the APIC gateway when an APIC Invoke policy uses it:  

```  
{
    "apigw": {
        "_global": {
            "override": [
                "loadbalancer-group lbg"
            ]
        }
    }
}
```  

## Create the manifest.json  
The manifest.json references and tells APIC to (1) import the lbg.zip into the apiconnect domain, and (2) add the LBG to the api gateway service LBG section.
```
{
   "extension":{
      "files":[
         {
            "filename":"lbg.zip",
            "deploy":"immediate",
            "type":"dp-import"
         },
         {
            "filename":"lbg-add-on-apigw.json",
            "type":"gwd_extension"
         }
      ]
   }
}
```

## Putting it altogether and uploading to APIC  
1. Once all is completed zip everything together as shown in the diagram below:  
![image](https://github.com/ibmArtifacts/APIC-LBG-Gateway-Extension/assets/66093865/c189a341-6f84-420f-b5eb-a65fd8387fb1)  

2. Navigate to the CM > Topology > click on the elipsis (dot, dot, dot) for the gateway and select Configure Gateway-Extension:
![image](https://github.com/ibmArtifacts/APIC-LBG-Gateway-Extension/assets/66093865/cc03cedb-b020-4f88-ac1a-d6032ef733b3)  

3. Go back to DataPower and ensure you are in the apiconnect domain. Navigate to the API Connect Gateway Service.  
Disable the service, apply, then re-enable, and apply again.
![image](https://github.com/ibmArtifacts/APIC-LBG-Gateway-Extension/assets/66093865/472ca0ec-61ca-4436-aaae-a0af17c1d06c)  

  
After disabling and re-enabling the API Connect Gateway Service, you should see the configuration attempt to load into the gateway via syslog:  
![image](https://github.com/ibmArtifacts/APIC-LBG-Gateway-Extension/assets/66093865/25f4fe24-a29b-45e5-97fb-e2a68a3edc30)  

You may verify that the LBG is uploaded to the apiconnect domain and assigned to the API Gateway Service (navigate to API gateway service in the apiconnect domain on DataPower):  
![image](https://github.com/ibmArtifacts/APIC-LBG-Gateway-Extension/assets/66093865/f40c1007-f855-4fab-ade7-df02c006d1f5)  

## Use the LBG in an API on APIC  
To use the LBG, you may simply just use the name of the LBG with the Invoke policy as shown in the diagram:   
![image](https://github.com/ibmArtifacts/APIC-LBG-Gateway-Extension/assets/66093865/7959ef8b-89d1-4ea1-a1da-97a740c029f8)  










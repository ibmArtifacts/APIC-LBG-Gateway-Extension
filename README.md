# APIC Load Balancer Group (LBG) Gateway-Extension  
Since there's not an out-of-the-box configuration for a DataPower Load Balancer Group (LBG) on APIC, this article showcases how to create an [APIC gateway-extension](https://www.ibm.com/docs/en/api-connect/10.0.5.x_lts?topic=environment-extending-gateway-server-behavior) to extend a DataPower LBG on APIC, and use it from an API.  
NOTE: This will not work for a v5c extension, this is only for the native api gateway.  
WARNING: Deploying this gateway-extension may cause a short outage. The API Connect Gateway Service will be  
  
[What is a DataPower Load Balancer Group (LBG)?](https://www.ibm.com/docs/en/datapower-gateway/10.5.0?topic=administration-load-balancer-groups)  
  
**Requirements:**  
- Access to DataPower to create the LBG.  
- Access to the APIC Cloud Manager (CM) to upload the gateway-extension to the gateway in the Topology.  
- Access to APIC API Manager (APIM) to create an api and invoke the LBG.  
  
**Files to be created for the LBG gateway-extension:**  
- manifest.json: The file that APIC will read to import the DataPower LBG export and add it to the apigw object in the apiconnect domain. [More details here.](https://www.ibm.com/docs/en/api-connect/10.0.5.x_lts?topic=gateway-extensions-manifest)
- lbg.zip: The DataPower LBG export.  
- lbg-add-on-apigw.json: The file that APIC will update the APIC gateway configuration to include the LBG to be used later.
  

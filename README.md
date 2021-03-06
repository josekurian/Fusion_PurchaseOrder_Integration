# Workshop: Fusion & salesforce with Oracle Integration & Apiary

<p align="center">
  <img src="https://github.com/GaryHostt/Fusion_PurchaseOrder_Integration/blob/master/genericScreenshots/z.png?raw=true" alt="comic"/>
</p>

## Objectives

This lab will various ways to use the ERP adapter in OIC.

## Outline

**CMD/CTRL F to skip to relevant part**

### Pre-requisites
1. Configure ERP to send business events to OIC
2. Connect ERP adapter
3. Create a free apiary account

### Generic REST Trigger walkthrough, order creation
1. Verify you have a Generic REST trigger
2. Create an app-driven orchestration
3. Submit a payload with Postman
4. Tracking & Activity Stream

### Generic REST Trigger walkthrough, querying Fusion & salesforce

**1. Background on using the Fusion REST API** (Where to start Fusion Integration Development)

2. Create an app-driven orchestration
3. Extending with salesforce (or another application)

### SaaS Trigger + Apiary walkthrough (Business Events from ERP)
1. Create an apiary mock endpoint
2. Create the integration
3. Mapping
4. Perform the ERP business event
5. Monitor the integration 
6. See the apiary log

### FBDI & Scheduled Orchestrations

Please see my [colleague's write up](https://github.com/maldu23/Fusion-FBDI-Integration/blob/master/FBDI_Wkshp.md)

### Additional Resources & API blueprint

# Pre-requisites 

## 1.	Configure ERP to send business events to OIC* 

**While this is only necessary for the 2nd walkthrough, this may take a while to properly configure.**

*this is only necessary for the SaaS trigger walkthrough*

View these links from the a-team to learn how to configure ERP to send business events to OIC. 

[Using Business Events in Fusion-based SaaS with Oracle Integration Cloud - Part 1](https://blogs.oracle.com/imc/subscribe-to-business-events-in-fusion-based-saas-applications-from-oracle-integration-cloud-oic-part-1-prerequisites)

[Using Business Events in Fusion-based SaaS with Oracle Integration Cloud - Part 2](http://www.ateam-oracle.com/using-business-events-with-integration-cloud-part-2)

After doing the above configuration, you can create the connection with the adapter in OIC. 

## 2. Create a free apiary account

You can create a free Apiary account [here](https://login.apiary.io/register). *It is only necessary for the SaaS trigger walkthrough as well.*

## 3. Use Cloud ERP adapter to make a connection to ERP

![](200/1.png)

In OIC, select the Oracle ERP cloud adapter.

![](200/2.png)

Name your connection.

![](200/71.png)

Configure your connection details. [Further details on the fields are here.](https://docs.oracle.com/en/cloud/paas/integration-cloud/erp-adapter/create-connection.html#GUID-1B92F72F-4AA8-4C2B-9E93-8F9760EEE859)

**Use the user your configured in step 1 if doing the SaaS trigger walkthrough.**

# Generic REST Trigger Walkthrough, order creation

The .iar file for this integration is in this repo, ERP_CREATE_ORDER.iar. If you import this to your environment, update the connection details to use your environments before activating the integration.

## Verify you have a Generic REST trigger

![](genericScreenshots/1.png)

Under connections, verify you have a generic or all purpose REST trigger.

![](genericScreenshots/2.png)

It is just a REST adapter with no details. This is what we use when using OIC as a 'drag & drop API builder.' 


## Create an app-driven orchestration

![](genericScreenshots/3.png)

Return to the integrations tab and create an app-driven orchestration.


![](genericScreenshots/4.png)

Name your integration. 

![](genericScreenshots/5.png)

This is what your integration will look like at the end. Begin by placing your generic REST adapter on the canvas. 


![](genericScreenshots/7.png)

Input these details.

![](genericScreenshots/6.png)

Configure a payload & response. On the request and response page, select JSON and press the blue inline text. Insert these payloads.

Request:
```
{
  "TransactionIdentifier" : "Creekside Warehouse-14073",
  "BuyingPartyName" : "Pinnacle Technologies",
  "BuyingPartyContactName" : "Isaac Nelson",
  "BusinessUnitName" : "US1 Business Unit",
  "ShipToPartyIdentifier" : "A100000000409220",
  "ShipToPartyName" : "Pinnacle Technologies",
  "BillToCustomerName" : "Pinnacle Technologies",
  "ShipToPartySiteIdentifier" : "A300000048361113",
  "BillToAccountSiteUseIdentifier" : "A300000048361115",
  "OrderItems" : [ {
    "ProductId" : "AS46336",
    "Quantity" : 3
  }, {
    "ProductId" : "AS46336",
    "Quantity" : 3
  } ]
}
```

Response:
```
{
  "orderNumber" : "A11231",
  "orderStatus" : "Success"
}
```

![](genericScreenshots/8.png)

Select the CRUD action.


![](genericScreenshots/9.png)

Select the order import service and the createOrders action.


![](genericScreenshots/10.png)

Your summary page should look like this.

## Mapping between REST & Fusion, activating your integration

There are many fields an Order will take in Fusion. Read the 'treatise' portion of this [article](https://github.com/GaryHostt/Oracle_Integration/blob/master/README.md) to understand how you should tackle novel mapping situation. The screenshots below show the mapping for the fields if you do not copy the .iar. 

![](genericScreenshots/11.png)
![](genericScreenshots/12.png)
![](genericScreenshots/13.png)
![](genericScreenshots/14.png)
![](genericScreenshots/15.png)

For the f(x) mappings, you will use the XSLT functions shown in the following screenshots. 
![](genericScreenshots/16.png)
![](genericScreenshots/17.png)
![](genericScreenshots/18.png)

You can hardcode these values:
![](genericScreenshots/19.png)

Resume with these mappings:
![](genericScreenshots/20.png)
![](genericScreenshots/21.png)

These are all the request mappings:
![](genericScreenshots/22.png)

This is the mapping for the response back to the REST adapter:
![](genericScreenshots/23.png)

Don't forget to add a field to tracking:
![](genericScreenshots/24.png)

Now you can activate your integration.

## Using Postman

![](genericScreenshots/25.png)

After adding your basic auth to the authorization tab, you can submit your request with the below payload, or you can copy the payload from the metadata endpoint of your activated integration.

Some fields have been made blank to attempt to alleviate the difficulties of interacting with different ERP systems. 
```
{ "TransactionIdentifier": "Creekside Warehouse-14073", "BuyingPartyName": "Pinnacle Technologies", "BuyingPartyContactName": "Isaac Nelson", "BusinessUnitName": "US1 Business Unit", "ShipToPartyIdentifier": "", "ShipToPartyName": "Pinnacle Technologies", "BillToCustomerName": "Pinnacle Technologies", "ShipToPartySiteIdentifier": "", "BillToAccountSiteUseIdentifier": "", "OrderItems": [ { "ProductId": "AS46336", "Quantity": 3 }, { "ProductId": "AS46336", "Quantity": 3 } ] }
```


![](genericScreenshots/26.png)

You can use the activity stream from the tracking page to see if there were any problems with your integration. Click the underlined Message (or your equivalent).


![](genericScreenshots/27.png)

The message reveals that there was a problem with the Bill_to_site_use_id. From here we can troubleshoot the fields line by line.

# Generic REST Trigger walkthrough, querying bank accounts

0. Verify you have a Generic REST trigger (see previous instructions)

## 1. Background on the Fusion REST API

When building this workshop and other integrations, these are the steps I follow, which can be generalized from other endpoints.

Of course, configure your authorization tab. Basic auth should work with the username & password you used to connect your adapter in OIC.

To understand where to get the information about my ERP's bank accounts, I look at the [Fusion REST API documentation for Bank accounts](https://docs.oracle.com/en/cloud/saas/financials/18c/farfa/api-bank-accounts.html).

First I call the 'get all' Bank Accounts endpoint

```
https://<Your Base URL (from adapter screen)>/fscmRestApi/resources/11.13.18.05/cashBankAccounts
```
this gives me a variety of entries with Primary keys to play around with - notice the BankAccountId field. Now I can call the endpoint to return just one bank account, so that I can see all of the fields related to Bank accounts at the individual level. 
```
https://<Your Base URL>/fscmRestApi/resources/11.13.18.05/cashBankAccounts/<BankAccountId>
```
If I need to know what data type a field is when mapping in OIC, returning to the 'get 1' endpoint may reveal that to me, such as the "BankBranchName", which is not in the 'get all' endpoint. To find where to get an entry for the 'get 1 endpoint' I look at the 'get all' endpoint.

Note: you can use the REST adapter to perform these operations from OIC as well. 

In my integration, I will be using a specific BankAccountId

## 2. Create an app-driven orchestration


![](genericScreenshots/a.png)

This is what your integration will look like in the end. The REST adapter receives a field that it passed to the id field below.


![](genericScreenshots/b.png)

This id field is the 'BankAccountId' field from the background section.


![](genericScreenshots/c.png)

We can see the response fields from the adapter, in this case I map the name to the return statement of the REST adapter. 

## 3. Extending with salesforce

What if we want to create one endpoint that is able to query multiple systems? Enabling our developers to worry about one less authentication to worry about? Below we will be creating an endpoint that can also take a key for a salesforce business object, and return information about it. Keep in mind for production, the 'fusionKey' & 'salesforceKey' could be the same, like 'businessKey', and both mapped to get different responses from both systems. 

![](genericScreenshots/d.png)

This is what your integration should look like in the end.

I use the POST action on the REST adapter taking in these keys to query the systems, but the same functionality could be implemented by using the GET verb and inputting these as parameters instead. Just because we are using POST, does not mean we are changing anything in these systems, because we are not. 

Request payload for REST:

```
{
  "fusionKey" : "",
  "salesforceKey" : ""
}
```
Response payload for REST:
```
{
  "fusionResponse1" : "",
  "fusionResponse2" : "",
  "SFDCResponse1" : ""
}
```
Of course, you'll need an active salesforce connection using the adapter, like below. 

![](genericScreenshots/e.png)

After putting it on your orchestration, configure the adapter as follow:

![](genericScreenshots/f.png)
![](genericScreenshots/g.png)
![](genericScreenshots/h.png)
![](genericScreenshots/i.png)

Next you'll have to map your REST payload to the input for your salesforce adapter. 

![](genericScreenshots/j.png)

For mapping to the salesforce adapter, take your salesforce key and map it to the ids. For submitting the payload, you can find a relevant contact id in your salesforce environment using SOQL, this can even be done from the salesforce adapter in OIC, shown below.

![](genericScreenshots/k.png)
![](genericScreenshots/l.png)

Next, you'll have to map to outputs from Fusion & salesforce to your REST adapter's return, shown below.

![](genericScreenshots/n.png)
![](genericScreenshots/o.png)
![](genericScreenshots/m.png)

Congratulations, now you can query for a contact's name by its id i salesforce, and get back Bank Account information. If I was operating a proper business, my ERP system likely would've contained a 'salesforce_contact_id' field, in which case I could have had this integration only start with one field to have the same information returned. Never the less, now we have an understanding of integration, for the purpose of querying multiple systems. 

In lieu of salesforce, we could have used a GET endpoint on Apiary to simulate the same effect. Continue below to learn how to use Apiary in such a manner.

# SaaS Trigger + Apiary Walkthrough (Business Events from ERP)

**Note: You must have done step 1 of the pre-requisites, Configure ERP to send business events to OIC**

## 1. Create the apiary mock endpoint

![](100/71.png)

After copying the apiary blue print at the bottom of this lab, you can get your base URL from the location in the screenshot above.

![](100/72.png)

Create a REST connection and type your base url, make sure you have no security policy selected.

## 3. Create the integration to receive the PO data upon creation

Create an app driven orchestration integration. Then start the integration with your ERP connection. The response from the PO creation event will be mapped to the request to your Apiary REST connection.

## 4. Create a PO in ERP 

[Watch this video to see PO creation](https://www.youtube.com/watch?v=jCUEBjNi86k)

## 5. Verify & monitor the integration on OIC & Apiary

![](genericScreenshots/p.png)

On apiary's inspector tab, we should see that an API call was made a few seconds ago. 

# FBDI

Please see my [colleague's write up](https://github.com/maldu23/Fusion-FBDI-Integration/blob/master/FBDI_Wkshp.md).

# Further work

[Creating POs with VBCS](http://niallcblogs.blogspot.com/2019/03/695-oic-subscribing-to-fusion-erp.html)

[Using a Digital Assistant Custom skill to create a PO](https://github.com/oracle/bots-node-sdk)

[PO SOAP endpoint](https://docs.oracle.com/en/cloud/saas/procurement/18b/oeswp/Purchase-Order-Service-Version-2-PurchaseOrderService-svc-3.html)

[More Fusion labs + extending with CPQ, Process Automation, ATP](https://github.com/GaryHostt/OIC_SaaS_integration)

[Oracle Developer Services](https://github.com/GaryHostt/OCI_DevOps)

[Oracle Integration Master Repository](https://github.com/GaryHostt/Oracle_Integration)


# Apiary blueprint
```
FORMAT: 1A

# PO_Creation

This is a simple API allowing the receiving of PO fields from Fusion ERP.

## Questions Collection [/purchaseOrders]

### Post a PO [POST]

You may create your own question using this action. It takes a JSON
object containing a question and a collection of answers in the
form of choices.

+ Request (application/json)

        {
            "OrderNumber": "12345",
            "POHeaderId": 1234,
            "ProcurementBusinessUnit":"12345",
            "BuyerEmail":"email@email.com"
        }

+ Response 201 (application/json)

    + Headers

            Location: /PO/2

    + Body

            {"status":"success"}

```


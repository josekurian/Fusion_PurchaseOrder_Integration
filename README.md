# Workshop: Fusion & Purchase orders with Oracle Integration

![](100/69.png)

How to receive business events from Fusion and create purchase orders with OIC. 

## Objectives

This lab will show how to configure ERP as an integration trigger with the ERP adapter, and how to invoke it.

### Outline

#### Pre-requisites
1. Configure ERP to send business events to OIC
2. Connect ERP adapter

#### Generic REST Trigger walkthrough

#### SaaS Trigger walkthrough
1. Create an apiary mock endpoint
2. Create the integration
3. Performan the ERP business event
4. Test & monitor the integration 

#### Scheduled Invoke walkthrough

# Pre-requisites 

## 1.	Configure ERP to send business events to OIC* 

**While this is only necessary for the 2nd walkthrough, this may take a while to properly configure.**

*this is only necessary for the SaaS trigger walkthrough*

View these links from the a-team to learn how to configure ERP to send business events to OIC. 

[Using Business Events in Fusion-based SaaS with Oracle Integration Cloud - Part 1](https://blogs.oracle.com/imc/subscribe-to-business-events-in-fusion-based-saas-applications-from-oracle-integration-cloud-oic-part-1-prerequisites)

[Using Business Events in Fusion-based SaaS with Oracle Integration Cloud - Part 2](http://www.ateam-oracle.com/using-business-events-with-integration-cloud-part-2)

After doing the above configuration, you can create the connection with the adapter in OIC. 

## 2. Use Cloud ERP adapter to make a connection to ERP

![](200/1.png)

In OIC, select the Oracle ERP cloud adapter.

![](200/2.png)

Name your connection.

![](200/3.png)

Configure your connection details, all of these fields need to be filled out. [Further details on the fields are here.](https://docs.oracle.com/en/cloud/paas/integration-cloud-service/icser/creating-connection.html#GUID-1B92F72F-4AA8-4C2B-9E93-8F9760EEE859)

# Generic REST Trigger Walkthrough

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


Submit with blank because issues
```
{ "TransactionIdentifier": "Creekside Warehouse-14073", "BuyingPartyName": "Pinnacle Technologies", "BuyingPartyContactName": "Isaac Nelson", "BusinessUnitName": "US1 Business Unit", "ShipToPartyIdentifier": "", "ShipToPartyName": "Pinnacle Technologies", "BillToCustomerName": "Pinnacle Technologies", "ShipToPartySiteIdentifier": "", "BillToAccountSiteUseIdentifier": "", "OrderItems": [ { "ProductId": "AS46336", "Quantity": 3 }, { "ProductId": "AS46336", "Quantity": 3 } ] }
```
# SaaS Trigger Walkthrough

## 1. Create the apiary mock endpoint

![](100/71.png)

After copying the apiary blue print at the bottom of this lab, you can get your base URL from the location in the screenshot above.

![](100/72.png)

Create a REST connection and type your base url, make sure you have no security policy selected.

## 3. Create the integration to receive the PO data upon creation

Create an app driven orchestration integration. Then start the integration with your ERP connection.

## 4. Create a PO in ERP 

## 5. Verify & monitor the integration on OIC & Apiary

[Watch this video to see PO creation](https://www.youtube.com/watch?v=jCUEBjNi86k)

# Invoke Walkthrough

[This is the SOAP endpoint we will be using](https://docs.oracle.com/en/cloud/saas/procurement/18b/oeswp/Purchase-Order-Service-Version-2-PurchaseOrderService-svc-3.html)


# Further work

[Creating POs with VBCS](http://niallcblogs.blogspot.com/2019/03/695-oic-subscribing-to-fusion-erp.html)

[Using a Digital Assistant Custom skill to create a PO](https://github.com/oracle/bots-node-sdk)


## Apiary blueprint
```
FORMAT: 1A
HOST: https://polls.apiblueprint.org/

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


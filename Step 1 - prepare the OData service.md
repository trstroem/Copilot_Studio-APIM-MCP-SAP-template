# Prepare your SAP OData service

In this template, we will use an existing OData service as our starting point. Most - if not all - SAP systems come equipped with the GWSAMPLE_BASIC service, which is a comprehensive sample service with a range of entity sets including the following:

- SalesOrder
- Product
- BusinessPartner

... and others. This allows for extensive testing of read/modify/delete scenarios, as well as complex conversational interactions (example: "List all of my sales orders for customer X where the gross amount exceeds Y $..."). This is excellent for rapidly setting up a demo that showcases the power of agentic AI in an SAP context.

Note: the fact that we're using OData services also means we're not restricted to S/4HANA - or indeed HANA - systems. "Good ole ECC" also expose these services - after all, OData is not a new kid on the block :-)

Right. Let's start our Agentic AI journey - step 1.

Log in to your SAP system. If you don't have one, I suggest trying out one of the "Fully-activated appliances" on SAP's Cloud Appliance Library homepage - cal.sap.com.

You can verify the metadata of your GWSAMPLE_BASIC service. It should be available at the following url:

https://[your SAP DNS address or IP]/sap/opu/odata/IWBEP/GWSAMPLE_BASIC/$metadata

Proceed to [Step 2 - create MCP server and AI Agent](./Step%202%20-%20create%20MCP%20server%20and%20AI%20Agent) to continue.
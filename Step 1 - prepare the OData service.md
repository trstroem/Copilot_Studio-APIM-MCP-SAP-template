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

You will have to authenticate with your SAP user.

The metadata looks something like this:

![metadata](/media/metadata.png)

Our first step towards Agentic AI world domination involves exposing this OData service in Azure API Management (APIM). The reasons for using APIM are twofold: firstly, it keeps all of our services (perhaps with the exception of SAP itself) on Azure; secondly, APIM has a built-in MCP wizard.

MCP is like giving your AI a Swiss Army knife and a map — suddenly it not only knows where to go, it can also open every door along the way.

(Disclaimer: I used Copilot for that)...

Create an Azure API Management service (or use an existing one). This is done in the Azure portal by searching for "Azure API Management":

![APIM](/media/APIM.png)

The APIM service has different pricing tiers. Some of these may not work with the MCP setup needed for this template. As of January 2026, the following tier is tried & tested: "Basic V2". (Others may work as well - but for a successful PoC I suggest using Basic V2):

![Pricing tiers](/media/pricingtier.png)

For the remaining options, anything should be fine. You may want to set up the APIM service in the same region as your SAP workloads for latency and data transfer cost reasons.

Once the APIM service is available, we need to create the API endpoint for our GWSAMPLE_BASIC service. The easiest way to do this is by converting the metadata into an "OpenAPI" format, then upload this into APIM. Here is how:

Enter the address for the GWSAMPLE_BASIC service in a browser window, as described above. This will result in the metadata for your service to be displayed. Now, copy everything in the browser window (starting with the "<edmx:Edmx...> tag) to your clipboard (ctrlC). Then, open a new browser tab and point it to https://convert.odata-openapi.net/ - and paste the XML content you just copied into the "Manual Input..." field:

![Converting OData metadata](/media/convertodata.png)

Click the Convert to OpenAPI3 button. You should now have a "Converted Result" further down the page. Click the Download button to save it locally as a file.

Back in your APIM service, you can now create an API by clicking the API menu on the left of the page, then select the OpenAPI option:

![Creating an OpenAPI-based API](/media/createOpenAPI.png)

On the pop-up screen that follows, simply select the file with your OpenAPI definition, and select a suitable name for your new API.

We now have an API - where we can see the OData operations available as per the metadata definition of the OData service:

![Operations](/media/operations.png)

The next step is to add our authentication method for the API.

In this simplified template, we will opt for the basic authentication - user and password. In a production scenario, you would want to implement principal propagation - a future iteration of this repository will hopefully show how to do this. For now, the following steps are required:

In APIM, create two Named Values to hold the user and password for the SAP system: click "Named values" in the APIM menu on the left, then the "+ Add" button at the top of the screen:

![Named values](/media/namedvalues.png)

(As you can see, I've created two values - one for the standard user BPINST of my test system, and another for the password).

The first named value will be the user - it will be of type "Plain". The second will be the password, with type Secret.

These two values will now have to be added to our authentication method in the API. Go back to the API we just created, select the "Design" tab on the main API screen, and click the "base" button in the Inbound processing section:

![authentication](/media/base.png)

In the html code that opens, add the following line where shown:

<authentication-basic username="{{username}}" password="{{password}}" />

![adding authentication](/media/authentication.png)

Save this and move to the "Settings" tab of the API service. Here, we need to enter the Web service URL of our OData service. If we are using a "proper" SAP system with DNS resolution, enter this in the URL window. If using a temporary system provided with CAL, you will probably need to enter the external IP of that system, as shown here:

![External IP](/media/apidetails1.png)

There is an option to create a "subscription" in APIM - for now, we will not use this (it can be added later). By scrolling down the Settings page, ensure the options are as follows:

![More settings](/media/apidetails2.png)

The last step before testing our API is related to our simplistic implementation of authentication (user/password). For the API service to work, we need to define a "backend" - this allows us to bypass the certificate requirements when working with basic authentication. In APIM, select the "backend" tab, then "Create new backend":

![Creating a backend](/media/apidetails2.png)

In the Runtime URL field, enter the external IP of your SAP system, followed by "/sap", like this:

https://xxx.xxx.xxx.xxx/sap

Further down, open the "Advanced" section and de-select the Validate certificate boxes:

![Certificates](/media/backend1.png)

This concludes the configuration needed - Now we should be able to test the API. On the "Test" tab, scroll down through the available operations and find one of the "GET entities..."; for example the "Get entities from ProductSet". Enter a number - like 5 - in the $top parameter and click the "Send" button.

![Send](/media/test1.png)

The result should be like this:

![Result](/media/test2.png)

Important note for CAL-provided SAP systems: if you are using your own CAL-provided SAP instance, you probably have to explicitly allow inbound TCP requests to your external SAP IP - by adding an onbound rule in the NSG (Network Security Group) allocated to the SAP HANA DB server. This inbound rule must allow TCP access from your originating IP (where you make the calls from, e.g. your laptop), to port 44300 of the SAP HANA DB server. Another option is to allow VNet injection when setting up your APIM service; please see Azure documentation for instructions. 

We have now created & tested our OData service as an API - the next section will show how to expose it as an MCP server and invoke it in a Copilot Studio Agent.

Proceed to [Step 2 - create MCP server and AI Agent](./Step%202%20-%20create%20MCP%20server%20and%20AI%20Agent.md) to continue.


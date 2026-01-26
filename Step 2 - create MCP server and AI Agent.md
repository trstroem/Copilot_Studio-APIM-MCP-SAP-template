# Create an MCP server and AI Agent

We have created & tested our OData service as an API. In order to consume it in the AI Agent we are to set up, we fist need to expose it as an MCP server. This allows the agent to select between different operations based on the user input. For example, a query such as "show me the 5 most valuable orders by gross amount" should point the agent towards the "Get entities from SalesOrderLineItemSet". The advantage of using an MCP server is that this allows the agent's LLM (model) to deduce which operation to execute, instead of us pointing explicitly towards it. MCP provides the tools for us.

In APIM, we can set this up very easily. On the left menu, simply select "MCP servers" and then, in the "Create MCP server" dropdown, select "Expose an API as an MCP server".

This will open the below screen:

![New MCP server](/media/mcp1.png)

Here, we need to select our existing API, followed by the API operations. This is the interesting part; it consists of deciding what we want our MCP server to be capable of doing - think of it as designing your own personal Swiss army knife. For an initial test scenario, you could select the following operations:

- Get entities from BusinessPartnerSet
- Get entities from ProductSet
- Get entities from SalesOrderLineItemSet
- Get entities from SalesOrderSet
- Get entities from ProductSet by key
- Get entities from SalesOrderLineItemSet by key
- Get entities from SalesOrderSet by key

All of these operations are read-only; you could also experiment with adding or deleting entities.

![Adding operations to MCP server](/media/mcp2.png)

Provide a name and description to your MCP server - and you're good to go.

After saving, you will have a URL for your new MCP server. Copy this - we will need it in the next step, which is setting up the Copilot Agent.

# Creating an agent in Copilot Studio

We're almost there. By now, we have an OData API exposed as an MCP server. This will become the tool in our new AI Agent.

Open Copilot Studio at copilotstudio.microsoft.com - you will need to sign in. The landing page will look like this:

![Copilot Studio](/media/studio1.png)

Click the Agents icon on the left, then "Create blank Agent":

![Copilot Studio - create agent](/media/studio2.png)

On this screen, you can select your model - the default one (GPT-4.1 as per January 2026) should be fine.

Click the Tools section at the top - now it's time to equip the agent with our MCP server:

![Copilot Studio - create agent](/media/studio3.png)

On the following screen, click the "New tool" button on the far left (don't bother with searching the MCP server options):

![Copilot Studio - create agent](/media/studio4.png)

Now, select Model Context Protocol:

![Copilot Studio - create agent](/media/studio5.png)

On the following screen, add a name for your MCP server (you can type the same name as the MCP in APIM or something else), and the MCP server URL we just copied from APIM. You also have to provide a description of 30 characters or more before being allowed to save...

![Copilot Studio - create agent](/media/studio6.png)

This, really, is the end of the road. Now, after saving, you should be able to have a conversation with your agent. In the Test pane on the right, try asking a question:

![Copilot Studio - create agent](/media/conversation.png)

Follow up with more questions - try exploring the capabilities of your agent. On the left hand of the screen, you should be able to see the technical "path" taken by the agent when it tries to retrieve information. You can also ask the agent which steps it did to retrieve the answer:

![Copilot Studio - create agent](/media/techdetails.png)

# Final comments and observations

We have created an agent with Copilot Studio allowing us to interact with SAP data using OData services and MCP. We have added basic authentication - a productive scenario would need principal propagation involving EntraID. For now, we have a working template we could use in more specific scenarios and use cases.

Publishing the agent can be done using Copilot Studio's built-in Publish functionality. In principle, agents can be published to an internal Copilot tenant or Teams - allowing users across your organization to add the agent to their personal toolset.

MCP servers exposing SAP OData services should be limited to specific scenarios. Adding more operations than needed could possibly bog down the agent - just as Swiss army knives with too many tools can be complicated to use... but feel free to experiment!

# Building a Copilot Studio Agent with SAP OData Integration

A comprehensive guide to creating a Copilot Studio Agent that interacts with SAP OData services in real-time using Azure API Management (APIM) and Model Context Protocol (MCP).

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Step-by-Step Instructions](#step-by-step-instructions)
  - [Step 1: Set Up Azure API Management](#step-1-set-up-azure-api-management)
  - [Step 2: Configure SAP OData Service Connection](#step-2-configure-sap-odata-service-connection)
  - [Step 3: Set Up Model Context Protocol (MCP)](#step-3-set-up-model-context-protocol-mcp)
  - [Step 4: Create Copilot Studio Agent](#step-4-create-copilot-studio-agent)
  - [Step 5: Configure Agent Actions](#step-5-configure-agent-actions)
  - [Step 6: Test and Validate](#step-6-test-and-validate)
- [Screenshots](#screenshots)
- [Troubleshooting](#troubleshooting)
- [Best Practices](#best-practices)
- [Resources](#resources)

## Overview

This guide walks you through building an intelligent Copilot Studio Agent that can interact with SAP OData services in real-time. The solution uses:

- **Copilot Studio**: Microsoft's platform for building conversational AI agents
- **Azure API Management (APIM)**: Manages and secures API connections to SAP
- **Model Context Protocol (MCP)**: Enables structured communication between the agent and backend services
- **SAP OData Services**: RESTful APIs that expose SAP business data

### Use Cases

- Query SAP business data through natural language
- Retrieve customer information, orders, and invoices
- Check inventory levels and product availability
- Access real-time SAP data without custom development

## Architecture

The solution follows a layered architecture:

```
┌─────────────────────────────────────┐
│     Copilot Studio Agent            │
│  (Natural Language Interface)       │
└─────────────┬───────────────────────┘
              │
              ▼
┌─────────────────────────────────────┐
│  Model Context Protocol (MCP)       │
│  (Structured Communication)         │
└─────────────┬───────────────────────┘
              │
              ▼
┌─────────────────────────────────────┐
│  Azure API Management (APIM)        │
│  (Security, Gateway, Throttling)    │
└─────────────┬───────────────────────┘
              │
              ▼
┌─────────────────────────────────────┐
│     SAP OData Services              │
│  (Business Data & Logic)            │
└─────────────────────────────────────┘
```

### Key Benefits

- **Security**: APIM provides authentication, authorization, and rate limiting
- **Scalability**: APIM handles traffic management and caching
- **Flexibility**: MCP enables structured data exchange
- **User-Friendly**: Natural language interface for SAP data access

## Prerequisites

Before starting, ensure you have:

### Required Accounts & Subscriptions

- [ ] **Microsoft 365 subscription** with Copilot Studio access
- [ ] **Azure subscription** with permissions to create resources
- [ ] **SAP system access** with OData service endpoints enabled
- [ ] **Valid SAP credentials** (username and password or OAuth tokens)

### Required Knowledge

- Basic understanding of REST APIs and OData
- Familiarity with Azure portal navigation
- Knowledge of SAP system architecture (helpful but not required)
- Understanding of conversational AI concepts

### Tools & Software

- Modern web browser (Edge, Chrome, or Firefox)
- Access to Azure Portal (portal.azure.com)
- Access to Copilot Studio (copilotstudio.microsoft.com)
- SAP system credentials and endpoint URLs

## Step-by-Step Instructions

### Step 1: Set Up Azure API Management

Azure API Management acts as a secure gateway between your Copilot Studio Agent and SAP OData services.

#### 1.1 Create APIM Instance

1. Navigate to the [Azure Portal](https://portal.azure.com)
2. Click **"Create a resource"**
3. Search for **"API Management"**
4. Click **"Create"**

**Configuration Details:**

- **Subscription**: Select your Azure subscription
- **Resource Group**: Create new or select existing (e.g., `rg-copilot-sap`)
- **Region**: Choose a region close to your SAP system
- **Resource name**: Enter a unique name (e.g., `apim-copilot-sap`)
- **Organization name**: Your organization name
- **Administrator email**: Your email address
- **Pricing tier**: Developer or Standard (based on requirements)

5. Click **"Review + Create"**
6. Click **"Create"** and wait for deployment (may take 30-45 minutes)

> **📸 Screenshot Location**: `images/step1-1-create-apim.png`

#### 1.2 Configure APIM Policies

1. Once deployed, go to your APIM resource
2. In the left menu, select **"APIs"**
3. Click **"+ Add API"**
4. Select **"HTTP"** or **"Blank API"**

**API Configuration:**

- **Display name**: `SAP OData API`
- **Name**: `sap-odata-api`
- **Web service URL**: Your SAP OData endpoint (e.g., `https://your-sap-system.com/sap/opu/odata/sap/`)
- **URL scheme**: HTTPS
- **API URL suffix**: `sap`

5. Click **"Create"**

> **📸 Screenshot Location**: `images/step1-2-configure-api.png`

#### 1.3 Add Authentication

1. Select your newly created API
2. Click on **"Settings"** tab
3. Scroll to **"Security"** section

**Configure Authentication:**

Choose one of the following methods:

**Option A: Basic Authentication**
- Select **"Basic"** authentication
- Enter SAP username and password
- Store credentials in Azure Key Vault (recommended)

**Option B: OAuth 2.0**
- Select **"OAuth 2.0"**
- Configure Authorization server
- Set Token endpoint URL from SAP
- Configure Client ID and Client Secret

4. Click **"Save"**

> **📸 Screenshot Location**: `images/step1-3-authentication.png`

#### 1.4 Configure CORS Policy

1. In your API, go to **"All operations"**
2. In the **"Inbound processing"** section, click **"</>  Code editor"**
3. Add the following CORS policy inside the `<inbound>` section:

```xml
<cors allow-credentials="true">
    <allowed-origins>
        <origin>https://copilotstudio.microsoft.com</origin>
        <origin>https://*.powerplatform.com</origin>
    </allowed-origins>
    <allowed-methods>
        <method>GET</method>
        <method>POST</method>
        <method>OPTIONS</method>
    </allowed-methods>
    <allowed-headers>
        <header>*</header>
    </allowed-headers>
</cors>
```

4. Click **"Save"**

> **📸 Screenshot Location**: `images/step1-4-cors-policy.png`

### Step 2: Configure SAP OData Service Connection

#### 2.1 Identify SAP OData Services

1. Log in to your SAP system
2. Navigate to transaction code **`/IWFND/MAINT_SERVICE`**
3. Identify the OData services you want to expose
4. Note the service name and endpoint URL

Common SAP OData services:
- `API_BUSINESS_PARTNER` - Business Partner data
- `API_SALES_ORDER_SRV` - Sales Orders
- `API_MATERIAL_STOCK_SRV` - Material Stock/Inventory
- `API_PRODUCT_SRV` - Product Master data

> **📸 Screenshot Location**: `images/step2-1-sap-services.png`

#### 2.2 Test OData Service Endpoint

1. Open your web browser
2. Navigate to your SAP OData service URL:
   ```
   https://your-sap-system.com/sap/opu/odata/sap/API_BUSINESS_PARTNER/$metadata
   ```
3. Verify that the metadata XML is returned
4. Test a simple GET request:
   ```
   https://your-sap-system.com/sap/opu/odata/sap/API_BUSINESS_PARTNER/A_BusinessPartner
   ```

> **📸 Screenshot Location**: `images/step2-2-test-endpoint.png`

#### 2.3 Add Operations to APIM

1. Return to Azure Portal and your APIM instance
2. Select your **"SAP OData API"**
3. Click **"+ Add operation"**

**Example Operation - Get Business Partners:**

- **Display name**: `Get Business Partners`
- **Name**: `get-business-partners`
- **URL**: `GET /A_BusinessPartner`
- **Description**: `Retrieve list of business partners from SAP`

4. Click **"Save"**
5. Repeat for other operations you need (e.g., Get Sales Orders, Get Products)

> **📸 Screenshot Location**: `images/step2-3-add-operations.png`

#### 2.4 Configure Query Parameters

1. For each operation, click on it to expand
2. Go to **"Frontend"** section
3. Click **"+ Add parameter"**

**Common OData Query Parameters:**

- **Name**: `$filter` | **Type**: Query | **Required**: No
- **Name**: `$select` | **Type**: Query | **Required**: No
- **Name**: `$top` | **Type**: Query | **Required**: No
- **Name**: `$skip` | **Type**: Query | **Required**: No
- **Name**: `$orderby` | **Type**: Query | **Required**: No

4. Click **"Save"**

> **📸 Screenshot Location**: `images/step2-4-query-parameters.png`

### Step 3: Set Up Model Context Protocol (MCP)

The Model Context Protocol provides structured communication between Copilot Studio and your backend services.

#### 3.1 Understand MCP Concepts

MCP enables:
- **Structured Data Exchange**: Define schemas for requests and responses
- **Type Safety**: Ensure data consistency between systems
- **Function Calling**: Enable the agent to call specific backend operations
- **Context Preservation**: Maintain conversation state across interactions

#### 3.2 Create MCP Schema Definition

1. In APIM, navigate to your API
2. Select **"Schemas"** from the left menu
3. Click **"+ Add"**

**Schema for Business Partner Response:**

```json
{
  "type": "object",
  "properties": {
    "BusinessPartner": {
      "type": "string",
      "description": "Business Partner Number"
    },
    "BusinessPartnerName": {
      "type": "string",
      "description": "Name of the Business Partner"
    },
    "BusinessPartnerCategory": {
      "type": "string",
      "description": "Category (Person/Organization)"
    },
    "Country": {
      "type": "string",
      "description": "Country Code"
    }
  }
}
```

4. Click **"Save"**

> **📸 Screenshot Location**: `images/step3-2-mcp-schema.png`

#### 3.3 Configure Request/Response Transformations

1. Select an operation in your API
2. In the **"Inbound processing"**, add transformation policy:

```xml
<set-header name="Accept" exists-action="override">
    <value>application/json</value>
</set-header>
<set-header name="Content-Type" exists-action="override">
    <value>application/json</value>
</set-header>
```

3. In the **"Outbound processing"**, add response transformation if needed
4. Click **"Save"**

> **📸 Screenshot Location**: `images/step3-3-transformations.png`

### Step 4: Create Copilot Studio Agent

#### 4.1 Access Copilot Studio

1. Navigate to [Copilot Studio](https://copilotstudio.microsoft.com)
2. Sign in with your Microsoft 365 account
3. Select your environment or create a new one

> **📸 Screenshot Location**: `images/step4-1-copilot-studio.png`

#### 4.2 Create a New Agent

1. Click **"Create"** in the left navigation
2. Select **"New agent"**
3. Choose **"Skip to configure"** (or use template if available)

**Agent Configuration:**

- **Name**: `SAP Business Assistant`
- **Description**: `AI assistant for accessing SAP business data`
- **Language**: Select your preferred language
- **Instructions**: 
  ```
  You are a helpful AI assistant that helps users access SAP business data.
  You can retrieve business partner information, sales orders, and product details.
  Always provide accurate data from SAP and format responses in a clear, user-friendly way.
  If you're unsure about any data, ask clarifying questions.
  ```

4. Click **"Create"**

> **📸 Screenshot Location**: `images/step4-2-create-agent.png`

#### 4.3 Configure Agent Settings

1. In your agent, click on **"Settings"** (gear icon)
2. Navigate to **"Agent details"**

**Configure:**

- **Icon**: Upload or select an icon
- **Color scheme**: Choose colors that match your branding
- **Fallback behavior**: Configure what happens when agent doesn't understand

3. Click **"Save"**

> **📸 Screenshot Location**: `images/step4-3-agent-settings.png`

### Step 5: Configure Agent Actions

Actions enable your agent to call APIM endpoints and interact with SAP.

#### 5.1 Add a New Action

1. In your agent, go to **"Actions"** tab
2. Click **"+ Add an action"**
3. Select **"From a description"** or **"From an API"**
4. Choose **"Custom API"**

> **📸 Screenshot Location**: `images/step5-1-add-action.png`

#### 5.2 Connect to APIM

1. Click **"Add a connection"**
2. Select **"New connection"**

**Connection Details:**

- **Connection name**: `SAP OData Connection`
- **API type**: `HTTP with Azure API Management`
- **APIM instance**: Select your APIM instance
- **API**: Select `SAP OData API`
- **Authentication**: Use the authentication configured in APIM

3. Click **"Create"**

> **📸 Screenshot Location**: `images/step5-2-apim-connection.png`

#### 5.3 Define Action - Get Business Partners

1. Click **"+ New action"**
2. Configure the action:

**Action Configuration:**

- **Action name**: `GetBusinessPartners`
- **Description**: `Retrieves business partner information from SAP based on search criteria`
- **Operation**: Select `GET /A_BusinessPartner`

**Input Parameters:**

- **Parameter name**: `searchTerm`
- **Type**: `String`
- **Description**: `Search term for business partner name`
- **Required**: No

**Output Parameters:**

- **Response type**: `JSON`
- **Schema**: Use the MCP schema defined earlier

3. Click **"Save"**

> **📸 Screenshot Location**: `images/step5-3-action-definition.png`

#### 5.4 Map Action Parameters

1. In the action configuration, go to **"Parameters"** section
2. Map input parameters to query parameters:

**Mapping Example:**

- **Agent Input** `searchTerm` → **API Query Parameter** `$filter=contains(BusinessPartnerName,'${searchTerm}')`

3. Configure response parsing to extract relevant fields
4. Click **"Save"**

> **📸 Screenshot Location**: `images/step5-4-parameter-mapping.png`

#### 5.5 Add More Actions (Optional)

Repeat steps 5.3-5.4 to add additional actions:

- **GetSalesOrders**: Retrieve sales order data
- **GetProducts**: Retrieve product information
- **GetInventory**: Check stock levels

> **📸 Screenshot Location**: `images/step5-5-multiple-actions.png`

### Step 6: Test and Validate

#### 6.1 Test in Copilot Studio

1. In Copilot Studio, click **"Test"** button (top right)
2. The test panel opens on the right side
3. Try sample queries:

**Sample Test Queries:**

```
"Show me information about business partner 1000"
"What are the recent sales orders?"
"Find business partners in Germany"
"Get product details for product 12345"
```

4. Verify the agent responds with SAP data
5. Check if the responses are formatted correctly

> **📸 Screenshot Location**: `images/step6-1-test-agent.png`

#### 6.2 Debug Issues

If the agent isn't working correctly:

1. Click on a response in the test panel
2. Select **"View details"**
3. Check the **"Action log"** for errors
4. Verify:
   - API endpoint is accessible
   - Authentication is working
   - Response format matches schema

> **📸 Screenshot Location**: `images/step6-2-debug.png`

#### 6.3 Monitor APIM Logs

1. Go to Azure Portal
2. Navigate to your APIM instance
3. Select **"Application Insights"** or **"Logs"**
4. Review request/response logs:
   - Check for authentication failures
   - Verify API calls are reaching SAP
   - Review response times and errors

> **📸 Screenshot Location**: `images/step6-3-apim-logs.png`

#### 6.4 Test End-to-End

1. Publish your agent (click **"Publish"**)
2. Choose a publishing channel:
   - **Microsoft Teams**: Test in Teams
   - **Web**: Generate embed code for website
   - **Mobile app**: Test on mobile devices

3. Conduct end-to-end testing with real users
4. Gather feedback and iterate

> **📸 Screenshot Location**: `images/step6-4-publish.png`

## Screenshots

All screenshots referenced in this guide are located in the `images/` directory:

```
images/
├── step1-1-create-apim.png
├── step1-2-configure-api.png
├── step1-3-authentication.png
├── step1-4-cors-policy.png
├── step2-1-sap-services.png
├── step2-2-test-endpoint.png
├── step2-3-add-operations.png
├── step2-4-query-parameters.png
├── step3-2-mcp-schema.png
├── step3-3-transformations.png
├── step4-1-copilot-studio.png
├── step4-2-create-agent.png
├── step4-3-agent-settings.png
├── step5-1-add-action.png
├── step5-2-apim-connection.png
├── step5-3-action-definition.png
├── step5-4-parameter-mapping.png
├── step5-5-multiple-actions.png
├── step6-1-test-agent.png
├── step6-2-debug.png
├── step6-3-apim-logs.png
└── step6-4-publish.png
```

> **Note**: Screenshots should be added to the `images/` directory as you follow the steps. Each screenshot corresponds to the specific step in the instructions.

## Troubleshooting

### Common Issues and Solutions

#### Issue 1: Authentication Failures

**Symptoms:**
- 401 Unauthorized errors in APIM logs
- Agent returns "Unable to access data" messages

**Solutions:**
1. Verify SAP credentials are correct
2. Check if SAP user has necessary authorizations
3. Ensure OAuth tokens haven't expired
4. Review APIM authentication policy configuration

#### Issue 2: CORS Errors

**Symptoms:**
- Browser console shows CORS policy errors
- Agent actions fail to call APIM

**Solutions:**
1. Verify CORS policy is configured in APIM
2. Add Copilot Studio domains to allowed origins
3. Ensure preflight OPTIONS requests are handled
4. Check that allowed methods include GET, POST, OPTIONS

#### Issue 3: Slow Response Times

**Symptoms:**
- Agent takes too long to respond
- Timeout errors in action logs

**Solutions:**
1. Implement caching in APIM
2. Optimize OData queries with $select and $filter
3. Increase APIM tier for better performance
4. Use $top to limit result sets

#### Issue 4: Data Format Mismatches

**Symptoms:**
- Agent can't parse SAP responses
- Action logs show schema validation errors

**Solutions:**
1. Review MCP schema definition
2. Check OData response format in APIM test console
3. Add transformation policies to normalize data
4. Update action output parameters to match actual response

#### Issue 5: SAP Service Not Accessible

**Symptoms:**
- Connection timeout errors
- APIM can't reach SAP endpoint

**Solutions:**
1. Verify SAP system is accessible from Azure
2. Check network security groups and firewalls
3. Ensure SAP OData service is activated
4. Test connectivity from APIM test console

### Getting Help

If you encounter issues not covered here:

1. **Check APIM diagnostics**: Review detailed logs in Application Insights
2. **Test direct API calls**: Use Postman or curl to test APIM endpoints
3. **Review Copilot Studio docs**: Visit [Microsoft Learn](https://learn.microsoft.com/copilot-studio)
4. **SAP documentation**: Check SAP NetWeaver Gateway documentation
5. **Community forums**: Post questions on Microsoft Q&A or SAP Community

## Best Practices

### Security

1. **Never expose SAP credentials**: Always use Azure Key Vault
2. **Implement rate limiting**: Protect SAP backend from excessive calls
3. **Use OAuth 2.0**: Prefer OAuth over basic authentication
4. **Enable APIM logging**: Monitor for security issues
5. **Restrict CORS**: Only allow necessary domains
6. **Use HTTPS**: Ensure all connections are encrypted

### Performance

1. **Cache responses**: Implement caching in APIM for frequently accessed data
2. **Optimize queries**: Use OData $select to retrieve only needed fields
3. **Limit result sets**: Use $top and pagination for large datasets
4. **Monitor metrics**: Track response times and error rates
5. **Scale APIM**: Use appropriate tier for your workload

### Agent Design

1. **Clear instructions**: Provide comprehensive agent instructions
2. **Handle errors gracefully**: Configure fallback responses
3. **Validate inputs**: Use parameter validation in actions
4. **Format responses**: Make data easy for users to understand
5. **Test thoroughly**: Test with various user queries and edge cases

### Maintenance

1. **Version your API**: Use API versioning in APIM
2. **Document changes**: Keep documentation updated
3. **Monitor usage**: Track which actions are most used
4. **Regular testing**: Verify integration after SAP or Azure updates
5. **Backup configuration**: Export and save APIM and agent configurations

## Resources

### Microsoft Documentation

- [Copilot Studio Overview](https://learn.microsoft.com/microsoft-copilot-studio/)
- [Azure API Management Documentation](https://learn.microsoft.com/azure/api-management/)
- [Model Context Protocol (MCP)](https://modelcontextprotocol.io/)
- [Copilot Studio Actions](https://learn.microsoft.com/microsoft-copilot-studio/advanced-plugin-actions)

### SAP Documentation

- [SAP Gateway and OData](https://help.sap.com/docs/SAP_NETWEAVER_750/68bf513362174d54b58cddec28794093/e15f1fb7954744f4a3a8a23e0d13ba67.html)
- [SAP API Business Hub](https://api.sap.com/)
- [OData Protocol](https://www.odata.org/)

### Additional Resources

- [Azure Architecture Center](https://learn.microsoft.com/azure/architecture/)
- [REST API Best Practices](https://learn.microsoft.com/azure/architecture/best-practices/api-design)
- [Conversational AI Patterns](https://learn.microsoft.com/azure/architecture/ai-ml/openai/conversational-ai-patterns)

### Sample OData Queries

**Filter by specific criteria:**
```
$filter=Country eq 'DE'
```

**Select specific fields:**
```
$select=BusinessPartner,BusinessPartnerName,Country
```

**Order results:**
```
$orderby=BusinessPartnerName asc
```

**Limit results:**
```
$top=10
```

**Combined query:**
```
$filter=Country eq 'DE'&$select=BusinessPartner,BusinessPartnerName&$top=10
```

---

## Contributing

This is a template repository. Feel free to fork and adapt it for your specific needs.

## License

This documentation is provided as-is for educational and implementation purposes.

## Support

For questions or issues specific to:
- **Copilot Studio**: Contact Microsoft Support
- **Azure API Management**: Submit Azure support ticket
- **SAP OData**: Contact SAP Support or your SAP Basis team

---

**Last Updated**: January 2026

**Version**: 1.0

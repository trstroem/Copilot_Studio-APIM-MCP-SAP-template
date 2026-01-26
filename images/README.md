# Screenshots Directory

This directory contains all screenshots referenced in the main documentation.

## Screenshot Guidelines

When adding screenshots to this directory, follow these guidelines:

### Naming Convention

Screenshots should be named according to the step they illustrate:
- Format: `step{step-number}-{sub-step}-{description}.png`
- Example: `step1-1-create-apim.png`

### Required Screenshots

Below is a checklist of all screenshots needed for the complete documentation:

#### Step 1: Azure API Management Setup
- [ ] `step1-1-create-apim.png` - Creating APIM instance in Azure Portal
- [ ] `step1-2-configure-api.png` - Configuring the SAP OData API in APIM
- [ ] `step1-3-authentication.png` - Setting up authentication (Basic or OAuth)
- [ ] `step1-4-cors-policy.png` - Configuring CORS policy in APIM

#### Step 2: SAP OData Service Configuration
- [ ] `step2-1-sap-services.png` - SAP transaction showing available OData services
- [ ] `step2-2-test-endpoint.png` - Testing OData endpoint in browser
- [ ] `step2-3-add-operations.png` - Adding operations to APIM
- [ ] `step2-4-query-parameters.png` - Configuring query parameters

#### Step 3: Model Context Protocol Setup
- [ ] `step3-2-mcp-schema.png` - Creating MCP schema definition
- [ ] `step3-3-transformations.png` - Configuring request/response transformations

#### Step 4: Copilot Studio Agent Creation
- [ ] `step4-1-copilot-studio.png` - Copilot Studio main interface
- [ ] `step4-2-create-agent.png` - Creating a new agent
- [ ] `step4-3-agent-settings.png` - Configuring agent settings

#### Step 5: Agent Actions Configuration
- [ ] `step5-1-add-action.png` - Adding a new action to the agent
- [ ] `step5-2-apim-connection.png` - Connecting to APIM
- [ ] `step5-3-action-definition.png` - Defining the GetBusinessPartners action
- [ ] `step5-4-parameter-mapping.png` - Mapping action parameters
- [ ] `step5-5-multiple-actions.png` - Overview of multiple configured actions

#### Step 6: Testing and Validation
- [ ] `step6-1-test-agent.png` - Testing the agent with sample queries
- [ ] `step6-2-debug.png` - Debugging action issues
- [ ] `step6-3-apim-logs.png` - Viewing APIM logs in Azure Portal
- [ ] `step6-4-publish.png` - Publishing the agent

## Screenshot Best Practices

### Quality Standards
- **Resolution**: Minimum 1920x1080 for desktop screenshots
- **Format**: PNG with compression
- **Content**: Clear, focused on relevant UI elements
- **Annotations**: Use red boxes or arrows to highlight important areas
- **Sensitivity**: Remove or blur any sensitive information (credentials, personal data, internal URLs)

### What to Capture

1. **Full Context**: Include enough of the screen to show where the user is
2. **Key UI Elements**: Ensure buttons, forms, and settings are clearly visible
3. **Before and After**: For configuration changes, show the before and after states if helpful
4. **Success Messages**: Capture confirmation messages after completing steps

### Tools for Screenshots

- **Windows**: Snipping Tool, Windows + Shift + S, or Greenshot
- **Mac**: Command + Shift + 4, or CleanShot X
- **Browser**: Full page screenshot extensions
- **Annotation**: Use tools like Snagit, Greenshot, or built-in OS tools

### Privacy and Security

⚠️ **Important**: Before adding screenshots to this repository:

- Remove any sensitive business data
- Mask usernames, passwords, and credentials
- Obscure internal URLs and IP addresses
- Blur out proprietary SAP data
- Remove personal information (names, emails, phone numbers)

## Contributing Screenshots

If you're adding screenshots to this repository:

1. Follow the naming convention
2. Ensure images are optimized (compressed) to keep repository size manageable
3. Add alt text descriptions in the main README.md
4. Verify that no sensitive information is visible
5. Update this checklist when adding new screenshots

## Placeholder Images

If screenshots are not yet available, the documentation uses placeholders like:

> **📸 Screenshot Location**: `images/step1-1-create-apim.png`

These placeholders help readers understand where visual aids should appear while also serving as a guide for contributors.

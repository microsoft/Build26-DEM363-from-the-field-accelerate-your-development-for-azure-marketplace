# Azure Marketplace ARM/Bicep Deployment Workflow

This guide describes the complete workflow for creating, validating, packaging, and publishing an Azure Marketplace offer using Bicep, ARM templates, and a custom Azure Marketplace UI definition.

---

# Prerequisites

Before starting, ensure the following tools are installed:

- Azure CLI
- Bicep CLI
- PowerShell
- ARM Template Toolkit (arm-ttk)
- Access to Azure Marketplace Partner Center

---

# Step 1 - Declare Your Resources

Create a Bicep template and parameter file.

## Files

- `mainTemplate.bicep`
- `mainTemplate.parameters.json`

## Example Structure

```text
project/
│
├── mainTemplate.bicep
├── mainTemplate.parameters.json
├── createUiDefinition.json
└── mainTemplate.json
```

---

# Step 2 - Transpile the Bicep File to an ARM Template

Generate the ARM template from the Bicep file.

## Command

```powershell
az bicep build --file .\mainTemplate.bicep --outdir .\
```

## Result

This command creates:

```text
mainTemplate.json
```

---

# Step 3 - Test Deploy the ARM Template

Validate the deployment before publishing to Azure Marketplace.

## Command

```powershell
az deployment group create `
  --name 'MP-Test' `
  --resource-group 'MP-Test' `
  --template-file '.\mainTemplate.json' `
  --parameters .\mainTemplate.parameters.json
```

## Purpose

This step verifies:

- ARM template syntax
- Parameter mapping
- Resource deployment logic
- Dependencies
- Azure compatibility

---

# Step 4 - Design the Azure Marketplace UI Experience

Create the Azure Marketplace UI definition file:

```text
createUiDefinition.json
```

This file defines the deployment wizard users see in Azure Marketplace.

## Useful URLs

### Marketplace Sandbox

https://aka.ms/masandbox

### Marketplace UI Elements Documentation

https://aka.ms/maelements

## Common UI Components

- Textboxes
- Dropdowns
- Password fields
- Toggles
- Resource selectors
- Validation rules

---

# Step 5 - Map UI Outputs to ARM/Bicep Parameters

Ensure all outputs from:

```text
createUiDefinition.json
```

match the parameters defined in:

```text
mainTemplate.bicep
```

## Example

### UI Output

```json
"outputs": {
  "vmName": "[steps('basics').vmName]"
}
```

### Matching Bicep Parameter

```bicep
param vmName string
```

---

# Step 6 - Package the Marketplace Files

Create a ZIP archive containing:

- `mainTemplate.json`
- `createUiDefinition.json`

## Recommended Naming Convention

Include the version number in the ZIP filename.

### Example

```text
MyOffer_v0.0.1.zip
```

---

# Step 7 - Validate the ARM Template Using arm-ttk

Use Microsoft's ARM Template Toolkit (arm-ttk) to validate the deployment package.

## Download

https://aka.ms/arm-ttk

## PowerShell Commands

```powershell
Clear-Host

Set-Location "<path>\arm-ttk-master\arm-ttk"

Import-Module .\arm-ttk.psd1

Set-Location "<path>\version"

Test-AzTemplate
```

## Validation Checks

The toolkit validates:

- ARM best practices
- Naming conventions
- Template formatting
- Output consistency
- Marketplace requirements

---

# Step 8 - Upload the Offer to Azure Marketplace

Upload the ZIP package to Partner Center.

## Steps

1. Open Partner Center
2. Create or edit your Marketplace offer
3. Upload the deployment package
4. Configure offer metadata
5. Submit for validation

## Validation Includes

- ARM template validation
- Security checks
- UI validation
- Deployment testing

---

# Step 9 - Final Result

After successful validation and publishing:

- Customers can deploy the solution directly from Azure Marketplace
- The Marketplace wizard uses your custom UI
- The ARM/Bicep template deploys the resources automatically

---

# Recommended Workflow Summary

```text
Bicep Template
      ↓
Build ARM Template
      ↓
Test Deployment
      ↓
Create Marketplace UI
      ↓
Map Parameters
      ↓
Package ZIP
      ↓
Run arm-ttk
      ↓
Upload to Marketplace
      ↓
Publish Offer
```

---

# Additional Recommendations

## Versioning

Always version your deployment packages.

Example:

```text
v0.0.1
v0.0.2
v1.0.0
```

## Source Control

Store all Marketplace files in Git:

- Bicep templates
- ARM templates
- UI definitions
- Parameter files

## CI/CD

Consider automating:

- Bicep build
- ARM validation
- arm-ttk testing
- ZIP packaging

using:

- GitHub Actions
- Azure DevOps Pipelines

---

# Useful Resources

## Azure Marketplace Sandbox

https://aka.ms/masandbox

## Marketplace UI Definition Elements

https://aka.ms/maelements

## ARM Template Toolkit

https://aka.ms/arm-ttk

## Azure Bicep Documentation

https://learn.microsoft.com/azure/azure-resource-manager/bicep/

## Azure Marketplace Documentation

https://learn.microsoft.com/partner-center/marketplace/

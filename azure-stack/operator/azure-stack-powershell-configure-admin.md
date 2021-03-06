---
title: Connect to Azure Stack with PowerShell | Microsoft Docs
description: Learn how to connect to Azure Stack with PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''

ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 09/19/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/19/2019
---

# Connect to Azure Stack with PowerShell

*Applies to: Azure Stack integrated systems and Azure Stack Development Kit*

You can configure Azure Stack to use PowerShell to manage resources like creating offers, plans, quotas, and alerts. This topic helps you configure the operator environment.

## Prerequisites

Run the following prerequisites either from the [Azure Stack Development Kit (ASDK)](../asdk/asdk-connect.md#connect-with-rdp) or from a Windows-based external client if you're [connected to the ASDK through VPN](../asdk/asdk-connect.md#connect-with-vpn).

- Install [Azure Stack-compatible Azure PowerShell modules](azure-stack-powershell-install.md).  
- Download the [tools required to work with Azure Stack](azure-stack-powershell-download.md).  

## Connect with Azure AD

To configure the Azure Stack operator environment with PowerShell, run one of the scripts below. Replace the Azure Active Directory (Azure AD) tenantName and Azure Resource Manager endpoint values with your own environment configuration.

[!include[Remove Account](../../includes/remove-account.md)]

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    # Set your tenant name.
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

## Connect with AD FS

Connect to the Azure Stack operator environment with PowerShell with Azure Active Directory Federated Services (Azure AD FS). For the ASDK, this Azure Resource Manager endpoint is set to `https://adminmanagement.local.azurestack.external`. To get the Azure Resource Manager endpoint for Azure Stack integrated systems, contact your service provider.

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

  # Sign in to your environment.
  Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
  ```

> [!Note]  
> AD FS only supports interactive authentication with user identities. If a credential object is required, you must use a service principal (SPN). For more information on setting up a service principal with Azure Stack and AD FS as your identity management service, see [Manage an AD FS service principal](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

## Test the connectivity

Now that you've got everything set-up, use PowerShell to create resources within Azure Stack. For example, you can create a resource group for an app and add a virtual machine. Use the following command to create a resource group named **MyResourceGroup**.

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## Next steps

- [Develop templates for Azure Stack](../user/azure-stack-develop-templates.md).
- [Deploy templates with PowerShell](../user/azure-stack-deploy-template-powershell.md).
  - [Azure Stack Module Reference](https://docs.microsoft.com/powershell/azure/azure-stack/overview).

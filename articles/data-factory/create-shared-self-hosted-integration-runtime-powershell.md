---
title: Erstellen einer freigegebenen selbstgehosteten Integration Runtime mit PowerShell
description: Erfahren Sie, wie Sie in Azure Data Factory eine freigegebene, selbstgehostete Integration Runtime erstellen, damit mehrere Data Factorys auf die Integration Runtime zugreifen können.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: anansub
ms.custom: seo-lt-2019
ms.date: 06/10/2020
ms.openlocfilehash: 8734247a913bdf6a44a9156f6f87705b618f7228
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92632888"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Erstellen einer freigegebenen selbstgehosteten Integration Runtime in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Leitfaden erfahren Sie, wie Sie eine freigegebene selbstgehostete Integration Runtime in Azure Data Factory erstellen. Anschließend können Sie die freigegebene, selbstgehostete Integration Runtime in einer anderen Data Factory verwenden.

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>Erstellen einer freigegebenen selbstgehosteten IR über die Azure Data Factory-Benutzeroberfläche

Sie können die unten angegebenen Schritte ausführen, um über die Azure Data Factory-Benutzeroberfläche eine freigegebene selbstgehostete IR zu erstellen.

1. Wählen Sie in der freizugebenden selbstgehosteten Integration Runtime die Option **Anderen Data Factory die Berechtigung erteilen** , und wählen Sie auf der Seite „Integration Runtime-Setup“ die Data Factory aus, in der Sie die verknüpfte Integration Runtime erstellen möchten.
      
    ![Schaltfläche zum Erteilen von Berechtigungen auf der Registerkarte „Freigabe“](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)  
    
2. Notieren und kopieren Sie die obige „Ressourcen-ID“ der freizugebenden selbstgehosteten IR.
         
3. Erstellen Sie in der Data Factory, für die Berechtigungen erteilt wurden, eine neue selbstgehostete IR (verknüpft), und geben Sie die Ressourcen-ID ein.
      
    ![Schaltfläche zum Erstellen einer selbstgehosteten Integration Runtime](media/create-self-hosted-integration-runtime/create-linkedir-1.png)
   
    ![Schaltfläche zum Erstellen einer verknüpften selbstgehosteten Integration Runtime](media/create-self-hosted-integration-runtime/create-linkedir-2.png) 

    ![Felder für Name und Ressourcen-ID](media/create-self-hosted-integration-runtime/create-linkedir-3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>Erstellen einer freigegebenen selbstgehosteten IR mithilfe von Azure PowerShell

Sie können die unten angegebenen Schritte ausführen, um mithilfe von Azure PowerShell eine freigegebene selbstgehostete IR zu erstellen. 
1. Erstellen einer Data Factory. 
1. Erstellen Sie eine selbstgehostete Integration Runtime.
1. Freigeben der selbstgehosteten Integration Runtime für andere Data Factorys.
1. Erstellen einer verknüpften Integration Runtime.
1. Aufheben der Freigabe.

### <a name="prerequisites"></a>Voraussetzungen 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-Abonnement** . Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. 

- **Azure PowerShell** . Befolgen Sie die Anweisungen unter [Installieren von Azure PowerShell unter Windows mit PowerShellGet](/powershell/azure/install-az-ps). Verwenden Sie PowerShell zum Ausführen eines Skripts, um eine selbstgehostete Integration Runtime zu erstellen, die für andere Data Factorys freigegeben werden kann. 

> [!NOTE]  
> Eine Liste der Azure-Regionen, in denen Data Factory derzeit verfügbar ist, finden Sie, indem Sie die für Sie interessanten Regionen auswählen: [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

### <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Starten Sie die Windows PowerShell Integrated Scripting Environment (ISE).

1. Erstellen Sie Variablen. Kopieren und fügen Sie das folgende Skript ein. Ersetzen Sie Variablen wie **SubscriptionName** und **ResourceGroupName** durch tatsächliche Werte: 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. Melden Sie sich an, und wählen Sie ein Abonnement aus. Fügen Sie dem Skript zum Anmelden bei Ihrem Azure-Abonnement und Auswählen des Abonnements den folgenden Code hinzu:

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. Erstellen Sie eine Ressourcengruppe und eine Data Factory.

    > [!NOTE]  
    > Dieser Schritt ist optional. Wenn Sie bereits eine Data Factory verwenden, überspringen Sie diesen Schritt. 

    Erstellen Sie mit dem Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md). Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und als Gruppe verwaltet werden. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen `myResourceGroup` am Standort „WestEurope“ erstellt: 

    ```powershell
    New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Führen Sie den folgenden Befehl aus, um eine Data Factory zu erstellen: 

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

### <a name="create-a-self-hosted-integration-runtime"></a>Erstellen einer selbstgehosteten Integration Runtime

> [!NOTE]  
> Dieser Schritt ist optional. Wenn Sie bereits über die selbstgehostete Integration Runtime verfügen, die Sie für andere Data Factorys freigeben möchten, überspringen Sie diesen Schritt.

Führen Sie den folgenden Befehl aus, um eine selbstgehostete Integration Runtime zu erstellen:

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Abzurufen des Integration Runtime-Authentifizierungsschlüssels und Registrieren eines Knotens

Führen Sie den folgenden Befehl aus, um den Authentifizierungsschlüssel für die selbstgehostete Integration Runtime abzurufen:

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

Die Antwort enthält den Authentifizierungsschlüssel für diese selbstgehostete Integration Runtime. Verwenden Sie diesen Schlüssel beim Registrieren des Integration Runtime-Knotens.

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Installieren und Registrieren der selbstgehosteten Integration Runtime

1. Laden Sie den Installer der selbstgehosteten Integration Runtime-Installation von [Azure Data Factory Integration Runtime](https://aka.ms/dmg) herunter.

2. Führen Sie den Installer aus, um die selbstgehostete Integration Runtime auf einem lokalen Computer zu installieren.

3. Registrieren Sie die neue selbstgehostete Integration Runtime mit dem Authentifizierungsschlüssel, den Sie in einem vorherigen Schritt abgerufen haben.

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Freigeben der selbstgehosteten Integration Runtime für eine andere Data Factory

#### <a name="create-another-data-factory"></a>Erstellen einer anderen Data Factory

> [!NOTE]  
> Dieser Schritt ist optional. Wenn Sie bereits über die Data Factory verfügen, für die Sie die Freigabe ausführen möchten, überspringen Sie diesen Schritt. Um jedoch Rollenzuweisungen hinzufügen oder entfernen zu können, benötigen Sie `Microsoft.Authorization/roleAssignments/write`- und `Microsoft.Authorization/roleAssignments/delete`-Berechtigungen, wie z. B. [Benutzerzugriffsadministrator](../role-based-access-control/built-in-roles.md#user-access-administrator) oder [Besitzer](../role-based-access-control/built-in-roles.md#owner).

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>Erteilen einer Berechtigung

Erteilen Sie die Berechtigung für die Data Factory, die auf die selbstgehostete Integration Runtime zugreifen muss, die Sie erstellt und registriert haben.

> [!IMPORTANT]  
> Dieser Schritt darf nicht übersprungen werden!

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>Erstellen einer verknüpften, selbstgehosteten Integration Runtime

Führen Sie den folgenden Befehl aus, um eine verknüpfte, selbstgehostete Integration Runtime zu erstellen:

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Jetzt können Sie diese verknüpften Integration Runtime in einem beliebigen verknüpften Dienst verwenden. Die verknüpfte Integration Runtime verwendet die freigegebene Integration Runtime, um Aktivitäten auszuführen.

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Aufheben der Freigabe der Integration Runtime über eine Data Factory

Um den Zugriff einer Data Factory auf die freigegebene Integration Runtime aufzuheben, führen Sie den folgenden Befehl aus:

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

Um die vorhandenen verknüpfte Integration Runtime zu entfernen, führen Sie den folgenden Befehl für die freigegebene Integration Runtime aus:

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="next-steps"></a>Nächste Schritte

- Lesen Sie [Integration Runtime-Konzepte in Azure Data Factory](./concepts-integration-runtime.md).

- Informieren Sie sich über das [Erstellen einer selbstgehosteten Integration Runtime im Azure-Portal](./create-self-hosted-integration-runtime.md).
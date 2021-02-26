---
title: Erstellen eines ausführenden Azure Automation-Kontos
description: In diesem Artikel wird beschrieben, wie Sie mit PowerShell oder über das Azure-Portal ein ausführendes Konto erstellen.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: d6c8e96c325cfa6422ceb4efc55ef0c5cc149ae3
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056838"
---
# <a name="how-to-create-an-azure-automation-run-as-account"></a>Erstellen eines ausführenden Azure Automation-Kontos

Ausführende Konten in Azure Automation ermöglichen die Authentifizierung für die Verwaltung von Ressourcen mit dem Azure Resource Manager- oder dem klassischen Azure-Bereitstellungsmodell mithilfe von Automation-Runbooks und anderen Automation-Features. In diesem Artikel wird beschrieben, wie Sie über das Azure-Portal oder mit der Azure PowerShell ein ausführendes Konto oder ein klassisches ausführendes Konto erstellen.

## <a name="create-account-in-azure-portal"></a>Erstellen eines Kontos im Azure-Portal

Führen Sie die folgenden Schritte aus, um Ihr Azure Automation-Konto im Azure-Portal zu aktualisieren. Das ausführende Konto und das klassische ausführende Konto werden separat erstellt. Wenn Sie keine klassischen Ressourcen verwalten müssen, ist es nur erforderlich, das ausführende Azure-Konto zu erstellen.

1. Melden Sie sich mit einem Konto, das Mitglied der Rolle „Abonnement-Administratoren“ und Co-Administrator des Abonnements ist, beim Azure-Portal an.

2. Suchen Sie nach **Automation-Konten**, und wählen Sie diese Option aus.

3. Wählen Sie auf der Seite **Automation-Konten** in der entsprechenden Liste Ihr Automation-Konto aus.

4. Wählen Sie im linken Bereich im Abschnitt **Kontoeinstellungen** die Option **Ausführende Konten** aus.

    :::image type="content" source="media/create-run-as-account/automation-account-properties-pane.png" alt-text="Auswählen der Option „Ausführendes Konto“":::

5. Abhängig vom gewünschten Konto verwenden Sie den Bereich **+ Ausführendes Azure-Konto** oder **+ Klassisches ausführendes Azure-Konto**. Klicken Sie nach dem Überprüfen der Übersichtsinformationen auf **Erstellen**.

    :::image type="content" source="media/create-run-as-account/automation-account-create-run-as.png" alt-text="Auswählen der Option zum Erstellen eines ausführenden Kontos":::

6. Während das ausführende Konto in Azure erstellt wird, können Sie den Status unter **Benachrichtigungen** im Menü nachverfolgen. Es wird auch ein Banner mit dem Hinweis angezeigt, dass das Konto erstellt wird. Der Vorgang kann einige Minuten dauern.

## <a name="create-account-using-powershell"></a>Erstellen eines Kontos mithilfe von PowerShell

In der folgenden Liste sind die Anforderungen zum Erstellen eines ausführenden Kontos in PowerShell mithilfe eines bereitgestellten Skripts aufgeführt. Diese Anforderungen gelten für beide Typen von ausführenden Konten.

* Windows 10 oder Windows Server 2016 mit Azure Resource Manager-Modul 3.4.1 und höher. Das PowerShell-Skript unterstützt keine früheren Versionen von Windows.
* Azure PowerShell 6.2.4 oder höher. Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-az-ps).
* Ein Automation-Konto, auf das als Wert für die Parameter `AutomationAccountName` und `ApplicationDisplayName` verwiesen wird.
* Berechtigungen, die den unter [Berechtigungen zum Konfigurieren von ausführenden Konten](automation-security-overview.md#permissions) aufgelisteten entsprechen.

Führen Sie die folgenden Schritte aus, um die Werte für `AutomationAccountName`, `SubscriptionId` und `ResourceGroupName` abzurufen. Dies sind erforderliche Parameter für das PowerShell-Skript.

1. Melden Sie sich beim Azure-Portal an.

1. Suchen Sie nach **Automation-Konten**, und wählen Sie diese Option aus.

1. Wählen Sie auf der Seite „Automation-Konten“ in der entsprechenden Liste Ihr Automation-Konto aus.

1. Wählen Sie im linken Bereich **Eigenschaften** aus.

1. Notieren Sie sich auf der Seite **Eigenschaften** die Werte für **Name**, **Abonnement-ID** und **Ressourcengruppe**.

   ![Seite „Eigenschaften“ des Automation-Kontos](media/create-run-as-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>PowerShell-Skript zum Erstellen eines ausführenden Kontos

Das PowerShell-Skript unterstützt mehrere Konfigurationen.

* Erstellen eines ausführenden Kontos mit einem selbstsignierten Zertifikat
* Erstellen Sie ein ausführendes Konto und/oder ein klassisches ausführendes Konto mit einem selbstsignierten Zertifikat.
* Erstellen Sie ein ausführendes Konto und/oder ein klassisches ausführendes Konto, indem Sie ein Zertifikat nutzen, das von Ihrer Unternehmenszertifizierungsstelle ausgestellt wurde.
* Erstellen Sie ein ausführendes Konto und/oder ein klassisches ausführendes Konto mit einem selbstsignierten Zertifikat in der Azure Government-Cloud.

1. Laden Sie das Skript herunter, und speichern Sie es mit dem folgenden Befehl in einem lokalen Ordner.

    ```powershell
    wget https://raw.githubusercontent.com/azureautomation/runbooks/master/Utility/AzRunAs/Create-RunAsAccount.ps1 -outfile Create-RunAsAccount.ps1
    ```

2. Starten Sie PowerShell mit erhöhten Benutzerrechten, und navigieren Sie zu dem Ordner mit dem Skript.

3. Führen Sie einen der folgenden Befehle aus, um entsprechend Ihren Anforderungen ein ausführendes Konto und/oder ein klassisches ausführendes Konto zu erstellen.

    * Erstellen eines ausführenden Kontos mit einem selbstsignierten Zertifikat

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
        ```

    * Erstellen eines ausführenden Kontos und eines klassischen ausführenden Kontos mit einem selbstsignierten Zertifikat

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
        ```

    * Erstellen eines ausführenden Kontos und eines klassischen ausführenden Kontos mit einem Unternehmenszertifikat

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
        ```

        Wenn Sie ein klassisches ausführendes Konto mit einem öffentlichen Unternehmenszertifikat (CER-Datei) erstellt haben, sollten Sie dieses Zertifikat verwenden. Das Skript erstellt und speichert es im Ordner für temporäre Dateien auf Ihrem Computer unter dem Benutzerprofil `%USERPROFILE%\AppData\Local\Temp`, das Sie zum Ausführen der PowerShell-Sitzung verwendet haben. Siehe [Hochladen eines Verwaltungs-API-Zertifikats in das Azure-Portal](../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

    * Erstellen eines ausführenden Kontos und eines klassischen ausführenden Kontos mit einem selbstsignierten Zertifikat in der Azure Government-Cloud

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. Nach dem Ausführen des Skripts werden Sie aufgefordert, sich gegenüber Azure zu authentifizieren. Melden Sie sich mit einem Konto an, das Mitglied der Rolle „Abonnement-Administratoren“ ist. Wenn Sie ein klassisches ausführendes Konto erstellen, muss das Konto Co-Admin des Abonnements sein.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur grafischen Erstellung finden Sie unter [Erstellen grafischer Runbooks in Azure Automation](automation-graphical-authoring-intro.md).
* Informationen zu den ersten Schritten mit PowerShell-Runbooks finden Sie unter [Tutorial: Erstellen eines PowerShell-Runbooks](learn/automation-tutorial-runbook-textual-powershell.md).
* Informationen zu den ersten Schritten mit Python 3-Runbooks finden Sie im [Tutorial: Erstellen eines Python 3-Runbooks](learn/automation-tutorial-runbook-textual-python-3.md).
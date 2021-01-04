---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 26c158145de6ce729d8a7060152b19fb14b63d58
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95561962"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Vorbereiten der Authentifizierung von Anforderungen von Azure Resource Manager
Sie müssen alle Vorgänge authentifizieren, die Sie mithilfe von [Azure Resource Manager][lnk-authenticate-arm] mit Azure Active Directory (AD) für die Ressourcen ausführen. Die einfachste Möglichkeit hierzu ist die Verwendung von PowerShell oder der Azure-Befehlszeilenschnittstelle.

Installieren Sie die [Azure PowerShell-Cmdlets][lnk-powershell-install], bevor Sie den Vorgang fortsetzen.

Die folgenden Schritte zeigen, wie Sie die Kennwortauthentifizierung für eine AD-Anwendung mithilfe von PowerShell einrichten. Sie können diese Befehle in einer standardmäßigen PowerShell-Sitzung ausführen.

1. Melden Sie sich mithilfe des folgenden Befehls bei Ihrem Azure-Abonnement an:

    ```powershell
    Connect-AzAccount
    ```

1. Wenn Sie über mehrere Azure-Abonnements verfügen, erhalten Sie durch die Anmeldung bei Azure Zugriff auf alle Azure-Abonnements, die mit Ihren Anmeldeinformationen verknüpft sind. Führen Sie den folgenden Befehl aus, um eine Liste der Azure-Abonnements anzuzeigen, die Sie verwenden können:

    ```powershell
    Get-AzSubscription
    ```

    Führen Sie den folgenden Befehl aus, um das Abonnement auszuwählen, das Sie zum Ausführen der Befehle zum Verwalten Ihres IoT Hubs verwenden möchten. Sie können entweder den Abonnementnamen oder die ID aus der Ausgabe des vorherigen Befehls verwenden:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Notieren Sie sich Ihre **TenantId** und Ihre **SubscriptionId**. Diese werden später benötigt.
3. Erstellen Sie mit dem folgenden Befehl eine neue Azure Active Directory-Anwendung, und ersetzen Sie die Platzhalter:
   
   * **{Display name}:** ein Anzeigename für Ihre Anwendung, z.B. **MySampleApp**.
   * **{Home page URL}:** Die URL der Startseite der App, z. B. **http:\//mysampleapp/home**. Diese URL muss nicht auf eine echte Anwendung verweisen.
   * **{Anwendungs-ID}:** Ein eindeutiger Bezeichner, z. B. **http:\//mysampleapp**. Diese URL muss nicht auf eine echte Anwendung verweisen.
   * **{Kennwort}:** Ein Kennwort, das Sie zur Authentifizierung mit Ihrer App verwenden werden.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Notieren Sie sich die **ApplicationId** der Anwendung, die Sie erstellt haben. Sie benötigen sie später.
5. Erstellen Sie einen neuen Dienstprinzipal mithilfe des folgenden Befehls, und ersetzen Sie dabei **{MyApplicationId}** durch die **ApplicationId** aus dem vorherigen Schritt:
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Richten Sie eine Rollenzuweisung mithilfe des folgenden Befehls ein, und ersetzen Sie dabei **{MyApplicationId}** durch Ihre **ApplicationId**.
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Sie haben jetzt eine Azure AD-Anwendung fertig erstellt, mit der die Authentifizierung aus einer benutzerdefinierten C#-Anwendung möglich ist. Die folgenden Werte benötigen Sie später in diesem Tutorial:

* TenantId
* SubscriptionId
* ApplicationId
* Kennwort

[lnk-authenticate-arm]: /rest/api/
[lnk-powershell-install]: /powershell/azure/install-az-ps
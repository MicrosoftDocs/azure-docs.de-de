---
title: Bereitstellen benutzerdefinierter Richtlinien mit Azure Pipelines
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie in Azure AD B2C mithilfe von Azure Pipelines in Azure DevOps Services benutzerdefinierte Richtlinien in einer CI/CD-Pipeline bereitstellen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 13f5f8da0bd58cef0974e8ea8f5f3c5172daa0ba
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928731"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Bereitstellen benutzerdefinierter Richtlinien mit Azure Pipelines

Mithilfe einer CI/CD-Pipeline (Continuous Integration/Continuous Delivery), die Sie in [Azure Pipelines][devops-pipelines] einrichten, können Sie benutzerdefinierte Azure AD B2C-Richtlinien in Ihre Softwarebereitstellung und die Codesteuerungsautomatisierung einbeziehen. Bei der Bereitstellung in unterschiedlichen Azure AD B2C-Umgebungen (z. B. Entwicklung, Test und Produktion) empfiehlt es sich, manuelle Prozesse zu entfernen und automatisierte Tests mithilfe von Azure Pipelines auszuführen.

Drei Hauptschritte sind erforderlich, um Azure Pipelines zum Verwalten benutzerdefinierter Richtlinien in Azure AD B2C zu aktivieren:

1. Erstellen einer Webanwendungsregistrierung in Ihrem Azure AD B2C-Mandanten
1. Konfigurieren eines Azure-Repository
1. Konfigurieren einer Azure-Pipeline

> [!IMPORTANT]
> Bei der Verwaltung von benutzerdefinierten Richtlinien in Azure AD B2C mit Azure Pipelines werden derzeit **Vorschau**-Vorgänge verwendet, die im `/beta`-Endpunkt der Microsoft Graph-API zur Verfügung stehen. Die Verwendung dieser APIs in Produktionsanwendungen wird nicht unterstützt. Weitere Informationen finden Sie in der [Microsoft Graph-REST-API-Beta-Endpunkt-Referenz](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta).

## <a name="prerequisites"></a>Voraussetzungen

* [Azure AD B2C-Mandant](tutorial-create-tenant.md) und Anmeldeinformationen für einen Benutzer im Verzeichnis mit der Rolle [B2C-IEF-Richtlinienadministrator](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)
* [Benutzerdefinierte Richtlinien](custom-policy-get-started.md), die in Ihren Mandanten hochgeladen wurden
* [Verwaltungs-App](microsoft-graph-get-started.md), die mit der Microsoft Graph-API-Berechtigung *Policy.ReadWrite.TrustFramework* in Ihrem Mandanten registriert wurde
* [Azure-Pipeline](https://azure.microsoft.com/services/devops/pipelines/) und Zugriff auf ein [Azure DevOps Services-Projekt][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Gewährungsflow für Clientanmeldeinformationen

In dem hier beschriebenen Szenario wrden Dienst-zu-Dienst-Aufrufe zwischen Azure Pipelines und Azure AD B2C mithilfe des [Gewährungsflows für OAuth 2.0-Clientanmeldeinformationen](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) verwendet. Dieser Gewährungsflow erlaubt einem Webdienst wie Azure Pipelines (vertraulicher Client), beim Aufrufen eines anderen Webdiensts (in diesem Fall die Microsoft Graph-API) seine eigenen Anmeldeinformationen zum Authentifizieren zu verwenden, anstatt die Identität eines Benutzers anzunehmen. Azure Pipelines ruft ohne Benutzereingriff ein Token ab und sendet dann Anforderungen an die Microsoft Graph-API.

## <a name="register-an-application-for-management-tasks"></a>Registrieren einer Anwendung für Verwaltungstasks

Wie unter [Voraussetzungen](#prerequisites) erwähnt, benötigen Sie eine Anwendungsregistrierung, die Ihre PowerShell-Skripts (die von Azure Pipelines ausgeführt werden) für den Zugriff auf die Ressourcen in Ihrem Mandanten verwenden können.

Wenn Sie bereits über eine Anwendungsregistrierung verfügen, die Sie für Automatisierungsaufgaben verwenden, stellen Sie sicher, dass ihr in den **API-Berechtigungen** der App-Registrierung unter **Microsoft Graph** > **Richtlinie** > die Berechtigung **Policy.ReadWrite.TrustFramework** erteilt wurde.

Anweisungen zum Registrieren einer Verwaltungsanwendung finden Sie unter [Verwalten von Azure AD B2C mit Microsoft Graph](microsoft-graph-get-started.md).

## <a name="configure-an-azure-repo"></a>Konfigurieren eines Azure-Repository

Nach dem Registrieren der Verwaltungsanwendung können Sie ein Repository für Ihre Richtliniendateien konfigurieren.

1. Melden Sie sich bei Ihrer Azure DevOps Services-Organisation an.
1. [Erstellen Sie ein neues Projekt][devops-create-project], oder wählen Sie ein vorhandenes Projekt aus.
1. Navigieren Sie in Ihrem Projekt zu **Repos**, und wählen Sie die Seite **Dateien** aus. Wählen Sie für diese Übung ein vorhandenes Repository aus, oder erstellen Sie ein neues Repository.
1. Erstellen Sie einen Ordner namens *B2CAssets*. Geben Sie der erforderlichen Platzhalterdatei den Namen *README.md*, und führen Sie einen **Commit** für die Datei aus. Sie können diese Datei später bei Bedarf entfernen.
1. Fügen Sie Ihre Azure AD B2C-Richtliniendateien dem Ordner *B2CAssets* hinzu. Dazu zählen *TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml* und alle weiteren Richtlinien, die Sie erstellt haben. Notieren Sie sich die Dateinamen der einzelnen Azure AD B2C-Richtliniendateien, um sie in einem späteren Schritt (als PowerShell-Skriptargumente) zu verwenden.
1. Erstellen Sie im Stammverzeichnis des Repository einen Ordner namens *Skripts*, und geben Sie der Platzhalterdatei den Namen *DeployToB2c.ps1*. Führen Sie an diesem Punkt noch keinen Commit für die Datei aus. Dies geschieht in einem späteren Schritt.
1. Fügen Sie das folgende PowerShell-Skript in die Datei *DeployToB2c.ps1* ein, und führen Sie dann einen **Commit** für die Datei aus. Mit dem Skript wird ein Token von Azure AD abgerufen und die Microsoft Graph-API aufgerufen, um die Richtlinien in den Ordner *B2CAssets* in Ihrem Azure AD B2C-Mandanten hochzuladen.

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>Konfigurieren Ihrer Azure-Pipeline

Nachdem Sie Ihr Repository initialisiert und mit Ihren benutzerdefinierten Richtliniendateien aufgefüllt haben, können Sie die Freigabepipeline einrichten.

### <a name="create-pipeline"></a>Erstellen der Pipeline

1. Melden Sie sich bei Ihrer Azure DevOps Services-Organisation an, und navigieren Sie zu Ihrem Projekt.
1. Wählen Sie in Ihrem Projekt **Pipelines** > **Freigaben** > **Neue Pipeline** aus.
1. Wählen Sie unter **Vorlage auswählen** die Option **Leerer Auftrag** aus.
1. Geben Sie einen **Phasennamen** (z. B. *DeployCustomPolicies*) ein, und schließen Sie dann den Bereich.
1. Wählen Sie **Artefakt hinzufügen** aus, und wählen Sie unter **Quelltyp** die Option **Azure-Repository** aus.
    1. Wählen Sie das Quellrepository mit dem Ordner *Skripts* und dem gespeicherten PowerShell-Skript aus.
    1. Wählen Sie einen **Standardbranch** aus. Wenn Sie im vorherigen Abschnitt ein neues Repository erstellt haben, ist *master* der Standardbranch.
    1. Übernehmen Sie unter **Standardversion** die Einstellung *Neuestes Element aus dem Standardbranch*.
    1. Geben Sie für das Repository einen **Quellalias** ein, zum Beispiel *policyRepo*. Der Aliasname darf keine Leerzeichen enthalten.
1. Wählen Sie **Hinzufügen** aus.
1. Benennen Sie die Pipeline um, und geben Sie ihr einen aussagekräftigen Namen, zum Beispiel *Deploy Custom Policy Pipeline*.
1. Wählen Sie zum Speichern der Pipelinekonfiguration **Speichern** aus.

### <a name="configure-pipeline-variables"></a>Konfigurieren von Pipelinevariablen

1. Wählen Sie die Registerkarte **Variablen** aus.
1. Fügen Sie unter **Pipelinevariablen** die folgenden Variablen hinzu, und legen Sie die Werte wie angegeben fest:

    | Name | Wert |
    | ---- | ----- |
    | `clientId` | **Anwendungs-ID (Client)** der zuvor von Ihnen registrierten Anwendung. |
    | `clientSecret` | Wert des **geheimen Clientschlüssels**, den Sie zuvor erstellt haben. <br /> Ändern Sie den Variablentyp in **Geheimnis** (wählen Sie das Schlosssymbol aus). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, wobei *your-b2c-tenant* dem Namen Ihres Azure AD B2C-Mandanten entspricht. |

1. Wählen Sie **Speichern** aus, um die Variablen zu speichern.

### <a name="add-pipeline-tasks"></a>Hinzufügen von Pipelinetasks

Fügen Sie als Nächstes einen Task zum Bereitstellen einer Richtliniendatei hinzu.

1. Wählen Sie die Registerkarte **Tasks** aus.
1. Wählen Sie **Agent-Auftrag** und dann das Pluszeichen ( **+** ) aus, um dem Agent-Auftrag einen Task hinzuzufügen.
1. Suchen Sie nach dem Eintrag **PowerShell**, und wählen Sie ihn aus. Wählen Sie nicht „Azure PowerShell“, „PowerShell auf Zielcomputern“ oder einen anderen Eintrag im Zusammenhang mit PowerShell aus.
1. Wählen Sie den neu hinzugefügten Task **PowerShell-Skript** aus.
1. Geben Sie für den Task „PowerShell-Skript“ die folgenden Werte ein:
    * **Taskversion**: 2.*
    * **Anzeigename**: Der Name der Richtlinie, die dieser Task hochladen soll, zum Beispiel *B2C_1A_TrustFrameworkBase*.
    * **Typ:** Dateipfad
    * **Skriptpfad:** Wählen Sie die Auslassungspunkte (**_..._* _) aus, navigieren Sie zum Ordner _Skripts*, und wählen Sie dann die Datei *DeployToB2C.ps1* aus.
    * **Argumente**:

        Geben Sie als **Argumente** die folgenden Werte ein. Ersetzen Sie `{alias-name}` durch den Alias, den Sie im vorherigen Abschnitt definiert haben.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Wenn der von Ihnen definierte Alias beispielsweise *policyRepo* lautet, sollte die Argumentzeile wie folgt aussehen:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Wählen Sie **Speichern** aus, um den Agent-Auftrag zu speichern.

Mit dem gerade hinzugefügten Task wird *eine* Richtliniendatei in Azure AD B2C hochgeladen. Bevor Sie den Vorgang fortsetzen und weitere Tasks erstellen, sollten Sie manuell den Task auslösen (**Freigabe erstellen**), um sicherzustellen, dass er erfolgreich abgeschlossen wird.

Wenn der Task erfolgreich abgeschlossen wird, können Sie weitere Bereitstellungstasks hinzufügen. Führen Sie dazu für jede Datei mit einer benutzerdefinierten Richtlinie die oben beschriebenen Schritte aus. Ändern Sie für jede Richtlinie die Argumentwerte `-PolicyId` und `-PathToFile`.

`PolicyId` ist ein Wert, der sich am Anfang jeder XML-Richtliniendatei im TrustFrameworkPolicy-Knoten befindet. Beispiel: `PolicyId` in der folgenden XML-Richtliniendatei lautet *B2C_1A_TrustFrameworkBase*:

```xml
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

Stellen Sie beim Ausführen der Agents und beim Hochladen der Richtliniendatei unbedingt die folgende Reihenfolge sicher:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

Das Identity Experience Framework erzwingt diese Reihenfolge, da die Dateistruktur auf einer hierarchischen Kette beruht.

## <a name="test-your-pipeline"></a>Testen Ihrer Pipeline

So testen Sie Ihre Pipeline

1. Wählen Sie **Pipelines** und anschließend **Freigaben** aus.
1. Wählen Sie die zuvor erstellte Pipeline (z. B. *DeployCustomPolicies*) aus.
1. Wählen Sie **Freigabe erstellen** und dann **Erstellen** aus, um die Freigabe in die Warteschlange einzureihen.

Es sollte ein Benachrichtigungsbanner mit dem Hinweis angezeigt werden, dass eine Freigabe in die Warteschlange gestellt wurde. Um deren Status anzuzeigen, wählen Sie den Link im Benachrichtigungsbanner oder die Freigabe in der Liste auf der Registerkarte **Freigaben** aus.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen:

* [Dienst-zu-Dienst-Aufrufe mit Clientanmeldeinformationen](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)
* [Azure DevOps Services](/azure/devops/user-guide/)

<!-- LINKS - External -->
[devops]: /azure/devops/
[devops-create-project]:  /azure/devops/organizations/projects/create-project
[devops-pipelines]: /azure/devops/pipelines

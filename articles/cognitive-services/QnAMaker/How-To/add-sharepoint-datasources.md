---
title: SharePoint-Dateien – QnA Maker
description: Fügen Sie Ihrer Wissensdatenbank geschützte SharePoint-Datenquellen hinzu, um sie um Fragen und Antworten zu erweitern, die mit Active Directory geschützt werden können.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 0832b54e02cabecb0b1f0e7af600b8adc621a8b0
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584769"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Hinzufügen einer geschützten SharePoint-Datenquelle zu Ihrer Wissensdatenbank

Fügen Sie Ihrer Wissensdatenbank geschützte cloudbasierte SharePoint-Datenquellen hinzu, um sie um Fragen und Antworten zu erweitern, die mit Active Directory geschützt werden können.

Wenn Sie Ihrer Wissensdatenbank ein geschütztes SharePoint-Dokument hinzufügen, müssen Sie als QnA Maker-Manager eine Active Directory-Berechtigung für QnA Maker anfordern. Die SharePoint-Zugriffsberechtigung muss QnA Maker nur ein Mal durch den Active Directory-Manager erteilt werden. Bei nachfolgenden Dokumenthinzufügungen zur Wissensdatenbank ist keine Autorisierung mehr erforderlich, sofern die gleiche SharePoint-Ressource verwendet wird.

Falls der QnA Maker-Wissensdatenbank-Manager nicht der Active Directory-Manager ist, müssen Sie bei diesem Prozess mit dem Active Directory-Manager zusammenarbeiten.

## <a name="prerequisites"></a>Voraussetzungen

* Cloudbasiertes SharePoint – QnA Maker verwendet Microsoft Graph für Berechtigungen. Wenn Ihr SharePoint lokal verfügbar ist, können Sie nicht aus SharePoint extrahieren, da Microsoft Graph nicht in der Lage ist, die Berechtigungen zu bestimmen.
* URL-Format – QnA Maker unterstützt nur SharePoint-URLs, die für die Freigabe generiert werden und das Format `https://\*.sharepoint.com` aufweisen.

## <a name="add-supported-file-types-to-knowledge-base"></a>Hinzufügen unterstützter Dateitypen zur Wissensdatenbank

Sie können Ihrer Wissensdatenbank alle von QnA Maker unterstützten [Dateitypen](../index.yml) über eine SharePoint-Website hinzufügen. Ist die Dateiressource geschützt, müssen gegebenenfalls [Berechtigungen](#permissions) erteilt werden.

1. Wählen Sie in der Bibliothek mit der SharePoint-Website das Menü mit den Auslassungszeichen (`...`) der Datei aus.
1. Kopieren Sie die URL der Datei.

   ![Rufen Sie die URL der SharePoint-Datei ab, indem Sie das Menü mit den Auslassungszeichen der Datei auswählen und die URL kopieren.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. Navigieren Sie im QnA Maker-Portal zur Seite **Einstellungen**, und fügen Sie der Wissensdatenbank die URL hinzu.

### <a name="images-with-sharepoint-files"></a>Bilder mit SharePoint-Dateien

Wenn Dateien Bilder enthalten, werden diese nicht extrahiert. Sie können das Bild über das QnA Maker-Portal hinzufügen, nachdem Frage-und-Antwort-Paare aus der Datei extrahiert wurden.

Fügen Sie das Bild mit der folgenden Markdown-Syntax hinzu:

```markdown
![Explanation or description of image](URL of public image)
```

Der Text in den eckigen Klammern (`[]`) ist eine Erläuterung des Bilds. Die URL in Klammern (`()`) ist der direkte Link zum Bild.

Wenn Sie das Frage-und-Antwort-Paar im interaktiven Testbereich testen, wird im QnA Maker-Portal nicht der Markdown-Text angezeigt, sondern das Bild. Hierbei wird überprüft, ob das Bild öffentlich zugänglich ist und aus Ihrer Clientanwendung abgerufen werden kann.

## <a name="permissions"></a>Berechtigungen

Berechtigungen werden erteilt, wenn einer Wissensdatenbank eine geschützte Datei von einem SharePoint-Server hinzugefügt wird. Hierzu ist möglicherweise Folgendes erforderlich (abhängig von der SharePoint-Konfiguration und den Berechtigungen der Person, die die Datei hinzufügt):

* Keine weiteren Schritte: Die Person, die die Datei hinzufügt, verfügt über alle erforderlichen Berechtigungen.
* Schritte des [Wissensdatenbank-Managers](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) und des [Active Directory-Managers](#active-directory-manager-grant-file-read-access-to-qna-maker).

Die Schritte sind im Anschluss aufgeführt.

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Wissensdatenbank-Manager: Hinzufügen der SharePoint-Datenquelle über das QnA Maker-Portal

Wenn der **QnA Maker-Manager** einer Wissensdatenbank ein geschütztes SharePoint-Dokument hinzufügt, initiiert der Wissensdatenbank-Manager eine Berechtigungsanforderung, die der Active Directory-Manager abschließen muss.

Die Anforderung beginnt mit einer Popup-Aufforderung zur Authentifizierung eines Active Directory-Kontos.

![Authentifizieren des Benutzerkontos](../media/add-sharepoint-datasources/authenticate-user-account.png)

Nachdem der QnA Maker-Manager das Konto ausgewählt hat, erhält der Azure Active Directory-Administrator eine Benachrichtigung mit dem Hinweis, dass der QnA Maker-App (nicht dem QnA Maker-Manager) Zugriff auf die SharePoint-Ressource erhalten muss. Der Azure Active Directory-Manager muss diesen Schritt für jede SharePoint-Ressource, aber nicht für jedes Dokument in dieser Ressource ausführen.

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Active Directory-Manager: Gewähren von Dateilesezugriff für QnA Maker

Der Active Directory-Manager (nicht der QnA Maker-Manager) muss QnA Maker über [diesen Link](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) Zugriff auf die SharePoint-Ressource gewähren, um die App „QnAMakerPortalSharepoint“ zu autorisieren und ihr Dateileseberechtigungen zu erteilen.

![Azure Active Directory-Manager gewährt interaktiv Berechtigungen](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharePoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`.
-->
<!--
### Grant access from the interactive pop-up window

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharePoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharePoint**, and a list of permissions requested. Select **Accept** to provide those permissions.

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**.

1. Search for and select the **QnAMakerPortalSharePoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**.

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**.

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows.

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Gewähren des Zugriffs über das Azure Active Directory Admin Center

1. Der Active Directory-Manager meldet sich beim Azure-Portal an und öffnet **[Unternehmensanwendungen](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)** .

1. Suchen Sie nach `QnAMakerPortalSharePoint`, und wählen Sie dann die QnA Maker-App aus.

    [![Suchen Sie nach QnAMakerPortalSharepoint in der Liste der Unternehmensanwendungen](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. Wechseln Sie unter **Sicherheit** zu **Berechtigungen**. Wählen Sie **Administratoreinwilligung für Organisation gewähren** aus.

    [![Wählen Sie den authentifizierten Benutzer für Active Directory Admin aus](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Wählen Sie ein Anmeldekonto mit Berechtigungen, um Berechtigungen für Active Directory zu gewähren.




## <a name="add-sharepoint-data-source-with-apis"></a>Hinzufügen einer SharePoint-Datenquelle mit APIs

Es gibt eine Umgehung, wie aktuelle SharePoint-Inhalte über APIs mithilfe von Azure Blob Storage hinzugefügt werden können. Unten finden Sie die entsprechenden Schritte: 
1.  Laden Sie die SharePoint-Dateien lokal herunter. Der Benutzer, der die API aufruft, benötigt Zugriff auf SharePoint. 
1.  Laden Sie sie in den Blobspeicher in Azure hoch. Durch [Verwendung eines SAS-Token](../../../storage/common/storage-sas-overview.md#how-a-shared-access-signature-works) wird so ein sicherer gemeinsamer Zugriff ermöglicht. 
1. Übergeben Sie die mit dem SAS-Token generierte Blob-URL an die QnA Maker-API. Damit die Fragen und Antworten aus den Dateien extrahiert werden können, müssen Sie als Typ der Suffixdatei '&ext=pdf' oder '&ext=doc' am Ende der URL hinzufügen, bevor Sie sie an die QnA Maker-API übergeben.


<!--
## Get SharePoint File URI

Use the following steps to transform the SharePoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64).

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes.

    * Remove the equal character, `=` from the end of the value.
    * Replace `/` with `_`.
    * Replace `+` with `-`.
    * Append `u!` to be beginning of the string.

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a SharePoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) or [updating a knowledge base](/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<SharePoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from SharePoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center.
1. When you select the **QnAMakerPortalSharePoint**, select **Overview**.
1. Select **Delete** to remove permissions.

-->

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Zusammenarbeiten an einer Wissensdatenbank](../index.yml)

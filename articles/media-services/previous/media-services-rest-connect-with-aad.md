---
title: Verwenden der Azure AD-Authentifizierung zum Zugreifen auf die Azure Media Services-API per REST | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie auf die Azure Media Services-API mit Azure Active Directory-Authentifizierung per REST zugreifen.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: willzhan; johndeu
ms.openlocfilehash: 8bea4c049c3d7ea17e173f069a3e99cbcca1fe48
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041991"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>Verwenden der Azure AD-Authentifizierung zum Zugreifen auf die Media Services-API mit REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](../latest/index.yml) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

Bei Verwendung der Azure AD-Authentifizierung mit Azure Media Services können Sie auf zwei Arten authentifizieren:

- Die **Benutzerauthentifizierung** dient der Authentifizierung einer Person, die die App zum Interagieren mit Azure Media Services-Ressourcen verwendet. Die interaktive Anwendung soll den Benutzer zuerst zur Eingabe seiner Anmeldeinformationen auffordern. Ein Beispiel hierfür ist eine Verwaltungskonsolen-App, die von autorisierten Benutzern zum Überwachen von Codierungsaufträgen oder Livestreaming verwendet wird. 
- Die **Dienstprinzipalauthentifizierung** dient der Authentifizierung eines Diensts. Anwendungen, für die diese Authentifizierungsmethode normalerweise verwendet wird, sind Apps, die Daemondienste, Dienste der mittleren Ebene oder geplante Aufträge wie Web-Apps, Funktions-Apps, Logik-Apps, APIs oder Microservices ausführen.

    In diesem Tutorial wird gezeigt, wie Sie die Azure AD- **Dienstprinzipal** authentifizierung für den Zugriff auf die AMS-API mit REST verwenden. 

    > [!NOTE]
    > **Dienstprinzipale** werden als Best Practice für die meisten Anwendungen empfohlen, die eine Verbindung mit Azure Media Services herstellen. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Abrufen der Authentifizierungsinformationen aus dem Azure-Portal
> * Abrufen des Zugriffstokens mithilfe von Postman
> * Testen der **Assets** -API unter Verwendung des Zugriffstokens


> [!IMPORTANT]
> Derzeit wird für Media Services das Azure Access Control-Dienstauthentifizierungsmodell unterstützt. Die Access Control-Authentifizierung gilt aber ab dem 1. Juni 2018 als veraltet. Es wird empfohlen, möglichst bald zum Azure AD-Authentifizierungsmodell zu migrieren.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), bevor Sie beginnen.
- [Erstellen Sie ein Azure Media Services-Konto mithilfe des Azure-Portals](media-services-portal-create-account.md).
- Lesen Sie den Artikel [Zugreifen auf die Azure Media Services-API per Azure AD-Authentifizierung – Übersicht](media-services-use-aad-auth-to-access-ams-api.md).
- Installieren Sie den [Postman](https://www.getpostman.com/)-REST-Client, um die in diesem Artikel gezeigten REST-APIs auszuführen. 

    In diesem Tutorial wird **Postman** verwendet, grundsätzlich ist aber jedes REST-Tool geeignet. Andere Alternativen sind: **Visual Studio Code** mit dem REST-Plug-In oder **Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Abrufen der Authentifizierungsinformationen aus dem Azure-Portal

### <a name="overview"></a>Übersicht

Für den Zugriff auf die Media Services-API müssen Sie die folgenden Datenpunkte erfassen.

|Einstellung|Beispiel|BESCHREIBUNG|
|---|-------|-----|
|Azure Active Directory-Mandantendomäne|microsoft.onmicrosoft.com|Ein STS-Endpunkt (Secure Token Service, Sicherheitstokendienst) für Azure AD wird unter Verwendung des folgenden Formats erstellt: <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token>. Azure AD stellt ein JWT für den Ressourcenzugriff aus (ein Zugriffstoken).|
|REST-API-Endpunkt|<https://amshelloworld.restv2.westus.media.azure.net/api/>|Dies ist der Endpunkt, über den alle Aufrufe der Media Services-REST-API in Ihrer Anwendung durchgeführt werden.|
|Client-ID (Anwendungs-ID)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Client-ID der Azure AD-Anwendung. Die Client-ID ist erforderlich, um das Zugriffstoken abzurufen. |
|Geheimer Clientschlüssel|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Azure AD-Anwendungsschlüssel (Clientgeheimnis). Das Clientgeheimnis ist erforderlich, um das Zugriffstoken abzurufen.|

### <a name="get-azure-active-directory-auth-info-from-the-azure-portal"></a>Abrufen von Azure Active Directory-Authentifizierungsinformationen über das Azure-Portal

Führen Sie die folgenden Schritte aus, um die Informationen abzurufen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Navigieren zu Ihrer AMS-Instanz.
3. Wählen Sie **API-Zugriff** aus.
4. Klicken Sie auf **Verbindung mit Azure Media Services-API über Dienstprinzipal herstellen**.

    ![Screenshot der ausgewählten Option „API-Zugriff“ im Menü „Media Services“ und der im rechten Bereich ausgewählten Option „Verbindung mit Azure Media Services-API über Dienstprinzipal herstellen“](./media/connect-with-rest/connect-with-rest01.png)

5. Wählen Sie eine vorhandene **Azure AD-Anwendung** aus, oder erstellen Sie eine neue Anwendung (nachfolgend gezeigt).

    > [!NOTE]
    > Damit die Azure Media Services-REST-Anforderung erfolgreich ist, muss der aufrufende Benutzer über die Rolle **Mitwirkender** oder **Besitzer** für das Media Services-Konto verfügen, auf das zugegriffen werden soll. Wenn eine Ausnahme mit dem Text „Der Remoteserver hat einen Fehler zurückgegeben: 401 – Nicht autorisiert“ zurückgegeben wird, finden Sie weitere Informationen unter [Zugriffssteuerung](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Wenn Sie ein neues Speicherkonto erstellen möchten, gehen Sie wie folgt vor:
    
   1. Klicken Sie auf **Neu erstellen**.
   2. Geben Sie einen Namen ein.
   3. Klicken Sie erneut auf **Neu erstellen**.
   4. Klicken Sie auf **Speichern**.

      ![Screenshot des Dialogfelds „Neu erstellen“ mit hervorgehobenem Textfeld „App erstellen“ und ausgewählter Schaltfläche „Speichern“](./media/connect-with-rest/new-app.png)

      Die neue App wird auf der Seite angezeigt.

6. Rufen Sie die **Client-ID** (Anwendungs-ID) ab.
    
   1. Wählen Sie die Anwendung aus.
   2. Die **Client-ID** wird im Fenster rechts angezeigt. 

      ![Screenshot der ausgewählten Optionen „Azure AD-App“ und „Anwendung verwalten“ mit hervorgehobener Option „Client-ID“ im rechten Bereich](./media/connect-with-rest/existing-client-id.png)

7. Rufen Sie den **Schlüssel** der Anwendung (das Clientgeheimnis) ab. 

   1. Klicken Sie auf die Schaltfläche **Anwendung verwalten** (beachten Sie, dass sich die Client-ID unter **Anwendungs-ID** befindet). 
   2. Klicken Sie auf **Schlüssel**.
    
       ![Screenshot der ausgewählten Schaltfläche „Anwendung verwalten“ mit hervorgehobener Option „Anwendungs-ID“ im mittleren Bereich und ausgewählter Option „Schlüssel“ im rechten Bereich](./media/connect-with-rest/manage-app.png)
   3. Generieren Sie den App-Schlüssel (das Clientgeheimnis), indem Sie **BESCHREIBUNG** und **LÄUFT AB** ausfüllen und auf **Speichern** klicken.
    
       Nach dem Klicken auf die Schaltfläche **Speichern** wird der Schlüsselwert angezeigt. Kopieren Sie den Schlüsselwert, bevor Sie das Blatt verlassen.

   ![API-Zugriff](./media/connect-with-rest/connect-with-rest03.png)

Sie können Werte für AD-Verbindungsparameter für Ihre Datei „web.config“ oder „app.config“ hinzufügen, um diese später im Code zu verwenden.

> [!IMPORTANT]
> Der **Clientschlüssel** ist ein wichtiges Geheimnis und muss sicher in einem Schlüsseltresor gespeichert oder in der Produktion verschlüsselt werden.

## <a name="get-the-access-token-using-postman"></a>Abrufen des Zugriffstokens mithilfe von Postman

In diesem Abschnitt wird gezeigt, wie Sie mithilfe von **Postman** eine REST-API ausführen, die ein JWT-Bearertoken (Zugriffstoken) zurückgibt. Um eine beliebige Media Services-REST-API aufzurufen, müssen Sie Ihren Aufrufen den Autorisierungsheader und den Wert „Bearer *Ihr_Zugriffstoken* “ hinzufügen (wie im nächsten Abschnitt dieses Tutorials gezeigt). 

1. Öffnen Sie **Postman**.
2. Wählen Sie **POST** aus.
3. Geben Sie die URL mit Ihrem Mandantennamen im folgenden Format ein: Der Mandantenname muss auf **.onmicrosoft.com** , die URL muss auf **oauth2/token** enden: 

    `https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token`

4. Wählen Sie die Registerkarte **Headers** (Header) aus.
5. Geben Sie die Informationen für **Headers** (Header) mithilfe der Schlüssel/Wert-Daten ein. 

    ![Screenshot der Registerkarte „Header“ mit ausgewählter Aktion „Massenbearbeitung“](./media/connect-with-rest/headers-data-grid.png)

    Alternativ können Sie auf den Link **Bulk Edit** (Massenbearbeitung) im rechten Bereich des Postman-Fensters klicken und den folgenden Code einfügen.

    ```javascript
    Content-Type:application/x-www-form-urlencoded
    Keep-Alive:true
    ```

6. Wechseln Sie zur Registerkarte **Body** (Anforderungskörper).
7. Geben Sie die Informationen für den Anforderungskörper mithilfe der Schlüssel/Wert-Daten ein (ersetzen Sie die Werte für Client-ID und Geheimnis). 

    ![Datentabelle](./media/connect-with-rest/data-grid.png)

    Alternativ können Sie auf den Link **Bulk Edit** (Massenbearbeitung) im rechten Bereich des Postman-Fensters klicken und den folgenden Anforderungskörper einfügen (ersetzen Sie die Werte für Client-ID und Geheimnis):

    ```javascript
    grant_type:client_credentials
    client_id:{Your Client ID that you got from your Azure AD Application}
    client_secret:{Your client secret that you got from your Azure AD Application's Keys}
    resource:https://rest.media.azure.net
    ```

8. Klicken Sie auf **Senden**.

    ![Screenshot des Textfelds „Veröffentlichen“ und der Registerkarten „Header“ und „Text“ mit hervorgehobener Option „access_token“ und ausgewählter Schaltfläche „Senden“](./media/connect-with-rest/connect-with-rest04.png)

Die zurückgegebene Antwort enthält das **Zugriffstoken** , das Sie für den Zugriff auf beliebige AMS-APIs verwenden.

## <a name="test-the-assets-api-using-the-access-token"></a>Testen der **Assets** -API unter Verwendung des Zugriffstokens

In diesem Abschnitt wird gezeigt, wie Sie mithilfe von **Postman** auf die **Assets** -API zugreifen.

1. Öffnen Sie **Postman**.
2. Wählen Sie **GET** aus.
3. Fügen Sie den REST-API-Endpunkt ein (z.B. https://amshelloworld.restv2.westus.media.azure.net/api/Assets).
4. Wählen Sie die Registerkarte **Authorization** (Autorisierung) aus. 
5. Klicken Sie auf **Bearer Token** (Bearertoken).
6. Fügen Sie das Token ein, das im vorherigen Abschnitt erstellt wurde.

    ![Tokenabruf](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > Die Postman-Benutzeroberfläche für einen Mac und einen PC unterscheidet sich möglicherweise. Wenn die Mac-Version im Abschnitt **Authentication** (Authentifizierung) keine Option „Bearer Token“ (Bearertoken) in der Dropdownliste aufweist, müssen Sie den **Authorization** -Header auf dem Mac-Client manuell hinzufügen.

   ![Autorisierungsheader](./media/connect-with-rest/auth-header.png)

7. Wählen Sie **Headers** (Header) aus.
5. Klicken Sie im rechten Bereich des Postman-Fensters auf **Bulk Edit** (Massenbearbeitung).
6. Fügen Sie folgenden Header ein:

    ```javascript
    x-ms-version:2.19
    Accept:application/json
    Content-Type:application/json
    DataServiceVersion:3.0
    MaxDataServiceVersion:3.0
    ```

7. Klicken Sie auf **Senden**.

Die zurückgegebene Antwort enthält die Assets in Ihrem Konto.

## <a name="next-steps"></a>Nächste Schritte

* Probieren Sie den Beispielcode aus, der in [Verwenden der Azure AD-Authentifizierung zum Zugreifen auf die Azure Media Services-API per REST](https://github.com/willzhan/WAMSRESTSoln) verwendet wird.
* [Hochladen von Dateien mit .NET](media-services-dotnet-upload-files.md)

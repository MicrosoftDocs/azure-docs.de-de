---
title: Konfigurieren von Postman für die Azure Media Services v3-REST-API
description: In diesem Artikel wird veranschaulicht, wie Sie Postman so konfigurieren, dass das Tool für den Aufruf von Azure Media Services-REST-APIs verwendet werden kann.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: b0a521ddeee6d93ce809e5e6e85eefa1f01a3f12
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953970"
---
# <a name="configure-postman-for-media-services-v3-rest-api-calls"></a>Konfigurieren von Postman für Media Services v3 REST-API-Aufrufe

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In diesem Artikel wird veranschaulicht, wie Sie **Postman** so konfigurieren, dass das Tool für den Aufruf von Azure Media Services-REST-APIs verwendet werden kann. In diesem Artikel wird veranschaulicht, wie Umgebungs- und Sammlungsdateien in **Postman** importiert werden. Die Sammlung enthält gruppierte Definitionen von HTTP-Anforderungen, die Azure Media Services-REST-APIs aufrufen. Die Umgebungsdatei enthält Variablen, die von der Sammlung verwendet werden.

Bevor Sie mit der Entwicklung beginnen, lesen Sie [Entwickeln mit Media Services v3-APIs](media-services-apis-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen Sie ein Media Services-Konto.](./create-account-howto.md) Merken Sie sich den Namen der Ressourcengruppe und den Namen des Media Services-Kontos. 
- Beschaffen Sie die Informationen, die für den [Zugriff auf APIs](./access-api-howto.md) benötigt werden.
- Installieren Sie den REST-Client von [Postman](https://www.getpostman.com/), um die REST-APIs auszuführen, die in einigen der AMS REST-Tutorials gezeigt werden. 

    Wir verwenden **Postman**, aber jedes REST-Tool wäre geeignet. Andere Alternativen sind: **Visual Studio Code** mit dem REST-Plug-In oder **Telerik Fiddler**. 

> [!IMPORTANT]
> Informieren Sie sich über die [Namenskonventionen](media-services-apis-overview.md#naming-conventions).

## <a name="download-postman-files"></a>Herunterladen von Postman-Dateien

Klonen Sie ein GitHub-Repository mit der Postman-Sammlung und den Umgebungsdateien.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Konfigurieren von Postman

### <a name="configure-the-environment"></a>Konfigurieren der Umgebung 

1. Öffnen Sie die App **Postman**.
2. Wählen Sie auf der rechten Seite des Bildschirms die Option **Manage environment** (Umgebung verwalten) aus.

    ![Verwalten der Umgebung](./media/develop-with-postman/postman-import-env.png)
4. Klicken Sie im Dialogfeld **Manage environment** (Umgebung verwalten) auf **Import**.
2. Navigieren Sie zur Datei `Azure Media Service v3 Environment.postman_environment.json`, die beim Klonen von `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` heruntergeladen wurde.
6. Die Umgebung **Azure Media Service v3 Environment** wird hinzugefügt.

    > [!Note]
    > Aktualisieren Sie die Zugriffsvariablen mit Werten aus dem Abschnitt **Zugreifen auf die Media Services-API**.

7. Doppelklicken Sie auf die ausgewählte Datei, und geben Sie Werte ein, die Sie durch Ausführen der Schritte für den Zugriff auf die API erhalten.
8. Schließen Sie das Dialogfeld.
9. Wählen Sie in der Dropdownliste die Umgebung **Azure Media Service v3 Environment** aus.

    ![Auswählen der Umgebung](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Konfigurieren der Sammlung

1. Klicken Sie auf **Import**, um die Sammlungsdatei zu importieren.
1. Navigieren Sie zur Datei `Media Services v3.postman_collection.json`, die beim Klonen von `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` heruntergeladen wurde.
3. Wählen Sie die Datei **Media Services v3.postman_collection.json** aus.

    ![Importieren einer Datei](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Abrufen des Azure AD-Tokens 

Bevor Sie mit dem Ändern von AMS v3-Ressourcen beginnen, müssen Sie das Azure AD-Token für die Dienstprinzipalauthentifizierung abrufen und festlegen.

1. Wählen Sie im linken Fenster der Postman-App „Step 1: Get AAD Auth token“ (Schritt 1: AAD-Authentifizierungstoken abrufen).
2. Klicken Sie anschließend auf „Get Azure AD Token for Service Principal Authentication“ (Azure AD-Token für Dienstprinzipalauthentifizierung abrufen).
3. Klicken Sie auf **Senden**.

    Der folgende Vorgang vom Typ **POST** wird gesendet:

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. Die zurückgegebene Antwort enthält das Token und legt die Umgebungsvariable „AccessToken“ auf den Tokenwert fest.  

    ![Abrufen des AAD-Tokens](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="troubleshooting"></a>Problembehandlung 

* Wenn Ihre Anwendung den Fehler „HTTP 504: Gateway-Timeout“ anzeigt, stellen Sie sicher, dass die location-Variable nicht explizit auf einen anderen Wert als den erwarteten Speicherort des Media Services-Kontos festgelegt wurde. 
* Wenn Sie den Fehler „Konto nicht gefunden“ erhalten, überprüfen Sie auch, ob die location-Eigenschaft in der Body-JSON-Meldung auf den Speicherort festgelegt ist, an dem sich das Media Services-Konto befindet. 

## <a name="see-also"></a>Weitere Informationen

- [Erstellen von Filtern mit der Media Services-REST-API](filters-dynamic-manifest-rest-howto.md)
- [Auf Azure Resource Manager basierte REST-API](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Hochladen, Codieren und Streamen von Videos mit REST](stream-files-tutorial-with-rest.md).  
- [Tutorial: Codieren einer Remotedatei anhand einer URL und Streamen des Videos über – REST](stream-files-tutorial-with-rest.md)

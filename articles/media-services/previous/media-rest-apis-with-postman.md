---
title: Konfigurieren von Postman für Azure Media Services-REST-API-Aufrufe
description: In diesem Artikel wird beschrieben, wie Sie Postman für Media Services-REST-API-Aufrufe konfigurieren.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: juliako
ms.openlocfilehash: d235316ad6a16cdc535c705ec88ec13d6963fb41
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92910286"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Konfigurieren von Postman für Media Services v2 REST-API-Aufrufe

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](../latest/index.yml) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

Dieses Tutorial zeigt Ihnen, wie Sie **Postman** so konfigurieren, dass es für den Aufruf von Azure Media Services-REST-APIs verwendet werden kann. Das Tutorial zeigt, wie Umgebungs- und Sammlungsdateien in **Postman** importiert werden. Die Sammlung enthält gruppierte Definitionen von HTTP-Anforderungen, die Azure Media Services-REST-APIs aufrufen. Die Umgebungsdatei enthält Variablen, die von der Sammlung verwendet werden.

Diese Umgebung und Sammlung werden in Artikeln verwendet, die zeigen, wie verschiedene Aufgaben mit den Azure Media Services-REST-APIs durchgeführt werden können.

## <a name="prerequisites"></a>Voraussetzungen

- Installieren Sie den REST-Client von [Postman](https://www.getpostman.com/), um die REST-APIs auszuführen, die in einigen der AMS REST-Tutorials gezeigt werden. 

    Wir verwenden **Postman** , aber jedes REST-Tool wäre geeignet. Andere Alternativen sind: **Visual Studio Code** mit dem REST-Plug-In oder **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Konfigurieren der Umgebung 

1. Erstellen Sie eine JSON-Datei, die die Umgebungsvariablen enthält, die in AMS-Tutorials verwendet werden. Benennen Sie die Datei (z.B. **AzureMediaServices.postman_environment.json** ). Öffnen Sie die Datei, und fügen Sie den Code ein, der die Postman-Umgebung über [diese Codeliste](postman-environment.md) definiert. 
2. Öffnen Sie **Postman**.
3. Wählen Sie auf der rechten Seite des Bildschirms die Option **Manage environment** (Umgebung verwalten) aus.

    ![Screenshot der ausgewählten Option „Umgebung verwalten“.](./media/media-services-rest-upload-files/postman-create-env.png)
4. Klicken Sie im Dialogfeld **Manage environment** (Umgebung verwalten) auf **Import**.
5. Navigieren Sie zur Datei **AzureMediaServices.postman_environment.json** und wählen Sie sie aus.
6. Die **AzureMedia** -Umgebung wird hinzugefügt.
7. Schließen Sie das Dialogfeld.
8. Wählen Sie die **AzureMedia** -Umgebung aus.

    ![Screenshot der ausgewählten AzureMedia-Umgebung.](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Konfigurieren der Sammlung

1. Erstellen Sie eine JSON-Datei, die die Sammlung **Postman** mit allen Vorgängen enthält, die zum Hochladen einer Datei in Media Services erforderlich sind. Benennen Sie die Datei (z.B. **AzureMediaServicesOperations.postman_collection.json** ). Öffnen Sie die Datei, und fügen Sie den Code ein, der die **Postman** -Sammlung über [diese Codeliste](postman-collection.md) definiert.
2. Klicken Sie auf **Import** , um die Sammlungsdatei zu importieren.
3. Wählen Sie die Datei **AzureMediaServicesOperations.postman_collection.json** aus.

    ![Screenshot des Dialogfelds „Importieren“ mit ausgewählter Option „Dateien wählen“.](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den Artikel [Hochladen von Medienobjekten](media-services-rest-upload-files.md) an.  

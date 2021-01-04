---
title: Bearbeiten von APIs im Azure-Portal | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie API Management (APIM) zum Bearbeiten einer API verwenden. Sie können Vorgänge in der APIM-Instanz hinzufügen, löschen oder umbenennen oder den API-Swagger bearbeiten.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: 1c4e64251390936e8a63ee904ec69f173cac6114
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146704"
---
# <a name="edit-an-api"></a>Bearbeiten von APIs

Die Schritte in diesem Tutorial veranschaulichen, wie Sie API Management (APIM) verwenden, um eine API zu bearbeiten. 

+ Die Bearbeitung erfolgt durch das Hinzufügen, Löschen oder Umbenennen von Vorgängen in der APIM-Instanz. 
+ Sie können auch den Swagger Ihrer API bearbeiten.

## <a name="prerequisites"></a>Voraussetzungen

+ [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)
+ [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="edit-an-api-in-apim"></a>Bearbeiten von APIs in APIM

![Screenshot, der den Vorgang des Bearbeitens einer API in APIM veranschaulicht.](./media/edit-api/edit-api001.png)

1. Klicken Sie auf die Registerkarte **APIs**.
2. Wählen Sie eine der APIs aus, die Sie zuvor importiert haben.
3. Klicken Sie auf die Registerkarte **Entwurf**.
4. Wählen Sie einen Vorgang aus, den Sie bearbeiten möchten.
5. Wählen Sie zum Umbenennen des Vorgangs einen **Stift** im Fenster **Front-End** aus.

## <a name="update-the-swagger"></a>Aktualisieren des Swaggers

Sie können Ihre Back-End-API im Azure-Portal aktualisieren, indem Sie folgende Schritte ausführen:

1. Auswählen von **Alle Vorgänge**
2. Klicken Sie auf den Stift im Fenster **Front-End**.

    ![Screenshot, der das Stiftsymbol im Bildschirm „Front-End“ hervorhebt.](./media/edit-api/edit-api002.png)

    Ihr API-Swagger wird angezeigt.

    ![Bearbeiten von APIs](./media/edit-api/edit-api003.png)

3. Aktualisieren Sie den Swagger.
4. Klicken Sie auf **Speichern**.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Beispiele für APIM-Richtlinien](./policy-reference.md)
> [Transformieren und Schützen veröffentlichter APIs](transform-api.md)
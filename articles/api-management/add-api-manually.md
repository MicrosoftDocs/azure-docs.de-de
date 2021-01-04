---
title: Manuelles Hinzufügen einer SOAP-API mithilfe des Azure-Portals | Microsoft-Dokumentation
description: Dieses Tutorial veranschaulicht, wie Sie API Management (APIM) verwenden, um eine API manuell hinzuzufügen.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 39a3b9d7dd9efbda93de0b5d7c5f9938922d0012
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183824"
---
# <a name="add-an-api-manually"></a>Manuelles Hinzufügen einer API

Die Schritte in diesem Artikel zeigen, wie Sie das Azure-Portal verwenden, um der API Management-Instanz (APIM) manuell eine API hinzuzufügen. Ein häufiges Szenario für das Erstellen einer leeren API und das manuelle Definieren ist, wenn Sie die API simulieren möchten. Weitere Informationen zum Simulieren von APIs finden Sie unter [Simulieren von API-Antworten](mock-api-responses.md).

Wenn Sie eine vorhandene API importieren möchten, finden Sie im Abschnitt [Verwandte Themen](#related-topics) weiterführende Informationen.

In diesem Artikel erstellen wir eine leere API und geben als Back-End-API [httpbin.org](https://httpbin.org) (einen öffentlichen Testdienst) an.

## <a name="prerequisites"></a>Voraussetzungen

Bearbeiten Sie den folgenden Schnellstart: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>Erstellen einer API

1. Navigieren Sie im Azure-Portal zu Ihrem API Management-Dienst, und wählen Sie im Menü **APIs** aus.
2. Wählen Sie im linken Menü die Option **+ API hinzufügen** aus.
3. Wählen Sie in der Liste **Leere API** aus.  
    ![Leere API](media/add-api-manually/blank-api.png)  
4. Geben Sie Einstellungen für die API ein. Die Einstellungen werden im Tutorial [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md#import-and-publish-a-backend-api) erläutert.
5. Klicken Sie auf **Erstellen**.

Zu diesem Zeitpunkt enthält API Management noch keine Vorgänge, die Vorgängen in Ihrer Back-End-API zugeordnet sind. Wenn Sie einen Vorgang aufrufen, der über das Back-End und nicht über API Management verfügbar gemacht wird, erhalten Sie den Fehler **404**.

>[!NOTE] 
> Wenn Sie eine API hinzufügen, macht APIM standardmäßig Vorgänge erst verfügbar, wenn Sie diese zulassen. Dies gilt auch dann, wenn die API mit einem Back-End-Dienst verbunden ist. Um einen Vorgang Ihres Back-End-Diensts zuzulassen, erstellen Sie einen APIM-Vorgang, der dem Back-End-Vorgang zugeordnet ist.

## <a name="add-and-test-an-operation"></a>Hinzufügen und Testen von Vorgängen

In diesem Abschnitt wird zeigt, wie Sie einen „/get“-Vorgang hinzufügen, um diesen dem Back-End-Vorgang „http://httpbin.org/get“ zuzuordnen.

### <a name="add-an-operation"></a>Hinzufügen eines Vorgangs

1. Wählen Sie die API aus, die Sie im vorherigen Schritt erstellt haben.
2. Klicken Sie auf **+ Vorgang hinzufügen**.
3. Wählen Sie in **URL** die Option **GET** aus, und geben Sie */get* in der Ressource ein.
4. Geben Sie *FetchData* als **Anzeigename** ein.
5. Wählen Sie **Speichern** aus.

### <a name="test-an-operation"></a>Testen eines Vorgangs

Testen Sie den Vorgang im Azure-Portal. Alternativ können Sie ihn auch im **Entwicklerportal** testen.

1. Wählen Sie die Registerkarte **Testen** aus.
2. Wählen Sie **FetchData** aus.
3. Klicken Sie auf **Senden**.

Die Antwort, die der Vorgang „http://httpbin.org/get“ generiert, wird angezeigt. Wenn Sie Ihre Vorgänge transformieren möchten, lesen Sie unter [Transformieren und Schützten Ihrer API](transform-api.md) nach.

## <a name="add-and-test-a-parameterized-operation"></a>Hinzufügen und Testen parametrisierter Vorgänge

In diesem Abschnitt wird gezeigt, wie Sie einen Vorgang hinzufügen, der einen Parameter akzeptiert. In diesem Fall ordnen wir den Vorgang „http://httpbin.org/status/200“ zu.

### <a name="add-the-operation"></a>Hinzufügen des Vorgangs

1. Wählen Sie die API aus, die Sie im vorherigen Schritt erstellt haben.
2. Klicken Sie auf **+ Vorgang hinzufügen**.
3. Wählen Sie in **URL** die Option **GET** aus, und geben Sie */status/{Code}* in der Ressource ein. Optional können Sie einige Informationen zu diesem Parameter angeben. Geben Sie z.B. *Number* als **TYPE** oder *200* (Standard) für **VALUES** ein.
4. Geben Sie „GetStatus“ als **Anzeigename** ein.
5. Wählen Sie **Speichern** aus.

### <a name="test-the-operation"></a>Testen des Vorgangs 

Testen Sie den Vorgang im Azure-Portal.  Alternativ können Sie ihn auch im **Entwicklerportal** testen.

1. Wählen Sie die Registerkarte **Testen** aus.
2. Wählen Sie **GetStatus** aus. Standardmäßig ist der Codewert auf *200* festgelegt. Sie können ihn ändern, um andere Werte zu testen. Geben Sie beispielsweise *418* ein.
3. Klicken Sie auf **Senden**.

    Die Antwort, die der Vorgang „http://httpbin.org/status/200“ generiert, wird angezeigt. Wenn Sie Ihre Vorgänge transformieren möchten, lesen Sie unter [Transformieren und Schützten Ihrer API](transform-api.md) nach.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Transformieren und Schützen einer veröffentlichten API](transform-api.md)

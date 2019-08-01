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
ms.devlang: na
ms.topic: tutorial
ms.date: 08/27/2018
ms.author: apimpm
ms.openlocfilehash: 0094364ed2e5d6c024f75a88db90eb703792f9f3
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405833"
---
# <a name="add-an-api-manually"></a>Manuelles Hinzufügen einer API

Die Schritte in diesem Artikel zeigen, wie Sie das Azure-Portal verwenden, um der API Management-Instanz (APIM) manuell eine API hinzuzufügen. Ein häufiges Szenario für das Erstellen einer leeren API und das manuelle Definieren ist, wenn Sie die API simulieren möchten. Weitere Informationen zum Simulieren von APIs finden Sie unter [Simulieren von API-Antworten](mock-api-responses.md).

Wenn Sie eine vorhandene API importieren möchten, finden Sie im Abschnitt [Verwandte Themen](#related-topics) weiterführende Informationen.

In diesem Artikel erstellen wir eine leere API und geben als Back-End-API [httpbin.org](https://httpbin.org) (einen öffentlichen Testdienst) an.

## <a name="prerequisites"></a>Voraussetzungen

Absolvieren Sie die folgende Schnellstartanleitung: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>Erstellen einer API

1. Wählen Sie unter **API MANAGEMENT** die Option **APIs** aus.
2. Wählen Sie im linken Menü die Option **+ API hinzufügen** aus.
3. Wählen Sie in der Liste **Leere API** aus.

    ![Leere API](media/add-api-manually/blank-api.png)
4. Geben Sie Einstellungen für die API ein.

    |**Name**|**Wert**|**Beschreibung**|
    |---|---|---|
    |**Anzeigename**|*Leere API*|Dieser Name wird im Entwicklerportal angezeigt.|
    |**Name**|*blank-api*|Stellt einen eindeutigen Namen für die API bereit.|
    |**Webdienst-URL** (optional)|*https://httpbin.org*| Wenn Sie eine API simulieren möchten, müssen Sie nichts eingeben. <br/>In diesem Fall geben Sie [https://httpbin.org](https://httpbin.org) ein. Dies ist ein öffentlicher Testdienst. <br/>Wenn Sie eine API importieren möchten, die automatisch einem Back-End zugeordnet wird, sehen Sie sich die Themen im Abschnitt [Verwandte Themen](#related-topics) an.|
    |**URL-Schema**|*HTTPS*|Obwohl das Back-End in diesem Fall keinen sicheren HTTP-Zugriff hat, geben wir einen sicheren HTTPS-APIM-Zugriff auf das Back-End an. <br/>Diese Art von Szenario (HTTPS nach HTTP) wird als HTTPS-Beendigung bezeichnet. Sie können dies verwenden, wenn sich Ihre API in einem virtuellen Netzwerk befindet (in dem Sie wissen, dass der Zugriff sicher ist, auch wenn nicht HTTPS verwendet wird). <br/>Sie können die „HTTPS-Beendigung“ auch verwenden, um einige CPU-Zyklen zu sparen.|
    |**URL-Suffix**|*hbin*| Das Suffix ist ein Name, der diese spezifische API in dieser APIM-Instanz identifiziert. Es muss in dieser APIM-Instanz eindeutig sein.|
    |**Produkte**|*Unbegrenzt*|Veröffentlichen Sie die API, indem Sie sie einem Produkt zuordnen. Wenn Sie möchten, dass die API veröffentlicht wird und dann Entwicklern zur Verfügung steht, fügen Sie sie einem Produkt hinzu. Sie können dies während der Erstellung der API vornehmen oder später festlegen.<br/><br/>Bei Produkten handelt es sich um API-Zuordnungen. Sie können eine Reihe von APIs einfügen und sie Entwicklern über das Entwicklerportal zur Verfügung stellen. <br/>Entwickler müssen ein Produkt zunächst abonnieren, um Zugriff auf die API zu erhalten. Wenn sie ein Produkt abonnieren, erhalten sie einen Abonnementschlüssel, der für jede API in diesem Produkt gilt. Wenn Sie die APIM-Instanz erstellt haben, sind Sie bereits Administrator und haben dadurch standardmäßig alle Produkte abonniert.<br/><br/> Standardmäßig enthält jede API Management-Instanz zwei Beispielprodukte: **Starter** und **Unbegrenzt**.| 
5. Klicken Sie auf **Erstellen**.

An diesem Punkt enthält APIM noch keine Vorgänge, die Vorgängen in Ihrer Back-End-API zugeordnet sind. Wenn Sie einen Vorgang aufrufen, der über das Back-End und nicht über APIM verfügbar gemacht wird, erhalten Sie den Fehler **404**.

>[!NOTE] 
> Wenn Sie eine API hinzufügen, macht APIM standardmäßig Vorgänge erst verfügbar, wenn Sie diese auf die Whitelist setzen. Dies gilt auch dann, wenn die API mit einem Back-End-Dienst verbunden ist. Um einen Vorgang Ihres Back-End-Diensts auf die Whitelist zu setzen, erstellen Sie einen APIM-Vorgang, der dem Back-End-Vorgang zugeordnet ist.

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

<!-- Test -->

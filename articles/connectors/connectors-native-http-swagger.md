---
title: Aufrufen von REST-Endpunkten über Azure Logic Apps | Microsoft-Dokumentation
description: Verwenden des Connectors „HTTP + Swagger“ in Azure Logic Apps zum Automatisieren von Aufgaben und Workflows, die mit REST-Endpunkten kommunizieren
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: eccfd87c-c5fe-4cf7-b564-9752775fd667
tags: connectors
ms.topic: article
ms.date: 07/18/2016
ms.openlocfilehash: 9408b66f74391b080ef46c758b07850b2ae8de57
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60448627"
---
# <a name="call-rest-endpoints-with-http--swagger-connector-in-azure-logic-apps"></a>Aufrufen von REST-Endpunkten mit dem Connector „HTTP + Swagger“ in Azure Logic Apps

Sie können einen erstklassigen Connector für beliebige REST-Endpunkte über ein [Swagger-Dokument](https://swagger.io) erstellen, indem Sie die Aktion „HTTP + Swagger“ in Ihrem Logik-App-Workflow verwenden. Sie können Logik-Apps auch so erweitern, dass sie beliebige REST-Endpunkte mit erstklassigen Logik-App-Designer-Funktionen aufruft.

Informationen zum Erstellen von Logik-Apps mit Connectors finden Sie unter [Erstellen einer neuen Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Verwenden von „HTTP + Swagger“ als Trigger oder Aktion

Der Trigger und die Aktion „HTTP + Swagger“ funktionieren wie die [HTTP-Aktion](connectors-native-http.md), lassen sich im Logik-App-Designer aber besser verwenden, da sie die API-Struktur und Ausgaben der [Swagger-Metadaten](https://swagger.io) verfügbar machen. Sie können auch den „HTTP + Swagger“-Connector als Trigger verwenden. Wenn Sie einen Abruftrigger implementieren möchten, verwenden Sie das unter [Erstellen benutzerdefinierter APIs zum Aufrufen anderer APIs, Dienste und Systeme aus Logik-Apps](../logic-apps/logic-apps-create-api-app.md#polling-triggers) verwenden.

Weitere Informationen über Logik-App-Trigger und -Aktionen finden Sie [hier](../connectors/apis-list.md).

Im Folgenden finden Sie ein Beispiel der Verwendung des Vorgangs „HTTP + Swagger“ als Aktion in einem Workflow in einer Logik-App.

1. Wählen Sie die Schaltfläche **Neuer Schritt** aus.
2. Wählen Sie **Aktion hinzufügen**aus.
3. Geben Sie im Aktionssuchfeld die Zeichenfolge **swagger** ein, um die Aktion „HTTP + Swagger“ anzuzeigen.
   
    ![Aktion „HTTP + Swagger“ auswählen](./media/connectors-native-http-swagger/using-action-1.png)
4. Geben Sie die URL für ein Swagger-Dokument ein:
   
   * Die URL muss ein HTTPS-Endpunkt und für CORS aktiviert sein, damit sie im Logik-App-Designer funktioniert.
   * Wenn das Swagger-Dokument diese Anforderung nicht erfüllt, können Sie Azure Storage mit aktiviertem CORS zum Speichern des Dokuments verwenden.
5. Klicken Sie auf **Weiter** , um das Swagger-Dokument zu lesen und zum Rendern zu nutzen.
6. Geben Sie alle Parameter an, die ggf. für den HTTP-Aufruf erforderlich sind.
   
    ![Konfigurieren der HTTP-Aktion](./media/connectors-native-http-swagger/using-action-2.png)
7. Klicken Sie zum Speichern und Veröffentlichen der Logik-App auf der Designer-Symbolleiste auf **Speichern**.

### <a name="host-swagger-from-azure-storage"></a>Hosten von Swagger aus Azure Storage
Möglicherweise möchten Sie auf ein Swagger-Dokument verweisen, das nicht gehostet wird, oder nicht die Sicherheits- und CORS-Anforderungen für den Designer erfüllt. Um dieses Problem zu lösen, können Sie das Swagger-Dokument in Azure Storage speichern und CORS aktivieren, um das Dokument zu referenzieren.  

Im Folgenden finden Sie die Schritte zum Erstellen, Konfigurieren und Speichern von Swagger-Dokumenten in Azure Storage:

1. [Erstellen Sie ein Azure Storage-Konto mit Azure Blob Storage](../storage/common/storage-create-storage-account.md). Legen Sie für diesen Schritt die Berechtigungen auf **Öffentlicher Zugriff**fest.

2. Aktivieren Sie CORS für das Blob. 

   Sie können diese Einstellung mit [diesem PowerShell-Skript](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1) automatisch konfigurieren.

3. Laden Sie die Swagger-Datei in das Blob hoch. 

   Hierzu können Sie das [Azure-Portal](https://portal.azure.com) oder ein Tool wie den [Azure Storage-Explorer](https://storageexplorer.com/) verwenden.

4. Verweisen Sie mit einem HTTPS-Link auf das Dokument in Azure Blob Storage. 

   Der Link hat das folgende Format:

   `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`

## <a name="technical-details"></a>Technische Details
Im Folgenden finden Sie Details zu den von diesem „HTTP + Swagger“-Connector unterstützten Triggern und Aktionen.

## <a name="http--swagger-triggers"></a>„HTTP + Swagger“-Trigger
Ein Trigger ist ein Ereignis, mit dem ein in einer Logik-App definierter Workflow gestartet werden kann. Der Connector „HTTP + Swagger“ verfügt über einen Trigger. Weitere Informationen zu Triggern finden Sie [hier](../connectors/apis-list.md).

| Trigger | BESCHREIBUNG |
| --- | --- |
| HTTP + Swagger |Führt einen HTTP-Aufruf durch und gibt den Antwortinhalt zurück. |

## <a name="http--swagger-actions"></a>„HTTP + Swagger“-Aktionen
Eine Aktion ist ein Vorgang, der durch den in einer Logik-App definierten Workflow ausgeführt wird. Der Connector „HTTP + Swagger“ verfügt über eine mögliche Aktion. Weitere Informationen zu Aktionen finden Sie [hier](../connectors/apis-list.md).

| Aktion | BESCHREIBUNG |
| --- | --- |
| HTTP + Swagger |Führt einen HTTP-Aufruf durch und gibt den Antwortinhalt zurück. |

### <a name="action-details"></a>Aktionsdetails
Der Connector „HTTP + Swagger“ verfügt über eine mögliche Aktion. Im Anschluss finden Sie Informationen zu den einzelnen Aktionen und den erforderlichen und optionalen Eingabefeldern sowie entsprechende Ausgabedetails in Verbindung mit deren Verwendung.

#### <a name="http--swagger"></a>HTTP + Swagger
Erstellen Sie eine ausgehende HTTP-Anforderung mit Unterstützung von Swagger-Metadaten.
Ein * bedeutet, dass es sich um ein Pflichtfeld handelt.

| `Display name` | Eigenschaftenname | BESCHREIBUNG |
| --- | --- | --- |
| Methode* |method |Zu verwendendes HTTP-Verb. |
| URI* |uri |URI für die HTTP-Anforderung. |
| Header |headers |Ein JSON-Objekt für die einzubeziehenden HTTP-Header. |
| Body |body |Der HTTP-Anforderungstext. |
| Authentication |authentication |Für die Anforderung zu verwendende Authentifizierung. Weitere Informationen finden Sie unter [HTTP-Connector](connectors-native-http.md#authentication). |

**Ausgabedetails**

HTTP-Antwort

| Eigenschaftenname | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| headers |object |Antwortheader |
| Body |object |Antwortobjekt |
| Statuscode |int |HTTP-Statuscode |

### <a name="http-responses"></a>HTTP-Antworten
Das Aufrufen verschiedener Aktionen löst unter Umständen bestimmte Antworten aus. Die folgende Tabelle enthält entsprechende Antworten und Beschreibungen.

| NAME | BESCHREIBUNG |
| --- | --- |
| 200 |OK |
| 202 |Zulässig |
| 400 |Ungültige Anforderung |
| 401 |Nicht autorisiert |
| 403 |Verboten |
| 404 |Nicht gefunden |
| 500 |Interner Serverfehler. Unbekannter Fehler. |

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Andere Connectors](apis-list.md)

---
title: Herstellen der Verbindung mit REST-Endpunkten von Azure Logic Apps
description: Überwachen von REST-Endpunkten in automatisierten Aufgaben, Prozessen und Workflows mithilfe von Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: af98811e158b9613e41389e08e19cb36797aa272
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790592"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Aufrufen von REST-Endpunkten mit Azure Logic Apps

Mit [Azure Logic Apps](../logic-apps/logic-apps-overview.md) und dem integrierten „HTTP + Swagger“-Connector können Sie durch Erstellung von Logik-Apps Workflows automatisieren, die regelmäßig REST-Endpunkte über eine [Swagger-Datei](https://swagger.io) aufrufen. Der „HTTP + Swagger“-Trigger und die Aktion funktionieren wie [HTTP-Trigger und Aktion](connectors-native-http.md), lassen sich im Logik-App-Designer aber besser verwenden, da sie die API-Struktur und Ausgaben verfügbar machen, die in der Swagger-Datei beschrieben werden. Um einen Abruftrigger zu implementieren, verwenden Sie das unter [Erstellen benutzerdefinierter APIs zum Aufrufen anderer APIs, Dienste und Systeme aus Logik-Apps](../logic-apps/logic-apps-create-api-app.md#polling-triggers) beschriebene Abrufmuster.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Die URL für die Swagger-Datei (nicht OpenAPI), die den REST-Zielendpunkt beschreibt

  Der REST-Endpunkt muss in der Regel diese Kriterien für den Connector erfüllen, damit der Connector funktioniert:

  * Die Swagger-Datei muss auf einer HTTPS-URL gehostet werden, die öffentlich zugänglich ist.
  
  * Die Swagger-Datei muss ein `operationID` für jeden Vorgang in der Definition enthalten. Wenn das nicht der Fall ist, zeigt der Connector nur den letzten Vorgang in der Swagger-Datei an. 

  * Für die Swagger-Datei muss [Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) aktiviert sein.

  Um auf eine Swagger-Datei zu verweisen, die nicht gehostet wird oder nicht den Sicherheitsanforderungen und Anforderungen für die Ressourcenfreigabe zwischen verschiedenen Ursprüngen entspricht, können Sie [die Swagger-Datei in einen Blobcontainer in einem Azure-Speicherkonto hochladen](#host-swagger) und CORS auf diesem Speicherkonto aktivieren, sodass Sie auf die Datei verweisen können.

  Die Beispiele in diesem Thema verwenden die [Cognitive Services Face-API](../cognitive-services/face/overview.md), die ein [Cognitive Services-Konto und einen Zugriffsschlüssel](../cognitive-services/cognitive-services-apis-create-account.md) erfordert.

* Grundlegende Kenntnisse über das [Erstellen von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

* Die Logik-App, von der aus Sie den Zielendpunkt aufrufen möchten. Um mit dem „HTTP + Swagger“-Trigger zu beginnen, [erstellen Sie eine leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). Um die „HTTP + Swagger“-Aktion zu verwenden, starten Sie Ihre Logik-App mit einem beliebigen Trigger. Dieses Beispiel verwendet den „HTTP + Swagger“-Trigger als ersten Schritt.

## <a name="add-an-http--swagger-trigger"></a>Hinzufügen eines „HTTP + Swagger“-Triggers

Dieser integrierte Trigger sendet eine HTTP-Anfrage an eine URL für eine Swagger-Datei, die eine REST-API beschreibt und eine Antwort zurückgibt, die den Inhalt dieser Datei enthält.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Öffnen Sie Ihre leere Logik-App im Logik-App-Designer.

1. Geben Sie im Suchfeld des Designers „swagger“ als Filter ein. Wählen Sie in der Liste **Trigger** den **HTTP + Swagger** -Trigger aus.

   ![Auswählen des „HTTP + Swagger“-Triggers](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. Geben Sie im Feld **SWAGGER-ENDPUNKT-URL** die URL für die Swagger-Datei ein, und wählen Sie **Weiter** aus.

   Dieses Beispiel verwendet die Swagger-URL, die sich in der Region "USA, Westen" für die [Cognitive Services Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) befindet:

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Screenshot: Logik-App-Designer mit dem Trigger „HTTP + Swagger“ und der auf einen URL-Wert festgelegten Eigenschaft „Swagger-Endpunkt-URL“](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Wenn der Designer die von der Swagger-Datei beschriebenen Vorgänge anzeigt, wählen Sie den Vorgang aus, den Sie verwenden möchten.

   ![Screenshot: Logik-App-Designer mit dem Trigger „HTTP + Swagger“ und einer Liste mit Swagger-Vorgängen](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Geben Sie die je nach dem ausgewählten Vorgang variierenden Werte für die Triggerparameter an, die Sie in den Endpunktaufruf aufnehmen möchten. Geben Sie mithilfe einer Wiederholung an, wie oft der Trigger den Endpunkt aufrufen soll.

   Dieses Beispiel benennt den Trigger in „HTTP + Swagger trigger: Face – Detect“ („HTTP + Swagger“-Trigger: Gesicht – Erkennen) um, damit der Schritt eine aussagekräftigere Beschreibung hat.

   ![Screenshot: Logik-App-Designer mit dem Trigger „HTTP + Swagger“ und dem Vorgang „ Gesicht – Erkennen“](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Öffnen Sie zum Hinzufügen weiterer verfügbarer Parameter die Liste **Neuen Parameter hinzufügen** , und wählen Sie die gewünschten Parameter aus.

   Weitere Informationen zu verfügbaren Authentifizierungstypen für HTTP und Swagger finden Sie unter [Hinzufügen von Authentifizierung zu ausgehenden Aufrufen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Fahren Sie mit dem Erstellen des Workflows Ihrer Logik-App fort, und fügen Sie weitere Aktionen hinzu, die bei Auslösung des Triggers ausgeführt werden.

1. Speichern Sie die Logik-App unbedingt, wenn Sie fertig sind. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

## <a name="add-an-http--swagger-action"></a>Hinzufügen einer „HTTP + Swagger“-Aktion

Diese integrierte Aktion sendet eine HTTP-Anfrage an eine URL für die Swagger-Datei, die eine REST-API beschreibt und eine Antwort zurückgibt, die den Inhalt dieser Datei enthält.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Öffnen Sie Ihre Logik-App im Logik-App-Designer.

1. Wählen Sie im Schritt zum Hinzufügen der „HTTP + Swagger“-Aktion die Option **Neuer Schritt** aus.

   Wenn Sie zwischen Schritten eine Aktion einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen den Schritten. Wählen Sie das angezeigte Pluszeichen ( **+** ) aus, und wählen Sie dann **Aktion hinzufügen** aus.

1. Geben Sie im Suchfeld des Designers „swagger“ als Filter ein. Wählen Sie in der Liste **Aktionen** die **HTTP + Swagger** -Aktion aus.

    ![Aktion „HTTP + Swagger“ auswählen](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. Geben Sie im Feld **SWAGGER-ENDPUNKT-URL** die URL für die Swagger-Datei ein, und wählen Sie **Weiter** aus.

   Dieses Beispiel verwendet die Swagger-URL, die sich in der Region "USA, Westen" für die [Cognitive Services Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) befindet:

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Eingeben der URL für den Swagger-Endpunkt](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Wenn der Designer die von der Swagger-Datei beschriebenen Vorgänge anzeigt, wählen Sie den Vorgang aus, den Sie verwenden möchten.

   ![Vorgänge in der Swagger-Datei](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Geben Sie die je nach dem ausgewählten Vorgang variierenden Werte für die Aktionsparameter an, die Sie in den Endpunktaufruf aufnehmen möchten.

   Dieses Beispiel hat keine Parameter, aber benennt die Aktion in „HTTP + Swagger action: Face – Identify“ („HTTP + Swagger“-Aktion: Gesicht – Identifizieren) um, damit der Schritt eine aussagekräftigere Beschreibung hat.

   ![Vorgangsdetails](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Öffnen Sie zum Hinzufügen weiterer verfügbarer Parameter die Liste **Neuen Parameter hinzufügen** , und wählen Sie die gewünschten Parameter aus.

   Weitere Informationen zu verfügbaren Authentifizierungstypen für HTTP und Swagger finden Sie unter [Hinzufügen von Authentifizierung zu ausgehenden Aufrufen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Speichern Sie die Logik-App unbedingt, wenn Sie fertig sind. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Hosten von Swagger in Azure Storage

Sie können auf eine Swagger-Datei verweisen, die nicht gehostet wird oder nicht den Sicherheitsanforderungen und Anforderungen für die Ressourcenfreigabe zwischen verschiedenen Ursprüngen entspricht, indem Sie diese Datei in einen Blobcontainer in einem Azure-Speicherkonto hochladen und CORS auf diesem Speicherkonto aktivieren. Um Swagger-Dateien in Azure Storage zu erstellen, einzurichten und zu speichern, führen Sie diese Schritte aus:

1. [Erstellen Sie ein Azure-Speicherkonto](../storage/common/storage-account-create.md).

1. Aktivieren Sie jetzt CORS für das Blob. Wählen Sie im Menü Ihres Speicherkontos **CORS** aus. Geben Sie auf der Registerkarte **Blob-Dienst** diese Werte ein, und wählen Sie dann **Speichern** aus.

   | Eigenschaft | Wert |
   |----------|-------|
   | **Zulässige Ursprünge** | `*` |
   | **Zulässige Methoden** | `GET`, `HEAD`, `PUT` |
   | **Zulässige Header** | `*` |
   | **Verfügbar gemachte Header** | `*` |
   | **Max. Alter** (in Sekunden) | `200` |
   |||

   Obwohl in diesem Beispiel das [Azur-Portal](https://portal.azure.com) verwendet wird, können Sie ein Tool wie [Azure Storage Explorer](https://storageexplorer.com/) verwenden oder diese Einstellung automatisch mit diesem [PowerShell-Beispielskript](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1) konfigurieren.

1. [Erstellen Sie einen Blobcontainer](../storage/blobs/storage-quickstart-blobs-portal.md). Wählen Sie im Bereich **Übersicht** des Containers **Zugriffsebene ändern** aus. Wählen Sie in der Liste **Öffentliche Zugriffsebene** den Eintrag **Blob (anonymer Lesezugriff nur für Blobs)** und dann **OK** aus.

1. [Laden Sie die Swagger-Datei in den Blobcontainer hoch](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), entweder mit dem [Azure-Portal](https://portal.azure.com) oder [Azure Storage-Explorer](https://storageexplorer.com/).

1. Um auf die Datei im Blobcontainer zu verweisen, rufen Sie die HTTPS-URL im folgenden Format (Beachtung der Groß-/Kleinschreibung) aus Azure Storage-Explorer ab:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<complete-swagger-file-name>?<query-parameters>`

## <a name="connector-reference"></a>Connector-Referenz

Hier finden Sie weitere Informationen zu den Ausgaben eines „HTTP + Swagger“-Triggers oder einer „HTTP + Swagger“-Aktion. Der „HTTP + Swagger“-Aufruf gibt diese Informationen zurück:

| Eigenschaftenname | type | BESCHREIBUNG |
|---------------|------|-------------|
| headers | Objekt (object) | Die Header aus der Anforderung |
| body | Objekt (object) | JSON-Objekt | Das Objekt mit dem Inhalt des Texts aus der Anforderung |
| status code | INT | Der Statuscode aus der Anforderung |
|||

| Statuscode | Beschreibung |
|-------------|-------------|
| 200 | OK |
| 202 | Zulässig |
| 400 | Ungültige Anforderung |
| 401 | Nicht autorisiert |
| 403 | Verboten |
| 404 | Nicht gefunden |
| 500 | Interner Serverfehler. Unbekannter Fehler. |
|||

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)

---
title: Verwenden von Moderationsaufträgen über die REST-API-Konsole – Content Moderator
titleSuffix: Azure Cognitive Services
description: Verwenden Sie die Auftragsvorgänge der Überprüfungs-API, um End-to-End-Inhaltsmoderationsaufträge für Bild- und Textinhalt in Azure Content Moderator zu initiieren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 924c21037a464770fac13c9b45ddcf261ff5a058
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905151"
---
# <a name="define-and-use-moderation-jobs-api-console"></a>Definieren und Verwenden von Moderationsaufträgen (API-Konsole)

Ein Moderationsauftrag dient als eine Art Wrapper für die Funktionen der Inhaltsmoderation und von Workflows und Überprüfungen. In dieser Anleitung wird erläutert, wie Sie mithilfe der REST-APIs für Aufträge Inhaltsmoderationsaufträge initiieren und überprüfen können. Nachdem Sie sich mit der Struktur der APIs vertraut gemacht haben, können Sie diese Aufrufe bequem zu jeder REST-kompatiblen Plattform portieren.

## <a name="prerequisites"></a>Voraussetzungen

- Melden Sie sich auf der Website des [Content Moderator-Prüfungstools](https://contentmoderator.cognitive.microsoft.com/) an, oder erstellen Sie dort ein Konto.
- (Optional:) [Definieren Sie einen benutzerdefinierten Workflow](./Review-Tool-User-Guide/Workflows.md) zur Verwendung mit dem Auftrag. Genauso können Sie auch den Standardworkflow verwenden.

## <a name="create-a-job"></a>Erstellen eines Auftrags

Um einen Moderationsauftrag zu erstellen, wechseln Sie zur API-Referenzseite [Auftrag – Erstellen](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5), und wählen Sie die Schaltfläche für Ihre Abonnementregion aus. Sie können Ihre Region in der Endpunkt-URL auf der Seite **Anmeldeinformationen** des [Prüfungstools](https://contentmoderator.cognitive.microsoft.com/) finden. Dadurch wird die API-Konsole gestartet, über die Sie ganz einfach REST-API-Aufrufe erstellen und ausführen können.

![Regionsauswahl auf der Seite „Job – Create“ (Auftrag – erstellen)](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Eingeben von REST-Aufrufparametern

Geben Sie die folgenden Werte zum Erstellen des REST-Aufrufs ein:

- **teamName**: Die Team-ID, die Sie beim Einrichten Ihres Kontos für das [Prüfungstool](https://contentmoderator.cognitive.microsoft.com/) erstellt haben (zu finden im Feld **ID** auf der Seite „Anmeldeinformationen“ des Prüfungstools).
- **ContentType:** Dies kann „Image“, „Text“ oder „Video“ sein.
- **ContentId**: Eine benutzerdefinierte Bezeichnerzeichenfolge. Diese Zeichenfolge wird an die API übergeben und durch den Rückruf zurückgegeben. Sie ist nützlich, um interne Bezeichner oder Metadaten mit den Ergebnissen eines Moderationsauftrags zu verknüpfen.
- **Workflowname:** Der Name des Workflows, den Sie zuvor erstellt haben (oder „default“ für den Standardworkflow).
- **CallbackEndpoint:** (Optional:) Die URL zum Empfangen von Rückrufinformationen, wenn die Überprüfung abgeschlossen ist.
- **Ocp-Apim-Subscription-Key**: Ihr Content Moderator-Schlüssel. Diesen Schlüssel finden Sie auf der Registerkarte **Einstellungen** des [Prüfungstools](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Ausfüllen des Anforderungstexts

Der Text des REST-Aufrufs enthält ein Feld: **ContentValue**. Fügen Sie bei der Moderation von Text den reinen Textinhalt ein, oder geben Sie bei der Moderation von Bildern oder Videos eine Bild- oder Video-URL ein. Sie können die folgende Beispielbild-URL verwenden: [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Abfrageparameter, Header und Feld „Anforderungstext“ in der Konsole „Auftrag – Erstellen“](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Senden der Anforderung

Wählen Sie **Send** (Senden) aus. Wenn der Vorgang erfolgreich ist, lautet der **Antwortstatus**`200 OK`, und im Feld **Antwortinhalt** wird eine ID für den Auftrag angezeigt. Kopieren Sie diese ID für die folgenden Schritte.

![Feld „Antwortinhalt“ mit Überprüfungs-ID in der Konsole „Überprüfen – Erstellen“](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Abrufen des Auftragsstatus

Um den Status und Details zu einem ausgeführten oder abgeschlossenen Auftrag abzurufen, navigieren Sie zur API-Referenzseite, und wählen Sie unter [Job – Get](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) (Auftrag – abrufen) die Schaltfläche für Ihre Region aus (die Region, in der Ihr Schlüssel verwaltet wird).

![Regionsauswahl auf der Seite „Job – Get“ (Auftrag – abrufen)](images/test-drive-region.png)

Geben Sie die REST-Aufrufparameter wie im obigen Abschnitt ein. In diesem Schritt haben Sie beim Erstellen des Auftrags **JobId** als eindeutige ID-Zeichenfolge erhalten. Wählen Sie **Send** (Senden) aus. Wenn der Vorgang erfolgreich ist, lautet der **Antwortstatus**`200 OK`, und im Feld **Antwortinhalt** wird der Auftrag im JSON-Format wie folgt angezeigt:

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![Job – Get (Auftrag – abrufen), Antwort auf REST-Aufruf](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Untersuchen der neuen Überprüfungen

Wenn Ihr Inhaltsauftrag zur Erstellung einer Überprüfung geführt hat, können Sie ihn im [Prüfungstool](https://contentmoderator.cognitive.microsoft.com) anzeigen. Wählen Sie **Überprüfung** > **Bild**/**Text**/**Video** aus (abhängig vom verwendeten Inhalt). Der Inhalt sollte angezeigt werden und kann durch Personen überprüft werden. Nachdem eine Person die automatisch zugewiesenen Tags und Vorhersagedaten überprüft und eine finale Moderationsentscheidung getroffen hat, sendet die Überprüfungs-API alle Informationen an den festgelegten Rückrufendpunkt.

## <a name="next-steps"></a>Nächste Schritte

In dieser Anleitung haben Sie erfahren, wie Inhaltsmoderationsaufträge mithilfe der REST-API erstellt und abgefragt werden. Als Nächstes integrieren Sie Aufträge in ein End-to-End-Moderationsszenario, z. B. das im Tutorial [E-Commerce-Moderation](./ecommerce-retail-catalog-moderation.md).
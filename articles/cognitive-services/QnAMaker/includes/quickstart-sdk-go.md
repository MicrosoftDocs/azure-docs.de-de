---
title: 'Schnellstart: QnA Maker-Clientbibliothek für Go'
description: Diese Schnellstartanleitung zeigt Ihnen die ersten Schritte mit der QnA Maker-Clientbibliothek für Go.
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: 1ca800574f4a1c370d953c90ea5abad1a64f47b2
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351013"
---
Verwenden Sie die QnA Maker-Clientbibliothek für Go für Folgendes:

* Erstellen einer Wissensdatenbank
* Aktualisieren einer Wissensdatenbank
* Veröffentlichen einer Wissensdatenbank
* Abrufen von Endpunktschlüsseln der Vorhersage-Runtime
* Warten auf Aufgaben mit langer Ausführungsdauer
* Herunterladen einer Wissensdatenbank
* Abrufen der Antwort
* Löschen einer Wissensdatenbank

[Referenz (Erstellung)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker) | [Referenz (Runtime)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime) | [Quellcode der Bibliothek (Erstellung)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v4.0/qnamaker) | [Quellcode der Bibliothek (Runtime)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v4.0/qnamakerruntime) | [Beispiele](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/QnAMaker)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* [Go](https://golang.org/)
* Sobald Sie über Ihr Azure-Abonnement verfügen, erstellen Sie im Azure-Portal eine [QnA Maker-Ressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), um Ihren Erstellungsschlüssel und Endpunkt zu erhalten. Wählen Sie nach Abschluss der Bereitstellung **Zu Ressource wechseln** aus.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der QnA Maker-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-go-application"></a>Erstellen einer neuen Go-Anwendung

Erstellen Sie eine neue Datei namens `kb_sample.go`, und importieren Sie die folgenden Bibliotheken:

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="dependencies":::

Erstellen Sie Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource.

> [!IMPORTANT]
> Navigieren Sie zum Azure-Portal, und suchen Sie den Schlüssel und den Endpunkt für die unter „Voraussetzungen“ erstellte QnA Maker-Ressource. Sie befinden sich auf der Seite mit dem **Schlüssel und Endpunkt** der Ressource unter **Ressourcenverwaltung**.
> Sie benötigen den gesamten Schlüssel, um Ihre Wissensdatenbank zu erstellen. Sie benötigen nur den Ressourcennamen vom Endpunkt. Das Format ist `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Beispielsweise bietet der [Azure-Schlüsseltresor](../../../key-vault/general/overview.md) sichere Aufbewahrung von Schlüsseln.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="environment":::

## <a name="object-models"></a>Objektmodelle

QnA Maker verwendet zwei unterschiedliche Objektmodelle:
* **[QnAMakerClient](#qnamakerclient-object-model)** ist das Objekt zum Erstellen, Verwalten, Veröffentlichen und Herunterladen der Wissensdatenbank.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** ist das Objekt, das für Abfragen der Wissensdatenbank mit der GenerateAnswer-API und zum Senden neuer vorgeschlagener Fragen mithilfe der Train-API (im Rahmen des [aktiven Lernens](../concepts/active-learning-suggestions.md)) verwendet wird.

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient-Objektmodell

Verwenden Sie [KnowledgebaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient), um Ihre Wissensdatenbank zu erstellen, aktualisieren, veröffentlichen, herunterzuladen und zu löschen. Diese Vorgänge werden in den folgenden Abschnitten behandelt.

Verwenden Sie [OperationsClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#OperationsClient), um den Status zeitintensiver Vorgänge (etwa Erstellen und Aktualisieren von Wissensdatenbanken) zu überprüfen.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient-Objektmodell

Verwenden Sie nach der Veröffentlichung Ihrer Wissensdatenbank [RuntimeClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime#RuntimeClient), um Ihre Wissensdatenbank abzufragen. Dieser Vorgang wird unter [Abfragen der Wissensdatenbank](#query-the-knowledge-base) behandelt.

## <a name="create-a-knowledge-base"></a>Erstellen einer Wissensdatenbank

Eine Wissensdatenbank speichert Frage- und Antwortpaare für das [CreateKbDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#CreateKbDTO)-Objekt aus drei Quellen:

* Für **redaktionellen Inhalt** verwenden Sie das [QnADTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#QnADTO)-Objekt.
    * Verwenden Sie für Metadaten und Folgeaufforderungen den redaktionellen Kontext, da diese Daten auf der Ebene der einzelnen QnA-Paare hinzugefügt werden.
* Für **Dateien** verwenden Sie das [FileDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#FileDTO)-Objekt. Dieses Objekt enthält den Dateinamen sowie die öffentliche URL für den Zugriff auf die Datei.
* Verwenden Sie für **URLs** eine Liste von Zeichenfolgen zur Darstellung von öffentlich verfügbaren URLs.

Rufen Sie die [create](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Create)-Methode auf, und übergeben Sie dann die Eigenschaft `operationId`des zurückgegebenen Vorgangs an die [getDetails](#get-status-of-an-operation)-Methode, um den Status abzurufen.

In der letzten Zeile des folgenden Codes wird die Wissensdatenbank-ID zurückgegeben:

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="create_kb":::

## <a name="update-a-knowledge-base"></a>Aktualisieren einer Wissensdatenbank

Sie können eine Wissensdatenbank aktualisieren, indem Sie [update](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Update) aufrufen und die Wissensdatenbank-ID sowie ein [UpdateKbOperationDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTO)-Objekt übergeben. Dieses Objekt kann wiederum Folgendes enthalten:
- [add](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTOAdd)
- [update](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTOUpdate)
- [delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTODelete)

Übergeben Sie die Eigenschaft `operationId` des zurückgegebenen Vorgangs an die [getDetails](#get-status-of-an-operation)-Methode, um den Status abzurufen.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="update_kb":::

## <a name="download-a-knowledge-base"></a>Herunterladen einer Knowledge Base

Verwenden Sie die [download](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Download)-Methode, um die Datenbank als Liste von [QnADocumentsDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#QnADocumentsDTO) herunterzuladen. Dies entspricht _nicht_ dem Export des QnA Maker-Portals von der Seite **Einstellungen**, da das Ergebnis dieser Methode keine TSV-Datei ist.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="download_kb":::

## <a name="publish-a-knowledge-base"></a>Veröffentlichen einer Wissensdatenbank

Veröffentlichen Sie die Wissensdatenbank mit der [publish](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Publish)-Methode. Diese übernimmt das aktuell gespeicherte und trainierte Modell, auf das von der Knowledge Base-ID verwiesen wird, und veröffentlicht dieses an einem Endpunkt.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="publish_kb":::

## <a name="query-the-knowledge-base"></a>Abfragen der Wissensdatenbank

Nach dem Veröffentlichen einer Wissensdatenbank benötigen Sie den Runtimeendpunktschlüssel, um die Wissensdatenbank abzufragen. Dieser ist nicht mit dem Abonnementschlüssel identisch, der zum Erstellen des Erstellungsclients verwendet wurde.

Erstellen Sie ein [EndpointKeysClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#EndpointKeysClient)-Element, und verwenden Sie die Methode [getKeys](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#EndpointKeysClient.GetKeys), um das Objekt [EndpointKeysDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#EndpointKeysDTO) abzurufen.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="get_runtime_endpoint_key":::

Erstellen Sie ein [RuntimeClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime#RuntimeClient)-Element, und rufen Sie die Methode [GenerateAnswer](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime#RuntimeClient.GenerateAnswer) auf, um die Wissensdatenbank abzufragen.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="query_kb":::

Dies ist ein einfaches Beispiel zum Abfragen einer Wissensdatenbank. Informationen zu erweiterten Abfrageszenarien finden Sie in [weiteren Abfragebeispielen](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Löschen einer Wissensdatenbank

Löschen Sie die Wissensdatenbank mit der [delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Delete)-Methode mit einem Parameter der Wissensdatenbank-ID.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="delete_kb":::

## <a name="get-status-of-an-operation"></a>Abrufen des Status eines Vorgangs

Einige Methoden, wie „create“ und „update“, können so viel Zeit in Anspruch nehmen, dass ein [Vorgang](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#Operation) zurückgegeben wird, anstatt auf den Abschluss des Prozesses zu warten. Verwenden Sie die Vorgangs-ID des Vorgangs, um den Status der ursprünglichen Methode per Abruf (mit Wiederholungslogik) zu bestimmen.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="wait_for_operation":::

## <a name="handle-errors"></a>Behandeln von Fehlern

Der folgende Code veranschaulicht, wie Fehler behandelt werden, die von den Funktionen des SDK unter Umständen zurückgegeben werden:

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="error_handling":::

## <a name="run-the-application"></a>Ausführen der Anwendung

Hier sehen Sie die Main-Methode für die Anwendung:

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="main":::

Führen Sie die Anwendung mit dem Go-Befehl für die Schnellstartdatei aus.

```console
go run kb_sample.go
```

Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/QnAMaker/sdk/kb_sample.go).
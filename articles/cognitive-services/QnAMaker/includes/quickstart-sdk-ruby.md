---
title: 'Schnellstart: QnA Maker-Clientbibliothek für Ruby'
description: Diese Schnellstartanleitung zeigt Ihnen die ersten Schritte mit der QnA Maker-Clientbibliothek für Ruby.
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: edb881b10db1d617e1b1f6b1167c014d6d51020e
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351017"
---
Verwenden Sie die QnA Maker-Clientbibliothek für Ruby für Folgendes:

* Erstellen einer Wissensdatenbank
* Aktualisieren einer Wissensdatenbank
* Veröffentlichen einer Wissensdatenbank
* Abrufen von Endpunktschlüsseln der Vorhersage-Runtime
* Warten auf Aufgaben mit langer Ausführungsdauer
* Herunterladen einer Wissensdatenbank
* Abrufen der Antwort
* Löschen einer Wissensdatenbank

[Quellcode der Bibliothek (Erstellung)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated) | [Quellcode der Bibliothek (Runtime)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated) | [Paket (Erstellung)](https://rubygems.org/gems/azure_cognitiveservices_qnamaker) | [Paket (Runtime)](https://rubygems.org/gems/azure_cognitiveservices_qnamakerruntime) | [Ruby-Beispiele](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/ruby/qnamaker)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* [Ruby 2.x](https://www.ruby-lang.org/)
* Sobald Sie über Ihr Azure-Abonnement verfügen, erstellen Sie im Azure-Portal eine [QnA Maker-Ressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), um Ihren Erstellungsschlüssel und Endpunkt zu erhalten. Wählen Sie nach Abschluss der Bereitstellung **Zu Ressource wechseln** aus.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der QnA Maker-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

### <a name="install-the-client-libraries"></a>Installieren der Clientbibliotheken

Nach der Installation von Ruby können Sie die Clientbibliotheken mit Folgendem installieren:

```console
gem install azure_cognitiveservices_qnamaker
gem install azure_cognitiveservices_qnamakerruntime
```

### <a name="create-a-new-ruby-application"></a>Erstellen einer neuen Ruby-Anwendung

Erstellen Sie eine neue Datei namens `quickstart.rb`, und importieren Sie die folgenden Bibliotheken:

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Dependencies":::

Erstellen Sie Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource.

> [!IMPORTANT]
> Navigieren Sie zum Azure-Portal, und suchen Sie den Schlüssel und den Endpunkt für die unter „Voraussetzungen“ erstellte QnA Maker-Ressource. Sie befinden sich auf der Seite mit dem **Schlüssel und Endpunkt** der Ressource unter **Ressourcenverwaltung**.
> Sie benötigen den gesamten Schlüssel, um Ihre Wissensdatenbank zu erstellen. Sie benötigen nur den Ressourcennamen vom Endpunkt. Das Format ist `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Beispielsweise bietet der [Azure-Schlüsseltresor](../../../key-vault/general/overview.md) sichere Aufbewahrung von Schlüsseln.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Resourcevariables":::

## <a name="object-models"></a>Objektmodelle

QnA Maker verwendet zwei unterschiedliche Objektmodelle:
* **[QnAMakerClient](#qnamakerclient-object-model)** ist das Objekt zum Erstellen, Verwalten, Veröffentlichen und Herunterladen der Wissensdatenbank.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** ist das Objekt, das für Abfragen der Wissensdatenbank mit der GenerateAnswer-API und zum Senden neuer vorgeschlagener Fragen mithilfe der Train-API (im Rahmen des [aktiven Lernens](../concepts/active-learning-suggestions.md)) verwendet wird.

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient-Objektmodell

Der erstellende QnA Maker-Client ist ein [QnAMakerClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/qnamaker_client.rb)-Objekt, das mithilfe der Klasse „MsRest::ServiceClientCredentials“, die Ihren Schlüssel enthält, bei Azure authentifiziert wird.

Nachdem der Client erstellt wurde, verwenden Sie die Methoden der [knowledgebase](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb)-Eigenschaft des Clients, um Ihre Wissensdatenbank zu erstellen, zu verwalten und zu veröffentlichen.

Bei sofortigen Vorgängen gibt eine Methode in der Regel das Ergebnis zurück, falls vorhanden. Bei zeitintensiven Vorgängen ist die Antwort ein Objekt vom Typ [operation](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation.rb). Rufen Sie die [operations.get_details](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/operations.rb#L33)-Methode mit dem Wert `operation.operation_id` auf, um den [Status der Anforderung](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation_state_type.rb) zu bestimmen.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient-Objektmodell

Der QnA Maker-Runtimeclient ist ein [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/qnamaker_runtime_client.rb)-Objekt.

Verwenden Sie nach dem Veröffentlichen der Wissensdatenbank mithilfe des Erstellungsclients die Methode [runtime.generate_answer](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/runtime.rb#L34) des Runtimeclients, um eine Antwort aus der Wissensdatenbank abzurufen.

Wenn Sie `generate_answer` aufrufen, übergeben Sie einen Hash für den optionalen Parameter `custom_headers`. Dieser Hash sollte den Schlüssel `Authorization` und den Wert `EndpointKey YOUR_ENDPOINT_KEY` enthalten. Verwenden Sie für den Wert YOUR_ENDPOINT_KEY den Erstellungsclient, um [endpoint_keys.get_keys](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/endpoint_keys.rb#L32) aufzurufen.

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Authentifizieren des Clients zum Erstellen der Wissensdatenbank

Instanziieren Sie einen Client mit dem Erstellungsendpunkt und dem Abonnementschlüssel.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="AuthorizationAuthor":::

## <a name="create-a-knowledge-base"></a>Erstellen einer Wissensdatenbank

Eine Wissensdatenbank speichert Frage- und Antwortpaare für das [CreateKbDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/create_kb_dto.rb)-Objekt aus drei Quellen:

* Für **redaktionellen Inhalt** verwenden Sie das [QnADTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/qn_adto.rb)-Objekt.
    * Verwenden Sie für Metadaten und Folgeaufforderungen den redaktionellen Kontext, da diese Daten auf der Ebene der einzelnen QnA-Paare hinzugefügt werden.
* Für **Dateien** verwenden Sie das [FileDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/file_dto.rb)-Objekt. Dieses Objekt enthält den Dateinamen sowie die öffentliche URL für den Zugriff auf die Datei.
* Verwenden Sie für **URLs** eine Liste von Zeichenfolgen zur Darstellung von öffentlich verfügbaren URLs.

Rufen Sie die [create](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L554)-Methode auf, und übergeben Sie dann die Eigenschaft `operation_id`des zurückgegebenen Vorgangs an die [operations.get_details](#get-status-of-an-operation)-Methode, um den Status abzurufen.

In der letzten Zeile des folgenden Codes wird die Wissensdatenbank-ID zurückgegeben:

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="CreateKBMethod":::

## <a name="update-a-knowledge-base"></a>Aktualisieren einer Wissensdatenbank

Sie können eine Wissensdatenbank aktualisieren, indem Sie [knowledgebase.update](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L455) aufrufen und die Wissensdatenbank-ID sowie ein [UpdateKbOperationDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dto.rb)-Objekt übergeben. Dieses Objekt kann wiederum Folgendes enthalten:
- [add](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtoadd.rb)
- [update](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtoupdate.rb)
- [delete](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtodelete.rb)

Übergeben Sie die Eigenschaft `operation_id` des zurückgegebenen Vorgangs an die [operations.get_details](#get-status-of-an-operation)-Methode, um den Status abzurufen.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="UpdateKBMethod":::

## <a name="download-a-knowledge-base"></a>Herunterladen einer Knowledge Base

Verwenden Sie die [knowledgebase.download](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L651)-Methode, um die Datenbank als Liste von [QnADocumentsDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/qn_adocuments_dto.rb) herunterzuladen. Dies entspricht _nicht_ dem Export des QnA Maker-Portals von der Seite **Einstellungen**, da das Ergebnis dieser Methode keine TSV-Datei ist.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="DownloadKB":::

## <a name="publish-a-knowledge-base"></a>Veröffentlichen einer Wissensdatenbank

Veröffentlichen Sie die Wissensdatenbank mit der [knowledgebase.publish](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L283)-Methode. Diese übernimmt das aktuell gespeicherte und trainierte Modell, auf das von der Knowledge Base-ID verwiesen wird, und veröffentlicht dieses an einem Endpunkt.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="PublishKB":::

## <a name="get-query-runtime-key"></a>Abrufen des Abfrage-Runtimeschlüssels

Nach dem Veröffentlichen einer Wissensdatenbank benötigen Sie den Runtimeendpunktschlüssel, um die Wissensdatenbank abzufragen. Dieser ist nicht mit dem Abonnementschlüssel identisch, der zum Erstellen des Erstellungsclients verwendet wurde.

Verwenden Sie die Methode [endpoint_keys.get_keys](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/endpoint_keys.rb#L32), um das Objekt [EndpointKeysDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/endpoint_keys_dto.rb) abzurufen.

Verwenden Sie eine der im Objekt zurückgegeben Schlüsseleigenschaften, um die Wissensdatenbank abzufragen.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="GetQueryEndpointKey":::

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Authentifizieren der Runtime zum Erstellen einer Antwort

Erstellen Sie ein [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/qnamaker_runtime_client.rb)-Element, um die Wissensdatenbank abzufragen und eine Antwort zu generieren oder mit aktivem Lernen zu trainieren.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="AuthorizationQuery":::

Verwenden Sie den QnAMakerRuntimeClient, um eine Antwort aus der Wissensdatenbank abzurufen oder neue Fragenvorschläge für [aktives Lernen](../concepts/active-learning-suggestions.md) an sie zu senden.

## <a name="generate-an-answer-from-the-knowledge-base"></a>Generieren einer Antwort auf der Grundlage der Wissensdatenbank

Generieren Sie mithilfe der Methode „RuntimeClient.runtime.generateAnswer“ eine Antwort aus einer veröffentlichten Wissensdatenbank. Diese Methode akzeptiert die Wissensdatenbank-ID und die QueryDTO-Klasse. Greifen Sie auf weitere QueryDTO-Eigenschaften zu (etwa „Top“ und „Context“), und verwenden Sie sie in Ihrem Chatbot.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="GenerateAnswer":::

Dies ist ein einfaches Beispiel zum Abfragen einer Wissensdatenbank. Informationen zu erweiterten Abfrageszenarien finden Sie in [weiteren Abfragebeispielen](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Löschen einer Wissensdatenbank

Löschen Sie die Wissensdatenbank mit der [knowledgebase.delete](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L205)-Methode mit einem Parameter der Wissensdatenbank-ID.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="DeleteKB":::

## <a name="get-status-of-an-operation"></a>Abrufen des Status eines Vorgangs

Einige Methoden, wie „create“ und „update“, können so viel Zeit in Anspruch nehmen, dass ein [Vorgang](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation.rb) zurückgegeben wird, anstatt auf den Abschluss des Prozesses zu warten. Verwenden Sie die Vorgangs-ID des Vorgangs, um den Status der ursprünglichen Methode per Abruf (mit Wiederholungslogik) zu bestimmen.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="MonitorOperation":::

## <a name="run-the-application"></a>Ausführen der Anwendung

Hier sehen Sie die Main-Methode für die Anwendung:

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Main":::

Führen Sie die Anwendung mit dem Ruby-Befehl für die Schnellstartdatei aus.

```console
ruby quickstart.rb
```

Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/ruby/qnamaker/sdk/quickstart.rb).
---
title: 'Schnellstart: QnA Maker-Clientbibliothek für Java'
description: Diese Schnellstartanleitung zeigt Ihnen die ersten Schritte mit der QnA Maker-Clientbibliothek für Java.
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: c9d7ab86230cd531df316892b0d809fe264199e0
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351012"
---
Verwenden Sie die QnA Maker-Clientbibliothek für Java für Folgendes:

* Erstellen einer Wissensdatenbank
* Aktualisieren einer Wissensdatenbank
* Veröffentlichen einer Wissensdatenbank
* Abrufen von Endpunktschlüsseln der Vorhersage-Runtime
* Warten auf Aufgaben mit langer Ausführungsdauer
* Herunterladen einer Wissensdatenbank
* Abrufen der Antwort
* Löschen einer Wissensdatenbank

[Quellcode der Bibliothek (Erstellung)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker) | [Paket](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker) | [Beispiele](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/qnamaker)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* [JDK](https://www.oracle.com/java/technologies/javase-downloads.html)
* Sobald Sie über Ihr Azure-Abonnement verfügen, erstellen Sie im Azure-Portal eine [QnA Maker-Ressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), um Ihren Erstellungsschlüssel und Endpunkt zu erhalten. Wählen Sie nach Abschluss der Bereitstellung **Zu Ressource wechseln** aus.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der QnA Maker-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

### <a name="install-the-client-libraries"></a>Installieren der Clientbibliotheken

Nach der Installation von Java können Sie die Clientbibliotheken mithilfe von [Maven](https://maven.apache.org/) aus dem [MVN-Repository](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker) installieren.

### <a name="create-a-new-java-application"></a>Erstellen einer neuen Java-Anwendung

Erstellen Sie eine neue Datei namens `quickstart.java`, und importieren Sie die folgenden Bibliotheken:

:::code language="java" source="~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java" id="dependencies":::

Erstellen Sie Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource.

> [!IMPORTANT]
> Navigieren Sie zum Azure-Portal, und suchen Sie den Schlüssel und den Endpunkt für die unter „Voraussetzungen“ erstellte QnA Maker-Ressource. Sie befinden sich auf der Seite mit dem **Schlüssel und Endpunkt** der Ressource unter **Ressourcenverwaltung**.
> Sie benötigen den gesamten Schlüssel, um Ihre Wissensdatenbank zu erstellen. Sie benötigen nur den Ressourcennamen vom Endpunkt. Das Format ist `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Beispielsweise bietet der [Azure-Schlüsseltresor](../../../key-vault/general/overview.md) sichere Aufbewahrung von Schlüsseln.

:::code language="java" source="~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java" id="resourceKeys":::

## <a name="object-models"></a>Objektmodelle

QnA Maker verwendet zwei unterschiedliche Objektmodelle:
* **[QnAMakerClient](#qnamakerclient-object-model)** ist das Objekt zum Erstellen, Verwalten, Veröffentlichen und Herunterladen der Wissensdatenbank.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** ist das Objekt, das für Abfragen der Wissensdatenbank mit der GenerateAnswer-API und zum Senden neuer vorgeschlagener Fragen mithilfe der Train-API (im Rahmen des [aktiven Lernens](../concepts/active-learning-suggestions.md)) verwendet wird.

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient-Objektmodell

Der erstellende QnA Maker-Client ist ein [QnAMakerClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java)-Objekt, das mithilfe der Klasse „MsRest::ServiceClientCredentials“, die Ihren Schlüssel enthält, bei Azure authentifiziert wird.

Nachdem der Client erstellt wurde, verwenden Sie die Methoden der [Knowledgebases](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java)-Eigenschaft des Clients, um Ihre Wissensdatenbank zu erstellen, zu verwalten und zu veröffentlichen.

Bei sofortigen Vorgängen gibt eine Methode in der Regel das Ergebnis zurück, falls vorhanden. Bei zeitintensiven Vorgängen ist die Antwort ein Objekt vom Typ [Operation](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/Operation.java). Rufen Sie die [getDetails](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Operations.java#L32)-Methode mit dem Wert `operation.operationId` auf, um den [Status der Anforderung](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/OperationStateType.java) zu bestimmen.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient-Objektmodell

Der QnA Maker-Runtimeclient ist ein [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java)-Objekt.

Verwenden Sie nach dem Veröffentlichen der Wissensdatenbank mithilfe des Erstellungsclients die Methode [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Runtimes.java#L36) des Runtimeclients, um eine Antwort aus der Wissensdatenbank abzurufen.

Sie erstellen einen Runtimeclient durch Aufrufen von [QnAMakerRuntimeManager.authenticate](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerRuntimeManager.java#L29) und Übergeben eines Runtimeendpunktschlüssels. Verwenden Sie zum Abrufen des Runtimeendpunktschlüssels den Erstellungsclient, um [getKeys](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/EndpointKeys.java#L30) aufzurufen.

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Authentifizieren des Clients zum Erstellen der Wissensdatenbank

Instanziieren Sie einen Client mit dem Erstellungsendpunkt und dem Abonnementschlüssel.

:::code language="java" source="~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java" id="authenticate":::

## <a name="create-a-knowledge-base"></a>Erstellen einer Wissensdatenbank

Eine Wissensdatenbank speichert Frage- und Antwortpaare für das [CreateKbDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/CreateKbDTO.java)-Objekt aus drei Quellen:

* Für **redaktionellen Inhalt** verwenden Sie das [QnADTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QnADTO.java)-Objekt.
    * Verwenden Sie für Metadaten und Folgeaufforderungen den redaktionellen Kontext, da diese Daten auf der Ebene der einzelnen QnA-Paare hinzugefügt werden.
* Für **Dateien** verwenden Sie das [FileDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/FileDTO.java)-Objekt. Dieses Objekt enthält den Dateinamen sowie die öffentliche URL für den Zugriff auf die Datei.
* Verwenden Sie für **URLs** eine Liste von Zeichenfolgen zur Darstellung von öffentlich verfügbaren URLs.

Rufen Sie die [create](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L173)-Methode auf, und übergeben Sie dann die Eigenschaft `operationId`des zurückgegebenen Vorgangs an die [getDetails](#get-status-of-an-operation)-Methode, um den Status abzurufen.

In der letzten Zeile des folgenden Codes wird die Wissensdatenbank-ID zurückgegeben:

:::code language="java" source="~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java" id="createKb":::

## <a name="update-a-knowledge-base"></a>Aktualisieren einer Wissensdatenbank

Sie können eine Wissensdatenbank aktualisieren, indem Sie [update](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L150) aufrufen und die Wissensdatenbank-ID sowie ein [UpdateKbOperationDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTO.java)-Objekt übergeben. Dieses Objekt kann wiederum Folgendes enthalten:
- [add](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTOAdd.java)
- [update](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTOUpdate.java)
- [delete](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTODelete.java)

Übergeben Sie die Eigenschaft `operationId` des zurückgegebenen Vorgangs an die [getDetails](#get-status-of-an-operation)-Methode, um den Status abzurufen.

:::code language="java" source="~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java" id="updateKb":::

## <a name="download-a-knowledge-base"></a>Herunterladen einer Knowledge Base

Verwenden Sie die [download](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L196)-Methode, um die Datenbank als Liste von [QnADocumentsDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QnADocumentsDTO.java) herunterzuladen. Dies entspricht _nicht_ dem Export des QnA Maker-Portals von der Seite **Einstellungen**, da das Ergebnis dieser Methode keine TSV-Datei ist.

:::code language="java" source="~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java" id="downloadKb":::

## <a name="publish-a-knowledge-base"></a>Veröffentlichen einer Wissensdatenbank

Veröffentlichen Sie die Wissensdatenbank mit der [publish](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L196)-Methode. Diese übernimmt das aktuell gespeicherte und trainierte Modell, auf das von der Knowledge Base-ID verwiesen wird, und veröffentlicht dieses an einem Endpunkt.

:::code language="java" source="~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java" id="publishKb":::

## <a name="generate-an-answer-from-the-knowledge-base"></a>Generieren einer Antwort auf der Grundlage der Wissensdatenbank

Nach dem Veröffentlichen einer Wissensdatenbank benötigen Sie den Runtimeendpunktschlüssel, um die Wissensdatenbank abzufragen. Dieser ist nicht mit dem Abonnementschlüssel identisch, der zum Erstellen des Erstellungsclients verwendet wurde.

Verwenden Sie die Methode [getKeys](https://github.com/Azure/azure-sdk-for-java/blob/b637366e32edefb0fe63962983715a02c1ad2631/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/EndpointKeys.java#L30), um das Objekt [EndpointKeysDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/EndpointKeysDTO.java) abzurufen.

Erstellen Sie einen Runtimeclient durch Aufrufen von [QnAMakerRuntimeManager.authenticate](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerRuntimeManager.java#L29) und Übergeben eines Runtimeendpunktschlüssels aus dem EndpointKeysDTO-Objekt.

Generieren Sie mithilfe der Methode [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Runtimes.java#L36) eine Antwort aus einer veröffentlichten Wissensdatenbank. Diese Methode akzeptiert die Wissensdatenbank-ID und das [QueryDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QueryDTO.java)-Objekt.

:::code language="java" source="~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java" id="queryKb":::

Dies ist ein einfaches Beispiel zum Abfragen einer Wissensdatenbank. Informationen zu erweiterten Abfrageszenarien finden Sie in [weiteren Abfragebeispielen](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Löschen einer Wissensdatenbank

Löschen Sie die Wissensdatenbank mit der [delete](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L81)-Methode mit einem Parameter der Wissensdatenbank-ID.

:::code language="java" source="~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java" id="deleteKb":::

## <a name="get-status-of-an-operation"></a>Abrufen des Status eines Vorgangs

Einige Methoden, wie „create“ und „update“, können so viel Zeit in Anspruch nehmen, dass ein [Vorgang](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/Operation.java) zurückgegeben wird, anstatt auf den Abschluss des Prozesses zu warten. Verwenden Sie die Vorgangs-ID des Vorgangs, um den Status der ursprünglichen Methode per Abruf (mit Wiederholungslogik) zu bestimmen.

:::code language="java" source="~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java" id="waitForOperation":::

## <a name="run-the-application"></a>Ausführen der Anwendung

Hier sehen Sie die Main-Methode für die Anwendung:

:::code language="java" source="~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java" id="main":::

Führen Sie die Anwendung wie folgt aus: Dies setzt voraus, dass der Klassenname `Quickstart` lautet und sich die Abhängigkeiten in einem Unterordner mit dem Namen `lib` unterhalb des aktuellen Ordners befinden.

```console
javac Quickstart.java -cp .;lib\*
java -cp .;lib\* Quickstart
```

Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/qnamaker/sdk/quickstart.java).
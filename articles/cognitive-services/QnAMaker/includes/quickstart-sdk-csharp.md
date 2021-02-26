---
title: 'Schnellstart: QnA Maker-Clientbibliothek für .NET'
description: Dieser Schnellstart zeigt Ihnen die ersten Schritte mit der QnA Maker-Clientbibliothek für .NET. Führen Sie die nachfolgenden Schritte zum Installieren des Pakets aus, und testen Sie den Beispielcode für grundlegende Aufgaben.  Mit QnA Maker können Sie einen Frage-und-Antwort-Dienst auf der Grundlage Ihrer teilweise strukturierten Inhalte bereitstellen. Bei solchen Inhalten kann es sich beispielsweise um FAQ-Dokumente, URLs und Produkthandbücher handeln.
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: 18d8dbc59d1c43961cd665a3ea98d1041516afb7
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99616609"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/version-1)

Verwenden Sie die QnA Maker-Clientbibliothek für .NET für Folgendes:

 * Erstellen einer Wissensdatenbank
 * Aktualisieren einer Wissensdatenbank
 * Veröffentlichen einer Wissensdatenbank
 * Abrufen von Endpunktschlüsseln der Vorhersage-Runtime
 * Warten auf Aufgaben mit langer Ausführungsdauer
 * Herunterladen einer Wissensdatenbank
 * Abrufen einer Antwort aus einer Wissensdatenbank
 * Löschen einer Wissensdatenbank

[Referenzdokumentation](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/2.0.1) | [C#-Beispiele](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/version-2)

Verwenden Sie die QnA Maker-Clientbibliothek für .NET für Folgendes:

 * Erstellen einer Wissensdatenbank
 * Aktualisieren einer Wissensdatenbank
 * Veröffentlichen einer Wissensdatenbank
 * Warten auf Aufgaben mit langer Ausführungsdauer
 * Herunterladen einer Wissensdatenbank
 * Abrufen einer Antwort aus einer Wissensdatenbank
 * Löschen einer Wissensdatenbank

[Referenzdokumentation](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/3.0.0-preview.1) | [C#-Beispiele](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/version-1)

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Die [Visual Studio-IDE](https://visualstudio.microsoft.com/vs/) oder die aktuelle Version von [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Sobald Sie über Ihr Azure-Abonnement verfügen, erstellen Sie im Azure-Portal eine [QnA Maker-Ressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), um Ihren Erstellungsschlüssel und Ressourcennamen zu erhalten. Wählen Sie nach Abschluss der Bereitstellung **Zu Ressource wechseln** aus.
    * Sie benötigen den Schlüssel und Ressourcennamen aus der von Ihnen erstellten Ressource, um Ihre Anwendung mit der QnA Maker-API zu verbinden. Ihren Schlüssel und Ressourcennamen werden Sie weiter unten in der Schnellstartanleitung in den Code einfügen.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/version-2)

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Die [Visual Studio-IDE](https://visualstudio.microsoft.com/vs/) oder die aktuelle Version von [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Sobald Sie über Ihr Azure-Abonnement verfügen, erstellen Sie im Azure-Portal eine [QnA Maker-Ressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), um Ihren Erstellungsschlüssel und Endpunkt zu erhalten.
    * HINWEIS: Das Kontrollkästchen **Verwaltet** muss aktiviert sein.
    * Wählen Sie nach der Bereitstellung der QnA Maker-Ressource **Zu Ressource wechseln** aus. Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der QnA Maker-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

---

## <a name="setting-up"></a>Einrichten

### <a name="visual-studio-ide"></a>Visual Studio-IDE

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/version-1)

Erstellen Sie mithilfe von Visual Studio eine .NET Core-Anwendung, und installieren Sie die Clientbibliothek, indem Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappe klicken und **NuGet-Pakete verwalten** auswählen. Wählen Sie im daraufhin geöffneten Paket-Manager die Option **Durchsuchen** aus, und suchen Sie nach `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker`. Wählen Sie die Version `2.0.1` und anschließend **Installieren** aus.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/version-2)

Erstellen Sie mithilfe von Visual Studio eine .NET Core-Anwendung, und installieren Sie die Clientbibliothek, indem Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappe klicken und **NuGet-Pakete verwalten** auswählen. Wählen Sie im daraufhin geöffneten Paket-Manager die Option **Durchsuchen** aus, aktivieren Sie das Kontrollkästchen **Vorabversion einbeziehen**, und suchen Sie nach `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker`. Wählen Sie die Version `3.0.0-preview.1` und anschließend **Installieren** aus.

---

### <a name="cli"></a>Befehlszeilenschnittstelle (CLI)

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `qna-maker-quickstart`. Dieser Befehl erstellt ein einfaches C#-Projekt vom Typ „Hallo Welt“ mit einer einzelnen Quelldatei: *program.cs*.

```console
dotnet new console -n qna-maker-quickstart
```

Wechseln Sie zum Ordner der neu erstellten App. Sie können die Anwendung mit folgendem Befehl erstellen:

```console
dotnet build
```

Die Buildausgabe sollte keine Warnungen oder Fehler enthalten.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Installieren Sie im Anwendungsverzeichnis mit dem folgenden Befehl die QnA Maker-Clientbibliothek für .NET:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/version-1)

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 2.0.1
```

# <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/version-2)

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 3.0.0-preview.1
```

---

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/version-1)

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/version-2)

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.

---

### <a name="using-directives"></a>using-Direktiven

Öffnen Sie über das Projektverzeichnis die Datei *program.cs*, und fügen Sie die folgenden `using`-Direktiven hinzu:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/version-1)

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/version-2)

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Dependencies)]

---

### <a name="subscription-key-and-resource-endpoints"></a>Abonnementschlüssel und Ressourcenendpunkte

Fügen Sie in der `Main`-Methode der Anwendung Variablen und Code hinzu, wie im folgenden Abschnitt gezeigt wird, um mit den allgemeinen Aufgaben in dieser Schnellstartanleitung arbeiten zu können.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/version-1)

> [!IMPORTANT]
> Navigieren Sie zum Azure-Portal, und suchen Sie den Schlüssel und den Endpunkt für die unter „Voraussetzungen“ erstellte QnA Maker-Ressource. Sie befinden sich auf der Seite mit dem **Schlüssel und Endpunkt** der Ressource unter **Ressourcenverwaltung**. 

Die Begriffe „Abonnementschlüssel“ und Erstellungsschlüssel“ werden synonym verwendet. Ausführlichere Informationen zum Erstellungsschlüssel finden Sie unter [Schlüssel in QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/azure-resources?tabs=v1#keys-in-qna-maker).

- Erstellen Sie die Umgebungsvariablen „QNA_MAKER_SUBSCRIPTION_KEY“, „QNA_MAKER_ENDPOINT“ und „QNA_MAKER_RUNTIME_ENDPOINT“, um diese Werte zu speichern.
- Der Wert von „QNA_MAKER_ENDPOINT“ hat das Format `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. 
- Der Wert von „QNA_MAKER_RUNTIME_ENDPOINT“ hat das Format `https://YOUR-RESOURCE-NAME.azurewebsites.net`.
- In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Beispielsweise bietet der [Azure-Schlüsseltresor](../../../key-vault/general/overview.md) sichere Aufbewahrung von Schlüsseln.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/version-2)

> [!IMPORTANT]
> Navigieren Sie zum Azure-Portal, und suchen Sie den Schlüssel und den Endpunkt für die unter „Voraussetzungen“ erstellte QnA Maker-Ressource. Sie befinden sich auf der Seite mit dem **Schlüssel und Endpunkt** der Ressource unter **Ressourcenverwaltung**. 

Die Begriffe „Abonnementschlüssel“ und Erstellungsschlüssel“ werden synonym verwendet. Ausführlichere Informationen zum Erstellungsschlüssel finden Sie unter [Schlüssel in QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/azure-resources?tabs=v2#keys-in-qna-maker).

- Erstellen Sie die Umgebungsvariablen „QNA_MAKER_SUBSCRIPTION_KEY“ und „QNA_MAKER_ENDPOINT“, um diese Werte zu speichern.
- Der Wert von „QNA_MAKER_ENDPOINT“ hat das Format `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. 
- In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Beispielsweise bietet der [Azure-Schlüsseltresor](../../../key-vault/general/overview.md) sichere Aufbewahrung von Schlüsseln.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Resourcevariables)]

---


## <a name="object-models"></a>Objektmodelle

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/version-1)

Von [QnA Maker](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) werden zwei unterschiedliche Objektmodelle verwendet:
* **[QnAMakerClient](#qnamakerclient-object-model)** ist das Objekt zum Erstellen, Verwalten, Veröffentlichen und Herunterladen der Wissensdatenbank.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** ist das Objekt, das für Abfragen der Wissensdatenbank mit der GenerateAnswer-API und zum Senden neuer vorgeschlagener Fragen mithilfe der Train-API (im Rahmen des [aktiven Lernens](../concepts/active-learning-suggestions.md)) verwendet wird.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/version-2)

Von [QnA Maker](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) wird das folgende Objektmodell verwendet:
* **[QnAMakerClient](#qnamakerclient-object-model)** ist das Objekt zum Erstellen, Verwalten, Veröffentlichen, Herunterladen und Abfragen der Wissensdatenbank.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient-Objektmodell

Der erstellende QnA Maker-Client ist ein [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient)-Objekt, das mithilfe der „Microsoft.Rest.ServiceClientCredentials“-Klasse, die Ihren Schlüssel enthält, bei Azure authentifiziert wird.

Nachdem der Client erstellt wurde, verwenden Sie die [Knowledge Base](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase)-Eigenschaft, um Ihre Knowledge Base zu erstellen, zu verwalten und zu veröffentlichen.

Verwalten Sie Ihre Knowledge Base durch Senden eines JSON-Objekts. Bei sofortigen Vorgängen gibt eine Methode in der Regel ein JSON-Objekt zurück, das den Status angibt. Bei zeitintensiven Vorgängen ist die Antwort die Vorgangs-ID. Rufen Sie die [client.Operations.GetDetailsAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync)-Methode mit der Vorgangs-ID auf, um [den Status der Anforderung](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype) zu bestimmen.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient-Objektmodell

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/version-1)

Der QnA Maker-Vorhersageclient ist ein [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient)-Objekt, das sich bei Azure mithilfe von Microsoft.Rest.ServiceClientCredentials authentifiziert, die Ihren Schlüssel für die Vorhersage-Runtime enthalten, der nach dem Veröffentlichen der Wissensdatenbank vom Aufruf `client.EndpointKeys.GetKeys` des erstellenden Clients zurückgegeben wurde.

Mithilfe der Methode [GenerateAnswer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions) können Sie eine Antwort aus der Abfragelaufzeit abrufen.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/version-2)

Für eine von QnA Maker verwaltete Ressource muss das Objekt **QnAMakerRuntimeClient** nicht verwendet werden. Stattdessen können Sie die Methode [QnAMakerClient.Knowledgebase](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync) aufrufen.

---

## <a name="code-examples"></a>Codebeispiele

Mit den Codeausschnitten wird veranschaulicht, wie folgende Vorgänge mit der QnA Maker-Clientbibliothek für .NET durchgeführt werden:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/version-1)

* [Authentifizieren des Erstellungsclients](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Erstellen einer Wissensdatenbank](#create-a-knowledge-base)
* [Aktualisieren einer Wissensdatenbank](#update-a-knowledge-base)
* [Herunterladen einer Knowledge Base](#download-a-knowledge-base)
* [Veröffentlichen einer Wissensdatenbank](#publish-a-knowledge-base)
* [Löschen einer Wissensdatenbank](#delete-a-knowledge-base)
* [Abrufen des Abfrage-Runtimeschlüssels](#get-query-runtime-key)
* [Abrufen des Status eines Vorgangs](#get-status-of-an-operation)
* [Authentifizieren des Abfrage-Runtimeclients](#authenticate-the-runtime-for-generating-an-answer)
* [Generieren einer Antwort auf der Grundlage der Wissensdatenbank](#generate-an-answer-from-the-knowledge-base)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/version-2)

* [Authentifizieren des Erstellungsclients](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Erstellen einer Wissensdatenbank](#create-a-knowledge-base)
* [Aktualisieren einer Wissensdatenbank](#update-a-knowledge-base)
* [Herunterladen einer Knowledge Base](#download-a-knowledge-base)
* [Veröffentlichen einer Wissensdatenbank](#publish-a-knowledge-base)
* [Löschen einer Wissensdatenbank](#delete-a-knowledge-base)
* [Abrufen des Status eines Vorgangs](#get-status-of-an-operation)
* [Generieren einer Antwort auf der Grundlage der Wissensdatenbank](#generate-an-answer-from-the-knowledge-base)

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Authentifizieren des Clients zum Erstellen der Wissensdatenbank

Instanziieren Sie ein Clientobjekt mit Ihrem Schlüssel, und verwenden Sie es zusammen mit Ihrer Ressource, um den Endpunkt zum Erstellen eines [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient)-Objekts mit Ihrem Endpunkt und Schlüssel zu erstellen. Erstellen Sie ein [ServiceClientCredentials](/dotnet/api/microsoft.rest.serviceclientcredentials)-Objekt.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/version-1)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/version-2)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Erstellen einer Wissensdatenbank

Eine Wissensdatenbank speichert Frage- und Antwortpaare für das [CreateKbDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto)-Objekt aus drei Quellen:

* Für **redaktionellen Inhalt** verwenden Sie das [QnADTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto)-Objekt.
    * Verwenden Sie für Metadaten und Folgeaufforderungen den redaktionellen Kontext, da diese Daten auf der Ebene der einzelnen QnA-Paare hinzugefügt werden.
* Für **Dateien** verwenden Sie das [FileDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto)-Objekt. Dieses Objekt enthält den Dateinamen sowie die öffentliche URL für den Zugriff auf die Datei.
* Verwenden Sie für **URLs** eine Liste von Zeichenfolgen zur Darstellung von öffentlich verfügbaren URLs.

Der Erstellungsschritt umfasst auch Eigenschaften für die Wissensdatenbank mit Folgendem:
* `defaultAnswerUsedForExtraction`: die Rückgabe, wenn keine Antwort gefunden wird
* `enableHierarchicalExtraction`: automatisches Erstellen von Aufforderungsbeziehungen zwischen extrahierten QnA-Paaren
* `language`– bei der Erstellung der ersten Wissensdatenbank für eine Ressource Festlegen der Sprache, die im Azure-Suchindex verwendet werden soll

Rufen Sie die [CreateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync)-Methode auf, und übergeben Sie dann die zurückgegebene Vorgangs-ID an die [MonitorOperation](#get-status-of-an-operation)-Methode, um den Status abzurufen.

In der letzten Zeile des folgenden Codes wird die Knowledge Base-ID aus der Antwort von „MonitorOperation“ zurückgegeben.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/version-1)

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/version-2)

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=CreateKBMethod)]

---

Nehmen Sie zum erfolgreichen Erstellen einer Wissensdatenbank unbedingt die Funktion [`MonitorOperation`](#get-status-of-an-operation) mit auf, auf die im Code oben verwiesen wird.

## <a name="update-a-knowledge-base"></a>Aktualisieren einer Wissensdatenbank

Sie können eine Knowledge Base aktualisieren, indem Sie die Knowledge Base-ID und ein [UpdatekbOperationDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto), das [add](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd)-, [update](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate)- und [delete](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete)-DTO-Objekte enthält, als Eingabe an die [UpdateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync)-Methode übergeben. Verwenden Sie die [MonitorOperation](#get-status-of-an-operation) -Methode, um zu bestimmen, ob das Update erfolgreich war.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/version-1)

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/version-2)

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=UpdateKBMethod)]

---

Nehmen Sie zum erfolgreichen Aktualisieren einer Wissensdatenbank unbedingt die Funktion [`MonitorOperation`](#get-status-of-an-operation) mit auf, auf die im Code oben verwiesen wird.

## <a name="download-a-knowledge-base"></a>Herunterladen einer Knowledge Base

Verwenden Sie die [DownloadAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync)-Methode, um die Datenbank als Liste von [QnADocumentsDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto) herunterzuladen. Dies entspricht _nicht_ dem Export des QnA Maker-Portals von der Seite **Einstellungen**, weil das Ergebnis dieser Methode keine Datei ist.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/version-1)

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/version-2)

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Veröffentlichen einer Wissensdatenbank

Veröffentlichen Sie die Knowledge Base mithilfe der [PublishAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync)-Methode. Diese übernimmt das aktuell gespeicherte und trainierte Modell, auf das von der Knowledge Base-ID verwiesen wird, und veröffentlicht es an Ihrem Endpunkt. Dies ist ein erforderlicher Schritt zum Abfragen Ihrer Wissensdatenbank.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/version-1)

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/version-2)

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=PublishKB)]

---

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/version-1)

## <a name="get-query-runtime-key"></a>Abrufen des Abfrage-Runtimeschlüssels

Nach dem Veröffentlichen einer Wissensdatenbank benötigen Sie den Abfrage-Runtimeschlüssel, um die Runtime abzufragen. Dabei handelt es sich nicht um den Schlüssel, der zum Erstellen des ursprünglichen Clientobjekts verwendet wurde.

Verwenden Sie die Methode [EndpointKeys](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_), um die Klasse [EndpointKeysDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto) abzurufen.

Verwenden Sie eine der im Objekt zurückgegeben Schlüsseleigenschaften, um die Wissensdatenbank abzufragen.

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey)]

Ein Runtimeschlüssel ist zum Abfragen Ihrer Wissensdatenbank erforderlich.

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Authentifizieren der Runtime zum Erstellen einer Antwort

Erstellen Sie ein [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient)-Element, um die Wissensdatenbank abzufragen und eine Antwort zu generieren oder mit aktivem Lernen zu trainieren.

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

Verwenden Sie das „QnAMakerRuntimeClient“-Element für folgende Aktionen:
* Abrufen einer Antwort aus der Wissensdatenbank
* Senden von neuen Fragevorschlägen an die Wissensdatenbank für [aktives Lernen](../concepts/active-learning-suggestions.md)

## <a name="generate-an-answer-from-the-knowledge-base"></a>Generieren einer Antwort auf der Grundlage der Wissensdatenbank

Generieren Sie mithilfe der Methode [RuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync) eine Antwort aus einer veröffentlichten Wissensdatenbank. Diese Methode akzeptiert die Wissensdatenbank-ID und die [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto)-Klasse. Greifen Sie auf weitere QueryDTO-Eigenschaften zu (etwa [Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) und [Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context)), und verwenden Sie sie in Ihrem Chatbot.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/version-2)

## <a name="generate-an-answer-from-the-knowledge-base"></a>Generieren einer Antwort auf der Grundlage der Wissensdatenbank

Generieren Sie mithilfe der Methode [QnAMakerClient.Knowledgebase](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync) eine Antwort aus einer veröffentlichten Wissensdatenbank. Diese Methode akzeptiert die Wissensdatenbank-ID und die [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto)-Klasse. Greifen Sie auf weitere QueryDTO-Eigenschaften zu, etwa [Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top), [Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Context) und [AnswerSpanRequest](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.answerspanrequest#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_AnswerSpanRequest), um sie in Ihrem Chatbot zu verwenden.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=GenerateAnswer)]

---

Dies ist ein einfaches Beispiel zum Abfragen der Wissensdatenbank. Informationen zu erweiterten Abfrageszenarien finden Sie in [weiteren Abfragebeispielen](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Löschen einer Wissensdatenbank

Löschen Sie die Wissensdatenbank mithilfe der Methode [DeleteAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync) mit einem Parameter der Wissensdatenbank-ID.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/version-1)

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/version-2)

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>Abrufen des Status eines Vorgangs

Einige Methoden, wie „create“ und „update“, können so viel Zeit in Anspruch nehmen, dass ein [Vorgang](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation) zurückgegeben wird, anstatt auf den Abschluss des Prozesses zu warten. Verwenden Sie die [Vorgangs-ID](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) des Vorgangs, um den Status der ursprünglichen Methode mittels Abrufen (mit Wiederholungslogik) zu bestimmen.

_loop_ und _Task.Delay_ im folgenden Codeblock werden verwendet, um die Wiederholungslogik zu simulieren. Diese sollten durch Ihre eigene Wiederholungslogik ersetzt werden.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/version-1)

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/version-2)

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=MonitorOperation)]

---

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `dotnet run` aus dem Anwendungsverzeichnis aus.

```dotnetcli
dotnet run
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/version-1)

Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/version-2)

Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart).

---

---
title: 'Schnellstart: QnA Maker mit Rest-APIs für Node.js'
description: Dieser Schnellstart zeigt Ihnen die ersten Schritte mit den QnA Maker-REST-APIs für Node.js. Führen Sie die nachfolgenden Schritte zum Installieren des Pakets aus, und testen Sie den Beispielcode für grundlegende Aufgaben.  Mit QnA Maker können Sie einen Frage-und-Antwort-Dienst auf der Grundlage Ihrer teilweise strukturierten Inhalte bereitstellen. Bei solchen Inhalten kann es sich beispielsweise um FAQ-Dokumente, URLs und Produkthandbücher handeln.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-js
ms.topic: how-to
ms.openlocfilehash: 645f9ffb1d907eb576f5414b593ef57679d26daa
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351145"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>Schnellstart: QnA Maker-REST-APIs für Node.js

Steigen Sie in die QnA Maker-REST-APIs für Node.js ein. Führen Sie diese Schritte aus, um den Beispielcode für einfache Aufgaben zu testen.  Mit QnA Maker können Sie einen Frage-und-Antwort-Dienst auf der Grundlage Ihrer teilweise strukturierten Inhalte bereitstellen. Bei solchen Inhalten kann es sich beispielsweise um FAQ-Dokumente, URLs und Produkthandbücher handeln.

Verwenden Sie die QnA Maker-REST-APIs für Node.js für folgende Zwecke:

* Erstellen einer Wissensdatenbank
* Ersetzen einer Wissensdatenbank
* Veröffentlichen einer Wissensdatenbank
* Löschen einer Wissensdatenbank
* Herunterladen einer Knowledge Base
* Abrufen des Status eines Vorgangs

[Referenzdokumentation](/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Node.js-Beispiele](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Die aktuelle Version von [Node.js](https://nodejs.org).
* Sie benötigen einen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md). Wählen Sie für Ihre Ressource im Azure-Portal die Option **Schnellstart** aus, um den Schlüssel und den Endpunkt (der den Ressourcennamen enthält) abzurufen.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-qna-maker-azure-resource"></a>Erstellen einer QnA Maker-Azure-Ressource

Azure Cognitive Services werden von Azure-Ressourcen dargestellt, die Sie abonnieren. Erstellen Sie eine Ressource für QnA Maker mithilfe des [Azure-Portals](../../cognitive-services-apis-create-account.md) oder der [Azure CLI](../../cognitive-services-apis-create-account-cli.md) auf Ihrem lokalen Computer.

Führen Sie nach dem Abrufen eines Schlüssels aus Ihrer Ressource die [Erstellung von Umgebungsvariablen](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) mit den Namen `QNAMAKER_RESOURCE_KEY` und `QNAMAKER_AUTHORING_ENDPOINT` für die Ressource durch. Verwenden Sie die Schlüssel- und Endpunktwerte von der Seite **Schnellstart** der Ressource im Azure-Portal.

### <a name="create-a-new-nodejs-application"></a>Erstellen einer neuen Node.js-Anwendung

Erstellen Sie in einem Konsolenfenster (etwa cmd, PowerShell oder Bash) ein neues Verzeichnis für Ihre App, und rufen Sie es auf.

```console
mkdir myapp && cd myapp
```

Führen Sie den Befehl `npm init -y` aus, um die Knotendatei `package.json` zu erstellen.

```console
npm init -y
```

Fügen Sie die npm-Pakete `reqeuestretry` und `request` hinzu:

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>Codebeispiele

Mit diesen Codeausschnitten wird veranschaulicht, wie folgende Vorgänge mit den QnA Maker-REST-APIs für Node.js durchgeführt werden:

* [Erstellen einer Wissensdatenbank](#create-a-knowledge-base)
* [Ersetzen einer Wissensdatenbank](#replace-a-knowledge-base)
* [Veröffentlichen einer Wissensdatenbank](#publish-a-knowledge-base)
* [Löschen einer Wissensdatenbank](#delete-a-knowledge-base)
* [Herunterladen einer Knowledge Base](#download-the-knowledge-base)
* [Abrufen des Status eines Vorgangs](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Hinzufügen der Abhängigkeiten

Erstellen Sie eine Datei namens `rest-apis.js`, und fügen Sie die folgenden Abhängigkeiten hinzu:

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="dependencies":::

## <a name="add-utility-functions"></a>Hinzufügen von Hilfsfunktionen

Fügen Sie die folgenden Hilfsfunktionen hinzu.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="utility":::

## <a name="add-azure-resource-information"></a>Hinzufügen von Azure-Ressourceninformationen

Erstellen Sie Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource. Wenn Sie die Umgebungsvariable nach dem Start der Anwendung erstellt haben, müssen der Editor, die IDE oder die Shell, in dem bzw. der sie ausgeführt wird, geschlossen und erneut geöffnet werden, damit der Zugriff auf die Variable möglich ist.

Legen Sie die folgenden Umgebungsvariablen fest:

* `QNAMAKER_RESOURCE_KEY`: Der **Schlüssel** ist eine Zeichenfolge mit 32 Zeichen und im Azure-Portal in der QnA Maker-Ressource auf der **Schnellstartseite** verfügbar. Diese Ressource ist nicht mit dem Vorhersageendpunktschlüssel identisch.
* `QNAMAKER_AUTHORING_ENDPOINT` – Der Authoring Endpoint (Erstellungsendpunkt) im Format `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` enthält Ihren **Ressourcennamen**. Diese Ressource ist nicht die gleiche URL, die zum Abfragen des Vorhersageendpunkts verwendet wird.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="authorization":::

## <a name="create-a-knowledge-base"></a>Erstellen einer Wissensdatenbank

In einer Wissensdatenbank sind Frage-Antwort-Paare gespeichert, die aus einem JSON-Objekt mit folgendem Inhalt erstellt werden:

* **Redaktioneller Inhalt**
* **Dateien**: Lokale Dateien, für die keine Berechtigungen benötigt werden.
* **URLs**: Öffentlich verfügbare URLs.

Verwenden Sie die [REST-API, um eine Wissensdatenbank zu erstellen](/rest/api/cognitiveservices/qnamaker/knowledgebase/create).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="createKb":::

## <a name="replace-a-knowledge-base"></a>Ersetzen einer Wissensdatenbank

Verwenden Sie die [REST-API, um eine Wissensdatenbank zu ersetzen](/rest/api/cognitiveservices/qnamaker/knowledgebase/replace).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="replaceKb":::

## <a name="publish-a-knowledge-base"></a>Veröffentlichen einer Wissensdatenbank

Veröffentlichen der Wissensdatenbank Mit diesem Prozess wird die Wissensdatenbank über einen Vorhersageendpunkt für HTTP-Abfragen verfügbar gemacht.

Verwenden Sie die [REST-API, um eine Wissensdatenbank zu veröffentlichen](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="publishKb":::

## <a name="download-the-knowledge-base"></a>Herunterladen der Wissensdatenbank

Verwenden Sie die [REST-API, um eine Wissensdatenbank herunterzuladen](/rest/api/cognitiveservices/qnamaker/knowledgebase/download).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="downloadKb":::

## <a name="delete-a-knowledge-base"></a>Löschen einer Wissensdatenbank

Löschen Sie die Wissensdatenbank, wenn Sie die Nutzung beendet haben.

Verwenden Sie die [REST-API, um eine Wissensdatenbank zu löschen](/rest/api/cognitiveservices/qnamaker/knowledgebase/delete).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="deleteKb":::

## <a name="get-status-of-an-operation"></a>Abrufen des Status eines Vorgangs

Bei zeitintensiven Prozessen, z. B. dem Erstellungsprozess, wird eine Vorgangs-ID zurückgegeben, die mit einem separaten REST-API-Aufruf überprüft werden muss. Bei dieser Funktion wird der Text der Erstellungsantwort verwendet. Der wichtige Schlüssel ist `operationState`, um zu ermitteln, ob das Abfragen fortgesetzt werden soll.

Verwenden Sie die [REST-API zum Überwachen von Vorgängen in einer Wissensdatenbank](/rest/api/cognitiveservices/qnamaker/operations/getdetails).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="operationDetails":::

## <a name="add-main-method"></a>Hinzufügen der Main-Methode

Fügen Sie die folgende `main`-Methode hinzu.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="main":::

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `node rest-apis.js` über das Anwendungsverzeichnis aus.

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
>[Tutorial: ](./create-publish-knowledge-base.md)Erstellen und Beantworten einer Knowledge Base

* [Was ist die QnA Maker-API?](../Overview/overview.md)
* [Bearbeiten einer Knowledge Base](../how-to/edit-knowledge-base.md)
* [Abrufen von Nutzungsanalysen](../how-to/get-analytics-knowledge-base.md)
* Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js).
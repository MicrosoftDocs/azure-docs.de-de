---
title: 'Schnellstart: Formularerkennungs-Clientbibliothek für JavaScript'
description: Verwenden Sie die Clientbibliothek der Formularerkennung für JavaScript, um eine Formularverarbeitungs-App zu erstellen, die Schlüssel-Wert-Paare und Tabellendaten aus Ihren benutzerdefinierten Dokumenten extrahiert.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: e5a131753829edddbb4f385766a2d8697ebd0106
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584650"
---
> [!IMPORTANT]
> * Im Code dieses Artikels werden der Einfachheit halber synchrone Methoden und ein ungeschützter Anmeldeinformationsspeicher verwendet. Informationen finden Sie in der Referenzdokumentation weiter unten. 

[Referenzdokumentation](../../index.yml) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/) | [Paket (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer) | [Beispiele](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Die aktuelle Version von [Node.js](https://nodejs.org/)
* Trainingsdaten in einem Azure Storage-Blob. Tipps und Optionen für das Zusammenstellen eines Trainingsdatasets finden Sie unter [Erstellen eines Trainingsdatasets für ein benutzerdefiniertes Modell](../../build-training-data-set.md). In dieser Schnellstartanleitung können Sie die Dateien im Ordner **Trainieren** des [Beispieldatasets](https://go.microsoft.com/fwlink/?linkid=2090451) verwenden (*sample_data.zip* herunterladen und extrahieren).
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Erstellen einer Formularerkennungsressource"  target="_blank"> im Azure-Portal eine Formularerkennungsressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Formularerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-nodejs-application"></a>Erstellen einer neuen Node.js-Anwendung

Erstellen Sie in einem Konsolenfenster (etwa cmd, PowerShell oder Bash) ein neues Verzeichnis für Ihre App, und rufen Sie es auf. 

```console
mkdir myapp && cd myapp
```

Führen Sie den Befehl `npm init` aus, um eine Knotenanwendung mit der Datei `package.json` zu erstellen. 

```console
npm init
```

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Installieren Sie das NPM-Paket `ai-form-recognizer`:

```console
npm install @azure/ai-form-recognizer
```

Die Datei `package.json` Ihrer App wird mit den Abhängigkeiten aktualisiert.

Erstellen Sie eine Datei mit dem Namen `index.js`, öffnen Sie sie, und importieren Sie die folgenden Bibliotheken:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_imports)]


> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.

Erstellen Sie Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellte Formularerkennungsressource erfolgreich bereitgestellt wurde, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Schlüssel und Endpunkt finden Sie auf der Seite mit dem **Schlüssel und dem Endpunkt** der Ressource unter **Ressourcenverwaltung**. 
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](../../../cognitive-services-security.md).

## <a name="object-model"></a>Objektmodell 

Mit der Formularerkennung können Sie zwei verschiedene Clienttypen erstellen. Der erste (`FormRecognizerClient`) wird zum Abfragen des Diensts nach erkannten Formularfeldern und -inhalten verwendet. Der zweite (`FormTrainingClient`) wird zum Erstellen und Verwalten von benutzerdefinierten Modellen verwendet, mit denen Sie die Erkennung verbessern können. 

### <a name="formrecognizerclient"></a>FormRecognizerClient
`FormRecognizerClient` stellt Vorgänge für Folgendes bereit:

 * Erkennen von Formularfeldern und -inhalten mithilfe von benutzerdefinierten Modellen, die zur Analyse Ihrer benutzerdefinierten Formulare trainiert wurden. Diese Werte werden in einer Sammlung von `RecognizedForm`-Objekten zurückgegeben.
 * Erkennen von Formularinhalten (einschließlich Tabellen, Zeilen und Wörtern), ohne dass ein Modell trainiert werden muss. Der Formularinhalt wird in einer Sammlung von `FormPage`-Objekten zurückgegeben.
 * Erkennen allgemeiner Felder in Belegen unter Verwendung eines vorab trainierten Belegmodells für den Formularerkennungsdienst. Diese Felder und Metadaten werden in einer Sammlung von `RecognizedReceipt`-Objekten zurückgegeben.

### <a name="formtrainingclient"></a>FormTrainingClient
`FormTrainingClient` stellt Vorgänge für Folgendes bereit:

* Trainieren benutzerdefinierter Modelle, um alle Felder und Werte in Ihren benutzerdefinierten Formularen zu analysieren. Ein `CustomFormModel`-Element wird zurückgegeben, das angibt, welche Formulartypen vom Modell analysiert und welche Felder für jeden Formtyp extrahiert werden. Eine ausführlichere Erläuterung zum Erstellen eines Trainingsdatasets finden Sie in der [Dokumentation des Diensts für Modelltraining ohne Bezeichnungen](#train-a-model-without-labels).
* Trainieren benutzerdefinierter Modelle zum Analysieren bestimmter Felder und Werte, die Sie durch Bezeichnen Ihrer benutzerdefinierten Formulare angeben. Ein `CustomFormModel`-Element wird zurückgegeben, das die vom Modell extrahierten Felder sowie die geschätzte Genauigkeit für jedes Feld angibt. Eine ausführlichere Erläuterung zum Erstellen eines Trainingsdatasets finden Sie in der [Dokumentation des Diensts für Modelltraining mit Bezeichnungen](#train-a-model-with-labels).
* Verwalten der in Ihrem Konto erstellten Modelle
* Kopieren eines benutzerdefinierten Modells aus einer Formularerkennungsressource in eine andere

> [!NOTE]
> Modelle können auch mithilfe einer grafischen Benutzeroberfläche trainiert werden, z. B. mit dem [Formularerkennungstool für die Bezeichnung](../../quickstarts/label-tool.md).


## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie die folgenden Aufgaben mit der Formularerkennungs-Clientbibliothek für JavaScript ausgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Analysieren des Layouts](#analyze-layout)
* [Analysieren von Belegen](#analyze-receipts)
* [Trainieren eines benutzerdefinierten Modells](#train-a-custom-model)
* [Analysieren von Formularen mit einem benutzerdefinierten Modell](#analyze-forms-with-a-custom-model)
* [Verwalten von benutzerdefinierten Modellen](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients



Authentifizieren Sie ein Clientobjekt mithilfe der definierten Abonnementvariablen. Sie verwenden ein `AzureKeyCredential`-Objekt, damit Sie bei Bedarf den API-Schlüssel aktualisieren können, ohne neue Clientobjekte zu erstellen. Sie erstellen außerdem ein Trainingsclientobjekt.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_auth)]


## <a name="get-assets-for-testing"></a>Ressourcen zum Testen abrufen

Sie müssen außerdem Verweise auf die URLs für Ihre Trainings- und Testdaten hinzufügen.
* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]
   
   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS-URL-Abruf":::
* Verwenden Sie das Beispielformular und die Belegbilder in den Beispielen weiter unten (auch auf [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/test-assets) verfügbar). Alternativ können sie die oben aufgeführten Schritte ausführen, um den SAS-URL eines einzelnen Dokuments im Blobspeicher abzurufen. 


## <a name="analyze-layout"></a>Analysieren des Layouts

Mit der Formularerkennung können Sie Tabellen, Zeilen und Wörter in Dokumenten analysieren, ohne ein Modell trainieren zu müssen. Weitere Informationen zur Layoutextraktion finden Sie im [Konzeptleitfaden zum Layout](../../concept-layout.md). Verwenden Sie die Methode `beginRecognizeContentFromUrl`, um den Inhalt einer Datei an einem angegebenen URI zu analysieren.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_getcontent)]


> [!TIP]
> Außerdem können Sie Inhalte aus einer lokalen Datei abrufen. Mehr dazu erfahren Sie bei den [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient)-Methoden, z. B. **beginRecognizeContent**. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) Szenarien zu lokalen Bildern.

### <a name="output"></a>Output

```console
Page 1: width 8.5 and height 11 with unit inch
cell [0,0] has text Invoice Number
cell [0,1] has text Invoice Date
cell [0,2] has text Invoice Due Date
cell [0,3] has text Charges
cell [0,5] has text VAT ID
cell [1,0] has text 34278587
cell [1,1] has text 6/18/2017
cell [1,2] has text 6/24/2017
cell [1,3] has text $56,651.49
cell [1,5] has text PT
```



## <a name="train-a-custom-model"></a>Trainieren eines benutzerdefinierten Modells

In diesem Abschnitt wird gezeigt, wie Sie ein Modell mit eigenen Daten trainieren. Ein trainiertes Modell kann strukturierte Daten ausgeben, die die Schlüssel-Wert-Beziehungen im ursprünglichen Formulardokument enthalten. Nachdem das Modell trainiert wurde, können Sie es testen, neu trainieren und schließlich verwenden, um Daten aus weiteren Formularen zuverlässig nach Ihren Bedürfnissen zu extrahieren.

> [!NOTE]
> Sie können Modelle auch mithilfe einer grafischen Benutzeroberfläche trainieren, z. B. dem [Formularerkennungstool für die Bezeichnung von Beispielen](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Trainieren eines Modells ohne Bezeichnungen

Trainieren Sie benutzerdefinierte Modelle, sodass alle Felder und Werte in Ihren benutzerdefinierten Formularen analysiert werden, ohne dass Sie die Trainingsdokumente manuell beschriften müssen.

Die folgende Funktion trainiert ein Modell mit einem angegebenen Dokumentensatz und gibt den Status des Modells an der Konsole aus. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_train)]


### <a name="output"></a>Output

Hier sehen Sie die Ausgabe für ein Modell, das mit den Trainingsdaten aus dem [JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer) trainiert wurde. Diese Beispielausgabe wurde zur besseren Lesbarkeit gekürzt.

```console
training status: creating
training status: ready
Model ID: 9d893595-1690-4cf2-a4b1-fbac0fb11909
Status: ready
Training started on: Thu Aug 20 2020 20:27:26 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:27:37 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'field-0'
The model found field 'field-1'
The model found field 'field-2'
The model found field 'field-3'
The model found field 'field-4'
The model found field 'field-5'
The model found field 'field-6'
The model found field 'field-7'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: 
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: 
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: 
...
```

### <a name="train-a-model-with-labels"></a>Trainieren eines Modells mit Bezeichnungen

Sie können benutzerdefinierte Modelle auch trainieren, indem Sie die Trainingsdokumente manuell bezeichnen. Das Training mit Bezeichnungen führt in einigen Szenarien zu einer besseren Leistung. Zum Training mit Bezeichnungen benötigen Sie zusätzlich zu den Trainingsdokumenten spezielle Informationsdateien mit Bezeichnungen (`\<filename\>.pdf.labels.json`) in Ihrem Blobspeichercontainer. Das [Formularerkennungstool für die Bezeichnung von Beispielen](../../quickstarts/label-tool.md) bietet eine Benutzeroberfläche, auf der Sie diese Bezeichnungsdateien erstellen können. Sobald Sie darüber verfügen, können Sie die `beginTraining`-Methode mit dem auf `true` festgelegten Parameter `uselabels` aufrufen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_trainlabels)]


### <a name="output"></a>Output 

Hier sehen Sie die Ausgabe für ein Modell, das mit den Trainingsdaten aus dem [JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) trainiert wurde. Diese Beispielausgabe wurde zur besseren Lesbarkeit gekürzt.

```console
training status: creating
training status: ready
Model ID: 789b1b37-4cc3-4e36-8665-9dde68618072
Status: ready
Training started on: Thu Aug 20 2020 20:30:37 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:30:43 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'CompanyAddress'
The model found field 'CompanyName'
The model found field 'CompanyPhoneNumber'
The model found field 'DatedAs'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
...
```

## <a name="analyze-forms-with-a-custom-model"></a>Analysieren von Formularen mit einem benutzerdefinierten Modell

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe von Modellen, die Sie mit Ihren eigenen Formularen trainiert haben, Schlüssel-Wert-Informationen und andere Inhalte aus Ihren benutzerdefinierten Formulartypen extrahieren.

> [!IMPORTANT]
> Um dieses Szenario zu implementieren, müssen Sie bereits ein Modell trainiert haben, sodass Sie seine ID an die unten stehende Methode übergeben können. Weitere Informationen finden Sie im Abschnitt [Trainieren eines Modells](#train-a-model-without-labels).

Sie verwenden die Methode `beginRecognizeCustomFormsFromUrl`. Der zurückgegebene Wert ist eine Sammlung aus `RecognizedForm`-Objekten: eines für jede Seite im übermittelten Dokument.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_analyze)]

> [!TIP]
> Auch lokale Dateien können analysiert werden. Mehr dazu erfahren Sie bei den [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient)-Methoden, z. B. **beginRecognizeCustomForms**. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) Szenarien zu lokalen Bildern.


### <a name="output"></a>Output

```console
status: notStarted
status: succeeded
Forms:
custom:form, page range: [object Object]
Pages:
Page number: 1
Tables
cell (0,0) Invoice Number
cell (0,1) Invoice Date
cell (0,2) Invoice Due Date
cell (0,3) Charges
cell (0,5) VAT ID
cell (1,0) 34278587
cell (1,1) 6/18/2017
cell (1,2) 6/24/2017
cell (1,3) $56,651.49
cell (1,5) PT
Fields:
Field Merchant has value 'Invoice For:' with a confidence score of 0.116
Field CompanyPhoneNumber has value '$56,651.49' with a confidence score of 0.249
Field VendorName has value 'Charges' with a confidence score of 0.145
Field CompanyAddress has value '1 Redmond way Suite 6000 Redmond, WA' with a confidence score of 0.258
Field CompanyName has value 'PT' with a confidence score of 0.245
Field Website has value '99243' with a confidence score of 0.114
Field DatedAs has value 'undefined' with a confidence score of undefined
Field Email has value 'undefined' with a confidence score of undefined
Field PhoneNumber has value 'undefined' with a confidence score of undefined
Field PurchaseOrderNumber has value 'undefined' with a confidence score of undefined
Field Quantity has value 'undefined' with a confidence score of undefined
Field Signature has value 'undefined' with a confidence score of undefined
Field Subtotal has value 'undefined' with a confidence score of undefined
Field Tax has value 'undefined' with a confidence score of undefined
Field Total has value 'undefined' with a confidence score of undefined
```

## <a name="analyze-receipts"></a>Analysieren von Belegen

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe eines vorab trainierten Belegmodells gängige Felder in US-Belegen analysieren und extrahieren. Weitere Informationen zur Beleganalyse finden Sie im [Konzeptleitfaden zu Belegen](../../concept-receipts.md).

Verwenden Sie die Methode `beginRecognizeReceiptsFromUrl`, um Belege aus einem URI zu analysieren. Der folgende Code verarbeitet einen Beleg am angegebenen URI und gibt die wichtigsten Felder und Werte an der Konsole aus.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_receipts)]

> [!TIP]
> Auch lokale Belegbilder können analysiert werden. Mehr dazu erfahren Sie bei den [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-latest)-Methoden, z. B. **beginRecognizeReceipts**. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) Szenarien zu lokalen Bildern.

### <a name="output"></a>Output

```console
status: notStarted
status: running
status: succeeded
First receipt:
  Receipt Type: 'Itemized', with confidence of 0.659
  Merchant Name: 'Contoso Contoso', with confidence of 0.516
  Transaction Date: 'Sun Jun 09 2019 17:00:00 GMT-0700 (Pacific Daylight Time)', with confidence of 0.985
    Item Name: '8GB RAM (Black)', with confidence of 0.916
    Item Name: 'SurfacePen', with confidence of 0.858
  Total: '1203.39', with confidence of 0.774
```

## <a name="manage-your-custom-models"></a>Verwalten von benutzerdefinierten Modellen

In diesem Abschnitt wird veranschaulicht, wie Sie die in Ihrem Konto gespeicherten benutzerdefinierten Modelle verwalten. Der folgende Code verarbeitet beispielsweise sämtliche Modellverwaltungstasks in einer einzelnen Funktion. 

### <a name="get-number-of-models"></a>Abrufen der Anzahl der Modelle

Mit dem folgenden Codeblock wird die Anzahl der Modelle abgerufen, die sich derzeit in Ihrem Konto befinden.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_count)]


### <a name="get-list-of-models-in-account"></a>Abrufen der Liste mit den Modellen in einem Konto

Mit dem folgenden Codeblock wird eine vollständige Liste der verfügbaren Modelle in Ihrem Konto bereitgestellt, einschließlich Informationen zum Erstellungszeitpunkt des Modells und zum aktuellen Status.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_list)]


### <a name="output"></a>Output

```console
model 0:
{
  modelId: '453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e',
  status: 'invalid',
  trainingStartedOn: 2020-08-20T22:28:52.000Z,
  trainingCompletedOn: 2020-08-20T22:28:53.000Z
}
model 1:
{
  modelId: '628739de-779c-473d-8214-d35c72d3d4f7',
  status: 'ready',
  trainingStartedOn: 2020-08-20T23:16:51.000Z,
  trainingCompletedOn: 2020-08-20T23:16:59.000Z
}
model 2:
{
  modelId: '789b1b37-4cc3-4e36-8665-9dde68618072',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:30:37.000Z,
  trainingCompletedOn: 2020-08-21T03:30:43.000Z
}
model 3:
{
  modelId: '9d893595-1690-4cf2-a4b1-fbac0fb11909',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:27:26.000Z,
  trainingCompletedOn: 2020-08-21T03:27:37.000Z
}
```

### <a name="get-list-of-model-ids-by-page"></a>Abrufen der Liste mit den Modell-IDs nach Seite

Mit diesem Codeblock wird eine paginierte Liste der Modelle und Modell-IDs abgerufen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_listpages)]


### <a name="output"></a>Output

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
```

### <a name="get-model-by-id"></a>Abrufen eines Modells nach ID

Die folgende Funktion akzeptiert eine Modell-ID und ruft das entsprechende Modellobjekt ab. Diese Funktion wird nicht standardmäßig aufgerufen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_getmodel)]


### <a name="delete-a-model-from-the-resource-account"></a>Löschen eines Modells aus dem Ressourcenkonto

Sie können ein Modell auch aus Ihrem Konto löschen, indem Sie auf die ID des Modells verweisen. Diese Funktion löscht das Modell mit der angegebenen ID. Diese Funktion wird nicht standardmäßig aufgerufen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_delete)]


### <a name="output"></a>Output

```console
Model with id 789b1b37-4cc3-4e36-8665-9dde68618072 has been deleted
```

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `node` für die Schnellstartdatei aus.

```console
node index.js
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Problembehandlung

### <a name="enable-logs"></a>Aktivieren von Protokollen

Sie können die folgende Umgebungsvariable festlegen, um Debugprotokolle anzuzeigen, wenn Sie diese Bibliothek verwenden.

```
export DEBUG=azure*
```

Ausführlichere Anweisungen zum Aktivieren von Protokollen finden Sie in der [Dokumentation zum Paket @azure/logger](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger).

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie die Formularerkennungs-Clientbibliothek für JavaScript verwendet, um auf unterschiedliche Weise Modelle zu trainieren und Formulare zu analysieren. Lesen Sie im Anschluss die Tipps zum Erstellen eines besseren Trainingsdatasets und zum Generieren genauerer Modelle.

> [!div class="nextstepaction"]
> [Erstellen eines Trainingsdatasets](../../build-training-data-set.md)

## <a name="see-also"></a>Weitere Informationen

* [Was ist die Formularerkennung?](../../overview.md)
* Den Beispielcode aus diesem Leitfaden finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js).

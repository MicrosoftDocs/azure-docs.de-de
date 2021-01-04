---
title: 'Schnellstart: Formularerkennungs-Clientbibliothek für Python'
description: Verwenden Sie die Clientbibliothek der Formularerkennung für Python, um eine Formularverarbeitungs-App zu erstellen, die Schlüssel-Wert-Paare und Tabellendaten aus Ihren benutzerdefinierten Dokumenten extrahiert.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: 12b49bf81328cc405337b682241aef2cd965c3de
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356544"
---
> [!IMPORTANT]
> * Im Code dieses Artikels werden der Einfachheit halber synchrone Methoden und ein ungeschützter Anmeldeinformationsspeicher verwendet. Informationen finden Sie in der Referenzdokumentation weiter unten. 

[Referenzdokumentation](/python/api/azure-ai-formrecognizer) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer) | [Paket (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/) | [Beispiele](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Trainingsdaten in einem Azure Storage-Blob. Tipps und Optionen für das Zusammenstellen eines Trainingsdatasets finden Sie unter [Erstellen eines Trainingsdatasets für ein benutzerdefiniertes Modell](../../build-training-data-set.md). In dieser Schnellstartanleitung können Sie die Dateien im Ordner **Trainieren** des [Beispieldatasets](https://go.microsoft.com/fwlink/?linkid=2090451) verwenden (*sample_data.zip* herunterladen und extrahieren).
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Erstellen einer Formularerkennungsressource"  target="_blank"> im Azure-Portal eine Formularerkennungsressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Formularerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Nach der Installation von Python können Sie die aktuelle Version der Formularerkennungs-Clientbibliothek installieren:

#### <a name="version-30"></a>[Version 3.0](#tab/ga)

```console
pip install azure-ai-formrecognizer
```

#### <a name="version-31-preview"></a>[Version 3.1 (Vorschau)](#tab/preview)

```console
pip install azure-ai-formrecognizer --pre
```

---

### <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

Erstellen Sie eine neue Python-Anwendung in Ihrem bevorzugten Editor oder Ihrer bevorzugten IDE. Importieren Sie dann die folgenden Bibliotheken:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_imports)]

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.


Erstellen Sie Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_creds)]


## <a name="object-model"></a>Objektmodell 

Mit der Formularerkennung können Sie zwei verschiedene Clienttypen erstellen. Der erste (`form_recognizer_client`) wird zum Abfragen des Diensts nach erkannten Formularfeldern und -inhalten verwendet. Der zweite (`form_training_client`) wird zum Erstellen und Verwalten von benutzerdefinierten Modellen verwendet, mit denen Sie die Erkennung verbessern können. 

### <a name="formrecognizerclient"></a>FormRecognizerClient
`form_recognizer_client` stellt Vorgänge für Folgendes bereit:

 * Erkennen von Formularfeldern und -inhalten mithilfe von benutzerdefinierten Modellen, die zur Erkennung Ihrer benutzerdefinierten Formulare trainiert wurden. 
 * Erkennen von Formularinhalten (einschließlich Tabellen, Zeilen und Wörtern), ohne dass ein Modell trainiert werden muss 
 * Erkennen allgemeiner Felder in Belegen unter Verwendung eines vorab trainierten Belegmodells für den Formularerkennungsdienst

### <a name="formtrainingclient"></a>FormTrainingClient
`form_training_client` stellt Vorgänge für Folgendes bereit:

* Trainieren benutzerdefinierter Modelle, um alle Felder und Werte in Ihren benutzerdefinierten Formularen zu erkennen. Eine ausführlichere Erläuterung zum Erstellen eines Trainingsdatasets finden Sie in der [Dokumentation des Diensts für Modelltraining ohne Bezeichnungen](#train-a-model-without-labels).
* Trainieren benutzerdefinierter Modelle zum Erkennen bestimmter Felder und Werte, die Sie durch Bezeichnen Ihrer benutzerdefinierten Formulare angeben. Eine ausführlichere Erläuterung zum Erstellen eines Trainingsdatasets finden Sie in der [Dokumentation des Diensts für Modelltraining mit Bezeichnungen](#train-a-model-with-labels).
* Verwalten der in Ihrem Konto erstellten Modelle
* Kopieren eines benutzerdefinierten Modells aus einer Formularerkennungsressource in eine andere

> [!NOTE]
> Modelle können auch mithilfe einer grafischen Benutzeroberfläche trainiert werden, z. B. mit dem [Formularerkennungstool für die Bezeichnung](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie die folgenden Aufgaben mit der Formularerkennungs-Clientbibliothek für Python ausgeführt werden:

#### <a name="version-30"></a>[Version 3.0](#tab/ga)

* [Authentifizieren des Clients](#authenticate-the-client)
* [Erkennen von Formularinhalten](#recognize-form-content)
* [Erkennen von Belegen](#recognize-receipts)
* [Trainieren eines benutzerdefinierten Modells](#train-a-custom-model)
* [Analysieren von Formularen mit einem benutzerdefinierten Modell](#analyze-forms-with-a-custom-model)
* [Verwalten von benutzerdefinierten Modellen](#manage-your-custom-models)

#### <a name="version-31-preview"></a>[Version 3.1 (Vorschau)](#tab/preview)

* [Authentifizieren des Clients](#authenticate-the-client)
* [Erkennen von Formularinhalten](#recognize-form-content)
* [Erkennen von Belegen](#recognize-receipts)
* [Erkennen von Visitenkarten](#recognize-business-cards)
* [Erkennen von Rechnungen](#recognize-invoices)
* [Trainieren eines benutzerdefinierten Modells](#train-a-custom-model)
* [Analysieren von Formularen mit einem benutzerdefinierten Modell](#analyze-forms-with-a-custom-model)
* [Verwalten von benutzerdefinierten Modellen](#manage-your-custom-models)

---

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Hier authentifizieren Sie zwei Clientobjekte mithilfe der oben definierten Abonnementvariablen. Sie verwenden ein **AzureKeyCredential**-Objekt, damit Sie bei Bedarf den API-Schlüssel aktualisieren können, ohne neue Clientobjekte zu erstellen.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_auth)]


## <a name="get-assets-for-testing"></a>Ressourcen zum Testen abrufen

Sie müssen Verweise auf die URLs für Ihre Trainings- und Testdaten hinzufügen.
* Öffnen Sie zum Abrufen der SAS-URL für die Trainingsdaten Ihres benutzerdefinierten Modells den Microsoft Azure Storage-Explorer, klicken Sie mit der rechten Maustaste auf Ihren Container, und wählen Sie **Shared Access Signature abrufen** aus. Stellen Sie sicher, dass die Berechtigungen **Lesen** und **Auflisten**  aktiviert sind, und klicken Sie auf **Erstellen**. Kopieren Sie den Wert im **URL**-Abschnitt. Er muss das Format `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` aufweisen.
* Verwenden Sie das Beispielformular und die Belegbilder in den Beispielen weiter unten (auch auf [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) verfügbar). Alternativ können sie die oben aufgeführten Schritte ausführen, um den SAS-URL eines einzelnen Dokuments im Blobspeicher abzurufen. 

> [!NOTE]
> Die Codeausschnitte in dieser Anleitung verwenden Remoteformulare, auf die über URLs zugegriffen wird. Wenn Sie stattdessen lokale Formulardokumente verarbeiten möchten, finden Sie weitere Informationen unter den entsprechenden Methoden in der [Referenzdokumentation](/python/api/azure-ai-formrecognizer) und den [Beispielen](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

## <a name="recognize-form-content"></a>Erkennen von Formularinhalten

Mit der Formularerkennung können Sie Tabellen, Zeilen und Wörter in Dokumenten erkennen, ohne ein Modell trainieren zu müssen.

Um die Inhalte einer Datei unter einer angegebenen URL zu erkennen, verwenden Sie die `begin_recognize_content_from_url`-Methode. Der zurückgegebene Wert ist eine Sammlung aus `FormPage`-Objekten: eines für jede Seite im übermittelten Dokument. Der folgende Code durchläuft diese Objekte und gibt die extrahierten Schlüssel-Wert-Paare und Tabellendaten aus.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_getcontent)]

> [!TIP]
> Außerdem können Sie Inhalte aus lokalen Bildern beziehen. Mehr dazu erfahren Sie bei den [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python)-Methoden, z. B. `begin_recognize_content`. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) Szenarien zu lokalen Bildern.

### <a name="output"></a>Output

```console
Table found on page 1:
Cell text: Invoice Number
Location: [Point(x=0.5075, y=2.8088), Point(x=1.9061, y=2.8088), Point(x=1.9061, y=3.3219), Point(x=0.5075, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Date
Location: [Point(x=1.9061, y=2.8088), Point(x=3.3074, y=2.8088), Point(x=3.3074, y=3.3219), Point(x=1.9061, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Due Date
Location: [Point(x=3.3074, y=2.8088), Point(x=4.7074, y=2.8088), Point(x=4.7074, y=3.3219), Point(x=3.3074, y=3.3219)]
Confidence score: 1.0

Cell text: Charges
Location: [Point(x=4.7074, y=2.8088), Point(x=5.386, y=2.8088), Point(x=5.386, y=3.3219), Point(x=4.7074, y=3.3219)]
Confidence score: 1.0
...

```

## <a name="recognize-receipts"></a>Erkennen von Belegen

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe eines vorab trainierten Belegmodells gebräuchliche Felder in US-Belegen erkennen und extrahieren. Um Belege von einer URL zu erkennen, verwenden Sie die `begin_recognize_receipts_from_url`-Methode. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_receipts)]

> [!TIP]
> Sie können darüber hinaus lokale Belegbilder erkennen. Mehr dazu erfahren Sie bei den [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python)-Methoden, z. B. `begin_recognize_receipts`. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) Szenarien zu lokalen Bildern.

### <a name="output"></a>Output

```console
ReceiptType: Itemized has confidence 0.659
MerchantName: Contoso Contoso has confidence 0.516
MerchantAddress: 123 Main Street Redmond, WA 98052 has confidence 0.986
MerchantPhoneNumber: None has confidence 0.99
TransactionDate: 2019-06-10 has confidence 0.985
TransactionTime: 13:59:00 has confidence 0.968
Receipt Items:
...Item #1
......Name: 8GB RAM (Black) has confidence 0.916
......TotalPrice: 999.0 has confidence 0.559
...Item #2
......Quantity: None has confidence 0.858
......Name: SurfacePen has confidence 0.858
......TotalPrice: 99.99 has confidence 0.386
Subtotal: 1098.99 has confidence 0.964
Tax: 104.4 has confidence 0.713
Total: 1203.39 has confidence 0.774
```

#### <a name="version-30"></a>[Version 3.0](#tab/ga)

#### <a name="version-31-preview"></a>[Version 3.1 (Vorschau)](#tab/preview)

## <a name="recognize-business-cards"></a>Erkennen von Visitenkarten

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe eines vorab trainierten Modells gängige Felder englischsprachiger Visitenkarten erkennen und extrahieren. Für die Erkennung von Visitenkarten unter einer URL muss die Methode `begin_recognize_business_cards_from_url` verwendet werden. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_bc)]

> [!TIP]
> Sie können für die Erkennung auch lokale Visitenkartenbilder verwenden. Mehr dazu erfahren Sie bei den [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python)-Methoden, z. B. `begin_recognize_business_cards`. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) Szenarien zu lokalen Bildern.

## <a name="recognize-invoices"></a>Erkennen von Rechnungen

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe eines vorab trainierten Modells gängige Rechnungsfelder erkennen und extrahieren. Für die Erkennung von Rechnungen unter einer URL muss die Methode `begin_recognize_invoices_from_url` verwendet werden. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_invoice)]

> [!TIP]
> Sie können für die Erkennung auch lokale Rechnungsbilder verwenden. Mehr dazu erfahren Sie bei den [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python)-Methoden, z. B. `begin_recognize_invoices`. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) Szenarien zu lokalen Bildern.

---

## <a name="train-a-custom-model"></a>Trainieren eines benutzerdefinierten Modells

In diesem Abschnitt wird gezeigt, wie Sie ein Modell mit eigenen Daten trainieren. Ein trainiertes Modell kann strukturierte Daten ausgeben, die die Schlüssel-Wert-Beziehungen im ursprünglichen Formulardokument enthalten. Nachdem das Modell trainiert wurde, können Sie es testen, neu trainieren und schließlich verwenden, um Daten aus weiteren Formularen zuverlässig nach Ihren Bedürfnissen zu extrahieren.

> [!NOTE]
> Sie können Modelle auch mithilfe einer grafischen Benutzeroberfläche trainieren, z. B. dem [Formularerkennungstool für die Bezeichnung von Beispielen](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Trainieren eines Modells ohne Bezeichnungen

Trainieren Sie benutzerdefinierte Modelle, sodass alle Felder und Werte in Ihren benutzerdefinierten Formularen erkannt werden, ohne dass Sie die Trainingsdokumente manuell bezeichnen müssen.

Im folgenden Code wird der Trainingsclient mit der `begin_training`-Funktion verwendet, um ein Modell für eine bestimmte Gruppe von Dokumenten zu trainieren. Das zurückgegebene `CustomFormModel`-Objekt enthält Informationen zu den vom Modell erkannten Formulartypen und zu den Feldern, die das Modell aus jedem Formulartyp extrahieren kann. Der folgende Codeblock gibt diese Informationen an der Konsole aus.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_train)]


### <a name="output"></a>Output

Hier sehen Sie die Ausgabe für ein Modell, das mit den Trainingsdaten aus dem [Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training) trainiert wurde:

```console
Model ID: 628739de-779c-473d-8214-d35c72d3d4f7
Status: ready
Training started on: 2020-08-20 23:16:51+00:00
Training completed on: 2020-08-20 23:16:59+00:00

Recognized fields:
The submodel with form type 'form-0' has recognized the following fields: Additional Notes:, Address:, Company Name:, Company Phone:, Dated As:, Details, Email:, Hero Limited, Name:, Phone:, Purchase Order, Purchase Order #:, Quantity, SUBTOTAL, Seattle, WA 93849 Phone:, Shipped From, Shipped To, TAX, TOTAL, Total, Unit Price, Vendor Name:, Website:
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

### <a name="train-a-model-with-labels"></a>Trainieren eines Modells mit Bezeichnungen

Sie können benutzerdefinierte Modelle auch trainieren, indem Sie die Trainingsdokumente manuell bezeichnen. Das Training mit Bezeichnungen führt in einigen Szenarien zu einer besseren Leistung. Das zurückgegebene `CustomFormModel` gibt die Felder an, die das Modell extrahieren kann, und bietet Informationen zur geschätzten Genauigkeit in jedem Feld. Der folgende Codeblock gibt diese Informationen an der Konsole aus.

> [!IMPORTANT]
> Zum Training mit Bezeichnungen benötigen Sie zusätzlich zu den Trainingsdokumenten spezielle Informationsdateien mit Bezeichnungen (`\<filename\>.pdf.labels.json`) in Ihrem Blobspeichercontainer. Das [Formularerkennungstool für die Bezeichnung von Beispielen](../../quickstarts/label-tool.md) bietet eine Benutzeroberfläche, auf der Sie diese Bezeichnungsdateien erstellen können. Sobald Sie darüber verfügen, können Sie die `begin_training`-Funktion mit dem auf `true` festgelegten Parameter *use_training_labels* aufrufen.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_trainlabels)]

### <a name="output"></a>Output

Hier sehen Sie die Ausgabe für ein Modell, das mit den Trainingsdaten aus dem [Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training) trainiert wurde:

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91

Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00

Recognized fields:
The submodel with form type 'form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91' has recognized the following fields: CompanyAddress, CompanyName, CompanyPhoneNumber, DatedAs, Email, Merchant, PhoneNumber, PurchaseOrderNumber, Quantity, Signature, Subtotal, Tax, Total, VendorName, Website
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

## <a name="analyze-forms-with-a-custom-model"></a>Analysieren von Formularen mit einem benutzerdefinierten Modell

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe von Modellen, die Sie mit Ihren eigenen Formularen trainiert haben, Schlüssel-Wert-Informationen und andere Inhalte aus Ihren benutzerdefinierten Formulartypen extrahieren.

> [!IMPORTANT]
> Um dieses Szenario zu implementieren, müssen Sie bereits ein Modell trainiert haben, sodass Sie seine ID an die unten stehende Methode übergeben können. Weitere Informationen finden Sie im Abschnitt [Trainieren eines Modells](#train-a-model-without-labels).

Sie verwenden die Methode `begin_recognize_custom_forms_from_url`. Der zurückgegebene Wert ist eine Sammlung aus `RecognizedForm`-Objekten: eines für jede Seite im übermittelten Dokument. Der folgende Code gibt die Analyseergebnisse an der Konsole aus. Der Code gibt jedes erkannte Feld und den zugehörigen Wert sowie eine Zuverlässigkeitsbewertung aus.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_analyze)]

> [!TIP]
> Auch lokale Bilder können analysiert werden. Mehr dazu erfahren Sie bei den [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python)-Methoden, z. B. `begin_recognize_custom_forms`. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) Szenarien zu lokalen Bildern.


### <a name="output"></a>Output

Das Modell aus dem vorherigen Beispiel ergibt die folgende Ausgabe:

```console
Form type: form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Field 'Merchant' has label 'Merchant' with value 'Invoice For:' and a confidence score of 0.116
Field 'CompanyAddress' has label 'CompanyAddress' with value '1 Redmond way Suite 6000 Redmond, WA' and a confidence score of 0.258
Field 'Website' has label 'Website' with value '99243' and a confidence score of 0.114
Field 'VendorName' has label 'VendorName' with value 'Charges' and a confidence score of 0.145
Field 'CompanyPhoneNumber' has label 'CompanyPhoneNumber' with value '$56,651.49' and a confidence score of 0.249
Field 'CompanyName' has label 'CompanyName' with value 'PT' and a confidence score of 0.245
Field 'DatedAs' has label 'DatedAs' with value 'None' and a confidence score of None
Field 'Email' has label 'Email' with value 'None' and a confidence score of None
Field 'PhoneNumber' has label 'PhoneNumber' with value 'None' and a confidence score of None
Field 'PurchaseOrderNumber' has label 'PurchaseOrderNumber' with value 'None' and a confidence score of None
Field 'Quantity' has label 'Quantity' with value 'None' and a confidence score of None
Field 'Signature' has label 'Signature' with value 'None' and a confidence score of None
Field 'Subtotal' has label 'Subtotal' with value 'None' and a confidence score of None
Field 'Tax' has label 'Tax' with value 'None' and a confidence score of None
Field 'Total' has label 'Total' with value 'None' and a confidence score of None
```

## <a name="manage-your-custom-models"></a>Verwalten von benutzerdefinierten Modellen

In diesem Abschnitt wird veranschaulicht, wie Sie die in Ihrem Konto gespeicherten benutzerdefinierten Modelle verwalten. 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Überprüfen der Anzahl von Modellen im FormRecognizer-Ressourcenkonto

Der folgende Codeblock überprüft, wie viele Modelle in Ihrem Formularerkennungskonto gespeichert sind, und vergleicht diese Zahl mit dem Kontolimit.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_count)]


### <a name="output"></a>Output

```console
Our account has 5 custom models, and we can have at most 5000 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Auflisten der zurzeit im Ressourcenkonto gespeicherten Modelle

Der folgende Codeblock listet die aktuell in Ihrem Konto vorhandenen Modelle auf und gibt ihre Details an der Konsole aus. Außerdem speichert er einen Verweis auf das erste Modell.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_list)]


### <a name="output"></a>Output

Hier sehen Sie eine Beispielausgabe für das Testkonto:

```console
We have models with the following ids:
453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
628739de-779c-473d-8214-d35c72d3d4f7
ae636292-0b14-4e26-81a7-a0bfcbaf7c91
b4b5df77-8538-4ffb-a996-f67158ecd305
c6309148-6b64-4fef-aea0-d39521452699
```

### <a name="get-a-specific-model-using-the-models-id"></a>Abrufen eines bestimmten Modells anhand der Modell-ID

Der folgende Codeblock verwendet die im vorherigen Abschnitt gespeicherte Modell-ID und verwendet sie zum Abrufen von Details über das Modell.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_getmodel)]


### <a name="output"></a>Output

Hier sehen Sie die Beispielausgabe für das im vorherigen Beispiel erstellte benutzerdefinierte Modell:

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00
```

### <a name="delete-a-model-from-the-resource-account"></a>Löschen eines Modells aus dem Ressourcenkonto

Sie können ein Modell auch aus Ihrem Konto löschen, indem Sie auf die ID des Modells verweisen. Dieser Code löscht das im vorherigen Abschnitt verwendete Modell.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_delete)]


## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `python` für die Schnellstartdatei aus.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Problembehandlung

### <a name="general"></a>Allgemein

Die Clientbibliothek der Formularerkennung löst in [Azure Core](https://aka.ms/azsdk-python-azure-core) definierte Ausnahmen aus.

### <a name="logging"></a>Protokollierung

Diese Bibliothek verwendet für die Protokollierung die [Standardprotokollierungsbibliothek](https://docs.python.org/3/library/logging.html). Grundlegende Informationen zu HTTP-Sitzungen (URLs, Header usw.) werden auf der Ebene INFO protokolliert.

Eine ausführliche Protokollierung auf der Ebene DEBUG, einschließlich Anforderungs-/Antworttexten und vollständiger Header, kann auf einem Client mit dem Schlüsselwortargument `logging_enable` aktiviert werden:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_logging)]


Ebenso kann über `logging_enable` die ausführliche Protokollierung für einen einzelnen Vorgang aktiviert werden, auch wenn diese Funktion für den Client nicht aktiviert ist:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_example)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie die Formularerkennungs-Clientbibliothek für Python verwendet, um auf unterschiedliche Weise Modelle zu trainieren und Formulare zu analysieren. Lesen Sie im Anschluss die Tipps zum Erstellen eines besseren Trainingsdatasets und zum Generieren genauerer Modelle.

> [!div class="nextstepaction"]
> [Erstellen eines Trainingsdatasets](../../build-training-data-set.md)

* [Was ist die Formularerkennung?](../../overview.md)
* Den Beispielcode aus diesem Leitfaden finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py).
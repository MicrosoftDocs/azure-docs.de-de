---
title: 'Schnellstart: Formularerkennungs-Clientbibliothek für Java'
description: Verwenden Sie die Clientbibliothek der Formularerkennung für Java, um eine Formularverarbeitungs-App zu erstellen, die Schlüssel-Wert-Paare und Tabellendaten aus Ihren benutzerdefinierten Dokumenten extrahiert.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 09/21/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 4f48416153f5a378f9e4eff84802519250488301
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584667"
---
> [!IMPORTANT]
> Im Code dieses Artikels werden der Einfachheit halber synchrone Methoden und ein ungeschützter Anmeldeinformationsspeicher verwendet.

[Referenzdokumentation](/java/api/overview/azure/ai-formrecognizer-readme) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [Paket (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [Beispiele](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Aktuelle Version des [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle-Buildtool](https://gradle.org/install/) oder einen anderen Abhängigkeit-Manager
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Erstellen einer Formularerkennungsressource"  target="_blank"> im Azure-Portal eine Formularerkennungsressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Formularerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.
* Trainingsdaten in einem Azure Storage-Blob. Tipps und Optionen für das Zusammenstellen eines Trainingsdatasets finden Sie unter [Erstellen eines Trainingsdatasets für ein benutzerdefiniertes Modell](../../build-training-data-set.md). In dieser Schnellstartanleitung können Sie die Dateien im Ordner **Trainieren** des [Beispieldatasets](https://go.microsoft.com/fwlink/?linkid=2090451) verwenden (*sample_data.zip* herunterladen und extrahieren).


## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-gradle-project"></a>Erstellen eines neuen Gradle-Projekts

Erstellen Sie in einem Konsolenfenster (etwa cmd, PowerShell oder Bash) ein neues Verzeichnis für Ihre App, und rufen Sie es auf. 

```console
mkdir myapp && cd myapp
```

Führen Sie den Befehl `gradle init` in Ihrem Arbeitsverzeichnis aus. Mit diesem Befehl werden grundlegende Builddateien für Gradle erstellt, u. a. die Datei *build.gradle.kts*. Diese Datei wird zur Laufzeit zum Erstellen und Konfigurieren Ihrer Anwendung verwendet.

```console
gradle init --type basic
```

Wenn Sie zur Auswahl einer **DSL** aufgefordert werden, wählen Sie **Kotlin** aus.


### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

In dieser Schnellstartanleitung wird der Gradle-Abhängigkeits-Manager verwendet. Die Clientbibliothek und Informationen zu anderen Abhängigkeits-Managern finden Sie im [zentralen Maven-Repository](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

Fügen Sie in die Datei *build.gradle.kts* Ihres Projekts die Clientbibliothek als als `implementation`-Anweisung sowie die erforderlichen Plug-Ins und Einstellungen ein.

#### <a name="version-20"></a>[Version 2.0](#tab/ga)
```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.0.0")
}
```

> [!NOTE]
> Für das SDK von Formularerkennung 3.0.0 wird API-Version 2.0 verwendet.

#### <a name="version-31-preview"></a>[Version 3.1 (Vorschau)](#tab/preview)
```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.0-beta.1")
}
```

> [!NOTE]
> Für das SDK von Formularerkennung 3.1.0 wird API-Version 2.1 (Vorschau) verwendet.

---

### <a name="create-a-java-file"></a>Erstellen einer Java-Datei


Führen Sie in Ihrem Arbeitsverzeichnis den folgenden Befehl aus:

```console
mkdir -p src/main/java
```

Navigieren Sie zu dem neuen Ordner, und erstellen Sie eine Datei mit dem Namen *FormRecognizer.java*. Öffnen Sie sie in Ihrem bevorzugten Editor bzw. Ihrer bevorzugten IDE, und fügen Sie die folgenden `import`-Anweisungen hinzu:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_imports)]

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/FormRecognizer/FormRecognizer.java) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.


Erstellen Sie in der **FormRecognizer**-Klasse der Anwendung Variablen für den Schlüssel und Endpunkt Ihrer Ressource.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_creds)]

> [!IMPORTANT]
> Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellte Formularerkennungsressource erfolgreich bereitgestellt wurde, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Schlüssel und Endpunkt finden Sie auf der Seite mit dem **Schlüssel und dem Endpunkt** der Ressource unter **Ressourcenverwaltung**. 
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](../../../cognitive-services-security.md).

Fügen Sie in der **main**-Methode der Anwendung Aufrufe für die Methoden hinzu, die in dieser Schnellstartanleitung verwendet werden. Diese werden später definiert. Sie müssen außerdem Verweise auf die URLs für Ihre Trainings- und Testdaten hinzufügen.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]
  
   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS-URL-Abruf":::
* Um die URL eines zu testenden Formulars zu erhalten, können Sie anhand der oben beschriebenen Schritte die SAS-URL eines einzelnen Dokuments im Blobspeicher abrufen. Sie können auch die URL eines Dokuments an einem anderen Speicherort verwenden.
* Verwenden Sie die obige Methode, um auch die URL eines Belegbilds zu erhalten.

#### <a name="version-20"></a>[Version 2.0](#tab/ga)
[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_maincalls)]
#### <a name="version-21-preview"></a>[Version 2.1 (Vorschau)](#tab/preview)
[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_maincalls)]

---



## <a name="object-model"></a>Objektmodell 

Mit der Formularerkennung können Sie zwei verschiedene Clienttypen erstellen. Der erste (`FormRecognizerClient`) wird zum Abfragen des Diensts nach erkannten Formularfeldern und -inhalten verwendet. Der zweite (`FormTrainingClient`) wird zum Erstellen und Verwalten von benutzerdefinierten Modellen verwendet, mit denen Sie die Erkennung verbessern können. 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` stellt Vorgänge für Folgendes bereit:

- Erkennen von Formularfeldern und -inhalten mithilfe von benutzerdefinierten Modellen, die zur Analyse Ihrer benutzerdefinierten Formulare trainiert wurden.  Diese Werte werden in einer Sammlung von `RecognizedForm`-Objekten zurückgegeben. Sehen Sie sich das Beispiel zum [Analysieren benutzerdefinierter Formulare](#analyze-forms-with-a-custom-model) an.
- Erkennen von Formularinhalten (einschließlich Tabellen, Zeilen und Wörtern), ohne dass ein Modell trainiert werden muss.  Der Formularinhalt wird in einer Sammlung von `FormPage`-Objekten zurückgegeben. Weitere Informationen finden Sie im Beispiel zum [Analysieren des Layouts](#analyze-layout).
- Erkennen allgemeiner Felder in US-amerikanischen Belegen unter Verwendung eines vorab trainierten Belegmodells für den Formularerkennungsdienst.  Diese Felder und Metadaten werden in einer Sammlung von `RecognizedForm`-Objekten zurückgegeben. Weitere Informationen finden Sie im Beispiel zum [Analysieren von Belegen](#analyze-receipts).

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` stellt Vorgänge für Folgendes bereit:

- Trainieren benutzerdefinierter Modelle, um alle Felder und Werte in Ihren benutzerdefinierten Formularen zu analysieren.  Ein `CustomFormModel`-Element wird zurückgegeben, das angibt, welche Formulartypen vom Modell analysiert und welche Felder für jeden Formtyp extrahiert werden.
- Trainieren benutzerdefinierter Modelle zum Analysieren bestimmter Felder und Werte, die Sie durch Bezeichnen Ihrer benutzerdefinierten Formulare angeben.  Ein `CustomFormModel`-Element wird zurückgegeben, das die vom Modell extrahierten Felder sowie die geschätzte Genauigkeit für jedes Feld angibt.
- Verwalten der in Ihrem Konto erstellten Modelle
- Kopieren eines benutzerdefinierten Modells aus einer Formularerkennungsressource in eine andere

> [!NOTE]
> Modelle können auch mithilfe einer grafischen Benutzeroberfläche trainiert werden, z. B. mit dem [Formularerkennungstool für die Bezeichnung](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie die folgenden Aufgaben mit der Formularerkennungs-Clientbibliothek für Java ausgeführt werden:

#### <a name="version-20"></a>[Version 2.0](#tab/ga)
* [Authentifizieren des Clients](#authenticate-the-client)
* [Analysieren des Layouts](#analyze-layout)
* [Analysieren von Belegen](#analyze-receipts)
* [Trainieren eines benutzerdefinierten Modells](#train-a-custom-model)
* [Analysieren von Formularen mit einem benutzerdefinierten Modell](#analyze-forms-with-a-custom-model)
* [Verwalten von benutzerdefinierten Modellen](#manage-your-custom-models)
#### <a name="version-21-preview"></a>[Version 2.1 (Vorschau)](#tab/preview)
* [Authentifizieren des Clients](#authenticate-the-client)
* [Analysieren des Layouts](#analyze-layout)
* [Analysieren von Belegen](#analyze-receipts)
* [Analysieren von Visitenkarten](#analyze-business-cards)
* [Analysieren von Rechnungen](#analyze-invoices)
* [Trainieren eines benutzerdefinierten Modells](#train-a-custom-model)
* [Analysieren von Formularen mit einem benutzerdefinierten Modell](#analyze-forms-with-a-custom-model)
* [Verwalten von benutzerdefinierten Modellen](#manage-your-custom-models)

---

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Fügen Sie oben in Ihrer **main**-Methode den folgenden Code hinzu. Hier authentifizieren Sie zwei Clientobjekte mithilfe der oben definierten Abonnementvariablen. Sie verwenden ein **AzureKeyCredential**-Objekt, damit Sie bei Bedarf den API-Schlüssel aktualisieren können, ohne neue Clientobjekte zu erstellen.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_auth)]

## <a name="analyze-layout"></a>Analysieren des Layouts

Mit der Formularerkennung können Sie Tabellen, Zeilen und Wörter in Dokumenten analysieren, ohne ein Modell trainieren zu müssen. Weitere Informationen zur Layoutextraktion finden Sie im [Konzeptleitfaden zum Layout](../../concept-layout.md).

Verwenden Sie die Methode **beginRecognizeContentFromUrl**, um die Inhalte einer Datei unter einer angegebenen URL zu analysieren.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_call)]

> [!TIP]
> Außerdem können Sie Inhalte aus einer lokalen Datei abrufen. Mehr dazu erfahren Sie bei den [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient)-Methoden, z. B. **beginRecognizeContent**. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) Szenarien zu lokalen Bildern.

Der zurückgegebene Wert ist eine Sammlung aus **FormPage**-Objekten: eines für jede Seite im übermittelten Dokument. Der folgende Code durchläuft diese Objekte und gibt die extrahierten Schlüssel-Wert-Paare und Tabellendaten aus.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_print)]
### <a name="output"></a>Output

```console
Get form content...
----Recognizing content ----
Has width: 8.500000 and height: 11.000000, measured with unit: inch.
Table has 2 rows and 6 columns.
Cell has text Invoice Number.
Cell has text Invoice Date.
Cell has text Invoice Due Date.
Cell has text Charges.
Cell has text VAT ID.
Cell has text 458176.
Cell has text 3/28/2018.
Cell has text 4/16/2018.
Cell has text $89,024.34.
Cell has text ET.
```


## <a name="analyze-invoices"></a>Analysieren von Rechnungen

#### <a name="version-20"></a>[Version 2.0](#tab/ga)

> [!IMPORTANT]
> Dieses Feature ist in der ausgewählten API-Version nicht verfügbar.

#### <a name="version-21-preview"></a>[Version 2.1 (Vorschau)](#tab/preview)

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe eines vorab trainierten Modells gängige Rechnungsfelder analysieren und extrahieren. Weitere Informationen zur Rechnungsanalyse finden Sie im [Konzeptleitfaden zu Rechnungen](../../concept-invoices.md).

Verwenden Sie die Methode `beginRecognizeInvoicesFromUrl`, um Rechnungen unter einer URL zu analysieren. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_call)]

> [!TIP]
> Auch lokale Rechnungen können analysiert werden. Sehen Sie sich dazu die Methoden vom Typ [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable) an (beispielsweise **beginRecognizeInvoices**). Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) Szenarien zu lokalen Bildern.

Der zurückgegebene Wert ist eine Sammlung mit Objekten vom Typ **RecognizedForm** (jeweils ein Objekt für jede Rechnung im Dokument). Der folgende Code verarbeitet die Rechnung unter dem angegebenen URI und gibt die wichtigsten Felder und Werte in der Konsole aus:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_print)]

---

## <a name="train-a-custom-model"></a>Trainieren eines benutzerdefinierten Modells

In diesem Abschnitt wird gezeigt, wie Sie ein Modell mit eigenen Daten trainieren. Ein trainiertes Modell kann strukturierte Daten ausgeben, die die Schlüssel-Wert-Beziehungen im ursprünglichen Formulardokument enthalten. Nachdem das Modell trainiert wurde, können Sie es testen, neu trainieren und schließlich verwenden, um Daten aus weiteren Formularen zuverlässig nach Ihren Bedürfnissen zu extrahieren.

> [!NOTE]
> Sie können Modelle auch mithilfe einer grafischen Benutzeroberfläche trainieren, z. B. dem [Formularerkennungstool für die Bezeichnung von Beispielen](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Trainieren eines Modells ohne Bezeichnungen

Trainieren Sie benutzerdefinierte Modelle, sodass alle Felder und Werte in Ihren benutzerdefinierten Formularen analysiert werden, ohne dass Sie die Trainingsdokumente manuell beschriften müssen.

Die folgende Methode trainiert ein Modell mit einem angegebenen Dokumentensatz und gibt den Status des Modells an der Konsole aus. 


[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_call)]

Das zurückgegebene **CustomFormModel**-Objekt enthält Informationen zu den Formulartypen, die vom Modell analysiert werden können, und zu den Feldern, die das Modell aus jedem Formulartyp extrahieren kann. Der folgende Codeblock gibt diese Informationen an der Konsole aus.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_print)]

Schließlich gibt die Methode die eindeutige ID des Modells aus.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_return)]


### <a name="output"></a>Output

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

### <a name="train-a-model-with-labels"></a>Trainieren eines Modells mit Bezeichnungen

Sie können benutzerdefinierte Modelle auch trainieren, indem Sie die Trainingsdokumente manuell bezeichnen. Das Training mit Bezeichnungen führt in einigen Szenarien zu einer besseren Leistung. Zum Training mit Bezeichnungen benötigen Sie zusätzlich zu den Trainingsdokumenten spezielle Informationsdateien mit Bezeichnungen ( *\<filename\>.pdf.labels.json*) in Ihrem Blobspeichercontainer. Das [Formularerkennungstool für die Bezeichnung von Beispielen](../../quickstarts/label-tool.md) bietet eine Benutzeroberfläche, auf der Sie diese Bezeichnungsdateien erstellen können. Sobald Sie darüber verfügen, können Sie die **beginTraining**-Methode mit dem auf `true` festgelegten Parameter *useTrainingLabels* aufrufen.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_call)]


Das zurückgegebene **CustomFormModel** gibt die Felder an, die das Modell extrahieren kann, und bietet Informationen zur geschätzten Genauigkeit in jedem Feld. Der folgende Codeblock gibt diese Informationen an der Konsole aus.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_print)]


### <a name="output"></a>Output

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

## <a name="analyze-forms-with-a-custom-model"></a>Analysieren von Formularen mit einem benutzerdefinierten Modell

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe von Modellen, die Sie mit Ihren eigenen Formularen trainiert haben, Schlüssel-Wert-Informationen und andere Inhalte aus Ihren benutzerdefinierten Formulartypen extrahieren.

> [!IMPORTANT]
> Um dieses Szenario zu implementieren, müssen Sie bereits ein Modell trainiert haben, sodass Sie seine ID an die unten stehende Methode übergeben können. Weitere Informationen finden Sie im Abschnitt [Trainieren eines Modells](#train-a-model-without-labels).

Sie verwenden die **beginRecognizeCustomFormsFromUrl**-Methode. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_call)]

> [!TIP]
> Sie können auch eine lokale Datei analysieren. Mehr dazu erfahren Sie bei den [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient)-Methoden, z. B. **beginRecognizeCustomForms**. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) Szenarien zu lokalen Bildern.

Der zurückgegebene Wert ist eine Sammlung aus **RecognizedForm**-Objekten: eines für jede Seite im übermittelten Dokument. Mit dem folgenden Code werden die Analyseergebnisse auf der Konsole ausgegeben. Der Code gibt jedes erkannte Feld und den zugehörigen Wert sowie eine Zuverlässigkeitsbewertung aus.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_print)]


### <a name="output"></a>Output

```console
Analyze PDF form...
----------- Recognized custom form info for page 0 -----------
Form type: form-0
Field 'field-0' has label 'Address:' with a confidence score of 0.91.
Field 'field-1' has label 'Invoice For:' with a confidence score of 1.00.
Field 'field-2' has label 'Invoice Number' with a confidence score of 1.00.
Field 'field-3' has label 'Invoice Date' with a confidence score of 1.00.
Field 'field-4' has label 'Invoice Due Date' with a confidence score of 1.00.
Field 'field-5' has label 'Charges' with a confidence score of 1.00.
Field 'field-6' has label 'VAT ID' with a confidence score of 1.00.
```

## <a name="analyze-receipts"></a>Analysieren von Belegen

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe eines vorab trainierten Belegmodells gängige Felder in US-Belegen analysieren und extrahieren. Weitere Informationen zur Beleganalyse finden Sie im [Konzeptleitfaden zu Belegen](../../concept-receipts.md).

Verwenden Sie die Methode **beginRecognizeReceiptsFromUrl**, um Belege aus einem URI zu analysieren. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_call)]

> [!TIP]
> Auch lokale Belegbilder können analysiert werden. Mehr dazu erfahren Sie bei den [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable)-Methoden, z. B. **beginRecognizeReceipts**. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) Szenarien zu lokalen Bildern.

Der zurückgegebene Wert ist eine Sammlung aus **RecognizedReceipt**-Objekten: eines für jede Seite im übermittelten Dokument. Der nächste Codeblock durchläuft die Belege und gibt ihre Details an der Konsole aus.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print)]

Der nächste Codeblock durchläuft die einzelnen erkannten Elemente im Beleg und gibt die Details an der Konsole aus.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print_items)]

### <a name="output"></a>Output 

```console
Analyze receipt...
----------- Recognized Receipt page 0 -----------
Merchant Name: Contoso Contoso, confidence: 0.62
Merchant Address: 123 Main Street Redmond, WA 98052, confidence: 0.99
Transaction Date: 2020-06-10, confidence: 0.90
Receipt Items:
Name: Cappuccino, confidence: 0.96s
Quantity: null, confidence: 0.957s]
Total Price: 2.200000, confidence: 0.95
Name: BACON & EGGS, confidence: 0.94s
Quantity: null, confidence: 0.927s]
Total Price: null, confidence: 0.93
```

## <a name="analyze-business-cards"></a>Analysieren von Visitenkarten

#### <a name="version-20"></a>[Version 2.0](#tab/ga)

> [!IMPORTANT]
> Dieses Feature ist in der ausgewählten API-Version nicht verfügbar.

#### <a name="version-21-preview"></a>[Version 2.1 (Vorschau)](#tab/preview)

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe eines vorab trainierten Modells gängige Felder englischsprachiger Visitenkarten analysieren und extrahieren. Weitere Informationen zur Analyse von Visitenkarten finden Sie im [Konzeptleitfaden zu Visitenkarten](../../concept-business-cards.md).

Verwenden Sie die Methode `beginRecognizeBusinessCardsFromUrl`, um Visitenkarten unter einer URL zu analysieren. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_call)]

> [!TIP]
> Sie können auch lokale Visitenkartenbilder analysieren. Sehen Sie sich dazu die Methoden vom Typ [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable) an (beispielsweise **beginRecognizeBusinessCards**). Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) Szenarien zu lokalen Bildern.

Der zurückgegebene Wert ist eine Sammlung mit Objekten vom Typ **RecognizedForm** (jeweils ein Objekt für jede Karte im Dokument). Der folgende Code verarbeitet die Visitenkarte unter dem angegebenen URI und gibt die wichtigsten Felder und Werte in der Konsole aus:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_print)]

---

## <a name="manage-custom-models"></a>Verwalten benutzerdefinierter Modelle

In diesem Abschnitt wird veranschaulicht, wie Sie die in Ihrem Konto gespeicherten benutzerdefinierten Modelle verwalten. Der folgende Code verarbeitet beispielsweise sämtliche Modellverwaltungstasks in einer einzelnen Methode. Kopieren Sie zunächst die nachstehende Methodensignatur:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage)]


### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Überprüfen der Anzahl von Modellen im FormRecognizer-Ressourcenkonto

Der folgende Codeblock überprüft, wie viele Modelle in Ihrem Formularerkennungskonto gespeichert sind, und vergleicht diese Zahl mit dem Kontolimit.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_count)]


#### <a name="output"></a>Output 

```console
The account has 12 custom models, and we can have at most 250 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Auflisten der zurzeit im Ressourcenkonto gespeicherten Modelle

Der folgende Codeblock listet die aktuell in Ihrem Konto vorhandenen Modelle auf und gibt ihre Details an der Konsole aus.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_list)]


#### <a name="output"></a>Output 

Diese Antwort wurde zur besseren Lesbarkeit gekürzt.

```console
We have following models in the account:
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Status: ready
Training started on: 2020-06-04T18:33:08Z
Training completed on: 2020-06-04T18:33:10Z
Custom Model Form type: form-0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Custom Model Accuracy: 1.00
Field Text: invoice date
Field Accuracy: 1.00
Field Text: invoice number
Field Accuracy: 1.00
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Löschen eines Modells aus dem Ressourcenkonto

Sie können ein Modell auch aus Ihrem Konto löschen, indem Sie auf die ID des Modells verweisen.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_delete)]



## <a name="run-the-application"></a>Ausführen der Anwendung

Navigieren Sie zurück zum Projekthauptverzeichnis. Erstellen Sie anschließend die App mit dem folgenden Befehl:

```console
gradle build
```

Führen Sie die Anwendung mit dem Ziel `run` aus:

```console
gradle run
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Problembehandlung

Clients der Formularerkennung lösen Ausnahmen des Typs `ErrorResponseException` aus. Wenn Sie z. B. versuchen, eine ungültige Dateiquellen-URL anzugeben, wird eine `ErrorResponseException` mit einem Hinweis auf die Fehlerursache ausgelöst. Im folgenden Codeausschnitt wird der Fehler ordnungsgemäß behandelt, indem die Ausnahme abgefangen wird und zusätzliche Fehlerinformationen angezeigt werden.

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>Aktivieren der Clientprotokollierung

Die Azure SDKs für Java bieten eine konsistente Protokollierungsumgebung zur Unterstützung beim Troubleshooting von Anwendungsfehlern und zur Beschleunigung der Fehlerbehebung. Die erstellten Protokolle erfassen den Flow einer Anwendung, bevor sie den Endzustand erreichen. Dies trägt zur Ermittlung der Grundursache bei. Eine Anleitung zum Aktivieren der Protokollierung finden Sie im [Wiki-Artikel zur Protokollierung](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK).

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie die Formularerkennungs-Clientbibliothek für Java verwendet, um auf unterschiedliche Weise Modelle zu trainieren und Formulare zu analysieren. Lesen Sie im Anschluss die Tipps zum Erstellen eines besseren Trainingsdatasets und zum Generieren genauerer Modelle.

> [!div class="nextstepaction"]
> [Erstellen eines Trainingsdatasets](../../build-training-data-set.md)

* [Was ist die Formularerkennung?](../../overview.md)
* Den Beispielcode aus diesem Leitfaden (und vieles mehr) finden Sie auf [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples).

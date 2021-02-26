---
title: 'Schnellstart: Beschriften von Formularen, Trainieren eines Modells und Analysieren von Formularen mithilfe des Tools für die Beschriftung von Beispielen – Formularerkennung'
titleSuffix: Azure Cognitive Services
description: In dieser Schnellstartanleitung verwenden Sie das Formularerkennungstool für die Beschriftung von Beispielen, um Formulardokumente manuell zu beschriften. Danach trainieren Sie ein benutzerdefiniertes Modell für die Dokumentverarbeitung mit den beschrifteten Dokumenten und verwenden das Modell, um Schlüssel-Wert-Paare zu extrahieren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: Verarbeiten von Dokumenten
ms.openlocfilehash: 9642f9ce51cd3eb90344f96bc099da7adea93022
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364789"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Trainieren eines Formularerkennungsmodells mit Beschriftungen mithilfe des Tools für die Beschriftung von Beispielen

In dieser Schnellstartanleitung verwenden Sie die Formularerkennungs-REST-API zusammen mit dem Tool für die Beschriftung von Beispielen, um ein benutzerdefiniertes Modell für die Dokumentverarbeitung mit manuell beschrifteten Daten zu trainieren. Weitere Informationen zum überwachten Lernen mit der Formularerkennung finden Sie im Abschnitt [Trainieren mit Beschriftungen](../overview.md#train-with-labels) der Übersicht.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prerequisites"></a>Voraussetzungen

Für diesen Schnellstart benötigen Sie Folgendes:

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Erstellen einer Formularerkennungsressource"  target="_blank"> im Azure-Portal eine Formularerkennungsressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Formularerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.
* Einen Satz mit mindestens sechs Formularen desselben Typs. Diese Daten verwenden Sie zum Trainieren des Modells und zum Testen eines Formulars. Sie können ein [Beispieldataset](https://go.microsoft.com/fwlink/?linkid=2090451) für diese Schnellstartanleitung verwenden (*sample_data.zip* herunterladen und extrahieren). Laden Sie die Trainingsdateien in das Stammverzeichnis eines Blobspeichercontainers in einem Azure Storage-Konto mit der Leistungsstufe „Standard“ hoch.

## <a name="create-a-form-recognizer-resource"></a>Erstellen einer Formularerkennungsressource

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="try-it-out"></a>Ausprobieren

Um das Tool für die Beschriftung von Beispielen für die Formularerkennung online auszuprobieren, wechseln Sie zur [FOTT-Website](https://fott-preview.azurewebsites.net/).

# <a name="v20"></a>[v2.0](#tab/v2-0)
> [!div class="nextstepaction"]
> [Vordefinierte Modelle ausprobieren](https://fott.azurewebsites.net/)

# <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1)
> [!div class="nextstepaction"]
> [Vordefinierte Modelle ausprobieren](https://fott-preview.azurewebsites.net/)

---

Zum Ausprobieren des Formularerkennungsdiensts benötigen Sie ein Azure-Abonnement (kann [hier](https://azure.microsoft.com/free/cognitive-services) kostenlos erstellt werden), einen Endpunkt für eine [Formularerkennungsressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) und einen entsprechenden Schlüssel. 


## <a name="set-up-the-sample-labeling-tool"></a>Einrichten des Tools für die Beschriftung von Beispielen

Sie verwenden die Docker-Engine, um das Tool für die Beschriftung von Beispielen auszuführen. Gehen Sie folgendermaßen vor, um den Docker-Container einzurichten. Eine Einführung in Docker und Container finden Sie in der [Docker-Übersicht](https://docs.docker.com/engine/docker-overview/).

> [!TIP]
> Das OCR Form Labeling Tool steht auch als Open-Source-Projekt auf GitHub zur Verfügung. Bei dem Tool handelt es sich um eine mit React + Redux erstellte TypeScript-Webanwendung. Besuchen Sie das Repository [OCR Form Labeling Tool](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md#run-as-web-application), wenn Sie weitere Informationen benötigen oder sich beteiligen möchten. Wenn Sie Tool online ausprobieren möchten, besuchen Sie die [FOTT-Website](https://fott.azurewebsites.net/).   

1. Installieren Sie zunächst Docker auf einem Hostcomputer. In dieser Anleitung wird veranschaulicht, wie Sie den lokalen Computer als Host verwenden. Wenn Sie einen Docker-Hostingdienst in Azure verwenden möchten, hilft Ihnen die Anleitung zum [Bereitstellen des Tools für die Beschriftung von Beispielen](../deploy-label-tool.md) weiter. 

   Der Hostcomputer muss die folgenden Hardwareanforderungen erfüllen:

    | Container | Minimum | Empfohlen|
    |:--|:--|:--|
    |Tool für die Beschriftung von Beispielen|2 Kerne, 4 GB Arbeitsspeicher|4 Kerne, 8 GB Arbeitsspeicher|

   Installieren Sie Docker auf Ihrem Computer, indem Sie die passenden Anweisungen für Ihr Betriebssystem befolgen: 
   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [macOS](https://docs.docker.com/docker-for-mac/)
   * [Linux](https://docs.docker.com/install/)



1. Rufen Sie mit dem `docker pull`-Befehl den Container für das Tool für die Beschriftung von Beispielen ab.

    # <a name="v20"></a>[v2.0](#tab/v2-0)    
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
    # <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1)    
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview
    ```

    ---

1. Jetzt können Sie mit `docker run` den Container ausführen.

    # <a name="v20"></a>[v2.0](#tab/v2-0)    
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```
    # <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1)    
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview eula=accept    
    ```

    --- 

   Dieser Befehl macht das Tool für die Beschriftung von Beispielen über einen Webbrowser verfügbar. Gehe zu `http://localhost:3000`.

> [!NOTE]
> Sie können auch die Formularerkennungs-REST-API verwenden, um Dokumente zu beschriften und Modelle zu trainieren. Informationen zum Trainieren und Analysieren mit der REST-API finden Sie unter [Trainieren mit Beschriftungen mit der REST-API und Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md).

## <a name="set-up-input-data"></a>Einrichten von Eingabedaten

Stellen Sie zunächst sicher, dass alle Trainingsdokumente im selben Format vorliegen. Wenn Ihre Formulare unterschiedliche Formate aufweisen, sortieren Sie sie in Unterordner für jeweils ein Format. Beim Trainieren müssen Sie für die API einen der Unterordner angeben.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Konfigurieren der Ressourcenfreigabe zwischen verschiedenen Ursprüngen (CORS)

Aktivieren Sie CORS in Ihrem Speicherkonto. Wählen Sie im Azure-Portal Ihr Speicherkonto aus, und klicken Sie im linken Bereich auf die Registerkarte **CORS**. Geben Sie auf der untersten Zeile die folgenden Werte ein. Klicken Sie dann oben auf **Speichern**.

* Zulässige Ursprünge = * 
* Zulässige Methoden = \[alle auswählen\]
* Zulässige Header = *
* Verfügbar gemachte Header = * 
* Max. Alter = 200

> [!div class="mx-imgBorder"]
> ![Einrichtung von CORS im Azure-Portal](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Herstellen einer Verbindung mit dem Tool für die Beschriftung von Beispielen

Das Tool für die Beschriftung von Beispielen stellt eine Verbindung mit einer Quelle (in der sich Ihre Originalformulare befinden) und einem Ziel (in das die erstellten Beschriftungen und Ausgabedaten exportiert werden) her.

Verbindungen können projektübergreifend eingerichtet und freigegeben werden. Dabei wird ein erweiterbares Anbietermodell verwendet, sodass Sie ganz einfach neue Anbieter von Quellen und Zielen hinzufügen können.

Um eine neue Verbindung zu erstellen, klicken Sie in der linken Navigationsleiste auf das Symbol für **Neue Verbindung** (Stecker).

Geben Sie die folgenden Werte in die Felder ein:

* **Anzeigename**: der Anzeigename der Verbindung.
* **Beschreibung**: die Beschreibung Ihres Projekts.
* **SAS-URL**: die Shared Access Signature-URL (SAS) Ihres Azure Blob Storage-Containers. [!INCLUDE [get SAS URL](../includes/sas-instructions.md)]

   :::image type="content" source="../media/quickstarts/get-sas-url.png" alt-text="SAS-URL-Abruf":::

:::image type="content" source="../media/label-tool/connections.png" alt-text="Verbindungseinstellungen des Tools für die Beschriftung von Beispielen":::


## <a name="create-a-new-project"></a>Erstellen eines neuen Projekts

Im Tool für die Beschriftung von Beispielen werden Ihre Konfigurationen und Einstellungen in Projekten gespeichert. Erstellen Sie ein neues Projekt, und geben Sie die folgenden Werte in die Felder ein:

* **Anzeigename**: der Anzeigename des Projekts.
* **Sicherheitstoken**: Einige Projekteinstellungen können vertrauliche Werte wie z. B. API-Schlüssel oder andere gemeinsam genutzte Geheimnisse enthalten. Jedes Projekt generiert ein Sicherheitstoken, das zum Verschlüsseln und Entschlüsseln von vertraulichen Projekteinstellungen verwendet werden kann. Sie finden die Sicherheitstoken in den Anwendungseinstellungen, indem Sie unten auf der linken Navigationsleiste auf das Zahnradsymbol klicken.
* **Quellverbindung**: Die von Ihnen im vorherigen Schritt erstellte Azure Blob Storage-Verbindung, die Sie für dieses Projekt verwenden möchten.
* **Ordnerpfad** (optional): Wenn Ihre Quellformulare in einem Ordner im Blobcontainer gespeichert sind, geben Sie hier den Ordnernamen an.
* **URI des Formularerkennungsdiensts**: Die URL Ihres Formularerkennungs-Endpunkts.
* **API-Schlüssel**: Der Schlüssel Ihres Formularerkennungsabonnements.
* **Beschreibung** (optional): Projektbeschreibung.

:::image type="content" source="../media/label-tool/new-project.png" alt-text="Seite mit neuem Projekt im Tool für die Beschriftung von Beispielen":::

## <a name="label-your-forms"></a>Beschriften Ihrer Formulare

Wenn Sie ein Projekt erstellen oder öffnen, wird das Hauptfenster des Beschriftungs-Editors geöffnet. Der Beschriftungs-Editor besteht aus drei Teilen:

* Ein Vorschaubereich, dessen Größe angepasst werden kann und der eine scrollbare Liste mit Formularen aus der Quellverbindung enthält.
* Der Hauptbereich des Editors, in dem Sie Beschriftungen anwenden können.
* Der Bearbeitungsbereich des Editors, in dem Sie Beschriftungen ändern, sperren, neu anordnen und löschen können. 

### <a name="identify-text-elements"></a>Identifizieren von Textelementen

Klicken Sie im linken Bereich auf **OCR in allen Dateien ausführen**, um Textlayoutinformationen für jedes Dokument abzurufen. Das Beschriftungstool zeichnet einen Begrenzungsrahmen um jedes Textelement.

Es zeigt außerdem, welche Tabellen automatisch extrahiert wurden. Klicken Sie auf das Tabellen-/Rastersymbol auf der linken Seite des Dokuments, um die extrahierte Tabelle anzuzeigen. Da in dieser Schnellstartanleitung der Tabelleninhalt automatisch extrahiert wird, versehen Sie ihn nicht mit Bezeichnungen, sondern verlassen sich stattdessen auf die automatisierte Extraktion.

:::image type="content" source="../media/label-tool/table-extraction.png" alt-text="Tabellenvisualisierung im Tool für die Beschriftung von Beispielen":::

### <a name="apply-labels-to-text"></a>Anwenden von Beschriftungen auf Text

Als Nächstes erstellen Sie Tags (Beschriftungen) und wenden sie auf die Textelemente an, die das Modell analysieren soll.

# <a name="v20"></a>[v2.0](#tab/v2-0)  
1. Verwenden Sie zuerst den Bearbeitungsbereich des Editors, um die Tags zu erstellen, die Sie identifizieren möchten.
   1. Klicken Sie auf **+** , um ein neues Tag zu erstellen.
   1. Geben Sie den Tagnamen ein.
   1. Drücken Sie die EINGABETASTE, um das Tag zu speichern.
1. Klicken Sie im Hauptbereich des Editors, um Wörter in den markierten Textelementen auszuwählen.
1. Klicken Sie auf das Tag, das Sie anwenden möchten, oder drücken Sie die entsprechende Taste auf der Tastatur. Die Zifferntasten sind als Schnellzugriffstasten für die ersten zehn Tags zugewiesen. Sie können die Beschriftungen mithilfe der nach oben und unten weisenden Pfeilsymbole im Bearbeitungsbereich neu anordnen.
    > [!Tip]
    > Beachten Sie beim Beschriften Ihrer Formulare die folgenden Tipps.
    > * Sie können auf jedes ausgewählte Element nur ein Tag anwenden.
    > * Jedes Tag kann nur einmal pro Seite angewendet werden. Wenn ein Wert in demselben Formular mehrfach erscheint, sollten Sie für jede Instanz andere Tags erstellen. Beispiel: „rechnung 1“, „rechnung 2“ usw.
    > * Tags können nicht seitenübergreifend genutzt werden.
    > * Beschriften Sie Werte so, wie sie im Formular vorkommen. Versuchen Sie nicht, einen Wert mit zwei unterschiedlichen Tags in zwei Teile zu unterteilen. Ein Adressfeld sollte beispielsweise auch dann nur mit einem Tag beschriftet werden, wenn es über mehrere Zeilen verläuft.
    > * Fügen Sie in Ihre beschrifteten Felder keine Schlüssel ein, sondern nur die Werte.
    > * Die Tabellendaten sollten automatisch erkannt werden und sind in der fertigen JSON-Ausgabedatei enthalten. Falls das Modell nicht Ihre gesamten Tabellendaten erkennen kann, können Sie diese Felder auch manuell beschriften. Verwenden Sie für jede Zelle der Tabelle eine andere Beschriftung. Falls Ihre Formulare über Tabellen mit unterschiedlicher Anzahl von Zeilen verfügen, sollten Sie sicherstellen, dass Sie mindestens ein Formular mit der größtmöglichen Tabelle beschriften.
    > * Verwenden Sie die Schaltflächen rechts neben dem **+** -Zeichen, um Ihre Tags zu suchen, umzubenennen, neu anzuordnen und zu löschen.
    > * Um ein angewendetes Tag zu entfernen, ohne das Tag selbst zu löschen, wählen Sie das Tagrechteck in der Dokumentansicht aus und drücken die Taste ENTF.


# <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1) 
1. Verwenden Sie zuerst den Bearbeitungsbereich des Editors, um die Tags zu erstellen, die Sie identifizieren möchten.
   1. Klicken Sie auf **+** , um ein neues Tag zu erstellen.
   1. Geben Sie den Tagnamen ein.
   1. Drücken Sie die EINGABETASTE, um das Tag zu speichern.
1. Klicken Sie im Hauptbereich des Editors, um Wörter in den markierten Textelementen auszuwählen. In der API _v2.1 preview.2_ können Sie auch klicken, um _Auswahlmarkierungen_ wie Optionsfelder und Kontrollkästchen als Schlüssel-Wert-Paare auszuwählen. Die Formularerkennung ermittelt, ob für die Auswahlmarkierung der Wert „ausgewählt“ oder „nicht ausgewählt“ angegeben ist.
1. Klicken Sie auf das Tag, das Sie anwenden möchten, oder drücken Sie die entsprechende Taste auf der Tastatur. Die Zifferntasten sind als Schnellzugriffstasten für die ersten zehn Tags zugewiesen. Sie können die Beschriftungen mithilfe der nach oben und unten weisenden Pfeilsymbole im Bearbeitungsbereich neu anordnen.
    > [!Tip]
    > Beachten Sie beim Beschriften Ihrer Formulare die folgenden Tipps.
    > * Sie können auf jedes ausgewählte Element nur ein Tag anwenden.
    > * Jedes Tag kann nur einmal pro Seite angewendet werden. Wenn ein Wert in demselben Formular mehrfach erscheint, sollten Sie für jede Instanz andere Tags erstellen. Beispiel: „rechnung 1“, „rechnung 2“ usw.
    > * Tags können nicht seitenübergreifend genutzt werden.
    > * Beschriften Sie Werte so, wie sie im Formular vorkommen. Versuchen Sie nicht, einen Wert mit zwei unterschiedlichen Tags in zwei Teile zu unterteilen. Ein Adressfeld sollte beispielsweise auch dann nur mit einem Tag beschriftet werden, wenn es über mehrere Zeilen verläuft.
    > * Fügen Sie in Ihre beschrifteten Felder keine Schlüssel ein, sondern nur die Werte.
    > * Die Tabellendaten sollten automatisch erkannt werden und sind in der fertigen JSON-Ausgabedatei enthalten. Falls das Modell nicht Ihre gesamten Tabellendaten erkennen kann, können Sie diese Felder auch manuell beschriften. Verwenden Sie für jede Zelle der Tabelle eine andere Beschriftung. Falls Ihre Formulare über Tabellen mit unterschiedlicher Anzahl von Zeilen verfügen, sollten Sie sicherstellen, dass Sie mindestens ein Formular mit der größtmöglichen Tabelle beschriften.
    > * Verwenden Sie die Schaltflächen rechts neben dem **+** -Zeichen, um Ihre Tags zu suchen, umzubenennen, neu anzuordnen und zu löschen.
    > * Um ein angewendetes Tag zu entfernen, ohne das Tag selbst zu löschen, wählen Sie das Tagrechteck in der Dokumentansicht aus und drücken die Taste ENTF.


---

:::image type="content" source="../media/label-tool/main-editor-2-1.png" alt-text="Haupt-Editor-Fenster des Tools für die Beschriftung von Beispielen":::


Führen Sie die oben genannten Schritte aus, um mindestens fünf Ihrer Formulare zu beschriften.

### <a name="specify-tag-value-types"></a>Angeben von Tagwerttypen

Optional können Sie den erwarteten Datentyp für jedes Tag festlegen. Öffnen Sie das Kontextmenü rechts neben einem Tag, und wählen Sie im Menü einen Typ aus. Diese Funktion ermöglicht es dem Erkennungsalgorithmus, bestimmte Annahmen zu treffen, die die Genauigkeit der Texterkennung verbessern. Außerdem wird sichergestellt, dass die erkannten Werte in der endgültigen JSON-Ausgabe in einem standardisierten Format zurückgegeben werden. Informationen zum Werttyp werden in der Datei *fields.json* unter demselben Pfad wie Ihre Beschriftungsdateien gespeichert.

> [!div class="mx-imgBorder"]
> ![Werttypauswahl mit dem Tool für die Beschriftung von Beispielen](../media/whats-new/value-type.png)

Derzeit werden die folgenden Werttypen und Variationen unterstützt:
* `string`
    * Standardwert, `no-whitespaces`, `alphanumeric`
* `number`
    * Standardwert, `currency`
* `date` 
    * Standardwert, `dmy`, `mdy`, `ymd`
* `time`
* `integer`
* `selectionMark`: _Neu in v2.1-preview.1_

> [!NOTE]
> Siehe diese Regeln für die Datumsformatierung:
> 
> Sie müssen ein Format (`dmy`, `mdy`, `ymd`) angeben, damit die Datumsformatierung funktioniert.
>
> Die folgenden Zeichen können als Datumstrennzeichen verwendet werden: `, - / . \`. Leerzeichen können nicht als Trennzeichen verwendet werden. Beispiel:
> * 01.01.2020
> * 01-01-2020
> * 01/01/2020
>
> Tag und Monat können jeweils ein- oder zweistellig, die Jahreszahl zwei- oder vierstellig angegeben werden:
> * 1-1-2020
> * 1-01-20
>
> Wenn eine Datumszeichenfolge acht Ziffern hat, ist das Trennzeichen optional:
> * 01012020
> * 01 01 2020
>
> Der Monat kann auch als vollständiger oder Kurzname angegeben werden. Wenn der Name verwendet wird, sind Trennzeichen optional. Dieses Format kann jedoch weniger genau erkannt werden als andere.
> * 01/Jan/2020
> * 01Jan2020
> * 01 Jan 2020

## <a name="train-a-custom-model"></a>Trainieren eines benutzerdefinierten Modells

Klicken Sie im linken Bereich auf das Symbol „Trainieren“, um die Seite „Training“ zu öffnen. Klicken Sie dann auf die Schaltfläche **Trainieren**, um mit dem Training des Modells zu beginnen. Sobald der Trainingsprozess abgeschlossen ist, werden folgende Informationen angezeigt:

* **Modell-ID**: Die ID des Modells, das erstellt und trainiert wurde. Jeder Trainingsaufruf erstellt ein neues Modell mit eigener ID. Kopieren Sie diese Zeichenfolge an einen sicheren Speicherort. Sie werden sie benötigen, wenn Sie Vorhersageaufrufe über die [REST-API](./client-library.md?pivots=programming-language-rest-api) oder [Clientbibliothek](./client-library.md) ausführen möchten.
* **Durchschnittliche Genauigkeit**: Die durchschnittliche Genauigkeit des Modells. Sie können die Modellgenauigkeit verbessern, indem Sie weitere Formulare beschriften und erneut ein Training ausführen, um ein neues Modell zu erstellen. Wir empfehlen, zunächst fünf Formulare zu beschriften und dann bei Bedarf weitere Formulare hinzuzufügen.
* Die Liste der Beschriftungen und die geschätzte Genauigkeit für jede Beschriftung.


:::image type="content" source="../media/label-tool/train-screen.png" alt-text="Trainingsansicht":::

Untersuchen Sie nach Abschluss des Trainings den Wert **Durchschnittliche Genauigkeit**. Wenn dieser Wert niedrig ist, sollten Sie weitere Eingabedokumente hinzufügen und die oben beschriebenen Schritte wiederholen. Die von Ihnen bereits beschrifteten Dokumente verbleiben im Projektindex.

> [!TIP]
> Sie können den Trainingsprozess auch mit einem REST-API-Aufruf ausführen. Informationen dazu finden Sie unter [Trainieren mit Beschriftungen mit Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md).

## <a name="compose-trained-models"></a>Erstellen trainierter Modelle

# <a name="v20"></a>[v2.0](#tab/v2-0)  

Dieses Feature ist derzeit in der Vorschauversion v2.1 verfügbar. 

# <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1) 

Mit der Modellerstellung können Sie bis zu 100 Modelle mit einer einzelnen Modell-ID erstellen. Wenn Sie mit dieser zusammengesetzten Modell-ID die Option zum Analysieren aufrufen, klassifiziert die Formularerkennung zunächst das übermittelte Formular, gleicht es mit dem am ehesten übereinstimmenden Modell ab und gibt dann Ergebnisse für dieses Modell zurück. Dies ist nützlich, wenn eingehende Formulare zu einer von mehreren Vorlagen gehören können.

Um Modelle im Tool für die Beschriftung von Beispielen zu erstellen, klicken Sie auf der linken Seite auf das Symbol zum Erstellen von Modellen (zusammengeführter Pfeil). Wählen Sie auf der linken Seite die Modelle aus, die Sie zusammen erstellen möchten. Modelle mit dem Pfeilsymbol sind bereits zusammengesetzte Modelle. Klicken Sie auf die Schaltfläche „Erstellen“. Geben Sie im Popupfenster einen Namen für Ihr neu erstelltes Modell ein, und klicken Sie auf „Erstellen“. Nach Abschluss des Vorgangs sollte das neue erstellte Modell in der Liste angezeigt werden. 

:::image type="content" source="../media/label-tool/model-compose.png" alt-text="Benutzeroberflächenansicht für die Modellerstellung":::

---

## <a name="analyze-a-form"></a>Analysieren eines Formulars 

Klicken Sie links auf das Symbol für die Vorhersage (Glühbirne), um Ihr Modell zu testen. Laden Sie ein Formulardokument hoch, das Sie im Trainingsprozess nicht verwendet haben. Klicken Sie dann rechts auf die Schaltfläche **Vorhersage**, um die Schlüssel-Wert-Vorhersagen für das Formular zu erhalten. Das Tool wendet Beschriftungen in Begrenzungsrahmen an und meldet die Konfidenz jeder Beschriftung.

> [!TIP]
> Sie können die Analyse-API auch mit einem REST-Aufruf ausführen. Informationen dazu finden Sie unter [Trainieren mit Beschriftungen mit Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md).

## <a name="improve-results"></a>Verbessern der Ergebnisse

Je nach gemeldeter Genauigkeit können Sie weitere Trainingsiterationen durchführen, um das Modell zu verbessern. Untersuchen Sie nach jeder Vorhersage die Konfidenzwerte für jede angewendete Beschriftung. Wenn der durchschnittliche Genauigkeitswert im Training hoch war, aber die Konfidenzscores niedrig sind (oder die Ergebnisse ungenau sind), sollten Sie die für die Vorhersage verwendete Datei zum Trainingssatz hinzufügen, sie beschriften, und das Training erneut durchführen.

Die gemeldete durchschnittliche Genauigkeit, die Konfidenzscores und die tatsächliche Genauigkeit können inkonsistent sein, wenn sich die analysierten Dokumente von den im Training verwendeten Dokumenten unterscheiden. Denken Sie daran, dass Dokumente für das menschliche Auge gleich aussehen können, für das KI-Modell aber unterschiedlich sind. Ein Beispiel: Sie führen das Training mit einem Formulartyp durch, der zwei Variationen aufweist. Der Trainingssatz besteht zu 20 % aus Variation A und zu 80 % aus Variation B. In diesem Fall ist es wahrscheinlich, dass die Konfidenzscores für Variation A bei der Vorhersage niedriger sind.

## <a name="save-a-project-and-resume-later"></a>Speichern und späteres Fortsetzen eines Projekts

Um Ihr Projekt zu einem anderen Zeitpunkt oder in einem anderen Browser fortzusetzen, müssen Sie das Sicherheitstoken des Projekts speichern und später erneut eingeben. 

### <a name="get-project-credentials"></a>Abrufen der Projektanmeldeinformationen
Wechseln Sie zur Seite mit den Projekteinstellungen (Schiebereglersymbol), und merken Sie sich den Namen des Sicherheitstokens. Wechseln Sie dann zu Ihren Anwendungseinstellungen (Zahnradsymbol), in denen alle Sicherheitstoken in Ihrer aktuellen Browserinstanz angezeigt werden. Suchen Sie nach dem Sicherheitstoken Ihres Projekts, und kopieren Sie den Namen und Schlüsselwert an einen sicheren Speicherort.

### <a name="restore-project-credentials"></a>Wiederherstellen der Projektanmeldeinformationen
Wenn Sie Ihr Projekt fortsetzen möchten, müssen Sie zunächst eine Verbindung mit demselben Blobspeichercontainer herstellen. Wiederholen Sie dazu die oben genannten Schritte. Wechseln Sie zur Seite mit den Anwendungseinstellungen (Zahnradsymbol), und überprüfen Sie, ob sich das Sicherheitstoken Ihres Projekts dort befindet. Wenn dies nicht der Fall ist, fügen Sie ein neues Sicherheitstoken hinzu, und fügen Sie den Namen und Schlüssel des Tokens aus dem vorherigen Schritt ein. Klicken Sie dann auf „Einstellungen speichern“. 

### <a name="resume-a-project"></a>Fortsetzen eines Projekts

Wechseln Sie abschließend auf die Hauptseite (Haussymbol), und klicken Sie auf „Cloudprojekt öffnen“. Wählen Sie die Verbindung mit dem Blobspeicher und dann die Datei vom Typ *.fott* Ihres Projekts aus. Die Anwendung lädt sämtliche Projekteinstellungen, weil das Sicherheitstoken vorliegt.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie erfahren, wie Sie das Formularerkennungstool für die Beschriftung von Beispielen verwenden, um ein Modell mit manuell beschrifteten Daten zu trainieren. Wenn Sie ein eigenes Hilfsprogramm zum Beschriften von Trainingsdaten erstellen möchten, verwenden Sie die REST-APIs für das Trainieren beschrifteter Daten.

> [!div class="nextstepaction"]
> [Trainieren mit Beschriftungen mit Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)

* [Was ist die Formularerkennung?](../overview.md)
* [Formularerkennung: Schnellstart](client-library.md)

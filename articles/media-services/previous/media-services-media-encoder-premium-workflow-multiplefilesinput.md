---
title: Mehrere Eingabedateien und Komponenteneigenschaften mit Premium-Encoder – Azure | Microsoft-Dokumentation
description: In diesem Thema wird das Verwenden von „setRuntimeProperties“ zum Arbeiten mit mehreren Eingabedateien und Übergeben benutzerdefinierter Daten an den Medienprozessor „Media Encoder Premium Workflow“ erläutert.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: 7fb35bdd-9891-4401-a65b-ef3cc8190e8a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: xpouyat;anilmur;juliako
ms.openlocfilehash: 608ca4bc3b58dd3c718d6239f90260154d2f6c3a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61465373"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Verwenden mehrerer Eingabedateien und Komponenteneigenschaften mit Premium-Encoder
## <a name="overview"></a>Übersicht
Es gibt Szenarien, in denen Sie Komponenteneigenschaften möglicherweise anpassen, den XML-Inhalt von Cliplisten angeben oder mehrere Eingabedateien senden müssen, wenn Sie eine Aufgabe mit dem Medienprozessor **Media Encoder Premium Workflow** übermitteln. Hier einige Beispiele:

* Überlagern von Video mit Text und Festlegen des Textwerts (z.B. das aktuelle Datum) zur Laufzeit für jedes Eingabevideo.
* Anpassen des XML-Codes von Cliplisten (zum Angeben einer oder mehrerer Quelldateien, einer Kürzung usw.).
* Überlagern des Eingabevideos mit einem Logobild bei der Codierung des Videos.
* Codierung mit mehreren Audiosprachen.

Um den **Media Encoder Premium Workflow** zu informieren, dass Sie beim Erstellen des Tasks oder Senden mehrerer Eingabedateien einige Eigenschaften im Workflow ändern, müssen Sie eine Konfigurationszeichenfolge verwenden, die **setRuntimeProperties** und/oder **transcodeSource** enthält. In diesem Thema wird ihre Verwendung erläutert.

## <a name="configuration-string-syntax"></a>Syntax der Konfigurationszeichenfolge
Für die im Codierungstask festzulegende Konfigurationszeichenfolge wird ein XML-Dokument wie das folgende verwendet:

```xml
<?xml version="1.0" encoding="utf-8"?>
<transcodeRequest>
  <transcodeSource>
  </transcodeSource>
  <setRuntimeProperties>
    <property propertyPath="Media File Input/filename" value="VideoFileName.mp4" />
  </setRuntimeProperties>
</transcodeRequest>
```

Der folgende C#-Code liest die XML-Konfiguration aus einer Datei, aktualisiert sie mit dem richtigen Videodateinamen und übergibt sie der Aufgabe in einem Auftrag:

```csharp
string premiumConfiguration = ReadAllText(@"D:\home\site\wwwroot\Presets\SetRuntime.xml").Replace("VideoFileName", myVideoFileName);

// Declare a new job.
IJob job = _context.Jobs.Create("Premium Workflow encoding job");

// Get a media processor reference, and pass to it the name of the 
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

// Create a task with the encoding details, using a string preset.
ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                              processor,
                              premiumConfiguration,
                              TaskOptions.None);

// Specify the input assets
task.InputAssets.Add(workflow); // workflow asset
task.InputAssets.Add(video); // video asset with multiple files

// Add an output asset to contain the results of the job. 
// This output is specified as AssetCreationOptions.None, which 
// means the output asset is not encrypted. 
task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
```

## <a name="customizing-component-properties"></a>Anpassen von Komponenteneigenschaften
### <a name="property-with-a-simple-value"></a>Eigenschaft mit einem einfachen Wert
In einigen Fällen ist es sinnvoll, eine Komponenteneigenschaft zusammen mit der Workflowdatei anzupassen, die im Media Encoder Premium Workflow ausgeführt wird.

Angenommen, Sie haben einen Workflow entworfen, der Ihre Videos mit Text überlagert, und der Text (z.B. das aktuelle Datum) soll zur Laufzeit festgelegt werden. Senden Sie hierzu im Codierungstask den für die Texteigenschaft der Überlagerungskomponente als neuen Wert festzulegenden Text. Sie können diesen Mechanismus verwenden, um andere Eigenschaften einer Komponente im Workflow zu ändern (z.B. Position oder Farbe der Überlagerung, Bitrate des AVC-Encoders usw.).

**setRuntimeProperties** dient zum Überschreiben einer Eigenschaft in den Komponenten des Workflows.

Beispiel:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
      <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
  </setRuntimeProperties>
</transcodeRequest>
```

### <a name="property-with-an-xml-value"></a>Eigenschaft mit einem XML-Wert
Zum Festlegen einer Eigenschaft, die einen XML-Wert erwartet, nehmen Sie mithilfe von `<![CDATA[ and ]]>`eine Kapselung vor.

Beispiel:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

> [!NOTE]
> Setzen Sie hinter `<![CDATA[` kein Wagenrücklaufzeichen.

### <a name="propertypath-value"></a>Wert von „propertyPath“
In den vorherigen Beispielen hatte „propertyPath“ den Wert „/Media File Input/filename“ oder „/inactiveTimeout“ oder „clipListXml“.
Allgemein folgt also auf den Namen der Komponente der Name der Eigenschaft. Der Pfad kann mehr oder weniger Ebenen haben, wie z.B. „/primarySourceFile“ (da sich die Eigenschaft am Stamm des Workflows befindet) oder „/Video Processing/Graphic Overlay/Opacity“ (da sich die Überlagerung in einer Gruppe befindet).    

Um den Pfad und die Eigenschaft zu überprüfen, klicken Sie auf die Aktionsschaltfläche unmittelbar neben jeder Eigenschaft. Sie können auf diese Aktionsschaltfläche klicken und **Bearbeiten**auswählen. So wird der tatsächliche Namen der Eigenschaft und unmittelbar darüber der Namespace angezeigt.

![Aktion/Bearbeiten](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Eigenschaft](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Mehrere Eingabedateien
Jeder Task, den Sie an den **Media Encoder Premium Workflow** übermitteln, erfordert zwei Objekte:

* Das erste ist ein *Workflowobjekt* , das eine Workflowdatei enthält. Sie können Workflowdateien mit dem [Workflow-Designer](media-services-workflow-designer.md)entwerfen.
* Das zweite ist ein *Medienobjekt* , das die Mediendatei(en) enthält, die Sie codieren möchten.

Beim Senden mehrerer Mediendateien an den Encoder **Media Encoder Premium Workflow** gelten die folgenden Einschränkungen:

* Alle Mediendateien müssen im gleichen *Medienobjekt*enthalten sein. Das Verwenden mehrerer Medienobjekte wird nicht unterstützt.
* Sie müssen die primäre Datei in diesem Medienobjekt festlegen (im Idealfall ist dies die Hauptvideodatei, die der Encoder verarbeiten soll).
* Konfigurationsdaten, die das Element **setRuntimeProperties** und/oder **transcodeSource** enthalten, müssen an den Prozessor übergeben werden.
  * **setRuntimeProperties** dient zum Überschreiben des Dateinamens oder einer anderen Eigenschaft in den Komponenten des Workflows.
  * **transcodeSource** dient zum Angeben des XML-Inhalts der Clipliste.

Verbindungen im Workflow:

* Wenn Sie eine oder mehrere „Media File Input“-Komponenten verwenden und **setRuntimeProperties** zum Angeben des Dateinamens nutzen möchten, dürfen Sie diese nicht mit dem Pin der primären Dateikomponente verbinden. Stellen Sie sicher, dass es keine Verbindung zwischen dem primären Dateiobjekt und „Media File Input“-Komponenten gibt.
* Wenn Sie lieber mit Cliplisten-XML-Code und einer Medienquellenkomponente arbeiten, können Sie beide verbinden.

![Keine Verbindung der primären Quelldatei mit der Mediendateieingabe](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Es gibt keine Verbindung zwischen der primären Datei und „Media File Input“-Komponenten, wenn Sie setRuntimeProperties verwenden, um die Dateinameneigenschaft festzulegen.*

![Verbindung zwischen Cliplisten-XML-Code und Cliplistenquelle](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Sie können Cliplisten-XML-Code mit der Medienquelle verbinden und transcodeSource verwenden.*

### <a name="clip-list-xml-customization"></a>Anpassung des Cliplisten-XML-Codes
Sie können den Cliplisten-XML-Code im Workflow zur Laufzeit mithilfe von **transcodeSource** im XML-Code der Konfigurationszeichenfolge angeben. Hierfür muss der Pin „Cliplisten-XML-Code“ mit der Komponente „Medienquelle“ im Workflow verbunden werden.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <transcodeSource>
      <clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
      </clipList>
    </transcodeSource>
    <setRuntimeProperties>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Wenn Sie angeben möchten, dass „/primarySourceFile“ diese Eigenschaft zum Benennen der Ausgabedateien mithilfe von „Ausdrücken“ verwendet, sollten Sie den Cliplisten-XML-Code als Eigenschaft *nach* der /primarySourceFile-Eigenschaft übergeben. Dies verhindert, dass die Clipliste von der /primarySourceFile-Einstellung überschrieben wird.

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Mit zusätzlicher framegenauer Kürzung:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Beispiel 1: Überlagern des Videos mit einem Bild

### <a name="presentation"></a>Präsentation
Nehmen wir ein Beispiel, bei dem Sie das Eingabevideo bei der Codierung mit einem Logobild überlagern möchten. In diesem Beispiel heißt das Eingabevideo „Microsoft_HoloLens_Possibilities_816p24.mp4“, und das Logo heißt „logo.png“. Führen Sie die folgenden Schritte aus:

* Erstellen eines Workflowobjekts mit der Workflowdatei (siehe folgendes Beispiel).
* Erstellen eines Medienobjekts, das zwei Dateien enthält: „MyInputVideo.mp4“ als primäre Datei und „MyLogo.png“.
* Senden eines Tasks zum Medienprozessor „Media Encoder Premium Workflow“ mit den zuvor genannten Eingabemedienobjekten und Angeben der folgenden Konfigurationszeichenfolge.

Konfiguration:

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Im obigen Beispiel wird der Name der Videodatei an die „Media File Input“-Komponente und die Eigenschaft primarySourceFile gesendet. Der Name der Logodatei wird an eine andere „Media File Input“-Komponente gesendet, die mit der Grafiküberlagerungskomponente verbunden ist.

> [!NOTE]
> Der Name der Videodatei wird an die primarySourceFile-Eigenschaft übergeben. Der Grund dafür ist die Verwendung dieser Eigenschaft im Workflow, um z.B. mithilfe von Ausdrücken den ordnungsgemäßen Ausgabedateinamen zu erstellen.

### <a name="step-by-step-workflow-creation"></a>Schrittweise Workflowerstellung
Es folgen die Schritte zum Erstellen eines Workflows, der zwei Dateien als Eingabe verwendet: ein Video und ein Bild. Das Bild soll das Video überlagern.

Öffnen Sie den **Workflow-Designer**, und wählen Sie **Datei** > **Neuer Arbeitsbereich** > **Blaupause transcodieren**.

Der neue Workflow enthält drei Elemente:

* Primäre Quelldatei
* Clip List XML
* Output File/Asset  

![Neuer Workflow für Codierung](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Neuer Workflow für Codierung*

Wir beginnen mit dem Hinzufügen einer „Media File Input“-Komponente, damit unsere Eingabemediendatei akzeptiert wird. Um dem Workflow eine Komponente hinzuzufügen, suchen Sie im Repository-Suchfeld danach und ziehen den gewünschten Eintrag in den Designerbereich.

Fügen Sie als Nächstes die Datei hinzu, die zum Entwerfen Ihres Workflows verwendet werden soll. Klicken Sie hierzu auf den Hintergrundbereich des Workflow-Designers, und suchen Sie im rechten Eigenschaftsbereich die Option „Primäre Quelldatei“. Klicken Sie auf das Ordnersymbol, und wählen Sie die entsprechende Videodatei aus.

![Primäre Dateiquelle](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Primäre Dateiquelle*

Geben Sie die Datei anschließend im Feld „Mediendateieingabe“ ein.   

![Eingabequelle für Mediendatei](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Eingabequelle für Mediendatei*

Nachdem dieser Vorgang abgeschlossen ist, untersucht die „Media File Input“-Komponente die Datei und füllt ihre Ausgabepins auf, um die untersuchte Datei widerzuspiegeln.

Im nächsten Schritt wird ein „Videodatentyp-Updater“ zum Angeben des Farbraums als „Rec.709“ hinzugefügt. Fügen Sie einen „Videoformatkonverter“ hinzu, der auf „Datenlayout/Layouttyp = Konfigurierbar planar“ festgelegt ist. Dadurch werden der Videostream in ein Format konvertiert, das als Quelle für die Überlagerungkomponente verwendet werden kann.

![Videodatentyp-Updater und Formatkonverter](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Videodatentyp-Updater und Formatkonverter*

![Layouttyp = Konfigurierbar planar](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Layouttyp ist „Konfigurierbar planar“*

Als Nächstes fügen Sie eine Videoüberlagerungskomponente hinzu und verbinden den Pin „Nicht komprimiertes Video“ mit dem Pin „Nicht komprimiertes Video“ der Medieneingabedatei.

Fügen Sie eine andere „Media File Input“-Komponente (zum Laden der Logodatei) hinzu, klicken Sie auf diese Komponente, und benennen Sie sie in „Media File Input Logo“ um. Wählen Sie über die Dateieigenschaft ein Bild (z.B. eine PNG-Datei) aus. Verbinden Sie den Pin „Nicht komprimiertes Bild“ mit dem Pin „Nicht komprimiertes Bild“ der Überlagerung.

![Überlagerungskomponente und Bilddateiquelle](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Überlagerungskomponente und Bilddateiquelle*

Wenn Sie die Position des Logos für das Video ändern möchten (es z.B. lieber 10% entfernt von der linken oberen Ecke des Videos positionieren möchten), deaktivieren Sie das Kontrollkästchen „Manuelle Eingabe“. Dies ist möglich, da Sie eine „Media File Input“-Komponente verwenden, um der Überlagerungskomponente die Logodatei anzugeben.

![Position der Überlagerung](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Position der Überlagerung*

Fügen Sie zum Codieren des Videodatenstroms in H.264 der Designeroberfläche die Komponenten „AVC-Videoencoder“ und „AAC-Encoder“ hinzu. Verbinden Sie die Pins.
Richten Sie den AAC-Encoder ein, und wählen Sie: Audioformatkonvertierung/Voreinstellung: 2.0 (L, R).

![Audio- und Videoencoder](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Audio- und Videoencoder*

Fügen Sie nun die Komponenten **ISO Mpeg-4 Multiplexer** und **Dateiausgabe** hinzu, und verbinden Sie die Pins wie gezeigt.

![MP4-Multiplexer und Dateiausgabe](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4-Multiplexer und Dateiausgabe*

Sie müssen den Namen der Ausgabedatei festlegen. Klicken Sie auf die Komponente **Dateiausgabe** , und bearbeiten Sie den Ausdruck für die Datei:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Dateiausgabename](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Dateiausgabename*

Sie können den Workflow lokal ausführen, um sicherzustellen, dass er ordnungsgemäß ausgeführt wird.

Anschließend können Sie ihn in Azure Media Services ausführen.

Bereiten Sie zuerst in Azure Media Services ein Medienobjekt vor, das die Videodatei und das Logo enthält. Dazu können Sie .NET oder REST-API verwenden. Sie können hierzu auch das Azure-Portal oder den [Azure Media Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) nutzen.

Dieses Tutorial zeigt Ihnen, wie Medienobjekte mit dem AMSE verwaltet werden. Es gibt zwei Möglichkeiten, einem Medienobjekt Dateien hinzuzufügen:

* Erstellen Sie einen lokalen Ordner, in den Sie die beiden Dateien kopieren, und legen Sie ihn dann per Drag&amp;Drop auf der Registerkarte **Medienobjekt** ab.
* Laden Sie die Videodatei als Medienobjekt hoch. Zeigen Sie anschließend die Informationen zum Medienobjekt an, wechseln Sie zur Registerkarte „Dateien“, und laden Sie eine zusätzliche Datei (mit dem Logo) hoch.

> [!NOTE]
> Stellen Sie sicher, dass sich eine primäre Datei im Medienobjekt (der Hauptvideodatei) befindet.

![Medienobjektdateien im AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Medienobjektdateien im AMSE*

Wählen Sie das Medienobjekt aus, und codieren Sie es mit dem Premium-Encoder. Laden Sie den Workflow hoch, und wählen Sie ihn aus.

Klicken Sie auf die Schaltfläche, um Daten an den Prozessor zu übergeben, und fügen Sie zum Festlegen der Laufzeiteigenschaften den folgenden XML-Code hinzu:

![Premium-Encoder im AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Premium-Encoder im AMSE*

Fügen Sie dann die folgenden XML-Daten ein. Sie müssen für „Media File Input“ und „primarySourceFile“ den Namen der Videodatei angeben. Geben Sie auch den Namen der Datei mit dem Logo an.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*

Bei Verwendung des .NET SDK zum Erstellen und Ausführen des Tasks müssen diese XML-Daten als Konfigurationszeichenfolge übergeben werden.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

Nach Abschluss des Auftrags wird die MP4-Datei im Ausgabemedienobjekt mit der Überlagerung angezeigt!

![Überlagerung für das Video](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Überlagerung für das Video*

Sie können den Beispielworkflow von [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/)herunterladen.

## <a name="example-2--multiple-audio-language-encoding"></a>Beispiel 2: Codierung mit mehreren Audiosprachen

Ein Beispiel für einen Workflow zur Codierung mit mehreren Audiosprachen ist auf [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding) verfügbar.

Dieser Ordner enthält einen Beispielworkflow, der zum Codieren einer MXF-Datei als Objekt mit mehreren MP4-Dateien mit mehreren Audiospuren verwendet werden kann.

Dieser Workflow setzt voraus, dass die MXF-Datei eine Audiospur enthält; die weiteren Audiospuren sollten als separate Audiodateien (WAV oder MP4...) übergeben werden.

Führen Sie folgende Schritte für die Codierung aus:

* Erstellen Sie ein Media Services-Objekt mit der MXF-Datei und den Audiodateien (0 bis 18 Audiodateien).
* Stellen Sie sicher, dass die MXF-Datei als eine primäre Datei festgelegt ist.
* Erstellen Sie mithilfe des Premium Workflow-Encoderprozessors einen Auftrag und eine Aufgabe. Verwenden Sie den bereitgestellten Workflow (MultiMP4-1080p-19audio-v1.workflow).
* Übergeben Sie der Aufgabe die Daten aus „setruntime.xml“ (wenn Sie den Azure Media Services-Explorer nutzen, verwenden Sie dazu die Schaltfläche zum Übergeben von XML-Daten an den Workflow).
  * Aktualisieren Sie die XML-Daten, um die richtigen Dateinamen und Sprach-Tags anzugeben.
  * Der Workflow enthält Audiokomponenten mit den Namen Audio1 bis Audio 18.
  * RFC5646 wird für das Sprach-Tag unterstützt.

```xml
<?xml version="1.0" encoding="utf-16"?>
<transcodeRequest>
  <setRuntimeProperties>
    <property propertyPath="Media File Input Video/filename" value="MainVideo.mxf" />
    <property propertyPath="Language/language_code" value="en" />
    <property propertyPath="/primarySourceFile" value="MainVideo.mxf" />
    <property propertyPath="Audio 1/Media File Input/filename" value="french-audio.wav" />
    <property propertyPath="Audio 1/Language/language_code" value="fr" />
    <property propertyPath="Audio 2/Media File Input/filename" value="german-audio.wav" />
    <property propertyPath="Audio 2/Language/language_code" value="de" />
    <property propertyPath="Audio 3/Media File Input/filename" value="japanese-audio.wav" />
    <property propertyPath="Audio 3/Language/language_code" value="ja" />
  </setRuntimeProperties>
</transcodeRequest>
```

* Das codierte Objekt enthält Audiospuren für mehrere Sprachen, die in Azure Media Player auswählbar sein sollten.

## <a name="see-also"></a>Weitere Informationen
* [Introducing Premium Encoding in Azure Media Services (in englischer Sprache)](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [How to Use Premium Encoding in Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Codieren von On-Demand-Inhalten mit Azure Media Services](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Media Encoder Premium Workflow-Formate und -Codecs](media-services-premium-workflow-encoder-formats.md)
* [Workflow-Beispieldateien](https://github.com/Azure/azure-media-services-samples)
* [Azure Media Services Explorer](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

---
title: Erweiterte Media Encoder Premium Workflow-Tutorials
description: Dieses Dokument enthält exemplarische Vorgehensweisen zur Durchführung von erweiterten Aufgaben mit dem Media Encoder Premium Workflow und zur Erstellung komplexer Workflows mit Workflow Designer.
services: media-services
documentationcenter: ''
author: xstof
manager: femila
editor: ''
ms.assetid: 1ba52865-b4a8-4ca0-ac96-920d55b9d15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: christoc;xpouyat;juliako
ms.openlocfilehash: d227e3618c138e6661cc4be7caa2b9a3ba1af3f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61241556"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Erweiterte Media Encoder Premium Workflow-Tutorials
## <a name="overview"></a>Übersicht
Dieses Dokument enthält exemplarische Vorgehensweisen, die veranschaulichen, wie Sie Workflows mit **Workflow Designer** anpassen. Die dazugehörigen Workflowdateien finden Sie [hier](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>INHALTSVERZEICHNIS
Die folgenden Themen werden behandelt:

* [Codieren einer MXF-Datei in eine Single-Bitrate-MP4-Datei](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Starten eines neuen Workflows](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Verwenden von „Media File Input“](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Untersuchen von Mediendatenströmen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Hinzufügen eines Videoencoders für die Generierung von MP4-Dateien](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Codieren des Audiodatenstroms](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Durchführen von Multiplexing für Audio- und Videodatenströme in einen MP4-Container](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Schreiben der MP4-Datei](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Erstellen eines Media Services-Medienobjekts aus der Ausgabedatei](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Lokales Testen des fertigen Workflows](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Codieren von MXF in Multi-Bitrate-MP4-Dateien (mit aktivierter dynamischer Paketerstellung)](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Hinzufügen einer oder mehrerer zusätzlicher MP4-Ausgaben](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Konfigurieren der Dateiausgabenamen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Hinzufügen einer separaten Audiospur](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Hinzufügen der SMIL-Datei „ISM“](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Codieren von MXF in das Multi-Bitrate-MP4-Format – verbesserte Blaupause](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * Übersicht über Workflow – Verbesserung
  * [Benennungskonventionen für Dateien](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Veröffentlichen von Komponenteneigenschaften im Workflowstamm](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Generierte Ausgabedateinamen basierend auf veröffentlichten Eigenschaftswerten](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Hinzufügen von Miniaturbildern zur Multi-Bitrate-MP4-Ausgabe](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * Übersicht über Workflow – Hinzufügen von Miniaturbildern
  * [Hinzufügen von JPG-Codierung](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Durchführen der Farbraumkonvertierung](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Schreiben von Miniaturbildern](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Erkennen von Fehlern in einem Workflow](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Fertiger Workflow](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Zeitbasiertes Kürzen der Multi-Bitrate-MP4-Ausgabe](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Übersicht über Workflow – Beginnen mit dem Hinzufügen der Kürzung](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Verwenden des Stream Trimmers](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Fertiger Workflow](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Einführung in die Skriptkomponente](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Erstellen eines Skripts in einem Workflow: hello world](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Framebasiertes Kürzen der Multi-Bitrate-MP4-Ausgabe](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Übersicht über Blaupause – Beginnen mit dem Hinzufügen der Kürzung](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Verwenden der Cliplisten-XML-Datei](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Ändern der Clipliste über eine Skriptkomponente](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Hinzufügen einer ClippingEnabled-Hilfseigenschaft](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a id="MXF_to_MP4"></a>Codieren einer MXF-Datei in eine Single-Bitrate-MP4-Datei
In diesem Abschnitt wird das Erstellen einer Single-Bitrate-MP4-Datei mit AAC-HE-codierten Audiodaten aus einer MXF-Eingabedatei veranschaulicht.

### <a id="MXF_to_MP4_start_new"></a>Starten eines neuen Workflows
Öffnen Sie den Workflow-Designer, und wählen Sie „Datei“ > „Neuer Arbeitsbereich“ > „Blaupause transcodieren“.

Der neue Workflow enthält drei Elemente:

* Primäre Quelldatei
* Clip List XML
* Output File/Asset  

![Neuer Workflow für Codierung](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Neuer Workflow für Codierung*

### <a id="MXF_to_MP4_with_file_input"></a>Verwenden von „Media File Input“
Wir fügen zunächst eine Media File Input-Komponente hinzu, damit unsere Eingabemediendatei akzeptiert wird. Um dem Workflow eine Komponente hinzuzufügen, suchen Sie im Repository-Suchfeld danach und ziehen den gewünschten Eintrag in den Designerbereich. Wiederholen Sie die Aktion für die Media File Input-Komponente, und verbinden Sie die Primary Source File-Komponente mit dem Eingabepin der Media File Input-Komponente.

![Verbundene Media File Input-Komponente](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Verbundene Media File Input-Komponente*

Identifizieren Sie zunächst eine geeignete Beispieldatei zur Verwendung beim Entwerfen eines benutzerdefinierten Workflows. Klicken Sie hierzu auf den Hintergrund des Designerbereichs, und suchen Sie im rechten Eigenschaftsbereich nach der Eigenschaft „Primary Source File“. Klicken Sie auf das Ordnersymbol, und wählen Sie die gewünschte Datei zum Testen des Workflows aus. Die Komponente „Media File Input“ untersucht die Datei und füllt ihre Ausgabepins auf, um die Details der untersuchten Datei widerzuspiegeln.

![Aufgefüllte Komponente „Media File Input“](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Aufgefüllte Komponente „Media File Input“*

Da die Eingabe jetzt aufgefüllt ist, besteht der nächste Schritt in der Einrichtung der Codierungseinstellungen für die Ausgabe. Ähnlich wie bei der Konfiguration von „Primary Source File“ konfigurieren Sie direkt darunter jetzt die Eigenschaft „Output Folder Variable“.

![Konfigurierte Eingabe- und Ausgabeeigenschaften](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Konfigurierte Eingabe- und Ausgabeeigenschaften*

### <a id="MXF_to_MP4_streams"></a>Untersuchen von Mediendatenströmen
Häufig ist es wünschenswert zu wissen, wie der Datenstrom beim Durchlaufen des Workflow aussieht. Klicken Sie zum Untersuchen eines Datenstroms an einem beliebigen Punkt im Workflow einfach auf einen Ausgabe- oder Eingabepin einer Komponente. Versuchen Sie in diesem Fall, in der Komponente „Media File Input“ auf den Ausgabepin von „Uncompressed Video“ zu klicken. Ein Dialogfeld wird geöffnet, in dem Sie die ausgehenden Videodaten untersuchen können.

![Untersuchen des Ausgabepins von „Uncompressed Video“](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Untersuchen des Ausgabepins von „Uncompressed Video“*

In diesem Fall zeigt sich, dass das Video eine Eingabe von 1920x1080 mit 24 Bildern pro Sekunde und 4:2:2-Sampling für ein Video mit einer Länge von fast 2Minuten enthält.

### <a id="MXF_to_MP4_file_generation"></a>Hinzufügen eines Videoencoders für die Generierung von MP4-Dateien
Jetzt sind ein Uncompressed Video-Ausgabepin und mehrere Uncompressed Audio-Ausgabepins für die Verwendung in „Media File Input“ verfügbar. Zum Codieren der eingehenden Videodaten muss dem Workflow eine Codierungskomponente hinzugefügt werden – in diesem Fall zum Generieren von MP4-Dateien.

Fügen Sie zum Codieren des Videodatenstroms in H.264 der Designeroberfläche die Komponente „AVC Video Encoder“ hinzu. Für diese Komponente wird ein Datenstrom vom Typ „Uncompressed Video“ als Eingabe verwendet, und am Ausgabepin wird ein Datenstrom vom Typ „Compressed Video (AVC)“ bereitgestellt.

![Nicht verbundener AVC-Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Nicht verbundener AVC-Encoder*

Die jeweiligen Eigenschaften bestimmen, wie die Codierung genau abläuft. Werfen wir einen Blick auf einige der wichtigeren Einstellungen:

* „Output Width“ und „Output Height“ (Ausgabebreite und Ausgabehöhe): Dienen zum Bestimmen der Auflösung des codierten Videos. In diesem Fall ist 640x360 eine geeignete Einstellung.
* Frame Rate (Bildfrequenz): Bei der Einstellung auf „Passthrough“ wird nur die Quellbildfrequenz übernommen. Es ist aber möglich, dies außer Kraft zu setzen. Für diese Bildfrequenzkonvertierung wird keine Bewegungskompensation durchgeführt.
* „Profile“ und „Level“ (Profil und Ebene): Dienen zum Bestimmen des AVC-Profils und der dazugehörigen Ebene. Um auf bequeme Weise weitere Informationen zu den unterschiedlichen Ebenen und Profilen zu erhalten, klicken Sie in der Komponente „AVC Video Encoder“ auf das Symbol mit dem Fragezeichen. Auf der Hilfeseite werden weitere Details zu den einzelnen Ebenen angezeigt. Für dieses Beispiel verwenden Sie „Main Profile“ auf Ebene 3.2 (Standardeinstellung).
* Rate Control Mode and Bitrate (kbps) (Ratensteuermodus und Bitrate (KBit/s)): In diesem Szenario entscheiden wir uns für eine Ausgabe mit konstanter Bitrate (CBR) mit 1200 KBit/s.
* Video Format (Videoformat): Bietet Informationen zur Videonutzung (Video Usability Information, VUI), die in den H.264-Datenstrom geschrieben werden (Zusatzinformationen, die von einem Decoder zum Verbessern der Anzeige verwendet werden können, aber für das richtige Decodieren nicht entscheidend sind):
* NTSC (meist für USA oder Japan, 30 Bilder/Sekunde)
* PAL (meist für Europa, 25 Bilder/Sekunde)
* GOP Size Mode (GOP-Größenmodus): Festlegen einer festen GOP-Größe für unsere Zwecke mit einem Schlüsselintervall von zwei Sekunden mit geschlossenen GOPs. Mit der Einstellung von 2 Sekunden wird die Kompatibilität mit der dynamischen Paketerstellung von Azure Media Services sichergestellt.

Verbinden Sie für die Durchführung des Feeds für unseren AVC-Encoder den Ausgabepin „Uncompressed Video“ aus der Komponente „Media File Input“ mit dem Eingabepin „Uncompressed Video“ des AVC-Encoders.

![Verbundener AVC-Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Verbundener AVC-Hauptencoder*

### <a id="MXF_to_MP4_audio"></a>Codieren des Audiodatenstroms
Nun muss der ursprüngliche unkomprimierte Audiodatenstrom noch komprimiert werden. Für die Komprimierung des Audiostreams fügen Sie dem Workflow eine AAC-Encoderkomponente (Dolby) hinzu.

![Nicht verbundener AVC-Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Nicht verbundener AAC-Encoder*

Beachten Sie hierbei eine Inkompatibilität: Es ist nur ein einzelner Eingabepin „Uncompressed Audio“ vom AAC-Encoder verfügbar, und die Wahrscheinlichkeit ist groß, dass für „Media File Input“ zwei unterschiedliche nicht komprimierte Audiodatenströme verfügbar sind – einer für den linken Audiokanal und einer für den rechten Audiokanal. (Wenn Sie Surround Sound verwenden, sind es sechs Kanäle.) Es ist also nicht möglich, für die Audiodaten eine direkte Verbindung von der Media File Input-Quelle in den AAC-Audioencoder herzustellen. Die AAC-Komponente erwartet einen so genannten „überlappenden“ (interleaved) Audiodatenstrom: einen einzelnen Datenstrom, für den sich der linke und der rechte Kanal jeweils überlappen. Sobald wir aufgrund der Quellmediendatei wissen, welche Audiospuren sich in der Quelle an welcher Position befinden, können wir einen solchen überlappenden Audiodatenstrom mit den richtig zugewiesenen Lautsprecherpositionen für links und rechts generieren.

Zuerst sollten wir einen überlappenden Datenstrom aus den erforderlichen Quellaudiokanälen generieren. Dies übernimmt die Komponente „Audio Stream Interleaver“ für uns. Fügen Sie sie dem Workflow hinzu, und verbinden Sie die Audioausgaben von „Media File Input“ damit.

![Verbundener Audio Stream Interleaver](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Verbundener Audio Stream Interleaver*

Wir verfügen nun über einen überlappenden Audiodatenstrom, aber wir haben noch nicht angegeben, wie die Positionen des linken und rechten Lautsprechers zugewiesen werden sollen. Um dies anzugeben, können wir den „Speaker Position Assigner“ verwenden.

![Hinzufügen von „Speaker Position Assigner“](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Hinzufügen von „Speaker Position Assigner“*

Konfigurieren Sie „Speaker Position Assigner“ zur Verwendung mit einem Stereo-Eingabedatenstrom über einen benutzerdefinierten Encoder-Voreinstellungsfilter und der Kanalvoreinstellung „2.0 (L,R)“. (Die Position des linken Lautsprechers wird Kanal 1 zugewiesen, und die Position des rechten Lautsprechers wird Kanal 2 zugewiesen.)

Verbinden Sie den Ausgabepin von „Speaker Position Assigner“ mit dem Eingabepin des AAC-Encoders. Legen Sie für den AAC-Encoder dann die Verwendung der Kanaleinstellung „2.0 (L,R)“ fest, damit klar ist, dass Stereo-Audiodaten als Eingabe verwendet werden.

### <a id="MXF_to_MP4_audio_and_fideo"></a>Durchführen von Multiplexing für Audio- und Videodatenströme in einen MP4-Container
Wir können sowohl den codierten AVC-Videodatenstrom als auch den codierten AAC-Audiodatenstrom in einem MP4-Container erfassen. Das Vermischen unterschiedlicher Datenströme zu einem zentralen Datenstrom wird als „Multiplexing“ (auch „Muxing“) bezeichnet. In diesem Fall führen wir für die Audio- und Videodatenströme die Überlappung in einem einzelnen kohärenten MP4-Paket durch. Die Komponente, mit der dies für einen MP4-Container koordiniert wird, wird als „ISO MPEG-4 Multiplexer“ bezeichnet. Fügen Sie eine Komponente dieses Typs der Designeroberfläche hinzu, und verbinden Sie den AVC-Videoencoder und den AAC-Encoder jeweils mit den Eingabepins der Komponente.

![Verbundener MPEG4 Multiplexer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Verbundener MPEG4 Multiplexer*

### <a id="MXF_to_MP4_writing_mp4"></a>Schreiben der MP4-Datei
Zum Schreiben einer Ausgabedatei wird die Komponente „File Output“ verwendet. Wir können hierfür eine Verbindung mit der Ausgabe von „ISO MPEG-4 Multiplexer“ herstellen, damit die Ausgabe auf Datenträger geschrieben wird. Verbinden Sie hierzu den Ausgabepin „Container (MPEG-4)“ mit dem Eingabepin „Write“ von „File Output“.

![Verbundene Komponente „File Output“](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Verbundene Komponente „File Output“*

Der verwendete Dateiname wird von der File-Eigenschaft bestimmt. Für diese Eigenschaft kann zwar ein bestimmter Wert hartcodiert werden, aber meistens ist es ratsam, sie stattdessen über einen Ausdruck festzulegen.

Damit der Workflow die Filename-Ausgabeeigenschaft für einen Ausdruck automatisch bestimmen kann, klicken Sie auf die Schaltfläche neben dem Dateinamen (neben dem Ordnersymbol). Wählen Sie dann im Dropdownmenü die Option „Ausdruck“. Der Ausdrucks-Editor wird aufgerufen. Löschen Sie zuerst den Inhalt des Editors.

![Leerer Expression Editor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Leerer Expression Editor*

Der Ausdrucks-Editor ermöglicht Ihnen die Eingabe eines beliebigen Literalwerts, gemischt mit einer oder mehreren Variablen. Variablen beginnen mit einem Dollarzeichen. Wenn Sie die Taste „$“ drücken, wird im Editor ein Dropdownfeld mit einer Auswahl von verfügbaren Variablen angezeigt. In unserem Fall verwenden wir eine Kombination aus der Ausgabeverzeichnisvariablen und der grundlegenden Eingabedatei-Namensvariablen:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Ausgefüllter Expression Editor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Ausgefüllter Expression Editor*

> [!NOTE]
> Sie müssen im Ausdrucks-Editor einen Wert angeben, um eine Ausgabedatei Ihres Codierungauftrags in Azure anzuzeigen.
>
>

Wenn Sie den Ausdruck durch Drücken von „OK“ bestätigen, wird im Eigenschaftenfenster als Vorschau angezeigt, in welchen Wert die File-Eigenschaft gerade aufgelöst wird.

![Dateiausdruck löst Ausgabeverzeichnis auf](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Dateiausdruck löst Ausgabeverzeichnis auf*

### <a id="MXF_to_MP4_asset_from_output"></a>Erstellen eines Media Services-Medienobjekts aus der Ausgabedatei
Wir haben eine MP4-Ausgabedatei geschrieben, aber wir müssen noch angeben, dass diese Datei zu dem Ausgabemedienobjekt gehört, das von Media Services bei der Ausführung dieses Workflows generiert wird. Hierfür wird der Knoten „Output File/Asset“ auf der Workflow-Canvas verwendet. Alle eingehenden Dateien dieses Knotens werden zu einem Teil des sich ergebenden Azure Media Services-Medienobjekts.

Verbinden Sie die Komponente „File Output“ mit der Komponente „Output File/Asset“, um den Workflow fertigzustellen.

![Fertiger Workflow](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Fertiger Workflow*

### <a id="MXF_to_MP4_test"></a>Lokales Testen des fertigen Workflows
Um den Workflow lokal zu testen, klicken Sie auf die entsprechende Schaltfläche in der Symbolleiste am oberen Rand. Nachdem die Ausführung des Workflows beendet ist, können Sie sich die generierte Ausgabe im konfigurierten Ausgabeordner ansehen. Sie sehen die fertige MP4-Ausgabedatei, die aus der MXF-Eingabequelldatei codiert wurde.

## <a id="MXF_to_MP4_with_dyn_packaging"></a>Codieren von MXF in Multi-Bitrate-MP4-Dateien (mit aktivierter dynamischer Paketerstellung)
In dieser exemplarischen Vorgehensweise erstellen wir eine Gruppe von MP4-Dateien mit Mehrfachbitrate und AAC-codierten Audiodaten aus einer einzelnen MXF-Eingabedatei.

Wenn eine Multi-Bitrate-Medienobjektausgabe zur Verwendung in Kombination mit den Azure Media Services-Features für die dynamische Paketerstellung gewünscht wird, müssen mehrere MP4-Dateien mit GOP-Ausrichtung und jeweils unterschiedlicher Bitrate und Auflösung generiert werden. Hierfür ist die exemplarische Vorgehensweise unter [Codieren einer MXF-Datei in eine Single-Bitrate-MP4-Datei](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) ein guter Ausgangspunkt.

![Workflow als Ausgangspunkt](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Workflow als Ausgangspunkt*

### <a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Hinzufügen einer oder mehrerer zusätzlicher MP4-Ausgaben
Jede MP4-Datei in unserem sich ergebenden Azure Media Services-Medienobjekt unterstützt eine andere Bitrate und Auflösung. Wir fügen dem Workflow eine oder mehrere MP4-Ausgabedateien hinzu.

Um sicherzustellen, dass alle Videoencoder mit den gleichen Einstellungen erstellt werden, ist es am einfachsten, die bereits vorhandene AVC Video Encoder-Komponente zu duplizieren und eine weitere Kombination aus Auflösung und Bitrate zu konfigurieren (z.B. 960x540 mit 25 Bildern pro Sekunde und 2,5 MBit/s). Kopieren Sie den vorhandenen Encoder, und fügen Sie ihn auf der Designeroberfläche ein, um ihn zu duplizieren.

Verbinden Sie den Ausgabepin „Uncompressed Video“ von „Media File Input“ mit der neuen AVC-Komponente.

![Zweiter verbundener AVC-Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Zweiter verbundener AVC-Encoder*

Passen Sie die Konfiguration für unseren neuen AVC-Encoder nun für die Ausgabe mit 960x540 bei 2,5 MBit/s an. (Verwenden Sie hierfür seine Eigenschaften „Output Width“, „Output Height“ und „Bitrate (kbps)“.)

Wenn wir das sich ergebende Medienobjekt zusammen mit der dynamischen Paketerstellung von Azure Media Services verwenden möchten, muss Folgendes erfüllt sein: Der Streamingendpunkt muss die Generierung über diese HLS/Fragmented MP4/DASH-Fragmente von MP4-Dateien, die exakt aneinander ausgerichtet sind, so durchführen können, dass Clients, die zwischen verschiedenen Bitraten wechseln, eine reibungslose, fortlaufende Video- und Audioumgebung vorfinden. Hierfür müssen wir sicherstellen, dass die GOP-Größe (Group of pictures, Gruppe von Bildern) in den Eigenschaften beider AVC-Encoder für beide MP4-Dateien auf „2 Sekunden“ festgelegt ist. Gehen Sie wie folgt vor:

* Legen Sie „GOP Size Mode“ auf „Fixed GOP Size“ fest.
* Legen Sie „Key Frame Interval“ auf zwei Sekunden fest.
* Legen Sie außerdem „GOP IDR Control“ auf „Closed GOP“ fest, um sicherzustellen, dass alle GOPs eigenständig sind und keine Abhängigkeiten aufweisen.

Um diesen Workflow leichter verständlich zu machen, benennen Sie den ersten AVC-Encoder in „AVC-Videoencoder 640x360 1200 KBit/s“ und den zweiten AVC-Encoder in „AVC-Videoencoder 960x540 2500 KBit/s“ um.

Fügen Sie nun eine zweite Komponente „ISO MPEG-4 Multiplexer“ und eine zweite Komponente „File Output“ hinzu. Verbinden Sie den Multiplexer mit dem neuen AVC-Encoder, und stellen Sie sicher, dass die Ausgabe direkt in „File Output“ geleitet wird. Verbinden Sie anschließend auch die Ausgabe des AAC-Audioencoders mit der Eingabe des neuen Multiplexers. „File Output“ kann dann wiederum mit dem Knoten „Output File/Asset“ verbunden werden, um sie dem Media Services-Medienobjekt hinzuzufügen, das erstellt wird.

![Verbindung von zweitem Muxer und „File Output“](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Verbindung von zweitem Muxer und „File Output“*

Um die Kompatibilität mit der dynamischen Paketerstellung von Azure Media Services sicherzustellen, legen Sie den „Chunk Mode“ des Multiplexers auf „GOP count or duration“ und die GOPs pro Chunk auf „1“ fest. (Dies sollte die Standardeinstellung sein.)

![Chunk-Modi von Muxer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Chunk-Modi von Muxer*

Hinweis: Es kann ratsam sein, diesen Prozess für andere Kombinationen aus Bitrate und Auflösung zu wiederholen, die Sie der Medienobjektausgabe hinzufügen möchten.

### <a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Konfigurieren der Dateiausgabenamen
Wir haben dem Ausgabemedienobjekt mehr als eine Datei hinzugefügt. Daher müssen wir sicherstellen, dass sich die Dateinamen für die einzelnen Ausgabedateien voneinander unterscheiden, und unter Umständen sogar eine Benennungskonvention für Dateinamen anwenden. So ist dafür gesorgt, dass bereits am Dateinamen erkennbar ist, worum es geht.

Die Benennung der Dateiausgabe kann über Ausdrücke im Designer gesteuert werden. Öffnen Sie den Eigenschaftenbereich für eine der „File Output“-Komponenten, und öffnen Sie den Expression Editor für die File-Eigenschaft. Unsere erste Ausgabedatei wurde mit dem folgenden Ausdruck konfiguriert (siehe Tutorial für den Wechsel von [MXF zu einer Single-Bitrate-MP4-Ausgabe](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Dies bedeutet, dass unser Dateiname anhand von zwei Variablen bestimmt wird: dem Ausgabeverzeichnis, in das geschrieben wird, und dem Quelldatei-Basisnamen. Das Verzeichnis wird als Eigenschaft im Workflowstamm verfügbar gemacht, und der Name wird durch die eingehende Datei bestimmt. Das Ausgabeverzeichnis verwenden Sie für lokale Tests. Diese Eigenschaft wird vom Workflowmodul überschrieben, wenn der Workflow vom cloudbasierten Medienprozessor in Azure Media Services ausgeführt wird.
Ändern Sie den ersten Dateibenennungsausdruck in folgenden Ausdruck, um beiden Ausgabedateien einheitliche Ausgabenamen zu geben:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Ändern Sie den zweiten Ausdruck in:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Führen Sie einen Zwischentest durch, um sicherzustellen, dass beide MP4-Ausgabedateien richtig generiert werden.

### <a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Hinzufügen einer separaten Audiospur
Später beim Generieren einer ISM-Datei für unsere MP4-Ausgabedateien werden wir sehen, dass wir auch eine reine MP4-Audiodatei als Audiospur für das adaptive Streaming benötigen. Fügen Sie dem Workflow zum Erstellen dieser Datei einen zusätzlichen Muxer (ISO-MPEG-4 Multiplexer) hinzu, und verbinden Sie den Ausgabepin des AAC-Encoders mit dem Eingabepin für „Track 1“.

![Hinzugefügter Audio-Muxer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Hinzugefügter Audio-Muxer*

Erstellen Sie eine dritte File Output-Komponente, um den ausgehenden Datenstrom vom Muxer auszugeben, und konfigurieren Sie den Ausdruck für die Dateibenennung wie folgt:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Audio-Muxer erstellt „File Output“](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Audio-Muxer erstellt „File Output“*

### <a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Hinzufügen der ISM-SMIL-Datei
Damit die dynamische Paketerstellung mit beiden MP4-Dateien (und der reinen MP4-Audiodatei) in unserem Media Services-Medienobjekt funktioniert, benötigen wir auch eine Manifestdatei (auch als „SMIL“-Datei bezeichnet: Synchronized Multimedia Integration Language). Diese Datei gibt für Azure Media Services an, welche MP4-Dateien für die dynamische Paketerstellung verfügbar sind und welche Datei für das Audiostreaming berücksichtigt werden sollte. Eine typische Manifestdatei für eine Gruppe von MP4-Dateien mit einem einzelnen Audiodatenstrom sieht wie folgt aus:

```xml
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="https://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>
```

Die ISM-Datei enthält innerhalb einer switch-Anweisung einen Verweis auf die einzelnen MP4-Videodateien und zusätzlich einen oder mehrere Audiodateiverweise auf eine MP4-Datei, in der nur die Audiodaten enthalten sind.

Das Generieren der Manifestdatei für unsere Gruppe von MP4-Dateien kann auch über eine Komponente mit dem Namen „AMS Manifest Writer“ durchgeführt werden. Ziehen Sie die Komponente bei Bedarf auf die Benutzeroberfläche, und verbinden Sie die Ausgabepins von „Write Complete“ der drei File Output-Komponenten mit dem Eingabepin „AMS Manifest Writer“. Stellen Sie anschließend sicher, dass die Ausgabe von „AMS Manifest Writer“ mit „Output File/Asset“ verbunden ist.

Konfigurieren Sie wie bei den anderen Dateiausgabekomponenten den ISM-Dateiausgabenamen mit einem Ausdruck:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Der fertige Workflow sieht wie folgt aus:

![Fertiger Workflow: MXF zu Multi-Bitrate-MP4](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Fertiger Workflow: MXF zu Multi-Bitrate-MP4*

## <a id="MXF_to__multibitrate_MP4"></a>Codieren von MXF in das Multi-Bitrate-MP4-Format – verbesserte Blaupause
Bei der [exemplarischen Vorgehensweise für den vorherigen Workflow](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) haben wir gesehen, wie ein einzelnes MXF-Eingabemedienobjekt in ein Ausgabemedienobjekt mit MP4-Dateien mit Mehrfachbitrate, eine reine MP4-Audiodatei und eine Manifestdatei zur Verwendung mit der dynamischen Paketerstellung von Azure Media Services konvertiert werden kann.

In dieser exemplarischen Vorgehensweise wird gezeigt, wie einige Aspekte verbessert und benutzerfreundlicher gestaltet werden können.

### <a id="MXF_to_multibitrate_MP4_overview"></a>Übersicht über Workflow – Verbesserung
![Zu verbessernder Multi-Bitrate-MP4-Workflow](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Zu verbessernder Multi-Bitrate-MP4-Workflow*

### <a id="MXF_to__multibitrate_MP4_file_naming"></a>Benennungskonventionen für Dateien
Im vorherigen Workflow haben wir einen einfachen Ausdruck als Basis für die Generierung von Ausgabedateinamen angegeben. Es liegt jedoch eine Duplizierung vor: Dieser Ausdruck wurde für alle einzelnen Ausgabedateikomponenten angegeben.

Beispielsweise wurde unsere Dateiausgabekomponente für die erste Videodatei mit diesem Ausdruck konfiguriert:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Für das zweite Ausgabevideo wird folgender Ausdruck verwendet:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Wäre es nicht klarer, weniger fehleranfällig und benutzerfreundlicher, wenn wir diese Duplizierung teilweise entfernen und dafür die Konfigurierbarkeit verbessern würden? Glücklicherweise ist dies möglich: Dank der Ausdrucksfunktionen des Designers in Kombination mit der Fähigkeit, benutzerdefinierte Eigenschaften in unserem Workflowstamm zu erstellen, erhalten wir eine zusätzliche Ebene an Benutzerfreundlichkeit.

Angenommen, wir nutzen für die Dateinamenkonfiguration die Bitraten der einzelnen MP4-Dateien. Für diese Bitraten streben wir die Konfiguration an einem zentralen Ort an (Stamm unseres Graphen), an dem für die Konfiguration und Generierung von Dateinamen darauf zugegriffen werden kann. Wir beginnen hierbei, indem wir die Bitrateneigenschaft beider AVC-Encoder für den Stamm unseres Workflows veröffentlichen, damit sie sowohl über den Stamm als auch über die AVC-Encoder zugänglich ist. (Auch bei Anzeige an zwei unterschiedlichen Orten ist nur ein zugrunde liegender Wert vorhanden.)

### <a id="MXF_to__multibitrate_MP4_publishing"></a>Veröffentlichen von Komponenteneigenschaften im Workflowstamm
Öffnen Sie den ersten AVC-Encoder, greifen Sie auf die Eigenschaft „Bitrate (kbps)“ zu, und wählen Sie in der Dropdownliste die Option „Publish“.

![Veröffentlichen der Bitrateneigenschaft](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Veröffentlichen der Bitrateneigenschaft*

Konfigurieren Sie das Dialogfeld „Publish“ so, dass die Veröffentlichung auf der Stammebene des Workflowgraphen durchgeführt wird, wobei der veröffentlichte Name „video1bitrate“ und der besser lesbare Anzeigename „Video 1 Bitrate“ lautet. Konfigurieren Sie eine benutzerdefinierte Gruppe mit dem Namen „Streaming Bitrates“, und klicken Sie auf „Publish“.

![Veröffentlichen der Bitrateneigenschaft](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Veröffentlichungsdialogfeld für Bitrateneigenschaft*

Wiederholen Sie den Vorgang für die Bitrateneigenschaft des zweiten AVC-Encoders, und vergeben Sie den Namen „video2bitrate“ und den Anzeigenamen „Video 2 Bitrate“ in derselben benutzerdefinierten Gruppe „Streaming Bitrates“.

Wenn wir uns jetzt die Workflowstamm-Eigenschaften ansehen, wird unsere benutzerdefinierte Gruppe mit den beiden veröffentlichten Eigenschaften angezeigt. Für beide wird der Wert der entsprechenden Bitrate des AVC-Encoders angezeigt.

![Veröffentlichte Bitrateneigenschaften im Workflowstamm](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Wenn wir auf diese Eigenschaften aus Code oder einem Ausdruck zugreifen möchten, ist dies immer wie folgt möglich:

* Aus Inlinecode einer Komponente direkt unterhalb der Stammebene: node.getPropertyAsString('../video1bitrate',null)
* In einem Ausdruck: ${ROOT_video1bitrate}

Wir schließen die Gruppe „Streaming Bitrates“ nun ab, indem wir auch die Audiospur-Bitrate veröffentlichen. Suchen Sie in den Eigenschaften des AAC-Encoders nach der Einstellung „Bitrate“, und wählen Sie im Dropdownmenü daneben die Option „Publish“. Führen Sie die Veröffentlichung für den Stamm des Graphen mit dem Namen „audio1bitrate“ und dem Anzeigenamen „Audio 1 Bitrate“ in der benutzerdefinierten Gruppe „Streaming Bitrates“ durch.

![Veröffentlichungsdialogfeld für Audiobitrate](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Veröffentlichungsdialogfeld für Audiobitrate*

![Sich ergebende Video- und Audioeigenschaften auf Stammebene](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Sich ergebende Video- und Audioeigenschaften auf Stammebene*

Wenn diese drei Werte geändert werden, werden auch die Werte der entsprechenden Komponenten neu konfiguriert und geändert, mit denen sie verknüpft sind (und über die die Veröffentlichung durchgeführt wurde).

### <a id="MXF_to__multibitrate_MP4_output_files"></a>Generierte Ausgabedateinamen basierend auf veröffentlichten Eigenschaftswerten
Anstatt die generierten Dateinamen hartzucodieren, können wir den Dateinamenausdruck jetzt für jede Dateiausgabekomponente so ändern, dass er sich auf die im Stamm des Graphen veröffentlichten Bitrateneigenschaften bezieht. Wir beginnen mit der ersten Dateiausgabe, suchen nach der File-Eigenschaft und bearbeiten den Ausdruck wie folgt:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Sie können auf die verschiedenen Parameter in diesem Ausdruck zugreifen, um Eingaben vorzunehmen, indem Sie auf der Tastatur das Dollarzeichen drücken, während Sie sich im Ausdrucksfenster befinden. Einer der verfügbaren Parameter ist unsere video1bitrate-Eigenschaft, die wir zuvor veröffentlicht haben.

![Zugreifen auf Parameter in einem Ausdruck](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Zugreifen auf Parameter in einem Ausdruck*

Gehen Sie für die Dateiausgabe des zweiten Videos genauso vor:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

Ebenfalls für die reine Audiodateiausgabe:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Wenn wir die Bitrate für eine Video- oder Audiodatei nun ändern, wird der entsprechende Encoder neu konfiguriert, und die auf der Bitrate basierende Dateibenennungskonvention wird automatisch berücksichtigt.

## <a id="thumbnails_to__multibitrate_MP4"></a>Hinzufügen von Miniaturbildern zur Multi-Bitrate-MP4-Ausgabe
Wir beginnen jetzt mit einem Workflow, mit dem [eine Multi-Bitrate-MP4-Ausgabe aus einer MXF-Eingabe](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)generiert wird, und sehen uns das Hinzufügen von Miniaturbildern zur Ausgabe an.

### <a id="thumbnails_to__multibitrate_MP4_overview"></a>Übersicht über Workflow – Hinzufügen von Miniaturbildern
![Multi-Bitrate-MP4-Workflow als Ausgangspunkt](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Multi-Bitrate-MP4-Workflow als Ausgangspunkt*

### <a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Hinzufügen von JPG-Codierung
Das Herzstück der Miniaturbildgenerierung ist die Komponente „JPG Encoder“, mit der die Ausgabe von JPG-Dateien möglich ist.

![JPG Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG Encoder*

Es ist aber nicht möglich, für den Datenstrom „Uncompressed Video“ eine Verbindung von „Media File Input“ in den JPG Encoder herzustellen. Stattdessen werden einzelne Frames (Bilder) erwartet. Hierfür können wir die Komponente „Video Frame Gate“ verwenden.

![Verbindung von Frame Gate und JPG Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Verbindung von Frame Gate und JPG Encoder*

Das Frame Gate lässt ein Videobild jeweils nach einer bestimmten Anzahl von Sekunden oder Frames passieren. Das Intervall und der Zeitversatz dieses Vorgangs können in den Eigenschaften konfiguriert werden.

![Video Frame Gate-Eigenschaften](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Video Frame Gate-Eigenschaften*

Wir erstellen jede Minute ein Miniaturbild, indem wir den Modus auf „Time (seconds)“ und das Intervall auf „60“ festlegen.

### <a id="thumbnails_to__multibitrate_MP4_color_space"></a>Durchführen der Farbraumkonvertierung
Es scheint zwar logisch zu sein, dass beide Pins „Uncompressed Video“ des Frame Gate und „Media File Input“ jetzt verbunden werden können, aber wir erhalten eine Fehlermeldung, wenn wir dies tun.

![Fehler: Eingangsfarbraum](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Fehler: Eingangsfarbraum*

Der Grund ist, dass sich die Darstellungsweise der Farbinformationen in unserem ursprünglichen, nicht komprimierten Videorohdatenstrom von unserer MXF von den Daten unterscheidet, die der JPG-Encoder erwartet. Genauer gesagt: Es wird ein so genannter „Farbraum“ vom Typ „RGB“ oder „Graustufen“ erwartet. Dies bedeutet, dass auf den eingehenden Videodatenstrom des Video Frame Gate zuerst eine Konvertierung für den Farbraum angewendet werden muss.

Ziehen Sie die Komponente „Color Space Converter – Intel“ auf den Workflow, und verbinden Sie sie mit dem Frame Gate.

![Herstellen der Verbindung mit „Color Space Converter“](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Herstellen der Verbindung mit „Color Space Converter“*

Wählen Sie im Eigenschaftenfenster in der Liste „Preset“ den Eintrag „BGR 24“ aus.

### <a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Schreiben von Miniaturbildern
Im Gegensatz zu unseren MP4-Videos gibt die Komponente „JPG Encoder“ mehr als eine Datei aus. Hierfür kann die Komponente „Scene Search JPG File Writer“ verwendet werden: Die eingehenden JPG-Miniaturbilder werden geschrieben, und jeder Dateiname erhält als Suffix eine andere Zahl. (Mit der Zahl wird normalerweise die Anzahl von Sekunden/Einheiten in dem Datenstrom angegeben, aus dem das Miniaturbild erstellt wurde.)

![Einführung in Scene Search JPG File Writer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Einführung in Scene Search JPG File Writer*

Konfigurieren Sie die Output Folder Path-Eigenschaft mit dem folgenden Ausdruck: ${ROOT_outputWriteDirectory}

Konfigurieren Sie die Filename Prefix-Eigenschaft mit:

    ${ROOT_sourceFileBaseName}_thumb_

Das Präfix bestimmt, wie die Miniaturbilddateien benannt werden. Sie erhalten als Suffix eine Zahl, mit der die Position des Miniaturbilds im Datenstrom angegeben wird.

![Eigenschaften von Scene Search JPG File Writer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Eigenschaften von Scene Search JPG File Writer*

Verbinden Sie „Scene Search JPG File Writer“ mit dem Knoten „Output File/Asset“.

### <a id="thumbnails_to__multibitrate_MP4_errors"></a>Erkennen von Fehlern in einem Workflow
Verbinden Sie die Eingabe von „Color Space Converter“ mit der unformatierten „Uncompressed Video“-Ausgabe. Führen Sie nun einen lokalen Testlauf für den Workflow durch. Die Wahrscheinlichkeit ist hoch, dass die Ausführung des Workflows plötzlich angehalten und ein roter Rahmen für die Komponente angezeigt wird, für die ein Fehler aufgetreten ist:

![Color Space Converter: Fehler](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Color Space Converter: Fehler*

Klicken Sie oben rechts in der Komponente „Color Space Converter“ auf das kleine rote Symbol „E“, um anzuzeigen, was der Grund für den fehlgeschlagenen Codierversuch ist.

![Color Space Converter: Fehlerdialogfeld](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Color Space Converter: Fehlerdialogfeld*

Sie sehen, dass der eingehende Farbraumstandard für den Color Space Converter für unsere angeforderte Konvertierung von YUV in RGB „rec601“ lauten muss. Für unseren Datenstrom wird also scheinbar nicht „rec601“ angegeben. („Rec 601“ ist ein Standard für die Codierung von analogen Videosignalen mit Zeilensprung (Interlacing) in Form von digitalen Videos. Dabei wird ein aktiver Bereich angegeben, mit dem 720 Luminanz-Stichproben und 360 Chrominanz-Stichproben pro Zeile abgedeckt werden. Das Farbcodiersystem hat die Bezeichnung YCbCr 4:2:2.)

Um dies zu beheben, geben wir in den Metadaten unseres Datenstroms an, dass wir rec601-Inhalt verwenden. Hierfür setzen wir die Komponente „Video Data Type Updater“ ein, die wir zwischen unsere Rohdatenquelle und die Komponente für die Farbraumkonvertierung einfügen. Der „Video Data Type Updater“ ermöglicht die manuelle Aktualisierung bestimmter Videodatentyp-Eigenschaften. Konfigurieren Sie dies, indem Sie unter „Color Space Standard“ die Option „Rec 601“ angeben. Der Video Data Type Updater kennzeichnet den Datenstrom mit dem Farbraum „Rec 601“, falls bisher noch kein Farbraum definiert wurde. (Vorhandene Metadaten werden nicht überschrieben, es sei denn, das Kontrollkästchen „Override“ ist aktiviert.)

![Aktualisieren von „Color Space Standard“ im Data Type Updater](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Aktualisieren von „Color Space Standard“ im Data Type Updater*

### <a id="thumbnails_to__multibitrate_MP4_finish"></a>Fertiger Workflow
Nachdem unser Workflow jetzt fertiggestellt ist, führen wir einen weiteren Testlauf durch.

![Fertiger Workflow für Multi-MP4-Ausgabe mit Miniaturbildern](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Fertiger Workflow für Multi-MP4-Ausgabe mit Miniaturbildern*

## <a id="time_based_trim"></a>Zeitbasiertes Kürzen der Multi-Bitrate-MP4-Ausgabe
Wir beginnen mit einem Workflow, mit dem [eine Multi-Bitrate-MP4-Ausgabe aus einer MXF-Eingabe](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)generiert wird, und sehen uns das Kürzen des Quellvideos basierend auf Zeitstempeln an.

### <a id="time_based_trim_start"></a>Übersicht über Workflow – Beginnen mit dem Hinzufügen der Kürzung
![Startworkflow zum Hinzufügen der Kürzung](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Startworkflow zum Hinzufügen der Kürzung*

### <a id="time_based_trim_use_stream_trimmer"></a>Verwenden des Stream Trimmers
Mit der Komponente „Stream Trimmer“ können Sie den Anfang und das Ende eines Eingabedatenstroms anhand von Zeitinformationen (Sekunden, Minuten, ...) kürzen. Das framebasierte Kürzen wird vom Trimmer nicht unterstützt.

![Stream Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Stream Trimmer*

Anstatt die AVC-Encoder und den Speaker Position Assigner direkt mit „Media File Input“ zu verbinden, fügen wir dazwischen den Stream Trimmer ein. (Einen für das Videosignal und einen für das überlappende Audiosignal.)

![Einfügen des Stream Trimmers](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Einfügen des Stream Trimmers*

Wir konfigurieren den Trimmer so, dass nur Video- und Audiodaten zwischen 15 Sekunden und 60 Sekunden im Video verarbeitet werden.

Navigieren Sie zu den Video Stream Trimmer-Eigenschaften, und konfigurieren Sie die Eigenschaften „Start Time“ (15 Sek.) und „End Time“ (60 Sek.). Um sicherzustellen, dass unsere Trimmer für Audio und Video immer auf die gleichen Start- und Endwerte festgelegt sind, veröffentlichen wir diese im Stamm des Workflows.

![Veröffentlichen der Eigenschaft „Start Time“ für den Stream Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Veröffentlichen der Eigenschaft „Start Time“ für den Stream Trimmer*

![Veröffentlichungsdialogfeld für Startzeit](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Veröffentlichungsdialogfeld für Startzeit*

![Veröffentlichungsdialogfeld für Endzeit](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Veröffentlichungsdialogfeld für Endzeit*

Wenn wir uns nun den Stamm unseres Workflows ansehen, werden beide Eigenschaften gut sichtbar angezeigt und können konfiguriert werden.

![Veröffentlichte Eigenschaften im Stamm verfügbar](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Veröffentlichte Eigenschaften im Stamm verfügbar*

Öffnen Sie die Eigenschaften für das Kürzen im Audio-Trimmer, und konfigurieren Sie die Start- und Endzeit mit einem Ausdruck, der auf die veröffentlichten Eigenschaften im Stamm des Workflows verweist.

Für „Trimming Start Time“ für Audio:

    ${ROOT_TrimmingStartTime}

Für „Trimming End Time“:

    ${ROOT_TrimmingEndTime}

### <a id="time_based_trim_finish"></a>Fertiger Workflow
![Fertiger Workflow](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Fertiger Workflow*

## <a id="scripting"></a>Einführung in die Skriptkomponente
Skriptkomponenten können während der Ausführungsphasen unseres Workflows beliebige Skripts ausführen. Es gibt vier verschiedene Skripts, die ausgeführt werden können. Jedes verfügt über besondere Merkmale und seinen eigenen Platz im Lebenszyklus des Workflows:

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

In der Dokumentation zur Skriptkomponente finden Sie hierzu jeweils ausführliche Informationen. Im [folgenden Abschnitt](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)wird die Skriptkomponente **realizeScript** verwendet, um beim Starten des Workflows nebenbei eine Cliplist-XML-Datei zu erstellen. Dieses Skript wird während der Einrichtung der Komponente aufgerufen, und dieser Vorgang wird während des Lebenszyklus nur einmal durchgeführt.

### <a id="scripting_hello_world"></a>Erstellen eines Skripts in einem Workflow: hello world
Ziehen Sie eine Skriptkomponente auf die Designeroberfläche, und benennen Sie sie um (z. B. in „SetClipListXML“).

![Hinzufügen einer Skriptkomponente](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Hinzufügen einer Skriptkomponente*

Wenn Sie sich die Eigenschaften der Skriptkomponente ansehen, werden die vier unterschiedlichen Typen von Skripts angezeigt. Jeder davon ist für ein anderes Skript konfigurierbar.

![Eigenschaften von Skriptkomponenten](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Eigenschaften von Skriptkomponenten*

Löschen Sie processInputScript, und öffnen Sie den Editor für realizeScript. Wir haben die Einrichtung jetzt abgeschlossen und können mit der Skripterstellung beginnen.

Skripts werden in Groovy geschrieben. Hierbei handelt es sich um eine dynamisch kompilierte Skripterstellungssprache für die Java-Plattform, die mit Java kompatibel ist. Der Großteil des Java-Codes ist eigentlich gültiger Groovy-Code.

Wir schreiben nun im Kontext von realizeScript ein einfaches Groovy-Skript mit dem Namen „hello world“. Geben Sie im Editor Folgendes ein:

    node.log("hello world");

Führen Sie jetzt einen lokalen Testlauf aus. Sehen Sie sich nach dieser Ausführung die Logs-Eigenschaft an (auf der Registerkarte „System“ der Skriptkomponente).

![Protokollausgabe von „hello world“](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Protokollausgabe von „hello world“*

Das node-Objekt, über das wir die log-Methode aufrufen, bezieht sich auf den aktuellen Knoten („node“) oder die Komponente, die wir für die Skripterstellung verwenden. Jede Komponente verfügt quasi über die Fähigkeit, Protokollierungsdaten auszugeben. Sie können über die Registerkarte „System“ darauf zugreifen. In diesem Fall geben wir das Zeichenfolgenliteral „hello world“ aus. Hierbei ist es wichtig zu verstehen, dass dies als wertvolles Debugtool dienen und Ihnen Einblicke in die Vorgehensweise des Skripts liefern kann.

Über die Skripterstellungsumgebung haben wir auch Zugriff auf die Eigenschaften anderer Komponenten. Versuchen Sie Folgendes:

```java
    //inspect current node:
    def nodepath = node.getNodePath();
    node.log("this node path: " + nodepath);

    //walking up to other nodes:
    def parentnode = node.getParentNode();
    def parentnodepath = parentnode.getNodePath();
    node.log("parent node path: " + parentnodepath);

    //read properties from a node:
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null );
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null);
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);
```

Im Protokollfenster wird Folgendes angezeigt:

![Protokollausgabe für den Zugriff auf Knotenpfade](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Protokollausgabe für den Zugriff auf Knotenpfade*

## <a id="frame_based_trim"></a>Framebasiertes Kürzen der Multi-Bitrate-MP4-Ausgabe
Wir beginnen mit einem Workflow, mit dem [eine Multi-Bitrate-MP4-Ausgabe aus einer MXF-Eingabe](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)generiert wird, und sehen uns das Kürzen des Quellvideos basierend auf der Bildanzahl an.

### <a id="frame_based_trim_start"></a>Übersicht über Blaupause – Beginnen mit dem Hinzufügen der Kürzung
![Startworkflow zum Hinzufügen der Kürzung](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Startworkflow zum Hinzufügen der Kürzung*

### <a id="frame_based_trim_clip_list"></a>Verwenden der Cliplisten-XML-Datei
In allen vorherigen Workflow-Tutorials haben wir die Komponente „Media File Input“ als Videoeingabequelle verwendet. Für dieses spezielle Szenario verwenden wir stattdessen die Komponente „Clip List Source“. Dies sollte nicht als bevorzugte Vorgehensweise gewählt werden. Verwenden Sie „Clip List Source“ nur, wenn es dafür einen triftigen Grund gibt (wie im folgenden Fall, in dem wir die Funktionen zum Kürzen der Clipliste verwenden).

Um von „Media File Input“ zu „Clip List Source“ zu wechseln, ziehen Sie die Komponente „Clip List Source“ auf die Entwurfsoberfläche und verbinden den Pin von „Clip List XML“ mit dem Knoten „Clip List XML“ von Workflow Designer. Hierdurch wird „Clip List Source“ gemäß unserem Eingabevideo mit Ausgabepins gefüllt. Verbinden Sie als Nächstes die Pins „Uncompressed Video“ und „Uncompressed Audio“ von „Clip List Source“ mit den entsprechenden AVC-Encodern und dem Audio Stream Interleaver. Entfernen Sie anschließend die Komponente „Media File Input“.

![Ersetzen von „Media File Input“ durch „Clip List Source“](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Ersetzen von „Media File Input“ durch „Clip List Source“*

Die Komponente „Clip List Source“ verwendet als Eingabe eine Cliplisten-XML-Datei („Clip List XML“). Wenn Sie die Quelldatei für das lokale Testen auswählen, wird diese Cliplisten-XML-Datei automatisch für Sie aufgefüllt.

![Automatisch aufgefüllte Eigenschaft „Clip List XML“](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Automatisch aufgefüllte Eigenschaft „Clip List XML“*

Etwas näher betrachtet sieht die XML-Datei wie folgt aus:

![Dialogfeld zum Bearbeiten der Clipliste](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Dialogfeld zum Bearbeiten der Clipliste*

Hier werden allerdings nicht die Funktionen der Cliplisten-XML-Datei widergespiegelt. Beispielsweise können wir für die Videoquelle und die Audioquelle wie folgt ein trim-Element hinzufügen:

![Hinzufügen eines trim-Elements zur Clipliste](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Hinzufügen eines trim-Elements zur Clipliste*

Wenn Sie die Cliplisten-XML-Datei wie hier angegeben ändern und einen lokalen Testlauf durchführen, sehen Sie, dass das Video zwischen Sekunde 10 und 20 im Video richtig gekürzt wird.

Im Gegensatz zum Ergebnis eines lokales Testlaufs hätte dieselbe Cliplisten-XML-Datei aber nicht die gleiche Auswirkung, wenn sie in einem Workflow mit Ausführung von Azure Media Services angewendet wird. Wenn Azure Premium Encoder gestartet wird, wird die Cliplisten-XML-Datei jedes Mal neu generiert. Dies erfolgt basierend auf der Eingabedatei, die für den Codierauftrag bereitgestellt wurde. Dies bedeutet, dass alle Änderungen, die wir an der XML-Datei vornehmen, leider überschrieben werden.

Um das Löschen der Cliplisten-XML-Datei beim Starten eines Codierungsauftrags zu vermeiden, können wir sie nach dem Starten des Workflows nebenbei neu generieren. Für benutzerdefinierte Aktionen dieser Art kann eine so genannte „Skriptkomponente“ verwendet werden. Weitere Informationen finden Sie unter [Einführung in die Skriptkomponente](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Ziehen Sie eine Skriptkomponente auf die Designeroberfläche, und benennen Sie sie in „SetClipListXML“ um.

![Hinzufügen einer Skriptkomponente](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Hinzufügen einer Skriptkomponente*

Wenn Sie sich die Eigenschaften der Skriptkomponente ansehen, werden die vier unterschiedlichen Typen von Skripts angezeigt. Jeder davon ist für ein anderes Skript konfigurierbar.

![Eigenschaften von Skriptkomponenten](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Eigenschaften von Skriptkomponenten*

### <a id="frame_based_trim_modify_clip_list"></a>Ändern der Clipliste über eine Skriptkomponente
Bevor wir die Cliplisten-XML-Datei, die beim Starten des Workflows generiert wird, neu schreiben können, müssen wir Zugriff auf die clipListXML-Eigenschaft und den Inhalt haben. Dies erreichen wir wie folgt:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Protokollierte eingehende Clipliste](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Protokollierte eingehende Clipliste*

Zuerst müssen wir bestimmen können, ab welchem Punkt und bis zu welchem Punkt wir das Video kürzen möchten. Um dies auch für Workflowbenutzer mit geringeren technischen Kenntnissen einfach zu halten, veröffentlichen wir zwei Eigenschaften im Stamm des Graphen. Klicken Sie hierzu mit der rechten Maustaste auf die Designeroberfläche, und wählen Sie „Eigenschaft hinzufügen“:

* Erste Eigenschaft: „ClippingTimeStart“ des Typs: „TIMECODE“
* Zweite Eigenschaft: „ClippingTimeEnd“ des Typs: „TIMECODE“

![Dialogfeld „Add Property“ für Clipping-Startzeit](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Dialogfeld „Add Property“ für Clipping-Startzeit*

![Veröffentlichte Eigenschaften für Clipping-Zeit im Workflowstamm](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Veröffentlichte Eigenschaften für Clipping-Zeit im Workflowstamm*

Legen Sie beide Eigenschaften auf einen geeigneten Wert fest:

![Konfigurieren der Eigenschaften für Clipping-Start und -Ende](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Konfigurieren der Eigenschaften für Clipping-Start und -Ende*

Über unser Skript können wir jetzt wie folgt auf beide Eigenschaften zugreifen:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Protokollfenster mit Start- und Endzeit für Clipping](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Protokollfenster mit Start- und Endzeit für Clipping*

Wir bringen die timecode-Zeichenfolgen jetzt in eine benutzerfreundlichere Form, indem wir einen einfachen regulären Ausdruck verwenden:

```java
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);
    node.log("framerate end is: " + endframerate);
```

![Protokollfenster mit Ausgabe von analysiertem Zeitcode](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Protokollfenster mit Ausgabe von analysiertem Zeitcode*

Mit diesen Informationen können wir die Cliplisten-XML-Datei nun so ändern, dass sie die Start- und Endzeiten für das bis auf das Einzelbild genaue Kürzen des Films widerspiegelt.

![Skriptcode zum Hinzufügen von trim-Elementen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Skriptcode zum Hinzufügen von trim-Elementen*

Dies wurde mit normalen Vorgängen zum Ändern von Zeichenfolgen erreicht. Die sich ergebende geänderte Cliplisten-XML-Datei wird zurück in die clipListXML-Eigenschaft im Workflowstamm geschrieben, indem die setProperty-Methode verwendet wird. Im Protokollfenster wird nach einem weiteren Testlauf Folgendes angezeigt:

![Protokollieren der sich ergebenden Clipliste](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Protokollieren der sich ergebenden Clipliste*

Führen Sie einen Testlauf durch, um anzuzeigen, wie die Video- und Audiodatenströme gekürzt wurden. Wenn Sie mehr als einen Testlauf mit unterschiedlichen Werten für die Kürzungspunkte durchführen, werden Sie aber merken, dass diese nicht berücksichtigt werden. Dies liegt daran, dass der Designer – im Gegensatz zur Azure-Laufzeit – die Cliplisten-XML-Datei NICHT bei jeder Ausführung überschreibt. Dies bedeutet Folgendes: Nur das erstmalige Festlegen der Start- und Endpunkte führt zu einer Transformation der XML-Datei. In den nachfolgenden Fällen verhindert unsere Schutzklausel (if(`clipListXML.indexOf("<trim>") == -1`)), dass vom Workflow ein weiteres trim-Element hinzugefügt wird, wenn bereits ein trim-Element vorhanden ist.

Um das lokale Testen für unseren Workflow zu vereinfachen, fügen wir am besten Wartungscode hinzu, mit dem das Vorhandensein eines trim-Elements untersucht werden kann. Wenn dies der Fall ist, können wir es vor dem Fortfahren entfernen, indem wir die XML-Datei mit den neuen Werten ändern. Anstatt eine normale Zeichenfolgenänderung durchzuführen, ist es meistens sicherer, hierfür eine echte XML-Objektmodellanalyse zu verwenden.

Bevor wir diesen Code hinzufügen können, müssen wir aber am Anfang des Skripts einige Importanweisungen hinzufügen:

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;
```

Danach können wir den erforderlichen Bereinigungscode hinzufügen:

```java
    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes:
    elementsToDelete.each{
        e -> e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();
```

Dieser Code wird direkt über dem Punkt eingefügt, an dem wir die trim-Elemente der Cliplist-XML-Datei hinzufügen.

Wir können unseren Workflow jetzt so oft wie gewünscht ausführen und ändern, da die Änderungen jedes Mal angewendet werden.    

### <a id="frame_based_trim_clippingenabled_prop"></a>Hinzufügen einer ClippingEnabled-Hilfseigenschaft
Da das Kürzen vielleicht nicht jedes Mal durchgeführt werden soll, schließen wir den Workflow ab, indem wir ein hilfreiches boolesches Flag hinzufügen. Hiermit kann angegeben werden, ob das Kürzen aktiviert werden soll.

Veröffentlichen Sie wie zuvor im Stamm des Workflows eine neue Eigenschaft mit dem Namen „ClippingEnabled“ und dem Typ „BOOLEAN“.

![Veröffentlichte Eigenschaft zum Aktivieren der Kürzung](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Veröffentlichte Eigenschaft zum Aktivieren der Kürzung*

Mit der folgenden einfachen Wächterklausel können wir überprüfen, ob das Kürzen erforderlich ist, und entscheiden, ob unsere Clipliste geändert werden muss.

```java
    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }
```

### <a id="code"></a>Vollständiger Code

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;

    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);

    //for local testing: delete any pre-existing trim elements
    //from the clip list xml by parsing the xml into a DOM:

    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e ->
        e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }

    //add trim elements to cliplist xml
    if ( clipListXML.indexOf("<trim>") == -1 )
    {
        //trim video
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode +
            " </outPoint>\n </trim> \n");
        //trim audio
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" +
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML );
        node.setProperty("../clipListXml",clipListXML);
    }
```

## <a name="also-see"></a>Siehe auch
[Introducing Premium Encoding in Azure Media Services (in englischer Sprache)](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[How to Use Premium Encoding in Azure Media Services (in englischer Sprache)](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Codieren von On-Demand-Inhalten mit Azure Media Services](media-services-encode-asset.md#media-encoder-premium-workflow)

[Media Encoder Premium Workflow-Formate und -Codecs](media-services-premium-workflow-encoder-formats.md)

[Workflow-Beispieldateien](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Azure Media Services Explorer](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

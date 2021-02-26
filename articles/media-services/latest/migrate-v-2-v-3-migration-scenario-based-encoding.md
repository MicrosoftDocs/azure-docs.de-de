---
title: Migrationsleitfaden für die Codierung
description: In diesem Artikel finden Sie einen szenariobasierten Migrationsleitfaden für die Codierung, der Sie bei der Migration von Azure Media Services V2 zu V3 unterstützt.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: ab819239572fd99fdf5ff3bf23f81eb3cdff3b9a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940089"
---
# <a name="encoding-scenario-based-migration-guidance"></a>Szenariobasierter Migrationsleitfaden für die Codierung

![Logo des Migrationsleitfadens](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![Schritte bei der Migration 2](./media/migration-guide/steps-4.svg)

In diesem Artikel finden Sie einen szenariobasierten Migrationsleitfaden für die Codierung, der Sie bei der Migration von Azure Media Services V2 zu V3 unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Ihren Codierungsworkflow ändern, sollten Sie sich mit den Unterschieden bei der Art der Speicherverwaltung vertraut machen.  In AMS V3 erfolgt die Verwaltung der Speicherkonten, die Ihrem Media Services Konto zugeordnet sind, über die Azure Storage-API.

> [!NOTE]
> In V2 erstellte Aufträge und Aufgaben werden in V3 nicht angezeigt, da sie keiner Transformation zugeordnet sind. Es wird empfohlen, zu V3-Transformationen und -Aufträgen zu wechseln.

## <a name="encoding-workflow-comparison"></a>Vergleich der Codierungsworkflows

Nehmen Sie sich einige Minuten Zeit, und sehen Sie sich die unten aufgeführten Flussdiagramme an, in denen die Codierungsworkflows in V2 und V3 verglichen werden.

### <a name="v2-encoding-workflow"></a>V2-Codierungsworkflow

Klicken Sie auf das Bild unten, um eine größere Version anzuzeigen.

[ ![Codierungsworkflow für V2](./media/migration-guide/V2-pretty.svg) ](./media/migration-guide/V2-pretty.svg#lightbox)

1. Einrichtung
    1. Erstellen Sie ein Medienobjekt, oder verwenden Sie ein vorhandenes Objekt. Wenn Sie ein neues Medienobjekt verwenden, laden Sie Inhalt in dieses Medienobjekt hoch. Bei einem vorhandenen Medienobjekt sollten Sie Dateien codieren, die bereits im Medienobjekt vorhanden sind.
    2. Rufen Sie die Werte der folgenden Elemente ab:
        - Medienprozessor-ID oder Objekt
        - Codierungszeichenfolge (Name) des Encoders, den Sie verwenden möchten
        - Objekt-ID des neuen Medienobjekts ODER Objekt-ID des vorhandenen Medienobjekts
    3. Erstellen Sie für die Überwachung ein Benachrichtigungsabonnement auf Auftrags- oder auf Aufgabenebene oder einen SDK-Ereignishandler.
2. Erstellen Sie den Auftrag, der die Aufgaben enthält. Jede Aufgabe sollte die oben genannten Elemente und dazu noch Folgendes enthalten:
    - Eine Anweisung, dass ein Ausgabemedienobjekt erstellt werden muss.  Das Ausgabemedienobjekt wird vom System erstellt.
    - Den optionalen Namen für das Ausgabemedienobjekt
3. Übermitteln des Auftrags.
4. Überwachen Sie den Auftrag.

### <a name="v3-encoding-workflow"></a>V3-Codierungsworkflow

[ ![Codierungsworkflow für V3](./media/migration-guide/V3-pretty.svg) ](./media/migration-guide/V3-pretty.svg#lightbox)

1. Einrichtung
    1. Erstellen Sie ein Medienobjekt, oder verwenden Sie ein vorhandenes Objekt. Wenn Sie ein neues Medienobjekt verwenden, laden Sie Inhalt in dieses Medienobjekt hoch. Bei einem vorhandenen Medienobjekt sollten Sie Dateien codieren, die bereits im Medienobjekt vorhanden sind. Sie sollten *keine weiteren Inhalte in das Medienobjekt hochladen*.
    1. Erstellen eines Ausgabemedienobjekts  Im Ausgabemedienobjekt werden die codierten Dateien sowie die Eingabe- und Ausgabemetadaten gespeichert.
    1. Rufen Sie die Werte für die Transformation ab:
        - Voreinstellungen für den Media Encoder Standard
        - AMS-Ressourcengruppe
        - AMS-Kontoname
    1. Erstellen Sie eine Transformation, oder verwenden Sie eine vorhandene Transformation.  Transformationen sind wiederverwendbar. Es ist nicht erforderlich, jedes Mal für jede Übermittlung eines Auftrags eine neue Transformation zu erstellen.
1. Erstellen eines Auftrags
    1. Rufen Sie für den Auftrag die Werte der folgenden Elemente ab:
        - Transformationsname
        - Basis-URI für die SAS-URL für Ihr Medienobjekt, HTTPS-Quellpfad der Dateifreigabe oder lokaler Pfad der Dateien. `JobInputAsset` kann auch einen Objektnamen als Eingabe verwenden.
        - Dateinamen
        - Ausgabemedienobjekte
        - Eine Ressourcengruppe
        - AMS-Kontoname  
1. Verwenden Sie für das Überwachen Ihres Auftrags [Event Grid](monitor-events-portal-how-to.md).
1. Übermitteln des Auftrags.

## <a name="custom-presets-from-v2-to-v3-encoding"></a>Benutzerdefinierte Voreinstellungen für die Codierung bei V2 und V3

Wenn Ihr V2-Code den Media Encoder Standard mit einer benutzerdefinierten Voreinstellung aufgerufen hat, mussten Sie zunächst eine neue Transformation mit der benutzerdefinierten Voreinstellung für den Media Encoder Standard erstellen, bevor Sie einen Auftrag übermitteln konnten.

Die benutzerdefinierten Voreinstellungen basieren nun auf JSON und nicht mehr auf XML. Erstellen Sie Ihre Voreinstellung in JSON neu, und befolgen Sie dabei das Schema für benutzerdefinierte Voreinstellungen gemäß der Definition für die [offene Transform-API (Swagger)](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/examples/transforms-create.json).


<!-- removed because this is covered in the tutorials
Common custom [encoding](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json) scenarios:
        1. Create a custom Single Bitrate MP4 encode
        1. Create a custom [Adaptive Bitrate Encoding Ladder](autogen-bitrate-ladder.md)
        1. Creating Sprite Thumbnails
        1. Creating Thumbnails (see below for your preferred method)
        1. [Sub Clipping](subclip-video-rest-howto.md)
        1. Cropping
-->

## <a name="input-and-output-metadata-files-from-an-encoding-job"></a>Metadatendateien für Ein- und Ausgabe für einen Codierungsauftrag

In v2 werden Eingabe- und Ausgabe-Metadatendateien als Ergebnis eines Codierungsauftrags generiert. In v3 wurde das Metadatenformat von XML in JSON geändert. Weitere Informationen zu Metadaten finden Sie unter [Eingabemetadaten](input-metadata-schema.md) und [Ausgeben von Metadaten](output-metadata-schema.md).

## <a name="premium-encoder-to-v3-standard-encoder-or-partner-based-solutions"></a>Premium-Encoder zum V3-Media Encoder Standard oder zu Partnerlösungen

Der Premium-Encoder wird von der V2-API nicht mehr unterstützt. Wenn Sie zuvor den workflowbasierten Premium-Encoder für die HEVC-Codierung verwendet haben, sollten Sie zum neuen V3-[Media Encoder Standard](media-encoder-standard-formats.md) mit Unterstützung für die HEVC-Codierung migrieren.

Wenn Sie die erweiterten Workflowfunktionen des Premium-Encoders benötigen, sollten Sie eine der erweiterten Partnerlösungen für die Codierung in Azure von [Imagine Communications](https://imaginecommunications.com), [Telestream](https://www.telestream.net) oder [Bitmovin](https://bitmovin.com) einsetzen.

## <a name="jobs-with-inputs-that-are-on-https-hosted-urls"></a>Aufträge mit Eingaben von URLs über HTTPS

Sie können nun Aufträge in V3 aus Dateien übermitteln, die in Azure Storage, lokal oder auf externen Webservern gespeichert sind. Dies wird durch die [Unterstützung für HTTP(S)-Auftragseingaben](job-input-from-http-how-to.md) möglich.

Wenn Sie zuvor Workflows zum Kopieren von Dateien aus Azure-Blobdateien in leere Medienobjekte vor der Übermittlung von Aufträgen verwendet haben, können Sie Ihren Workflow nun vereinfachen, indem Sie direkt im Auftrag eine SAS-URL für die Datei in Azure Blob Storage übergeben.

## <a name="indexer-v1-audio-transcription-to-the-new-audioanalyzer-basic-mode"></a>Indexer V1-Audioaufzeichnung im neuen „einfachen Modus“ von AudioAnalyzer

Für Kunden, die den Indexer V1-Prozessor in der V2-API verwenden, müssen Sie vor dem Übermitteln eines Auftrags eine Transformation erstellen, die den neuen `AudioAnalyzer` im [einfachen Modus](how-to-create-basic-audio-transform.md) aufruft.

## <a name="encoding-transforms-and-jobs-concepts-tutorials-and-how-to-guides"></a>Konzepte, Tutorials und Schrittanleitungen für Codierung, Transformationen und Aufträge

### <a name="concepts"></a>Konzepte

- [Codieren von Video- und Audiodaten mit Media Services](encoding-concept.md)
- [Media Encoder Standard-Formate und -Codecs](media-encoder-standard-formats.md)
- [Codieren mit einer automatisch generierten Reihe von Bitraten-/Auflösungspaaren](autogen-bitrate-ladder.md)
- [Ermitteln des optimalen Bitratenwerts für eine bestimmte Auflösung unter Verwendung der Voreinstellung für die inhaltsbezogene Codierung](content-aware-encoding.md)
- [Reservierte Einheiten für Medien](concept-media-reserved-units.md)
- [Eingeben von Metadaten](input-metadata-schema.md)
- [Ausgeben von Metadaten](output-metadata-schema.md)
- [Dynamische Paketerstellung in Media Services V3: Audiocodecs](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging)

### <a name="tutorials"></a>Lernprogramme

- [Tutorial: Codieren einer Remotedatei anhand einer URL und Streamen des Videos – .NET](stream-files-dotnet-quickstart.md)
- [Tutorial: Hochladen, Codieren und Streamen von Videos mit Media Services v3](stream-files-tutorial-with-api.md)

### <a name="how-to-guides"></a>Anleitungen

- [Erstellen einer Auftragseingabe aus einer HTTPS-URL](job-input-from-http-how-to.md)
- [Erstellen einer Auftragseingabe aus einer lokalen Datei](job-input-from-local-file-how-to.md)
- [Erstellen einer einfachen Audiotransformation](how-to-create-basic-audio-transform.md)
- Mit .NET
  - [Codieren mit einer benutzerdefinierten Transformation – .NET](customize-encoder-presets-how-to.md)
  - [Gewusst wie: Erstellen einer Überlagerung mit Media Encoder Standard](how-to-create-overlay.md)
  - [Generieren von Vorschauminiaturen mithilfe des Media Encoder Standards mit .NET](media-services-generate-thumbnails-dotnet.md)
- Mit der Azure-Befehlszeilenschnittstelle
  - [Codieren mit einer benutzerdefinierten Transformation: Azure CLI](custom-preset-cli-howto.md)
- Mit REST
  - [Codieren mit einer benutzerdefinierten Transformation: REST](custom-preset-rest-howto.md)
  - [Generieren von Vorschauminiaturen mithilfe des Media Encoder Standards mit REST](media-services-generate-thumbnails-rest.md)
- [Erstellen von Subclips für ein Video beim Codieren mit Media Services: .NET](subclip-video-dotnet-howto.md)
- [Erstellen von Subclips für ein Video beim Codieren mit Media Services: REST](subclip-video-rest-howto.md)

## <a name="samples"></a>Beispiele

Sehen Sie sich auch den [Vergleich von V2- und V3-Code in den Codebeispielen](migrate-v-2-v-3-migration-samples.md) an.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]

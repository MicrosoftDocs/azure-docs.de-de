---
title: Fehler im Zusammenhang mit Azure Media Services-Paketerstellung und -Ursprung
description: In diesem Thema werden Fehler beschrieben, die beim Azure Media Services-Streamingendpunkt-Dienst (Ursprung) auftreten können.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 05/07/2019
ms.author: inhenkel
ms.openlocfilehash: 994e5ae0647f350e0a64f35318bd5803f4ed79b2
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98894982"
---
# <a name="streaming-endpoint-origin-errors"></a>Fehler beim Streamingendpunkt (Ursprung)

In diesem Thema werden Fehler beschrieben, die beim Azure Media Services-[Streamingendpunkt-Dienst](streaming-endpoint-concept.md) auftreten können.

## <a name="400-bad-request"></a>400 – Ungültige Anforderung

Die Anforderung enthält ungültige Informationen und wird mit diesen Fehlercodes und aus einem der folgenden Gründe abgelehnt:

|Fehlercode|Hexadezimalwert |Fehlerbeschreibung|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|URL-Syntax- oder Formatfehler. Beispiele sind Anforderungen für einen ungültigen Typ, ein ungültiges Fragment oder einen ungültigen Titel. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|Anforderung enthält kein Verschlüsselungstag in der URL. CMAF-Anforderungen müssen ein Verschlüsselungstag in der URL enthalten. Für andere Protokolle, die mit mehr als einem Verschlüsselungstyp konfiguriert sind, muss das Verschlüsselungstag ebenfalls angegeben werden, um Mehrdeutigkeit zu vermeiden. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|Fehler „Ungültige Anforderung“ bei Speicheranforderung zur Verarbeitung der Anforderung. |

## <a name="403-forbidden"></a>403 Verboten

Die Anforderung ist aus einem der folgenden Gründe nicht zulässig:

|Fehlercode|Hexadezimalwert |Fehlerbeschreibung|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|Authentifizierungsfehler bei Speicheranforderung zur Verarbeitung der Anforderung. Dies kann vorkommen, wenn die Speicherschlüssel rotieren und vom Dienst nicht synchronisiert werden konnten. <br/><br/>Wenden Sie sich im Azure-Portal über [Hilfe und Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) an den Azure-Support.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Fehler bei Speichervorgang; Zugriffsfehler aufgrund unzureichender Kontoberechtigungen. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |Fehler bei Speicheranforderung zur Verarbeitung der Anforderung, weil das Speicherkonto deaktiviert ist. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Fehler bei Speichervorgang; Zugriffsfehler aufgrund allgemeiner Fehler. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |Ausgabeformat aufgrund der Konfiguration in der Streamingrichtlinie blockiert. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |Verschlüsselung für den Inhalt und Bereitstellungsrichtlinie für das Ausgabeformat erforderlich. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |Keine Verschlüsselung in den Einstellungen für die Bereitstellungsrichtlinie festgelegt. |

## <a name="404-not-found"></a>404 – Nicht gefunden

Es wird versucht, eine nicht mehr vorhandene Ressource zu verarbeiten. Beispiel: Die Ressource wurde möglicherweise bereits gelöscht.

|Fehlercode|Hexadezimalwert |Fehlerbeschreibung|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |Angeforderter Titel nicht gefunden. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |Angeforderte Ressource nicht gefunden. |
|MPE_UNAUTHORIZED |0x80890244 |Der Zugriff ist nicht autorisiert. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |Angeforderter Zeitstempel nicht gefunden. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |Angeforderter dynamischer Manifestfilter nicht gefunden. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |Angeforderter Fragmentindex liegt außerhalb des gültigen Bereichs. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Livemedieneinträge zum Abrufen des moov-Puffers wurden nicht gefunden. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Fragment wurde zum angeforderten Zeitpunkt für einen bestimmten Titel nicht gefunden.<br/><br/>Möglicherweise befindet sich das Fragment nicht im Speicher. Versuchen Sie es mit einer anderen Darstellungsschicht, die ein Fragment enthalten könnte. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Medieneintrag für die angeforderte Bitrate wurde im Manifest nicht gefunden. <br/><br/>Möglicherweise hat der Spieler einen Videotitel einer bestimmten Bitrate angefordert, die im Manifest nicht enthalten war.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Bestimmte Metadaten im Manifest oder ein Rebase aus dem Speicher nicht gefunden. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Fehler bei Speichervorgang; Ressource nicht gefunden. |

## <a name="409-conflict"></a>409 – Konflikt

Die für eine Ressource in einer `PUT`- oder `POST`-Operation bereitgestellte ID wurde von einer vorhandenen Ressource übernommen. Verwenden Sie eine andere ID für die Ressource, um dieses Problem zu beheben.

|Fehlercode|Hexadezimalwert |Fehlerbeschreibung|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Fehler bei Speichervorgang; Konflikt.  |

## <a name="410"></a>410

|Fehlercode|Hexadezimalwert |Fehlerbeschreibung|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|Ist die Option „forceEndTimestamp“ des Filters auf „true“ festgelegt, liegt der Start- oder Endzeitstempel beim Livestreaming außerhalb des aktuellen DVR-Fensters.|

## <a name="412-precondition-failure"></a>412 Fehler bei Vorbedingung

In der Operation wurde ein eTag festgelegt, das sich von der auf dem Server verfügbaren Version unterscheidet, d.h., es liegt ein Fehler bei vollständiger Parallelität vor. Wiederholen Sie die Anforderung nach dem Lesen der neuesten Version der Ressource und dem Aktualisieren des eTag für die Anforderung.

|Fehlercode|Hexadezimalwert |Fehlerbeschreibung|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |Angefordertes Fragment nicht bereit.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Fehler bei Speichervorgang; Fehler bei Vorbedingung.|

## <a name="415-unsupported-media-type"></a>415 – Nicht unterstützter Medientyp

Das vom Client gesendete Nutzlastformat wird nicht unterstützt.

|Fehlercode|Hexadezimalwert |Fehlerbeschreibung|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Auf bereits verschlüsselte Inhalte darf keine Verschlüsselung angewendet werden.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |Verschlüsselung für das Eingabeformat ungültig.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| Bereitstellungsrichtlinientyp ungültig.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |Die ursprünglichen Einstellungen werden möglicherweise von mehreren Ausgabeformaten gemeinsam genutzt.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|Medienformat oder -typ nicht unterstützt. Media Services unterstützt beispielsweise keinen Qualitätsstufenzähler über 64. Im FLV-Videotag unterstützt Media Services keine Videoframes mit mehreren SPS und mehrere PPS.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| Eingabeformat des angeforderten Medienobjekts nicht unterstützt. Media Services unterstützt Smooth (live), MP4 (VoD) und Formate des progressiven Downloads.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|Angefordertes Ausgabeformat nicht unterstützt. Media Services unterstützt Smooth, DASH (CSF, CMAF), HLS (v3, v4, CMAF) und Formate des progressiven Downloads.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Nicht unterstützter Verschlüsselungstyp gefunden.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|Angeforderter Medientyp vom Ausgabeformat nicht unterstützt. Unterstützt werden Video, Audio oder „SUBT“-Untertitel.|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|Quellobjektmedium wurde mit einem Medienformat codiert, das nicht mit dem Ausgabeformat kompatibel ist.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|Quellmedienobjekt wurde mit einem Videoformat codiert, das nicht mit dem Ausgabeformat kompatibel ist. H.264, AVC, H.265 (HEVC, hev1 oder hvc1) werden unterstützt.|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|Quellmedienobjekt wurde mit einem Audioformat codiert, das nicht mit dem Ausgabeformat kompatibel ist. Unterstützte Audioformate sind AAC, E-AC3 (DD+), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|Das geschützte Quellmedienobjekt kann nicht in das Ausgabeformat konvertiert werden.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|Schutzformat wird vom Ausgabeformat nicht unterstützt.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|Schutzformat wird vom Eingabeformat nicht unterstützt.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Ungültige Video-NAL-Einheit, z.B. kann nur die erste NAL in diesem Sample ein AUD sein.|
|MPE_INVALID_NALU_SIZE|0x80890260|Ungültiger Wert für die Größe der NAL-Einheit.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Ungültiger Wert für die Länge der NAL-Einheit.|
|MPE_FILTER_INVALID|0x80890236|Ungültige dynamische Manifestfilter.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Ungültige oder nicht unterstützte Filterversionen.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Ungültiger Filtertyp.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|Im Filter angegebener Bereich ist ungültig.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Im Filter angegebenes Titelattribut ist ungültig.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|Im Filter angegebener Wert für die Länge des Darstellungsfensters ist ungültig.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Im Filter angegebener Wert für Livebackoff ist ungültig.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|In Legacyfiltern wird nur ein absTimeInHNS-Element unterstützt.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Nach Anwenden der Filter gibt es überhaupt keine Datenströme mehr.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|Livebackoff liegt außerhalb des DVR-Fensters.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|Livebackoff ist größer als das Darstellungsfenster.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Maximal zulässige Standardfilter (10) überschritten.|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Mehrere Operatoren für erste Videoqualität sind in kombinierten Anforderungsfiltern nicht zulässig.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|Die Anzahl der Bitratenattribute für erste Qualität muss eins (1) sein.|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|Dauer des HLS-Segments muss kleiner als ein Drittel des DVR-Fensters und HLS-Backoffs sein.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|Fragmentdauer muss kleiner oder gleich etwa 20 Sekunden sein, oder die Eingangsqualitätsstufen werden nicht an der Zeit ausgerichtet.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|DTS-spezifischer Fehler; während der DTS-Boxanalyse wurde die ReservedBox nicht gefunden, obwohl sie in der DTSSpecficBox vorhanden sein sollte.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|DTS-spezifischer Fehler; während der DTS-Boxanalyse wurden keine Kanäle gefunden.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|DTS-spezifischer Fehler; nicht übereinstimmende Samplingtypen in der DTSSpecficBox.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|DTS-spezifischer Fehler; Multi-Objekt ist festgelegt, jedoch gibt es nicht übereinstimmende DTSH-Samplingtypen.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|DTS-spezifischer Fehler; die Größe des Kernstreams ist ungültig.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|DTS-spezifischer Fehler; Sampleauflösung ist ungültig.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|DTS-spezifischer Fehler; Erweiterungsindex für untergeordneten Stream ist ungültig.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|DTS-spezifischer Fehler, die untergeordnete Stream Blocknummer ist ungültig.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|DTS-spezifischer Fehler; Samplingfrequenz ist ungültig.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|DTS-spezifischer Fehler; Referenztaktcode in Erweiterung für untergeordneten Stream ist ungültig.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|DTS-spezifischer Fehler; festgelegte Anzahl der Lautsprecher-Neuzuordnung ist ungültig.|

Artikel und Beispiele zur Verschlüsselung finden Sie unter:

- [Konzept: Inhaltsschutz](content-protection-overview.md)
- [Konzept: Richtlinien für Inhaltsschlüssel](content-key-policy-concept.md)
- [Konzept: Streamingrichtlinien](streaming-policy-concept.md)
- [Beispiel: Schutz durch AES-Verschlüsselung](protect-with-aes128.md)
- [Beispiel: Schutz mit DRM](protect-with-drm.md)

Anleitungen zu Filtern finden Sie unter:

- [Konzept: Dynamische Manifeste](filters-dynamic-manifest-overview.md)
- [Konzept: Filter](filters-concept.md)
- [Beispiel: Erstellen von Filtern mit REST-APIs](filters-dynamic-manifest-rest-howto.md)
- [Beispiel: Erstellen von Filtern mit .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Beispiel: Erstellen von Filtern mit der CLI](filters-dynamic-manifest-cli-howto.md)

Liveartikel und Beispiele finden Sie unter:

- [Konzept: Übersicht über das Livestreaming](live-streaming-overview.md)
- [Konzept: Liveereignisse und Liveausgaben](live-events-outputs-concept.md)
- [Beispiel: Tutorial zu Livestreaming](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 – Bereich kann nicht erfüllt werden

|Fehlercode|Hexadezimalwert |Fehlerbeschreibung|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Fehler bei Speichervorgang; HTTP 416-Fehler zurückgegeben, ungültiger Bereich.|

## <a name="500-internal-server-error"></a>500 Interner Serverfehler

Während der Verarbeitung der Anforderung tritt bei Media Services ein Fehler auf, der verhindert, dass die Verarbeitung fortgesetzt wird.  

|Fehlercode|Hexadezimalwert |Fehlerbeschreibung|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Empfangen und übersetzt aus Winhttp-Fehlercode von ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Empfangen und übersetzt aus Winhttp-Fehlercode von ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Empfangen und übersetzt aus Winhttp-Fehlercode von ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Fehler bei Speichervorgang; allgemeiner InternalError einer der HTTP 500-Fehler.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Fehler bei Speichervorgang; allgemeiner OperationTimedOut einer der HTTP 500-Fehler.|
|MPE_STORAGE_FAILURE|0x808900F2|Fehler bei Speichervorgang; andere HTTP 500-Fehler als InternalError oder OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 Dienst nicht verfügbar

Der Server kann derzeit keine Anforderungen empfangen. Dieser Fehler kann durch zu viele Anforderungen an den Dienst verursacht werden. Der Drosselungsmechanismus von Media Services schränkt die Ressourcenverwendung für Anwendungen ein, die zu viele Anforderungen an den Dienst ausgeben.

> [!NOTE]
> Überprüfen Sie die Fehlermeldung und die Fehlercode-Zeichenfolge, um ausführlichere Informationen zu dem Grund zu erhalten, aus dem Ihnen der 503-Fehler angezeigt wurde. Dieser Fehler bedeutet nicht immer eine Drosselung.
> 

|Fehlercode|Hexadezimalwert |Fehlerbeschreibung|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Fehler bei Speichervorgang; HTTP-Fehler 503 „Server ausgelastet“.|

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="see-also"></a>Weitere Informationen

- [Verschlüsselungsfehlercodes](/rest/api/media/jobs/get#joberrorcode)
- [Azure Media Services-Konzepte](concepts-overview.md)
- [Kontingente und Grenzwerte](limits-quotas-constraints.md)

## <a name="next-steps"></a>Nächste Schritte

[Beispiel: Zugriff auf ErrorCode und Meldung von ApiException mit .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)

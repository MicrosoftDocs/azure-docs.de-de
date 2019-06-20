---
title: 'Azure Media Services: Ergänzung zum Smooth Streaming-Protokoll (MS-SSTR) für HEVC | Microsoft-Dokumentation'
description: Diese Spezifikation beschreibt das Protokoll und Format für die fragmentierte MP4-basierte Livestreamingerfassung mit HEVC in Azure Media Services. Dieser Artikel ergänzt die Dokumentation zum Smooth Streaming-Protokoll (MS-SSTR) und beschäftigt sich mit der Unterstützung von HEVC-Erfassung und -Streaming. In diesem Artikel werden nur die für die Bereitstellung von HEVC erforderlichen Änderungen angegeben, ausgenommen sind Textstellen, die durch „(Keine Änderung)“ gekennzeichnet sind und der Verdeutlichung dienen.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: johndeu;
ms.openlocfilehash: b9756920d25891dbe6b71fa24c06ed8f0c8eabb0
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65963260"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Ergänzung zum Smooth Streaming-Protokoll (MS-SSTR) für HEVC 

## <a name="1-introduction"></a>1 Einführung 

Dieser Artikel enthält ergänzende Informationen, die für die Smooth Streaming-Protokollspezifikation [MS-SSTR] übernommen werden können, um Smooth Streaming von HEVC-kodierten Videos zu ermöglichen. In dieser Spezifikation werden ausschließlich die Änderungen beschrieben, die für die Bereitstellung des HEVC-Videocodecs erforderlich sind. Der Artikel folgt dem gleichen Nummerierungsschema wie die [MS-SSTR]-Spezifikation. Die leeren Überschriften im gesamten Artikel dienen dazu, dem Leser einen Hinweis auf seine Position in der [MS-SSTR]-Spezifikation zu geben.  „(Keine Änderung)“ bedeutet, dass der Text nur zur Verdeutlichung enthalten ist.

Der Artikel enthält technische Implementierungsanforderungen für die Signale von HEVC-Videocodecs in einem Smooth Streaming-Manifest. Zudem werden normative Verweise auf die aktuellen MPEG-Standards aktualisiert. Hierzu gehören HEVC, gemeinsame Verschlüsselung von HEVC und Feldnamen für das ISO-Basismediendateiformat, die mit den neuesten Spezifikationen aktualisiert wurden. 

Die referenzierte Smooth Streaming-Protokollspezifikation [MS-SSTR] beschreibt das Sendeformat für die Bereitstellung von Live- und On-Demand-Digitalmedien wie Audio und Video auf folgende Weise: von einem Encoder zu einem Webserver, von einem Server zu einem anderen Server und von einem Server zu einem HTTP-Client.
Die Verwendung einer MPEG-4 ([[MPEG4-RA]](https://go.microsoft.com/fwlink/?LinkId=327787))-basierten Datenstrukturbereitstellung über HTTP ermöglicht ein nahtloses Umschalten in nahezu Echtzeit zwischen verschiedenen Qualitätsstufen von komprimierten Medieninhalten. Das Ergebnis ist eine konstante Wiedergabeerfahrung für den HTTP-Clientendbenutzer, selbst wenn sich die Netzwerk- und Videorenderingbedingungen für den Clientcomputer oder das Clientgerät ändern.

## <a name="11-glossary"></a>1.1 Glossar 

Folgende Begriffe sind in *[MS-GLOS]* definiert:

>   **Globally Unique Identifier (GUID); Universally Unique Identifier (UUID)**

Die folgenden Begriffe sind für dieses Dokument spezifisch:

>  **Kompositionszeit:** Die Zeit, die bis zur Darstellung eines Samples im Client benötigt wird, wie in [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) definiert.
> 
>   **CENC**: Gemeinsame Verschlüsselung (Common Encryption), wie in der zweiten Auflage von [ISO/IEC 23001-7] definiert.
> 
>   **Decodierzeit:** Die Zeit, die für das Decodieren des Samples auf dem Client benötigt wird, wie in [[ISO/IEC 14496-12:2008]](https://go.microsoft.com/fwlink/?LinkId=183695) definiert.

**Fragment:** Eine unabhängig herunterladbare Einheit von **Medien**, die mindestens ein **Sample** umfasst.

>   **HEVC:** High Efficiency Video Coding, wie in [ISO/IEC 23008-2] definiert.
> 
>   **Manifest:** Metadata zur **Präsentation**, die dem Client das Senden von Anforderungen für **Medien** ermöglicht. **Medien:** Komprimierte Audio-, Video- und Textdaten, die vom Client zur Wiedergabe einer **Präsentation** verwendet werden. **Medienformat:** Ein klar definiertes Format zur Darstellung von Audio- oder Videodaten als komprimiertes **Sample**.
> 
>   **Präsentation:** Der Satz aller **Streams** und zugehörigen Metadaten, die zur Wiedergabe eines einzelnen Films benötigt werden. **Anforderung:** Eine HTTP-Nachricht, die vom Client an den Server gesendet wird, wie in [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372) definiert. **Antwort**: Eine HTTP-Nachricht, die vom Server an den Client gesendet wird, wie in [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372) definiert.
> 
>   **Sample:** Die kleinste grundlegende Einheit (wie ein Frame), in der **Medien** gespeichert und verarbeitet werden.
> 
>   **KÖNNEN, SOLLTEN, MÜSSEN, SOLLTEN NICHT, DÜRFEN NICHT:** Diese Begriffe (in Großbuchstaben) werden wie in [[RFC2119]](https://go.microsoft.com/fwlink/?LinkId=90317) beschrieben verwendet. Alle Anweisungen für optionales Verhalten verwenden MAY, SHOULD oder SHOULD NOT.

## <a name="12-references"></a>1.2 Verweise

>   Verweise auf die Microsoft Open Specifications-Dokumentation enthalten kein Veröffentlichungsjahr, da Links auf die neueste Version der Dokumente verweisen, die häufig aktualisiert werden. Verweise auf andere Dokumente beinhalten ein Veröffentlichungsjahr, wenn eines verfügbar ist.

### <a name="121-normative-references"></a>1.2.1 Normative Verweise 

>  [MS-SSTR] Smooth Streaming-Protokoll *v20140502* [https://msdn.microsoft.com/library/ff469518.aspx](https://msdn.microsoft.com/library/ff469518.aspx)
> 
>   [ISO/IEC 14496-12] Internationale Organisation für Normung (International Organization for Standardization, ISO), „Information technology -- Coding of audio-visual objects -- Part 12: ISO Base Media File Format“ (Informationstechnologie – Codierung von audiovisuellen Objekten – Teil 12: ISO-Basismediendateiformat), ISO/IEC 14496-12:2014, 4. Auflage, plus Berichtigung 1, Ergänzungen 1 und 2.
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/IEC 14496-15] Internationale Organisation für Normung (International Organization for Standardization, ISO), „Information technology – Coding of audio-visual objects – Part 15: Carriage of NAL unit structured video in the ISO Base Media File Format“ (Übertragung von NAL-Einheit strukturiertem Video im ISO-Basismediendateiformat), ISO 14496-15:2015, Edition 3.
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/IEC 23008-2] „Information technology – High efficiency coding and media delivery in heterogeneous environments (Informationstechnologie – Hocheffiziente Codierung und Medienbereitstellung in heterogenen Umgebungen) – Teil2: High efficiency video coding (Hocheffiziente Videocodierung): 2013 or newest edition (2013 oder neueste Auflage)<https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/IEC 23001-7] Information technology – MPEG systems technologies ([ISO/IEC 23001-7] Informationstechnologie – MPEG-Systemtechnologien) – Part 7 (Teil 7): Common encryption in ISO base media file format files, CENC Edition 2:2015 (Allgemeine Verschlüsselung in ISO-Basismediendateiformaten) <https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC-6381] IETF RFC-6381, „The 'Codecs' and 'Profiles' Parameters for "Bucket" Media Types“ (Die Parameter „Codecs“ und „Profiles“ für „Bucket“-Medientypen“) <https://tools.ietf.org/html/rfc6381>
> 
>   [MPEG4-RA] Die MP4-Registrierungsstelle, „MP4REG“, [http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   [RFC2119] Bradner, S., „Key words for use in RFCs to Indicate Requirement Levels“ (Schlüsselwörter zur Verwendung in RFCs zur Angabe von Anforderungsstufen), BCP 14, RFC 2119, März 1997,   [https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 Informative Verweise 

>   [MS-GLOS] Microsoft Corporation, „*Windows Protocols Master Glossary*“ (Hauptglossar zu Windows-Protokollen)
> 
>   [RFC3548] Josefsson, S., Ed., „The Base16, Base32, and Base64 Data Encodings“ (Die Datencodierungen Base16, Base32 und Base64), RFC 3548, Juli 2003, [https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   [RFC5234] Crocker, D., Ed., und Overell, P., „Augmented BNF for Syntax Specifications ABNF (Erweiterte BNF für Syntaxspezifikationen): ABNF“, STD 68, RFC 5234, Januar 2008,   [https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1.3 Übersicht 

>   Im Folgenden werden ausschließlich die Änderungen an der Smooth Streaming-Spezifikation angegeben, die zur Bereitstellung von HEVC erforderlich sind. Unveränderte Abschnittsüberschriften werden aufgeführt, um die Position in der referenzierten Smooth Streaming-Spezifikation [MS-SSTR] beizubehalten.

## <a name="14-relationship-to-other-protocols"></a>1.4 Beziehung zu anderen Protokollen 

## <a name="15-prerequisitespreconditions"></a>1.5 Voraussetzungen/Vorbedingungen 

## <a name="16-applicability-statement"></a>1.6 Anwendbarkeitsanweisung 

## <a name="17-versioning-and-capability-negotiation"></a>1.7 Versionsverwaltung und Funktionsübertragung 

## <a name="18-vendor-extensible-fields"></a>1.8 Durch Hersteller erweiterbare Felder 

>   Die folgende Methode SOLLTE verwendet werden, um Streams zu identifizieren, die das HEVC-Videoformat verwenden:
> 
>   * **Benutzerdefinierte beschreibende Codes für Medienformate:** Diese Funktion wird durch das **FourCC**-Feld bereitgestellt, wie in Abschnitt *2.2.2.5* beschrieben.
>   Ausführende können sicherstellen, dass Erweiterungen nicht zu Konflikten führen, indem sie Erweiterungscodes bei der MPEG4-RA registrieren, wie in [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) angegeben.

## <a name="19-standards-assignments"></a>1.9 Zuweisungen von Standards 

## <a name="2-messages"></a>2. Nachrichten 

## <a name="21-transport"></a>2.1 Transport

## <a name="22-message-syntax"></a>2.2 Nachrichtensyntax 

### <a name="221-manifest-request"></a>2.2.1 Manifestanforderung 

### <a name="222-manifest-response"></a>2.2.2 Manifestantwort 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion (Variable):** Die Nebenversion der Manifestantwortnachricht. Sie MUSS auf 2 festgelegt sein. (Keine Änderung)
> 
>   **TimeScale (Variable):** Die Zeitskala des Attributs „Duration“, angegeben als die Anzahl der Schritte in einer Sekunde. Der Standardwert lautet
> 1. (Keine Änderung)
> 
>    Der empfohlene Wert ist 90000, um die genaue Dauer von Videoframes und -fragmenten mit fraktionierter Framerate (z.B. 30/1.001 Hz) darzustellen.

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

Das ProtectionElement SOLLTE vorhanden sein, wenn Common Encryption (CENC) auf Video- oder Audiostreams angewendet wurde. HEVC-verschlüsselte Streams SOLLTEN der [ISO/IEC 23001-7], Common Encryption, 2. Auflage, entsprechen. Nur Slicedaten in VCL-NAL-Einheiten SOLLTEN verschlüsselt sein.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (Variable):** Die Zeitskala für Dauer und Zeitwerte in diesem Stream, angegeben als die Anzahl der Schritte in einer Sekunde. Für HEVC-Streams wird ein Wert von 90000 empfohlen. Für Audiostreams wird ein Wert empfohlen, der mit der Waveformsamplefrequenz (z.B. 48000 oder 44100) übereinstimmt.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (Variable):** Ein vierstelliger Code, der das für jedes Sample verwendete Medienformat identifiziert. Der folgende Wertebereich ist für die folgenden semantischen Bedeutungen reserviert:
> 
> * „hev1“: Videosamples für diesen Titel verwenden das HEVC-Videoformat mit dem in [ISO/IEC-14496-15] angegebenen „hev1“-Samplebeschreibungsformat.
> 
>   **CodecPrivateData (Variable):** Daten, die Parameter festlegen, die für das Medienformat spezifisch und für alle Samples im Titel allgemein gültig sind. Sie werden als Zeichenfolge von hexadezimal codierten Bytes dargestellt. Das Format und die semantische Bedeutung der Bytesequenz variiert mit dem Wert des Felds **FourCC**, wie folgt:
> 
>   * Wenn ein TrackElement ein HEVC-Video beschreibt, SOLLTE das Feld **FourCC** gleich **„hev1“** sein, und;
> 
>   Das Feld **CodecPrivateData** SOLLTE eine hexadezimal codierte Zeichenfolgendarstellung der folgenden Bytesequenz enthalten, wie in ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) angegeben (keine Änderung in MS-SSTR).
> 
>   * %x00 %x00 %x00 %x01 SPSField %x00 %x00 %x00 %x01 PPSField
> 
>   * SPSField enthält das Sequence Parameter Set (SPS).
> 
>   * PPSField enthält das Slice Parameter Set (PPS).
> 
>   Hinweis: Das Video Parameter Set (VPS) ist nicht in CodecPrivateData enthalten, sondern sollte im Dateiheader der gespeicherten Dateien im Feld „hvcC“ enthalten sein. Systeme, die das Smooth Streaming-Protokoll verwenden, müssen zusätzliche Decodierungsparameter (z.B. HEVC Tier) mit dem benutzerdefinierten Attribute „Codecs“ signalisieren.

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   Das Feld **SmoothStreamingMedia’s MajorVersion** MUSS auf 2 gesetzt werden, und das Feld **MinorVersion** MUSS auf 2 gesetzt werden. (Keine Änderung)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 Fragmentanforderung 

>   **Hinweis**: Das erforderliche Standardmedienformat für **MinorVersion** 2 und „hev1“ ist „iso8“ des ISO-Basismediendateiformat. Siehe [ISO/IEC 14496-12], ISO-Basismediendateiformat, 4. Auflage, und [ISO/IEC 23001-7] Common Encryption, 2. Auflage.

### <a name="224-fragment-response"></a>2.2.4 Fragmentantwort 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   **TfxdBox** ist veraltet und dessen Funktion wurde durch die Track Fragment Decode Time Box („tfdt“) ersetzt. Siehe [ISO/IEC 14496-12], Abschnitt 8.8.12.
> 
>   **Hinweis**: Ein Client kann die Dauer eines Fragments berechnen, indem er die in der Track Run Box („trun“) aufgeführten Samplezeiten addiert oder die Anzahl der Samples mit der Standardsampledauer multipliziert. baseMediaDecodeTime in „tfdt“ plus der Fragmentdauer entspricht dem URL-Zeitparameter für das nächste Fragment.
> 
>   Eine Producer Reference Time Box („prft“) SOLLTE bei Bedarf vor einer Movie Fragment Box („moof“) eingefügt werden, um die UTC-Zeit entsprechend der Track Fragment Decode Time des ersten von der Movie Fragment Box referenzierten Samples anzuzeigen. Siehe [ISO/IEC 14496-12], Abschnitt 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   **TfrfBox** ist veraltet und dessen Funktion wurde durch die Track Fragment Decode Time Box („tfdt“) ersetzt. Siehe [ISO/IEC 14496-12], Abschnitt 8.8.12.
> 
>   **Hinweis**: Ein Client kann die Dauer eines Fragments berechnen, indem er die in der Track Run Box („trun“) aufgeführten Samplezeiten addiert oder die Anzahl der Samples mit der Standardsampledauer multipliziert. baseMediaDecodeTime in „tfdt“ plus der Fragmentdauer entspricht dem URL-Zeitparameter für das nächste Fragment. Vorausschauende Adressen sind veraltet, weil sie das Livestreaming verzögern.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   **TfhdBox** und verwandte Felder kapseln Standardwerte für Metadaten pro Sample im Fragment. Die Syntax des Feldes **TfhdBox** ist eine strikte Teilmenge der Syntax der Track Fragment Header Box, die in [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695), Abschnitt 8.8.7, definiert ist.
> 
>   **BaseDataOffset (8 Bytes):** Der Offset in Bytes vom Anfang des Felds **MdatBox** bis hin zum Samplefeld im Feld **MdatBox**. Um diese Einschränkung zu signalisieren, muss das Flag „default-base-is-moof“ (0x020000) gesetzt werden.

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   **TrunBox** und verwandte Felder kapseln pro Samplemetadaten für das angeforderte Fragment. Die Syntax von **TrunBox** ist eine strikte Teilmenge der Track Fragment Run Box, Version 1, die in [[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]* , Abschnitt 8.8.8, definiert ist.
> 
>   **SampleCompositionTimeOffset (4 Bytes):** Der Samplekompositionszeitoffset für jedes Sample wird so angepasst, dass die Präsentationszeit des ersten dargestellten Samples im Fragment gleich der Decodierzeit des ersten decodierten Samples ist. Negative Kompositionsoffsets für Videosamples SOLLTEN verwendet werden,
> 
>   wie in [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) definiert.
> 
>   Hinweis: Dadurch wird ein Videosynchronisierungsfehler vermieden, der durch videoverzögerte Audiowiedergabe verursacht wird. Diese entspricht der größten Verzögerung bei der Entfernung des decodierten Bildpuffers, und das Präsentationstiming zwischen alternativen Fragmenten, die unterschiedliche Entfernungsverzögerungen haben können, wird beibehalten.
> 
>   Die Syntax der in diesem Abschnitt definierten Felder, festgelegt in ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096), bleibt bis auf folgende Ausnahmen unverändert:
> 
>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 Fragmentantwort für allgemeine Felder 

### <a name="225-sparse-stream-pointer"></a>2.2.5 Platzsparender Streamzeiger 

### <a name="226-fragment-not-yet-available"></a>2.2.6 Noch nicht verfügbares Fragment 

### <a name="227-live-ingest"></a>2.2.7 Liveerfassung 

#### <a name="2271-filetype"></a>2.2.7.1 FileType 

>   **FileType (Variable)** : Gibt den Untertyp und die beabsichtigte Verwendung der MPEG-4-Datei ([[MPEG4-RA]](https://go.microsoft.com/fwlink/?LinkId=327787)) sowie allgemeinen Attribute an.
> 
>   **MajorBrand (Variable):** Die Hauptmarke der Mediendatei. MUSS auf „isml“ festgelegt sein.
> 
>   **MinorVersion (Variable):** Die Nebenversion der Mediendatei. MUSS auf 1 festgelegt sein.
> 
>   **CompatibleBrands (Variable):** Gibt die unterstützten Marken von MPEG-4 an.
>   MUSS „ccff“ und „iso8“ enthalten.
> 
>   Die Syntax der in diesem Abschnitt definierten Felder, festgelegt in ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096), lautet wie folgt:

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**Hinweis**: Die Kompatibilitätsmarken „ccff“ und „iso8“ zeigen an, dass Fragmente dem „Common Container File Format“ und Common Encryption, [ISO/IEC 23001-7], sowie dem ISO-Basismediendateiformat, 4. Auflage, [ISO/IEC 14496-12], entsprechen.

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 Fragment 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 Erweiterter Header von Titelfragment 

### <a name="228-server-to-server-ingest"></a>2.2.8 Server-zu-Server-Erfassung 

## <a name="3-protocol-details"></a>3 Protokolldetails 


## <a name="31-client-details"></a>3.1 Clientdetails 

### <a name="311-abstract-data-model"></a>3.1.1 Abstraktes Datenmodell 

#### <a name="3111-presentation-description"></a>3.1.1.1 Präsentationsbeschreibung 

>   Das Datenelement für die Präsentationsbeschreibung kapselt alle Metadaten für die Präsentation.
> 
>   Präsentationsmetadaten: Ein Satz von Metadaten, der allen Streams in der Präsentation gemeinsam ist. Präsentationsmetadaten umfassen die folgenden Felder, wie in Abschnitt *2.2.2.1* angegebenen:
> 
> * **MajorVersion**
> * **MinorVersion**
> * **TimeScale**
> * **Duration**
> * **IsLive**
> * **LookaheadCount**
> * **DVRWindowLength**
> 
>   Präsentationen mit HEVC-Streams SOLLEN Folgendes festlegen:

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (Hinweis: Boxes: veraltet)
> 
>   Präsentationen SOLLEN ebenfalls festlegen:

    TimeScale = 90000

>   Streamsammlung: Eine Sammlung von Datenelementen der Streambeschreibung, wie in Abschnitt *3.1.1.1.2* angegeben.
> 
>   Schutzbeschreibung: Eine Sammlung von Datenelementen für die Metadatenbeschreibung des Schutzsystems, wie in Abschnitt *3.1.1.1.1* angegeben.

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 Metadatenbeschreibung des Schutzsystems 

>   Das Datenelement für die Metadatenbeschreibung des Schutzsystems kapselt Metadaten, die für ein einzelnes Inhaltsschutzsystem spezifisch sind. (Keine Änderung)
> 
>   Schutzheaderbeschreibung: Metadaten für den Inhaltsschutz, die zu einem einzelnen Content Protection-System gehören. Die Schutzheaderbeschreibung umfasst die folgenden Felder, wie in Abschnitt *2.2.2.2* angegebenen:
> 
>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 Streambeschreibung 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 Titelbeschreibung 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 Benutzerdefinierte Attributbeschreibung 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 Fragmentverweisbeschreibung 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 Titelspezifische Fragmentverweisbeschreibung 

#### <a name="3112-fragment-description"></a>3.1.1.2 Fragmentbeschreibung 

##### <a name="31121-sample-description"></a>3.1.1.2.1 Samplebeschreibung 

### <a name="312-timers"></a>3.1.2 Zeitgeber 

### <a name="313-initialization"></a>3.1.3 Initialisierung 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 Ausgelöste Ereignisse auf höherer Ebene 

#### <a name="3141-open-presentation"></a>3.1.4.1 Öffnen einer Präsentation 

#### <a name="3142-get-fragment"></a>3.1.4.2 Abrufen eines Fragments 

#### <a name="3143-close-presentation"></a>3.1.4.3 Schließen einer Präsentation 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 Verarbeitung von Ereignissen und Sequenzierung von Regeln 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 Manifestanforderung und Manifestantwort 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 Fragmentanforderung und Fragmentantwort

## <a name="32-server-details"></a>3.2 Serverdetails

## <a name="33-live-encoder-details"></a>3.3 Details zum Liveencoder 

## <a name="4-protocol-examples"></a>4 Beispiele für Protokolle 

## <a name="5-security"></a>5 Sicherheit 

## <a name="51-security-considerations-for-implementers"></a>5.1 Sicherheitsüberlegungen für Ausführende

>   Wenn der mit diesem Protokoll transportierte Inhalt einen hohen kommerziellen Wert hat, sollte ein Inhaltsschutzsystem verwendet werden, um eine unbefugte Nutzung des Inhalts zu verhindern. **ProtectionElement** kann verwendet werden, um die mit der Nutzung eines Inhaltsschutzsystems verbundenen Metadaten zu übertragen. Geschützte Audio- und Videoinhalte SOLLTEN gemäß MPEG Common Encryption [ISO/IEC 23001-7], 2. Auflage, verschlüsselt werden: 2015 [ISO/IEC 23001-7].
> 
>   **Hinweis**: Für HEVC-Videos werden nur Slicedaten in VCL-NAL-Einheiten verschlüsselt. Sliceheader und andere NAL-Einheiten sind vor der Entschlüsselung für die Präsentationsanwendungen verfügbar. In einem sicheren Videopfad sind verschlüsselte Informationen für Präsentationsanwendungen nicht verfügbar.

## <a name="52-index-of-security-parameters"></a>5.2 Index von Sicherheitsparameter 


| **Sicherheitsparameter**  | **Abschnitt**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Felder für gemeinsame Verschlüsselung (Common Encryption) | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5.3 Felder für gemeinsame Verschlüsselung (Common Encryption)

Die folgenden Felder können bei Anwendung von Common Encryption in Fragmentantworten vorhanden sein, wie in [ISO/IEC 23001-7] oder [ISO/IEC 14496-12] angegeben:

1.  Protection System Specific Header Box („pssh“)

2.  Sample Encryption Box („senc“)

3.  Sample Auxiliary Information Offset Box („saio“)

4.  Sample Auxiliary Information Size Box („saiz“)

5.  Sample Group Description Box („sgpd“)

6.  Sample to Group Box („sbgp“)

-----------------------

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png

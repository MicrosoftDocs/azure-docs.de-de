---
title: Azure Media Services-Eingabemetadaten-Schema | Microsoft Docs
description: Dieses Thema enthält eine Übersicht über das Azure Media Services-Eingabemetadaten-Schema.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: fa4487b07f130947ac5da2a5dbae6776b06acbe7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463768"
---
# <a name="input-metadata"></a>Eingeben von Metadaten 

Ein Codierauftrag ist einem Eingabeasset (oder mehreren) zugeordnet, für das Sie einige Codieraufgaben durchführen möchten.  Nach Abschluss einer Aufgabe wird ein Ausgabeasset erzeugt.  Das Ausgabeasset enthält Videodaten, Audiodaten, Miniaturansichten, das Manifest usw. Das Ausgabeasset enthält auch eine Datei mit Metadaten zum Eingabeasset. Der Name der XML-Metadatendatei weist das folgende Format auf: &lt;Asset-ID&gt;_metadata.xml (z.B. „41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml“), wobei &lt;Asset-ID&gt; der „AssetId“-Wert des Eingabeassets ist.  

Media Services überprüft die eingegebenen Assets nicht präventiv, um Metadaten zu generieren. Eingabemetadaten werden nur dann als Artefakt generiert, wenn ein Eingabeasset in einem Auftrag verarbeitet wird. Dieses Artefakt wird dann in das Ausgabeasset geschrieben. Zum Generieren von Metadaten für Eingabe- und Ausgabeassets werden verschiedene Tools eingesetzt. Deshalb weicht das Schema der Eingabemetadaten leicht von dem der Ausgabemetadaten ab.

Wenn Sie die Metadatendatei untersuchen möchten, können Sie einen **SAS**-Locator erstellen und die Datei auf Ihren lokalen Computer herunterladen. Ein Beispiel zum Erstellen eines SAS-Locators mit der Möglichkeit zum Herunterladen der Datei finden Sie in den [.NET SDK-Erweiterungen für Media Services](media-services-dotnet-get-started.md).  

In diesem Artikel werden die Elemente und Typen des XML-Schemas beschrieben, auf denen die Eingabemetadaten (&lt;asset_id&gt;_metadata.xml) basieren.  Weitere Informationen zu der Datei, in der die Metadaten zum Ausgabeasset enthalten sind, finden Sie unter [Ausgeben von Metadaten](media-services-output-metadata-schema.md).  

Sie finden den [Schemacode](media-services-input-metadata-schema.md#code) und ein [XML-Beispiel](media-services-input-metadata-schema.md#xml) am Ende dieses Artikels.  
 

## <a name="AssetFiles"></a> AssetFiles-Element (Stammelement)
Enthält eine Auflistung der [AssetFile-Elemente](media-services-input-metadata-schema.md#AssetFile) für den Codierauftrag.  

Ein XML-Beispiel finden Sie am Ende dieses Artikels: [XML-Beispiel](media-services-input-metadata-schema.md#xml).  

| NAME | BESCHREIBUNG |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs="1" maxOccurs="unbounded" |Ein einzelnes untergeordnetes Element. Weitere Informationen finden Sie unter [AssetFile-Element](media-services-input-metadata-schema.md#AssetFile). |

## <a name="AssetFile"></a> AssetFile-Element
 Enthält Attribute und Elemente, mit denen eine Assetdatei beschrieben wird.  

 Ein XML-Beispiel finden Sie am Ende dieses Artikels: [XML-Beispiel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribute
| NAME | Type | BESCHREIBUNG |
| --- | --- | --- |
| **Name**<br /><br /> Erforderlich |**xs:string** |Name der Assetdatei |
| **Größe**<br /><br /> Erforderlich |**xs:long** |Größe der Assetdatei in Byte |
| **Duration**<br /><br /> Erforderlich |**xs:duration** |Dauer der Inhaltswiedergabe. Beispiel: Duration="PT25M37.757S". |
| **NumberOfStreams**<br /><br /> Erforderlich |**xs:int** |Anzahl von Datenströmen in der Assetdatei |
| **FormatNames**<br /><br /> Erforderlich |**xs: string** |Formatierung von Namen |
| **FormatVerboseNames**<br /><br /> Erforderlich |**xs: string** |Formatieren von ausführlichen Namen |
| **StartTime** |**xs:duration** |Startzeit des Inhalts. Beispiel: StartTime="PT2.669S". |
| **OverallBitRate** |**xs: int** |Durchschnittliche Bitrate der Assetdatei in KBit/s. |

> [!NOTE]
> Die folgenden vier untergeordneten Elemente müssen als Sequenz angezeigt werden.  
> 
> 

### <a name="child-elements"></a>Untergeordnete Elemente
| NAME | Type | BESCHREIBUNG |
| --- | --- | --- |
| **Programs**<br /><br /> minOccurs="0" | |Sammlung mit allen [Programs-Elementen](media-services-input-metadata-schema.md#Programs), wenn die Assetdatei das Format MPEG-TS hat. |
| **VideoTracks**<br /><br /> minOccurs="0" | |Jede physische Medienobjektdatei kann null oder mehr Videospuren enthalten, die zu einem entsprechenden Containerformat zusammengeführt werden. Dieses Element enthält eine Sammlung mit allen [VideoTracks](media-services-input-metadata-schema.md#VideoTracks), die Teil der Medienobjektdatei sind. |
| **AudioTracks**<br /><br /> minOccurs="0" | |Jede physische Assetdatei kann null oder mehr Audiospuren enthalten, die zu einem entsprechenden Containerformat zusammengeführt werden. Dieses Element enthält eine Sammlung mit allen [AudioTracks](media-services-input-metadata-schema.md#AudioTracks), die Teil der Medienobjektdatei sind. |
| **Metadaten**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Darstellung der Metadaten einer Assetdatei als Schlüssel-Wert-Zeichenfolge. Beispiel:<br /><br /> **&lt;Metadata key="language" value="eng" /&gt;** |

## <a name="TrackType"></a> TrackType
Ein XML-Beispiel finden Sie am Ende dieses Artikels: [XML-Beispiel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribute
| NAME | Type | BESCHREIBUNG |
| --- | --- | --- |
| **Id**<br /><br /> Erforderlich |**xs:int** |Nullbasierter Index dieser Audio- oder Videospur.<br /><br /> Dies bedeutet nicht unbedingt, dass die TrackID in einer MP4-Datei verwendet wird. |
| **Codec** |**xs:string** |Codeczeichenfolge der Videospur |
| **CodecLongName** |**xs: string** |Langer Name des Audio- oder Videospurcodecs |
| **TimeBase**<br /><br /> Erforderlich |**xs:string** |Gibt die Zeitbasis an. Beispiel: TimeBase="1/48000" |
| **NumberOfFrames** |**xs:int** |Anzahl von Bildern (für Videospuren) |
| **StartTime** |**xs: duration** |Gibt die Startzeit des Titels an. Beispiel: StartTime="PT2.669S" |
| **Duration** |**xs:duration** |Gibt die Dauer des Titels an. Beispiel: Duration="PTSampleFormat M37.757S". |

> [!NOTE]
> Die folgenden beiden untergeordneten Elemente müssen als Sequenz angezeigt werden.  
> 
> 

### <a name="child-elements"></a>Untergeordnete Elemente
| NAME | Type | BESCHREIBUNG |
| --- | --- | --- |
| **Disposition**<br /><br /> minOccurs="0" maxOccurs="1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Enthält Informationen zur Darstellung (z.B. die Eignung einer bestimmten Audiospur für Zuschauer mit Sehbehinderung). |
| **Metadaten**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Generische Schlüssel-Wert-Zeichenfolgen, die für verschiedene Informationen verwendet werden können. Beispiele: key="language" und value="eng". |

## <a name="AudioTrackType"></a> AudioTrackType (erbt von TrackType)
 **AudioTrackType** ist ein globaler komplexer Typ, der von [TrackType](media-services-input-metadata-schema.md#TrackType) erbt.  

 Der Typ stellt eine bestimmte Audiospur in der Assetdatei dar.  

 Ein XML-Beispiel finden Sie am Ende dieses Artikels: [XML-Beispiel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribute
| NAME | Type | BESCHREIBUNG |
| --- | --- | --- |
| **SampleFormat** |**xs:string** |Beispielformat |
| **ChannelLayout** |**xs: string** |Kanallayout |
| **Kanäle**<br /><br /> Erforderlich |**xs:int** |Anzahl von Audiokanälen (0 oder mehr) |
| **SamplingRate**<br /><br /> Erforderlich |**xs:int** |Audiosamplingrate in Stichproben/Sekunde oder Hz |
| **Bitrate** |**xs:int** |Die durchschnittliche Audiobitrate in Bit/s gemäß Berechnung anhand der Assetdatei. Nur die elementare Datenstrom-Nutzlast wird gezählt. Der Aufwand für das Verpacken wird in diesen Wert nicht einbezogen. |
| **BitsPerSample** |**xs:int** |Bits pro Stichprobe für den Formattyp „wFormatTag“. |

## <a name="VideoTrackType"></a> VideoTrackType (erbt von TrackType)
**VideoTrackType** ist ein globaler komplexer Typ, der von [TrackType](media-services-input-metadata-schema.md#TrackType) erbt.  

Der Typ stellt eine bestimmte Videospur in der Assetdatei dar.  

Ein XML-Beispiel finden Sie am Ende dieses Artikels: [XML-Beispiel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribute
| NAME | Type | BESCHREIBUNG |
| --- | --- | --- |
| **FourCC**<br /><br /> Erforderlich |**xs:string** |Videocodec-FourCC-Code |
| **Profil** |**xs: string** |Profil der Videospur |
| **Level** |**xs: string** |Ebene der Videospur |
| **PixelFormat** |**xs: string** |Pixelformat der Videospur |
| **Width**<br /><br /> Erforderlich |**xs:int** |Breite des codierten Videos in Pixel |
| **Height**<br /><br /> Erforderlich |**xs:int** |Höhe des codierten Videos in Pixel |
| **DisplayAspectRatioNumerator**<br /><br /> Erforderlich |**xs: double** |Seitenverhältnis-Zähler der Videoanzeige |
| **DisplayAspectRatioDenominator**<br /><br /> Erforderlich |**xs:double** |Seitenverhältnis-Nenner der Videoanzeige |
| **DisplayAspectRatioDenominator**<br /><br /> Erforderlich |**xs: double** |Seitenverhältnis-Zähler des Videosamples |
| **SampleAspectRatioNumerator** |**xs: double** |Seitenverhältnis-Zähler des Videosamples |
| **SampleAspectRatioNumerator** |**xs:double** |Seitenverhältnis-Nenner des Videosamples |
| **FrameRate**<br /><br /> Erforderlich |**xs:decimal** |Gemessene Videobildfrequenz im Format „.3f“ |
| **Bitrate** |**xs:int** |Die durchschnittliche Videobildfrequenz in KBit/s gemäß Berechnung anhand der Assetdatei. Nur die elementare Datenstrom-Nutzlast wird gezählt. Der Aufwand für das Verpacken wird nicht einbezogen. |
| **MaxGOPBitrate** |**xs: int** |Maximale durchschnittliche GOP-Bitrate für diese Videospur in KBit/s |
| **HasBFrames** |**xs:int** |Videospuranzahl von B-Bildern |

## <a name="MetadataType"></a> MetadataType
**MetadataType** ist ein globaler komplexer Typ, mit dem die Metadaten einer Assetdatei als Schlüssel-Wert-Zeichenfolgen beschrieben werden. Beispiele: key="language" und value="eng".  

Ein XML-Beispiel finden Sie am Ende dieses Artikels: [XML-Beispiel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribute
| NAME | Type | BESCHREIBUNG |
| --- | --- | --- |
| **key**<br /><br /> Erforderlich |**xs:string** |Schlüssel des Schlüssel-Wert-Paars |
| **value**<br /><br /> Erforderlich |**xs:string** |Wert des Schlüssel-Wert-Paars |

## <a name="ProgramType"></a> ProgramType
**ProgramType** ist ein globaler komplexer Typ, der ein Programm beschreibt.  

### <a name="attributes"></a>Attribute
| NAME | Type | BESCHREIBUNG |
| --- | --- | --- |
| **ProgramId**<br /><br /> Erforderlich |**xs:int** |Programm-ID |
| **NumberOfPrograms**<br /><br /> Erforderlich |**xs:int** |Anzahl von Programmen |
| **PmtPid**<br /><br /> Erforderlich |**xs:int** |PMTs (Program Map Tables) enthalten Informationen zu Programmen.  Weitere Informationen finden Sie unter [PMT](https://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid**<br /><br /> Erforderlich |**xs: int** |Wird vom Decoder verwendet. Weitere Informationen finden Sie unter [PCR](https://en.wikipedia.org/wiki/MPEG_transport_stream#PCR). |
| **StartPTS** |**xs: long** |Startzeitstempel der Darstellung (Presentation Time Stamp, PTS) |
| **EndPTS** |**xs: long** |Endzeitstempel der Darstellung |

## <a name="StreamDispositionType"></a> StreamDispositionType
**StreamDispositionType** ist ein globaler komplexer Typ, der den Datenstrom beschreibt.  

Ein XML-Beispiel finden Sie am Ende dieses Artikels: [XML-Beispiel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribute
| NAME | Type | BESCHREIBUNG |
| --- | --- | --- |
| **Standard**<br /><br /> Erforderlich |**xs: int** |Legen Sie dieses Attribut auf 1 fest, um anzugeben, dass dies die Standarddarstellung ist. |
| **Dub**<br /><br /> Erforderlich |**xs:int** |Legen Sie dieses Attribut auf 1 fest, um anzugeben, dass dies die Dubbing-Darstellung ist. |
| **Original**<br /><br /> Erforderlich |**xs: int** |Legen Sie dieses Attribut auf 1 fest, um anzugeben, dass dies die ursprüngliche Darstellung ist. |
| **Comment**<br /><br /> Erforderlich |**xs:int** |Legen Sie dieses Attribut auf 1 fest, um anzugeben, dass diese Spur Kommentar enthält. |
| **Lyrics**<br /><br /> Erforderlich |**xs:int** |Legen Sie dieses Attribut auf 1 fest, um anzugeben, dass diese Spur Songtexte enthält. |
| **Karaoke**<br /><br /> Erforderlich |**xs:int** |Legen Sie dieses Attribut auf 1 fest, um anzugeben, dass dies die Karaoke-Spur ist (Hintergrundmusik ohne Gesang). |
| **Forced**<br /><br /> Erforderlich |**xs:int** |Legen Sie dieses Attribut auf 1 fest, um anzugeben, dass dies die erzwungene Darstellung ist. |
| **HearingImpaired**<br /><br /> Erforderlich |**xs:int** |Legen Sie dieses Attribut auf 1 fest, um anzugeben, dass diese Spur für Schwerhörige bestimmt ist. |
| **VisualImpaired**<br /><br /> Erforderlich |**xs:int** |Legen Sie dieses Attribut auf 1 fest, um anzugeben, dass diese Spur für Personen mit Sehbehinderung bestimmt ist. |
| **CleanEffects**<br /><br /> Erforderlich |**xs: int** |Legen Sie dieses Attribut auf 1 fest, um anzugeben, dass diese Spur für Bereinigungseffekte bestimmt ist. |
| **AttachedPic**<br /><br /> Erforderlich |**xs: int** |Legen Sie dieses Attribut auf 1 fest, um anzugeben, dass diese Spur über Bilder verfügt. |

## <a name="Programs"></a> Programs-Element
Wrapperelement, das mehrere **Program**-Elemente enthält.  

### <a name="child-elements"></a>Untergeordnete Elemente
| NAME | Type | BESCHREIBUNG |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |Enthält für Assetdateien im MPEG-TS-Format Informationen zu den Programmen in der Assetdatei. |

## <a name="VideoTracks"></a> VideoTracks-Element
 Wrapperelement, das mehrere **VideoTrack**-Elemente enthält.  

 Ein XML-Beispiel finden Sie am Ende dieses Artikels: [XML-Beispiel](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Untergeordnete Elemente
| NAME | Type | BESCHREIBUNG |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[VideoTrackType (erbt von TrackType)](media-services-input-metadata-schema.md#VideoTrackType) |Enthält Informationen zu den Videospuren in der Assetdatei. |

## <a name="AudioTracks"></a> AudioTracks-Element
 Wrapperelement, das mehrere **AudioTrack**-Elemente enthält.  

 Ein XML-Beispiel finden Sie am Ende dieses Artikels: [XML-Beispiel](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>Elemente
| NAME | Type | BESCHREIBUNG |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[AudioTrackType (erbt von TrackType)](media-services-input-metadata-schema.md#AudioTrackType) |Enthält Informationen zu den Audiospuren in der Assetdatei. |

## <a name="code"></a> Schemacode
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
               xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               elementFormDefault="qualified">  

      <xs:complexType name="MetadataType">  
        <xs:attribute name="key"   type="xs:string" use="required"/>  
        <xs:attribute name="value" type="xs:string" use="required"/>  
      </xs:complexType>  

      <xs:complexType name="ProgramType">  
        <xs:attribute name="ProgramId" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Program Id</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfPrograms" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Number of programs</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PmtPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pmt pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PcrPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pcr pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="StartPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>start pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="EndPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>end pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="StreamDispositionType">  
        <xs:attribute name="Default"          type="xs:int" use="required" />  
        <xs:attribute name="Dub"              type="xs:int" use="required" />  
        <xs:attribute name="Original"         type="xs:int" use="required" />  
        <xs:attribute name="Comment"          type="xs:int" use="required" />  
        <xs:attribute name="Lyrics"           type="xs:int" use="required" />  
        <xs:attribute name="Karaoke"          type="xs:int" use="required" />  
        <xs:attribute name="Forced"           type="xs:int" use="required" />  
        <xs:attribute name="HearingImpaired"  type="xs:int" use="required" />  
        <xs:attribute name="VisualImpaired"   type="xs:int" use="required" />  
        <xs:attribute name="CleanEffects"     type="xs:int" use="required" />  
        <xs:attribute name="AttachedPic"      type="xs:int" use="required" />  
      </xs:complexType>  

      <xs:complexType name="TrackType" abstract="true">  
        <xs:sequence>  
          <xs:element name="Disposition" type="StreamDispositionType" minOccurs="0" maxOccurs="1"/>  
          <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded"/>  
        </xs:sequence>  
        <xs:attribute name="Id" use="required">  
          <xs:annotation>  
            <xs:documentation>zero-based index of this video track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Codec" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec string</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="CodecLongName" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec long name</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="TimeBase"  type="xs:string" use="required">  
          <xs:annotation>  
            <xs:documentation>Time base. Example: TimeBase="1/48000"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfFrames">  
          <xs:annotation>  
            <xs:documentation>number of frames</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="StartTime" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track start time. Example: StartTime="PT2.669S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Duration" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track duration. Example: Duration="PT25M37.757S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="VideoTrackType">  
        <xs:annotation>  
          <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="FourCC" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>video codec FourCC code</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Profile" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>profile</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Level" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>level</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="PixelFormat" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>Video track's pixel format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Width" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video width in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Height" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video height in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioNumerator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioDenominator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioNumerator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioDenominator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="FrameRate" use="required">  
              <xs:annotation>  
                <xs:documentation>measured video frame rate in .3f format</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:decimal">  
                  <xs:minInclusive value="0"/>  
                  <xs:fractionDigits value="3"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="MaxGOPBitrate">  
              <xs:annotation>  
                <xs:documentation>Max GOP average bitrate for this video track, in kilobits per second</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="HasBFrames" type="xs:int">  
              <xs:annotation>  
                <xs:documentation>video track number of B frames</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:complexType name="AudioTrackType">  
        <xs:annotation>  
          <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="SampleFormat"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>sample format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="ChannelLayout"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>channel layout</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Channels" use="required">  
              <xs:annotation>  
                <xs:documentation>number of audio channels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SamplingRate" use="required">  
              <xs:annotation>  
                <xs:documentation>audio sampling rate in samples/sec or Hz</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="BitsPerSample">  
              <xs:annotation>  
                <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:element name="AssetFiles">  
        <xs:annotation>  
          <xs:documentation>Collection of AssetFile entries for the encoding job</xs:documentation>  
        </xs:annotation>  
        <xs:complexType>  
          <xs:sequence>  
            <xs:element name="AssetFile" minOccurs="1" maxOccurs="unbounded">  
              <xs:annotation>  
                <xs:documentation>asset file</xs:documentation>  
              </xs:annotation>  
              <xs:complexType>  
                <xs:sequence>  
                  <xs:element name="Programs" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>This is the collection of all programs when file is MPEG-TS</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="Program" type="ProgramType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="VideoTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="VideoTrack" type="VideoTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="AudioTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="AudioTrack" type="AudioTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded" />  
                </xs:sequence>  
                <xs:attribute name="Name" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>the media asset file name</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="Size" use="required">  
                  <xs:annotation>  
                    <xs:documentation>size of file in bytes</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:long">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
                <xs:attribute name="Duration" type="xs:duration" use="required">  
                  <xs:annotation>  
                    <xs:documentation>content play back duration. Example: Duration="PT25M37.757S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="NumberOfStreams" type="xs:int" use="required">  
                  <xs:annotation>  
                    <xs:documentation>number of streams in asset file</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatNames" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatVerboseName" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format verbose names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="StartTime" type="xs:duration">  
                  <xs:annotation>  
                    <xs:documentation>content start time. Example: StartTime="PT2.669S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="OverallBitRate">  
                  <xs:annotation>  
                    <xs:documentation>average bitrate of the asset file in kbps</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:int">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
              </xs:complexType>  
            </xs:element>  
          </xs:sequence>  
        </xs:complexType>  
      </xs:element>  
    </xs:schema>  


## <a name="xml"></a> XML-Beispiel
Unten ist ein Beispiel für die Datei mit den Eingabemetadaten angegeben.  

    <?xml version="1.0" encoding="utf-8"?>  
    <AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
      <AssetFile Name="bear.mp4" Size="1973733" Duration="PT12.678S" NumberOfStreams="2" FormatNames="mov,mp4,m4a,3gp,3g2,mj2" FormatVerboseName="QuickTime / MOV" StartTime="PT0S" OverallBitRate="1245">  
        <VideoTracks>  
          <VideoTrack Id="1" Codec="h264" CodecLongName="H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10" TimeBase="1/29970" NumberOfFrames="375" StartTime="PT0.034S" Duration="PT12.645S" FourCC="avc1" Profile="High" Level="4.1" PixelFormat="yuv420p" Width="512" Height="384" DisplayAspectRatioNumerator="4" DisplayAspectRatioDenominator="3" SampleAspectRatioNumerator="1" SampleAspectRatioDenominator="1" FrameRate="29.656" Bitrate="1043" HasBFrames="1">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Video Media Handler" />  
          </VideoTrack>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="aac" CodecLongName="AAC (Advanced Audio Coding)" TimeBase="1/44100" NumberOfFrames="546" StartTime="PT0S" Duration="PT12.678S" SampleFormat="fltp" ChannelLayout="stereo" Channels="2" SamplingRate="44100" Bitrate="156" BitsPerSample="0">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Sound Media Handler" />  
          </AudioTrack>  
        </AudioTracks>  
        <Metadata key="major_brand" value="mp42" />  
        <Metadata key="minor_version" value="0" />  
        <Metadata key="compatible_brands" value="mp42mp41" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:53" />  
        <Metadata key="comment" value="Courtesy of National Geographic.  Used by Permission." />  
      </AssetFile>  
    </AssetFiles>  

## <a name="next-steps"></a>Nächste Schritte
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


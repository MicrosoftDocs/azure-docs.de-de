---
title: Azure Media Services-Ausgabemetadaten-Schema | Microsoft Docs
description: Dieses Thema enthält eine Übersicht über das Azure Media Services-Ausgabemetadaten-Schema.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1ed84c88-eea5-4a24-9c4f-f2428157d08a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 42227095c69924cd2922673d020b349aa29f2daa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61129745"
---
# <a name="output-metadata"></a>Ausgeben von Metadaten
## <a name="overview"></a>Übersicht
Ein Codierauftrag ist einem (oder mehreren) Eingangsmedienobjekt zugeordnet, für das Sie einige Codieraufgaben durchführen möchten. Beispiele: Codieren einer MP4-Datei in H.264 MP4 Adaptive Bitrate-Sätze, Erstellen einer Miniaturansicht, Erstellen von Überlagerungen. Nach Abschluss einer Aufgabe wird ein Ausgabemedienobjekt erzeugt.  Das Ausgabemedienobjekt enthält Videodaten, Audiodaten, Miniaturansichten usw. Das Ausgabemedienobjekt enthält auch eine Datei mit Metadaten zum Ausgabemedienobjekt. Der Name der XML-Metadatendatei weist das folgende Format auf: &lt;Quelldateiname&gt;_manifest.xml (z.B. BigBuckBunny_manifest.xml).  

Media Services überprüft die eingegebenen Assets nicht präventiv, um Metadaten zu generieren. Eingabemetadaten werden nur dann als Artefakt generiert, wenn ein Eingabeasset in einem Auftrag verarbeitet wird. Dieses Artefakt wird dann in das Ausgabeasset geschrieben. Zum Generieren von Metadaten für Eingabe- und Ausgabeassets werden verschiedene Tools eingesetzt. Deshalb weicht das Schema der Eingabemetadaten leicht von dem der Ausgabemetadaten ab.

Wenn Sie die Metadatendatei untersuchen möchten, können Sie einen **SAS**-Locator erstellen und die Datei auf Ihren lokalen Computer herunterladen.  

In diesem Artikel werden die Elemente und Typen des XML-Schemas beschrieben, auf denen die Ausgabemetadaten (&lt;Quelldateiname&gt;_manifest.xml) basieren. Weitere Informationen zur Datei, in der die Metadaten zum Eingabemedienobjekt enthalten sind, finden Sie unter „Eingeben von Metadaten (veraltet)“.  

Sie finden den vollständigen Schemacode und das XML-Beispiel am Ende des Artikels.  

## <a name="AssetFiles"></a> „AssetFiles“-Stammelement
Sammlung von „AssetFile“-Einträgen für den Codierauftrag.  

### <a name="child-elements"></a>Untergeordnete Elemente
| NAME | BESCHREIBUNG |
| --- | --- |
| **AssetFile**<br/><br/> minOccurs="0" maxOccurs="1" |Ein AssetFile-Element, das Teil der AssetFiles-Sammlung ist. |

## <a name="AssetFile"></a> AssetFile-Element
[Hier](#xml) finden Sie ein XML-Beispiel.  

### <a name="attributes"></a>Attribute
| NAME | Type | BESCHREIBUNG |
| --- | --- | --- |
| **Name**<br/><br/> Erforderlich |**xs:string** |Der Name der Medienobjektdatei. |
| **Größe**<br/><br/> minInclusive ="0"<br/><br/> Erforderlich |**xs:long** |Größe der Assetdatei in Byte |
| **Duration**<br/><br/> Erforderlich |**xs:duration** |Dauer der Inhaltswiedergabe. |

### <a name="child-elements"></a>Untergeordnete Elemente
| NAME | BESCHREIBUNG |
| --- | --- |
| **Sources** |Sammlung von Eingabe-/Quellmediendateien, die verarbeitet wurde, um diese Medienobjektdatei zu erzeugen. Weitere Informationen finden Sie unter „Source-Element“. |
| **VideoTracks**<br/><br/> minOccurs="0" maxOccurs="1" |Jede physische Medienobjektdatei kann null oder mehr Videospuren enthalten, die zu einem entsprechenden Containerformat zusammengeführt werden. Weitere Informationen finden Sie unter „VideoTracks-Element“. |
| **AudioTracks**<br/><br/> minOccurs="0" maxOccurs="1" |Jede physische Medienobjektdatei kann null oder mehr Audiospuren enthalten, die zu einem entsprechenden Containerformat zusammengeführt werden. Dies ist die Sammlung aller dieser Audiospuren. Weitere Informationen finden Sie unter „AudioTracks-Element“. |

## <a name="Sources"></a> „Sources“-Element
Sammlung von Eingabe-/Quellmediendateien, die verarbeitet wurde, um diese Medienobjektdatei zu erzeugen.  

[Hier](#xml) finden Sie ein XML-Beispiel.  

### <a name="child-elements"></a>Untergeordnete Elemente
| NAME | BESCHREIBUNG |
| --- | --- |
| **Quelle**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Ein Eingabe-/Quelldatei, die zum Generieren dieses Medienobjekts verwendet wird. Weitere Informationen finden Sie unter „Source-Element“. |

## <a name="Source"></a> „Source“-Element
Ein Eingabe-/Quelldatei, die zum Generieren dieses Medienobjekts verwendet wird.  

[Hier](#xml) finden Sie ein XML-Beispiel.  

### <a name="attributes"></a>Attribute
| NAME | Type | BESCHREIBUNG |
| --- | --- | --- |
| **Name**<br/><br/> Erforderlich |**xs:string** |Name der Eingabequelldatei. |

## <a name="VideoTracks"></a> VideoTracks-Element
Jede physische Medienobjektdatei kann null oder mehr Videospuren enthalten, die zu einem entsprechenden Containerformat zusammengeführt werden. Das **VideoTracks**-Element stellt eine Sammlung aller Videotitel dar.  

[Hier](#xml) finden Sie ein XML-Beispiel.  

### <a name="child-elements"></a>Untergeordnete Elemente
| NAME | BESCHREIBUNG |
| --- | --- |
| **VideoTrack**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Eine bestimmte Videospur im übergeordneten Mediendateiobjekt. Weitere Informationen finden Sie unter „VideoTrack-Element“. |

## <a name="VideoTrack"></a> „VideoTrack“-Element
Eine bestimmte Videospur im übergeordneten Mediendateiobjekt.  

[Hier](#xml) finden Sie ein XML-Beispiel.  

### <a name="attributes"></a>Attribute
| NAME | Type | BESCHREIBUNG |
| --- | --- | --- |
| **Id**<br/><br/> minInclusive ="0"<br/><br/> Erforderlich |**xs:int** |Nullbasierter Index dieser Videospur. **Hinweis:**  Diese **ID** ist nicht unbedingt die „TrackID“, die in einer MP4-Datei verwendet wird. |
| **FourCC**<br/><br/> Erforderlich |**xs:string** |Videocodec-FourCC-Code |
| **Profil** |**xs:string** |H264-Profil (gilt nur für den H264-Codec). |
| **Level** |**xs:string** |H264-Pegel (gilt nur für den H264-Codec). |
| **Width**<br/><br/> minInclusive ="0"<br/><br/> Erforderlich |**xs:int** |Breite des codierten Videos in Pixel |
| **Height**<br/><br/> minInclusive ="0"<br/><br/> Erforderlich |**xs:int** |Höhe des codierten Videos in Pixel |
| **DisplayAspectRatioNumerator**<br/><br/> minInclusive ="0"<br/><br/> Erforderlich |**xs:double** |Seitenverhältnis-Zähler der Videoanzeige |
| **DisplayAspectRatioDenominator**<br/><br/> minInclusive ="0"<br/><br/> Erforderlich |**xs:double** |Seitenverhältnis-Nenner der Videoanzeige. |
| **Framerate**<br/><br/> minInclusive ="0"<br/><br/> Erforderlich |**xs:decimal** |Gemessene Videobildfrequenz im Format „.3f“. |
| **TargetFramerate**<br/><br/> minInclusive ="0"<br/><br/> Erforderlich |**xs:decimal** |Voreingestellte Videobildfrequenz im Format „.3f“. |
| **Bitrate**<br/><br/> minInclusive ="0"<br/><br/> Erforderlich |**xs:int** |Die durchschnittliche Videobildfrequenz in kBit/s gemäß Berechnung anhand der Medienobjektdatei. Zählt nur die elementare Datenstrom-Nutzlast, ohne den Aufwand für das Packen einzubeziehen. |
| **TargetBitrate**<br/><br/> minInclusive ="0"<br/><br/> Erforderlich |**xs:int** |Durchschnittliche Bitrate dieser Videospur entsprechend der Anforderung der Codierungsvoreinstellung in kBit/s. |
| **MaxGOPBitrate**<br/><br/> minInclusive ="0" |**xs:int** |Maximale durchschnittliche GOP-Bitrate für diese Videospur in kBit/s. |

## <a name="AudioTracks"></a> „AudioTracks“-Element
Jede physische Medienobjektdatei kann null oder mehr Audiospuren enthalten, die zu einem entsprechenden Containerformat zusammengeführt werden. Das **AudioTracks**-Element stellt eine Sammlung aller Audiotitel dar.  

[Hier](#xml) finden Sie ein XML-Beispiel.  

### <a name="child-elements"></a>Untergeordnete Elemente
| NAME | BESCHREIBUNG |
| --- | --- |
| **AudioTrack**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Eine bestimmte Audiospur im übergeordneten Mediendateiobjekt. Weitere Informationen finden Sie unter „AudioTrack-Element“. |

## <a name="AudioTrack"></a> „AudioTrack“-Element
Eine bestimmte Audiospur im übergeordneten Mediendateiobjekt.  

[Hier](#xml) finden Sie ein XML-Beispiel.  

### <a name="attributes"></a>Attribute
| NAME | Type | BESCHREIBUNG |
| --- | --- | --- |
| **Id**<br/><br/> minInclusive ="0"<br/><br/> Erforderlich |**xs:int** |Nullbasierter Index dieser Audiospur. **Hinweis:**  Dies ist nicht unbedingt die „TrackID“, die in einer MP4-Datei verwendet wird. |
| **Codec** |**xs:string** |Codec-Zeichenfolge der Audiospur. |
| **EncoderVersion** |**xs:string** |Optionale Zeichenfolge mit der Encoderversion, für EAC3 erforderlich. |
| **Channels**<br/><br/> minInclusive ="0"<br/><br/> Erforderlich |**xs:int** |Anzahl der Audiokanäle. |
| **SamplingRate**<br/><br/> minInclusive ="0"<br/><br/> Erforderlich |**xs:int** |Audiosamplingrate in Stichproben/Sekunde oder Hz |
| **Bitrate**<br/><br/> minInclusive ="0"<br/><br/> Erforderlich |**xs:int** |Die durchschnittliche Audiobitrate in Bit/s gemäß Berechnung anhand der Medienobjektdatei. Zählt nur die elementare Datenstrom-Nutzlast, ohne den Aufwand für das Packen einzubeziehen. |
| **BitsPerSample**<br/><br/> minInclusive ="0"<br/><br/> Erforderlich |**xs:int** |Bits pro Sample für den Formattyp „wFormatTag“. |

### <a name="child-elements"></a>Untergeordnete Elemente
| NAME | BESCHREIBUNG |
| --- | --- |
| **LoudnessMeteringResultParameters**<br/><br/> minOccurs="0" maxOccurs="1" |Parameter für das Messergebnis der Lautheit. Weitere Informationen finden Sie unter „LoudnessMeteringResultParameters-Element“. |

## <a name="LoudnessMeteringResultParameters"></a> „LoudnessMeteringResultParameters“-Element
Parameter für das Messergebnis der Lautheit.  

[Hier](#xml) finden Sie ein XML-Beispiel.  

### <a name="attributes"></a>Attribute
| NAME | Type | BESCHREIBUNG |
| --- | --- | --- |
| **DPLMVersionInformation** |**xs:string** |Development Kit-Version von **Dolby** Professional Loudness Metering (DPLM). |
| **DialogNormalization**<br/><br/> minInclusive="-31" maxInclusive="-1"<br/><br/> Erforderlich |**xs:int** |Über DPLM generierte „DialogNormalization“, bei Festlegung von „LoudnessMetering“ erforderlich. |
| **IntegratedLoudness**<br/><br/> minInclusive="-70" maxInclusive="10"<br/><br/> Erforderlich |**xs: float** |Integrierte Lautheit |
| **IntegratedLoudnessUnit**<br/><br/> Erforderlich |**xs:string** |Einheit der integrierten Lautheit. |
| **IntegratedLoudnessGatingMethod**<br/><br/> Erforderlich |**xs:string** |Gating-ID |
| **IntegratedLoudnessSpeechPercentage**<br/><br/> minInclusive ="0" maxInclusive="100" |**xs: float** |Sprachinhalt über das Programm als Prozentsatz. |
| **SamplePeak**<br/><br/> Erforderlich |**xs: float** |Absoluter Spitzenwert der Samples seit dem Zurücksetzen oder letzten Löschvorgang pro Kanal.  Die Einheit ist dbFS. |
| **SamplePeakUnit**<br/><br/> fixed="dBFS"<br/><br/> Erforderlich |**xs:anySimpleType** |Spitzeneinheit des Samples. |
| **TruePeak**<br/><br/> Erforderlich |**xs: float** |Maximaler echter Spitzenwert gemäß ITU-R BS.1770-2 seit dem Zurücksetzen oder letzten Löschvorgang pro Kanal. Die Einheit ist dBTP. |
| **TruePeakUnit**<br/><br/> fixed="dBTP"<br/><br/> Erforderlich |**xs:anySimpleType** |Echte Spitzeneinheit. |

## <a name="schema-code"></a>Schemacode
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.2"  
               xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
               targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
               elementFormDefault="qualified">  
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
                  <xs:element name="Sources">  
                    <xs:annotation>  
                      <xs:documentation>Collection of input/source media files, that was processed in order to produce this AssetFile</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="Source" minOccurs="1" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>An input/source file used when generating this asset</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
                            <xs:attribute name="Name" type="xs:string" use="required">  
                              <xs:annotation>  
                                <xs:documentation>input source file name</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                          </xs:complexType>  
                        </xs:element>  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="VideoTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="VideoTrack" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
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
                            <xs:attribute name="FourCC" type="xs:string" use="required">  
                              <xs:annotation>  
                                <xs:documentation>video codec FourCC code</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="Profile" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>H264 profile (only appliable for H264 codec)</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="Level" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>H264 level (only appliable for H264 codec)</xs:documentation>  
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
                            <xs:attribute name="Framerate" use="required">  
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
                            <xs:attribute name="TargetFramerate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>preset target video frame rate in .3f format</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:decimal">  
                                  <xs:minInclusive value="0"/>  
                                  <xs:fractionDigits value="3"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Bitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="TargetBitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>target average bitrate for this video track, as requested via the encoding preset, in kilobits per second</xs:documentation>  
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
                          </xs:complexType>  
                        </xs:element>  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="AudioTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="AudioTrack" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
                            <xs:sequence>  
                              <xs:element name="LoudnessMeteringResultParameters" minOccurs="0" maxOccurs="1">  
                                <xs:annotation>  
                                  <xs:documentation>Loudness Metering Result Parameters</xs:documentation>  
                                </xs:annotation>  
                                <xs:complexType>  
                                  <xs:attribute name="DPLMVersionInformation" type="xs:string">  
                                    <xs:annotation>  
                                      <xs:documentation>Dolby Professional Loudness Metering Development Kit Version</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="DialogNormalization" use="required">  
                                    <xs:annotation>  
                                      <xs:documentation> DialogNormalization generated through DPLM, required when LoudnessMetering is set</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:int">  
                                        <xs:minInclusive value="-31"/>  
                                        <xs:maxInclusive value="-1"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudness" use="required">  
                                    <xs:annotation>  
                                      <xs:documentation>Integrated loudness</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:float">  
                                        <xs:minInclusive value="-70"/>  
                                        <xs:maxInclusive value="10"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessUnit" use="required" type="xs:string">  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessGatingMethod" use="required" type="xs:string">  
                                    <xs:annotation>  
                                      <xs:documentation>Gating identifier</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessSpeechPercentage">  
                                    <xs:annotation>  
                                      <xs:documentation>Speech content over the program, as a percentage.</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:float">  
                                        <xs:minInclusive value="0"/>  
                                        <xs:maxInclusive value="100"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="SamplePeak" use="required" type="xs:float">  
                                    <xs:annotation>  
                                      <xs:documentation>Peak absolute sample value, since reset or since it was last cleared, per channel.  Units are dBFS.</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="SamplePeakUnit" use="required" fixed="dBFS">  
                                  </xs:attribute>  
                                  <xs:attribute name="TruePeak" use="required" type="xs:float">  
                                    <xs:annotation>  
                                      <xs:documentation>Maximum True Peak value, as per ITU-R BS.1770-2, since reset or since it was last cleared, per channel.  Units are dBTP.</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="TruePeakUnit" use="required" fixed="dBTP">  
                                  </xs:attribute>  
                                </xs:complexType>  
                              </xs:element>  
                            </xs:sequence>  
                            <xs:attribute name="Id" use="required">  
                              <xs:annotation>  
                                <xs:documentation>zero-based index of this audio track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Codec" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>audio track codec string</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="EncoderVersion" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>optional encoder version string, required for EAC3</xs:documentation>  
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
                            <xs:attribute name="Bitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="BitsPerSample" use="required">  
                              <xs:annotation>  
                                <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
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
                <xs:attribute name="Duration" use="required">  
                  <xs:annotation>  
                    <xs:documentation>content play back duration</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:duration"/>  
                  </xs:simpleType>  
                </xs:attribute>  
              </xs:complexType>  
            </xs:element>  
          </xs:sequence>  
        </xs:complexType>  
      </xs:element>  
    </xs:schema>  



## <a name="xml"></a> XML-Beispiel

Es folgt ein XML-Beispiel der Datei mit den Ausgabemetadaten.  

    <AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema"   
                xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata">  
      <AssetFile Name="BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4" Size="4646283" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.2" Width="1280" Height="720" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="4250" TargetBitrate="3400" MaxGOPBitrate="5514"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4" Size="3166728" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="2846" TargetBitrate="2250" MaxGOPBitrate="3630"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4" Size="2205095" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1932" TargetBitrate="1500" MaxGOPBitrate="2513"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4" Size="1508567" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1271" TargetBitrate="1000" MaxGOPBitrate="1527"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4" Size="1057155" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="843" TargetBitrate="650" MaxGOPBitrate="1086"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4" Size="699262" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="1.3" Width="320" Height="180" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="503" TargetBitrate="400" MaxGOPBitrate="661"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_AAC_und_ch2_96kbps.mp4" Size="166780" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_AAC_und_ch2_56kbps.mp4" Size="124576" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="53" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
    </AssetFiles>  

## <a name="next-steps"></a>Nächste Schritte
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

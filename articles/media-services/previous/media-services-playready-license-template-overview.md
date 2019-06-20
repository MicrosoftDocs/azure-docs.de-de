---
title: Media Services PlayReady-Lizenzvorlage – Übersicht
description: Dieses Thema bietet einen Überblick über eine PlayReady-Lizenzvorlage, mit der PlayReady-Lizenzen konfiguriert werden können.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: fddce5d0-1278-478f-ae05-9b985c748731
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: d2831472bcddf3480a87e69661b39e4ec715c786
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463207"
---
# <a name="media-services-playready-license-template-overview"></a>Media Services PlayReady-Lizenzvorlage – Übersicht
Azure Media Services bietet jetzt einen Dienst für die Bereitstellung von PlayReady-Lizenzen. Wenn der Player (z. B. Silverlight) versucht, Ihre durch PlayReady geschützten Inhalte wiederzugeben, wird eine Anforderung zum Erwerben einer Lizenz an den Lizenzübermittlungsdienst gesendet. Wenn der Lizenzdienst die Anforderung genehmigt, wird die Lizenz ausgegeben. Diese wird an den Client gesendet und zum Entschlüsseln und Wiedergeben des angegebenen Inhalts verwendet.

Media Services bietet außerdem APIs, mit denen Sie Ihre PlayReady-Lizenzen konfigurieren können. Lizenzen enthalten die Rechte und Einschränkungen, die von der PlayReady-DRM-Laufzeit durchgesetzt werden sollen, wenn ein Benutzer versucht, geschützte Inhalte wiederzugeben.
Hier finden Sie einige Beispiele für Einschränkungen für PlayReady-Lizenzen, die Sie angeben können:

* Das Datum und die Uhrzeit, ab wann die Lizenz gültig ist.
* Der DateTime-Wert für den Ablauf der Lizenz. 
* Ob die Lizenz im permanenten Speicher auf dem Client gespeichert werden soll. Permanente Lizenzen werden üblicherweise verwendet, um die Offline-Wiedergabe des Inhalts zu ermöglichen.
* Die Sicherheitsstufe, die ein Player zum Wiedergeben Ihres Inhalts mindestens aufweisen muss. 
* Die Schutzebene für die Ausgabesteuerelemente für Audio-/Videoinhalte. 
* Weitere Informationen finden Sie im Abschnitt zu „Ausgabesteuerelementen“ (3.5) im Dokument zu [PlayReady-Kompatibilitätsregeln](https://www.microsoft.com/playready/licensing/compliance/).

> [!NOTE]
> Derzeit können Sie nur PlayRight der PlayReady-Lizenz konfigurieren. Dieses Recht ist erforderlich. PlayRight ermöglicht es dem Client, den Inhalt wiederzugeben. Mit PlayRight können Sie auch spezifische Einschränkungen der Wiedergabe konfigurieren. Weitere Informationen finden Sie unter [PlayReadyPlayRight](media-services-playready-license-template-overview.md#PlayReadyPlayRight).
> 
> 

Zum Konfigurieren von PlayReady-Lizenzen mit Media Services müssen Sie die Media Services PlayReady-Lizenzvorlage konfigurieren. Die Vorlage wird im XML-Format definiert.

Das folgende Beispiel zeigt die einfachste (und am häufigsten verwendete) Vorlage, mit der eine grundlegende Streaming-Lizenz konfiguriert wird. Mit dieser Lizenz können Ihre Clients Ihre mit PlayReady geschützten Inhalte wiedergeben.

    <?xml version="1.0" encoding="utf-8"?>
    <PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" 
                                      xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
      <LicenseTemplates>
        <PlayReadyLicenseTemplate>
          <ContentKey i:type="ContentEncryptionKeyFromHeader" />
          <PlayRight />
        </PlayReadyLicenseTemplate>
      </LicenseTemplates>
    </PlayReadyLicenseResponseTemplate>

Der XML-Code entspricht dem XML-Schema der PlayReady-Lizenzvorlage, das im Abschnitt zum XML-Schema der PlayReady-Lizenzvorlage definiert wurde.

Media Services definiert auch eine Reihe von .NET-Klassen, die für die Serialisierung und Deserialisierung in und aus XML verwendet werden können. Eine Beschreibung der Hauptklassen finden Sie unter den [Media Services-.NET-Klassen](media-services-playready-license-template-overview.md#classes), die zum Konfigurieren von Lizenzvorlagen verwendet werden.

Ein End-to-End-Beispiel, in dem mit .NET-Klassen die PlayReady-Lizenzvorlage konfiguriert wird, finden Sie unter [Verwenden der dynamischen PlayReady-Verschlüsselung und des Lizenzübermittlungsdiensts](media-services-protect-with-playready-widevine.md).

## <a id="classes"></a>Media Services-.NET-Klassen zum Konfigurieren von Lizenzvorlagen
Die folgenden Klassen sind die wichtigsten .NET-Klassen zum Konfigurieren von PlayReady-Lizenzvorlagen für Media Services. Diese Klassen sind den im [XML-Schema für PlayReady-Lizenzvorlagen](media-services-playready-license-template-overview.md#schema)definierten Typen zugeordnet.

Die [MediaServicesLicenseTemplateSerializer](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.mediaserviceslicensetemplateserializer.aspx) -Klasse wird zum Serialisieren und Deserialisieren in und aus dem XML-Code für die Media Services-Lizenzvorlage verwendet.

### <a name="playreadylicenseresponsetemplate"></a>PlayReadyLicenseResponseTemplate
[PlayReadyLicenseResponseTemplate:](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicenseresponsetemplate.aspx) Diese Klasse stellt die Vorlage für die Antwort dar, die an den Benutzer zurückgesendet wird. Sie enthält ein Feld für eine benutzerdefinierte Datenzeichenfolge zwischen dem Lizenzserver und der Anwendung (kann für benutzerdefinierte Anwendungslogik nützlich sein). Sie enthält auch eine Liste mit einer oder mehreren Lizenzvorlagen.

Als „Klasse auf oberster Ebene“ in der Vorlagenhierarchie enthält die Antwortvorlage eine Liste von Lizenzvorlagen. Die Lizenzvorlagen umfassen (direkt oder indirekt) alle anderen Klassen, aus denen sich die zu serialisierenden Vorlagendaten zusammensetzen.

### <a name="playreadylicensetemplate"></a>PlayReadyLicenseTemplate
[PlayReadyLicenseTemplate:](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicensetemplate.aspx) Diese Klasse stellt eine Lizenzvorlage zum Erstellen von PlayReady-Lizenzen dar, die an Benutzer zurückgegeben werden. Sie enthält die Daten zum Inhaltsschlüssel in der Lizenz. Sie enthält auch alle Rechte oder Einschränkungen, die die PlayReady DRM-Runtime bei der Verwendung des Inhaltsschlüssels durchsetzen muss.

### <a id="PlayReadyPlayRight"></a>PlayReadyPlayRight
[PlayReadyPlayRight:](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadyplayright.aspx) Diese Klasse stellt PlayRight für eine PlayReady-Lizenz dar. Sie gewährt dem Benutzer die Möglichkeit, Inhalte gemäß den Einschränkungen wiederzugeben, die in der Lizenz oder in PlayRight selbst (für wiedergabespezifische Richtlinien) konfiguriert wurden. Ein Großteil der Richtlinien für PlayRight betrifft Ausgabeeinschränkungen, die die Art der Ausgaben steuern, über die der Inhalt wiedergegeben werden kann. Sie enthält auch alle Einschränkungen, die bei der Verwendung einer bestimmten Ausgabe festgelegt werden müssen. Wenn z. B. „DigitalVideoOnlyContentRestriction“ aktiviert ist, erlaubt die DRM-Runtime nur die Anzeige des Videos über digitale Ausgänge. (Analogen Videoausgaben ist es nicht gestattet, Inhalte zu übergeben.)

> [!IMPORTANT]
> Dieser Einschränkungstypen können effektiv sein, aber auch die Benutzerfreundlichkeit beeinträchtigen. Wenn der Ausgabeschutz zu viele Einschränkungen aufweist, kann der Inhalt möglicherweise in einigen Clients nicht wiedergegeben werden. Weitere Informationen finden Sie unter den [Regeln zur PlayReady Compliance](https://www.microsoft.com/playready/licensing/compliance/).
> 
> 

Ein Beispiel für die von Silverlight unterstützten Schutzebenen finden Sie unter [Silverlight-Unterstützung für Ausgabeschutz](https://go.microsoft.com/fwlink/?LinkId=617318).

## <a id="schema"></a>XML-Schema für PlayReady-Lizenzvorlage
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:ser="http://schemas.microsoft.com/2003/10/Serialization/" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
      <xs:import namespace="http://schemas.microsoft.com/2003/10/Serialization/" />
      <xs:complexType name="AgcAndColorStripeRestriction">
        <xs:sequence>
          <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction" />
      <xs:simpleType name="ContentType">
        <xs:restriction base="xs:string">
          <xs:enumeration value="Unspecified" />
          <xs:enumeration value="UltravioletDownload" />
          <xs:enumeration value="UltravioletStreaming" />
        </xs:restriction>
      </xs:simpleType>
      <xs:element name="ContentType" nillable="true" type="tns:ContentType" />
      <xs:complexType name="ExplicitAnalogTelevisionRestriction">
        <xs:sequence>
          <xs:element minOccurs="0" name="BestEffort" type="xs:boolean" />
          <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ExplicitAnalogTelevisionRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction" />
      <xs:complexType name="PlayReadyContentKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="PlayReadyContentKey" nillable="true" type="tns:PlayReadyContentKey" />
      <xs:complexType name="ContentEncryptionKeyFromHeader">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:PlayReadyContentKey">
            <xs:sequence />
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="ContentEncryptionKeyFromHeader" nillable="true" type="tns:ContentEncryptionKeyFromHeader" />
      <xs:complexType name="ContentEncryptionKeyFromKeyIdentifier">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:PlayReadyContentKey">
            <xs:sequence>
              <xs:element minOccurs="0" name="KeyIdentifier" type="ser:guid" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="ContentEncryptionKeyFromKeyIdentifier" nillable="true" type="tns:ContentEncryptionKeyFromKeyIdentifier" />
      <xs:complexType name="PlayReadyLicenseResponseTemplate">
        <xs:sequence>
          <xs:element name="LicenseTemplates" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
          <xs:element minOccurs="0" name="ResponseCustomData" nillable="true" type="xs:string">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
        </xs:sequence>
      </xs:complexType>
      <xs:element name="PlayReadyLicenseResponseTemplate" nillable="true" type="tns:PlayReadyLicenseResponseTemplate" />
      <xs:complexType name="ArrayOfPlayReadyLicenseTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfPlayReadyLicenseTemplate" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
      <xs:complexType name="PlayReadyLicenseTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AllowTestDevices" type="xs:boolean" />
          <xs:element minOccurs="0" name="BeginDate" nillable="true" type="xs:dateTime">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element name="ContentKey" nillable="true" type="tns:PlayReadyContentKey" />
          <xs:element minOccurs="0" name="ContentType" type="tns:ContentType">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ExpirationDate" nillable="true" type="xs:dateTime">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="GracePeriod" nillable="true" type="ser:duration">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="LicenseType" type="tns:PlayReadyLicenseType" />
          <xs:element minOccurs="0" name="PlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
      <xs:simpleType name="PlayReadyLicenseType">
        <xs:restriction base="xs:string">
          <xs:enumeration value="Nonpersistent" />
          <xs:enumeration value="Persistent" />
        </xs:restriction>
      </xs:simpleType>
      <xs:element name="PlayReadyLicenseType" nillable="true" type="tns:PlayReadyLicenseType" />
      <xs:complexType name="PlayReadyPlayRight">
        <xs:sequence>
          <xs:element minOccurs="0" name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="AllowPassingVideoContentToUnknownOutput" type="tns:UnknownOutputPassingOption">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="AnalogVideoOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="CompressedDigitalAudioOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="CompressedDigitalVideoOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="DigitalVideoOnlyContentRestriction" type="xs:boolean">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ExplicitAnalogTelevisionOutputRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="FirstPlayExpiration" nillable="true" type="ser:duration">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ImageConstraintForAnalogComponentVideoRestriction" type="xs:boolean">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ImageConstraintForAnalogComputerMonitorRestriction" type="xs:boolean">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="UncompressedDigitalAudioOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="UncompressedDigitalVideoOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
        </xs:sequence>
      </xs:complexType>
      <xs:element name="PlayReadyPlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
      <xs:simpleType name="UnknownOutputPassingOption">
        <xs:restriction base="xs:string">
          <xs:enumeration value="NotAllowed" />
          <xs:enumeration value="Allowed" />
          <xs:enumeration value="AllowedWithVideoConstriction" />
        </xs:restriction>
      </xs:simpleType>
      <xs:element name="UnknownOutputPassingOption" nillable="true" type="tns:UnknownOutputPassingOption" />
      <xs:complexType name="ScmsRestriction">
        <xs:sequence>
          <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction" />
    </xs:schema>



## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


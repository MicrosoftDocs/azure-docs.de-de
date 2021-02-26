---
title: Übersicht über Media Services v3 mit Widevine-Lizenzvorlage
description: Erfahren Sie mehr über Azure Media Services mit der Widevine-Lizenzvorlage und wie sie zum Konfigurieren von Widevine-Lizenzen verwendet wird.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 39cccd270a4947820640940ae43fa0feb3e52028
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954478"
---
# <a name="media-services-v3-with-widevine-license-template-overview"></a>Übersicht zu Media Services v3 mit Widevine-Lizenzvorlage

Mit Azure Media Services können Sie Ihre Inhalte mit **Google Widevine** verschlüsseln. Media Services bietet darüber hinaus einen Dienst zum Übermitteln von Widevine-Lizenzen. Widevine-Lizenzen können mithilfe von Azure Media Services-APIs konfiguriert werden. Wenn ein Player versucht, Ihre durch Widevine geschützten Inhalte wiederzugeben, wird eine Anforderung zum Erwerben einer Lizenz an den Lizenzbereitstellungsdienst gesendet. Wenn der Lizenzdienst die Anforderung genehmigt, stellt der Dienst die Lizenz aus. Sie wird an den Client gesendet und dient zum Entschlüsseln und Wiedergeben des angegebenen Inhalts.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

Eine Widevine-Lizenzanforderung ist als JSON-Nachricht formatiert.  



```json
{  
    "payload":"<license challenge>",
    "content_id": "<content id>"
    "provider": "<provider>"
    "allowed_track_types":"<types>",
    "content_key_specs":[  
        {  
            "track_type":"<track type 1>"
        },
        {  
            "track_type":"<track type 2>"
        },
        …
    ],
    "policy_overrides":{  
        "can_play":<can play>,
        "can persist":<can persist>,
        "can_renew":<can renew>,
        "rental_duration_seconds":<rental duration>,
        "playback_duration_seconds":<playback duration>,
        "license_duration_seconds":<license duration>,
        "renewal_recovery_duration_seconds":<renewal recovery duration>,
        "renewal_server_url":"<renewal server url>",
        "renewal_delay_seconds":<renewal delay>,
        "renewal_retry_interval_seconds":<renewal retry interval>,
        "renew_with_usage":<renew with usage>
    }
}
```

>[!NOTE]
> Sie können eine leere Nachricht ohne Werte erstellen, indem Sie einfach „{}“ verwenden. Dann wird eine Lizenzvorlage mit Standardeinstellungen erstellt. Die Standardeinstellungen funktionieren in den meisten Fällen. Microsoft-basierte Lizenzübermittlungsszenarien sollten immer die Standardeinstellungen verwenden. Wenn Sie die Werte für „provider“ und „content_id“ festlegen müssen, muss der Anbieter den Widevine-Anmeldeinformationen entsprechen.

## <a name="json-message"></a>JSON-Nachricht

| Name | Wert | BESCHREIBUNG |
| --- | --- | --- |
| payload |Base64-codierte Zeichenfolge |Die von einem Client gesendete Lizenzanforderung. |
| content_id |Base64-codierte Zeichenfolge |Bezeichner, der zum Ableiten der Schlüssel-ID und des Inhaltsschlüssels für „content_key_specs.track_type“ verwendet wird. |
| Provider |Zeichenfolge |Wird zum Nachschlagen von Inhaltsschlüsseln und Richtlinien verwendet. Wenn für die Widevine-Lizenzbereitstellung die Microsoft-Schlüsselübermittlung verwendet wird, wird dieser Parameter ignoriert. |
| policy_name |Zeichenfolge |Der Name einer zuvor registrierten Richtlinie. Optional. |
| allowed_track_types |enum |SD_ONLY oder SD_HD. Steuert, welche Inhaltsschlüssel in eine Lizenz aufgenommen werden. |
| content_key_specs |Array von JSON-Strukturen, siehe Abschnitt „Spezifikationen für Inhaltsschlüssel“.  |Eine feiner abgestimmte Steuerung der zurückzugebenden Inhaltsschlüssel. Weitere Informationen finden Sie im Abschnitt „Spezifikationen für Inhaltsschlüssel“. Nur einer der Werte („allowed_track_types“ oder „content_key_specs“) kann angegeben werden. |
| use_policy_overrides_exclusively |Boolescher Wert, true oder false |Verwenden Sie Richtlinienattribute, die von „policy_overrides“ angegeben werden, und lassen Sie alle zuvor gespeicherten Richtlinien weg. |
| policy_overrides |JSON-Struktur, siehe Abschnitt „Außerkraftsetzungen von Richtlinien“. |Richtlinieneinstellungen für diese Lizenz.  Falls dieses Medienobjekt über eine vorab definierte Richtlinie verfügt, werden diese angegebenen Werte verwendet. |
| session_init |JSON-Struktur, siehe Abschnitt „Sitzungsinitialisierung“. |An die Lizenz werden optionale Daten übergeben. |
| parse_only |Boolescher Wert, true oder false |Die Lizenzanforderung wird analysiert, jedoch wird keine Lizenz ausgestellt. Allerdings werden Werte aus der Lizenzanforderung in der Antwort zurückgegeben. |

## <a name="content-key-specs"></a>Spezifikationen für Inhaltsschlüssel
Wenn eine Richtlinie bereits vorhanden ist, müssen keine Werte in der Spezifikation für Inhaltsschlüssel angegeben werden. Die mit diesen Inhalten verbundene, bereits bestehende Richtlinie wird zur Bestimmung des Ausgabeschutzes verwendet, z.B. HDCP (High-bandwidth Digital Content Protection) und CGMS (Copy General Management System). Wenn eine vorhandene Richtlinie nicht beim Widevine-Lizenzserver registriert ist, kann der Inhaltsanbieter die Werte in die Lizenzanforderung einfügen.   

Jeder content_key_specs-Wert muss für alle Titel angegeben werden, unabhängig von der Option „use_policy_overrides_exclusively“. 

| Name | Wert | BESCHREIBUNG |
| --- | --- | --- |
| content_key_specs. track_type |Zeichenfolge |Der Name eines Titeltyps. Wenn „content_key_specs“ in der Lizenzanforderung angegeben ist, sollten Sie unbedingt alle Titeltypen explizit angeben. Andernfalls wird die Wiedergabe nach 10 Sekunden beendet. |
| content_key_specs  <br/> security_level |uint32 |Definiert die Clientstabilitätsanforderungen für die Wiedergabe. <br/> – Softwarebasierte White-Box-Kryptografie ist erforderlich. <br/> – Softwarekryptografie und ein verborgener Decoder sind erforderlich. <br/> – Die zentralen Vorgänge für Daten und Kryptografie müssen innerhalb einer hardwaregestützten vertrauenswürdigen Ausführungsumgebung ausgeführt werden. <br/> – Kryptografie und Decodierung müssen innerhalb einer hardwaregestützten vertrauenswürdigen Ausführungsumgebung ausgeführt werden.  <br/> – Kryptografie, Decodierung und Verarbeitung von Medien (komprimiert und nicht komprimiert) müssen innerhalb einer hardwaregestützten vertrauenswürdigen Ausführungsumgebung verarbeitet werden. |
| content_key_specs <br/> required_output_protection.hdc |Zeichenfolge, HDCP_NONE, HDCP_V1 oder HDCP_V2 |Gibt an, ob HDCP erforderlich ist. |
| content_key_specs <br/>Schlüssel |Base64-<br/>codierte Zeichenfolge |Der Inhaltsschlüssel, der für diesen Titel verwendet werden soll. Wenn ein Wert angegeben wird, ist „track_type“ oder „key_id“ erforderlich. Mit dieser Option können Inhaltsanbieter den Inhaltsschlüssel für diesen Titel einfügen, statt einen Schlüssel durch den Widevine-Lizenzserver zu generieren oder zu suchen. |
| content_key_specs.key_id |Base64-codierte binäre Zeichenfolge, 16 Bytes |Eindeutiger Bezeichner für den Schlüssel. |

## <a name="policy-overrides"></a>Außerkraftsetzungen von Richtlinien
| Name | Wert | BESCHREIBUNG |
| --- | --- | --- |
| policy_overrides&#46;can_play |Boolescher Wert, true oder false |Gibt an, dass die Wiedergabe des Inhalts zulässig ist. Der Standardwert ist "false". |
| policy_overrides&#46;can_persist |Boolescher Wert, true oder false |Gibt an, dass die Lizenz in einem permanenten Speicher für die Offlineverwendung beibehalten werden kann. Der Standardwert ist "false". |
| policy_overrides&#46;can_renew |Boolescher Wert, true oder false |Gibt an, dass die Verlängerung dieser Lizenz zulässig ist. Bei „true“ kann die Dauer der Lizenz über den Takt verlängert werden. Der Standardwert ist "false". |
| policy_overrides&#46;license_duration_seconds |int64 |Gibt das Zeitfenster für diese bestimmte Lizenz an. Der Wert 0 gibt an, dass es keine Beschränkung für die Dauer gibt. Standard ist "0". |
| policy_overrides&#46;rental_duration_seconds |int64 |Gibt das Zeitfenster an, in dem die Wiedergabe zulässig ist. Der Wert 0 gibt an, dass es keine Beschränkung für die Dauer gibt. Standard ist "0". |
| policy_overrides&#46;playback_duration_seconds |int64 |Das Anzeigezeitfenster, sobald die Wiedergabe innerhalb der Lizenzdauer beginnt. Der Wert 0 gibt an, dass es keine Beschränkung für die Dauer gibt. Standard ist "0". |
| policy_overrides&#46;renewal_server_url |Zeichenfolge |Alle Heartbeatanforderungen (Verlängerung) für diese Lizenz werden an die angegebene URL weitergeleitet. Dieses Feld wird nur verwendet, wenn „can_renew“ auf „true“ festgelegt ist. |
| policy_overrides&#46;renewal_delay_seconds |int64 |Die Anzahl der Sekunden nach „license_start_time“, bevor der erste Versuch einer Verlängerung unternommen wird. Dieses Feld wird nur verwendet, wenn „can_renew“ auf „true“ festgelegt ist. Standard ist "0". |
| policy_overrides&#46;renewal_retry_interval_seconds |int64 |Gibt die Verzögerung in Sekunden zwischen den nachfolgenden Anforderungen zu Lizenzverlängerung an, falls ein Fehler auftritt. Dieses Feld wird nur verwendet, wenn „can_renew“ auf „true“ festgelegt ist. |
| policy_overrides&#46;renewal_recovery_duration_seconds |int64 |Das Zeitfenster, für das die Wiedergabe fortgesetzt werden kann, während der Versuch einer Verlängerung unternommen wird, aber aufgrund von Back-End-Problemen mit dem Lizenzserver noch nicht erfolgreich war. Der Wert 0 gibt an, dass es keine Beschränkung für die Dauer gibt. Dieses Feld wird nur verwendet, wenn „can_renew“ auf „true“ festgelegt ist. |
| policy_overrides&#46;renew_with_usage |Boolescher Wert, true oder false |Gibt an, dass die Lizenz zur Verlängerung gesendet wird, wenn die Verwendung startet. Dieses Feld wird nur verwendet, wenn „can_renew“ auf „true“ festgelegt ist. |

## <a name="session-initialization"></a>Sitzungsinitialisierung
| Name | Wert | BESCHREIBUNG |
| --- | --- | --- |
| provider_session_token |Base64-codierte Zeichenfolge |Dieses Sitzungstoken wird wieder in der Lizenz übergeben und ist in nachfolgenden Verlängerungen vorhanden. Das Sitzungstoken bleibt nicht über Sitzungen hinaus erhalten. |
| provider_client_token |Base64-codierte Zeichenfolge |Clienttoken, das in der Lizenzantwort zurück gesendet wird. Wenn die Lizenzanforderung ein Clienttoken enthält, wird dieser Wert ignoriert. Das Clienttoken bleibt über Lizenzsitzungen hinaus erhalten. |
| override_provider_client_token |Boolescher Wert, true oder false |Wenn „false“ festgelegt wird und die Lizenzanforderung ein Clienttoken enthält, verwenden Sie das Token aus der Anforderung, selbst wenn ein Clienttoken in dieser Struktur angegeben wurde. Verwenden Sie bei „true“ immer das Token, das in dieser Struktur angegeben ist. |

## <a name="configure-your-widevine-license-with-net"></a>Konfigurieren der Widevine-Lizenz mit .NET 

Media Services umfasst eine Klasse, mit der Sie eine Widevine-Lizenz konfigurieren können. Um die Lizenz zu erstellen, übergeben Sie eine JSON-Zeichenfolge an [WidevineTemplate](/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

Zum Konfigurieren der Vorlage haben Sie folgende Möglichkeiten:

### <a name="directly-construct-a-json-string"></a>Sie können direkt eine JSON-Zeichenfolge erstellen.

Diese Methode kann jedoch fehleranfällig sein. Es wird empfohlen, eine andere Methode zu verwenden, wie in [Definieren der erforderlichen Klassen und Serialisieren in JSON](#classes).

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a name="define-needed-classes-and-serialize-to-json"></a><a id="classes"></a> Definieren von erforderlichen Klassen und Serialisieren in JSON

#### <a name="define-classes"></a>Definieren von Klassen

Im Folgenden sehen Sie ein Beispiel für die Definition von Klassen, die dem Widevine JSON-Schema zugeordnet sind. Sie können die Klassen instanziieren, bevor Sie sie in der JSON-Zeichenfolge serialisieren.  

```csharp
/// <summary>
/// Widevine PolicyOverrides class.
/// </summary>
public class PolicyOverrides
{
    /// <summary>
    /// Gets or sets a value indicating whether playback of the content is allowed. Default is false.
    /// </summary>
    [JsonProperty("can_play")]
    public bool CanPlay { get; set; }

    /// <summary>
    /// Gets or sets a value indicating whether the license might be persisted to nonvolatile storage for offline use. Default is false.
    /// </summary>
    [JsonProperty("can_persist")]
    public bool CanPersist { get; set; }

    /// <summary>
    /// Gets or sets a value indicating whether renewal of this license is allowed. If true, the duration of the license can be extended by heartbeat. Default is false.
    /// </summary>
    [JsonProperty("can_renew")]
    public bool CanRenew { get; set; }

    /// <summary>
    /// Gets or sets the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("rental_duration_seconds")]
    public int RentalDurationSeconds { get; set; }

    /// <summary>
    /// Gets or sets the viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("playback_duration_seconds")]
    public int PlaybackDurationSeconds { get; set; }

    /// <summary>
    /// Gets or sets the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("license_duration_seconds")]
    public int LicenseDurationSeconds { get; set; }
}

/// <summary>
/// Widevine ContentKeySpec class.
/// </summary>
public class ContentKeySpec
{
    /// <summary>
    /// Gets or sets track type.
    /// If content_key_specs is specified in the license request, make sure to specify all track types explicitly.
    /// Failure to do so results in failure to play back past 10 seconds.
    /// </summary>
    [JsonProperty("track_type")]
    public string TrackType { get; set; }

    /// <summary>
    /// Gets or sets client robustness requirements for playback.
    /// Software-based white-box cryptography is required.
    /// Software cryptography and an obfuscated decoder are required.
    /// The key material and cryptography operations must be performed within a hardware-backed trusted execution environment.
    /// The cryptography and decoding of content must be performed within a hardware-backed trusted execution environment.
    /// The cryptography, decoding, and all handling of the media (compressed and uncompressed) must be handled within a hardware-backed trusted execution environment.
    /// </summary>
    [JsonProperty("security_level")]
    public int SecurityLevel { get; set; }

    /// <summary>
    /// Gets or sets the OutputProtection.
    /// </summary>
    [JsonProperty("required_output_protection")]
    public OutputProtection RequiredOutputProtection { get; set; }
}

/// <summary>
/// OutputProtection Widevine class.
/// </summary>
public class OutputProtection
{
    /// <summary>
    /// Gets or sets HDCP protection.
    /// Supported values : HDCP_NONE, HDCP_V1, HDCP_V2
    /// </summary>
    [JsonProperty("hdcp")]
    public string HDCP { get; set; }

    /// <summary>
    /// Gets or sets CGMS.
    /// </summary>
    [JsonProperty("cgms_flags")]
    public string CgmsFlags { get; set; }
}

/// <summary>
/// Widevine template.
/// </summary>
public class WidevineTemplate
{
    /// <summary>
    /// Gets or sets the allowed track types.
    /// SD_ONLY or SD_HD.
    /// Controls which content keys are included in a license.
    /// </summary>
    [JsonProperty("allowed_track_types")]
    public string AllowedTrackTypes { get; set; }

    /// <summary>
    /// Gets or sets a finer-grained control on which content keys to return.
    /// For more information, see the section "Content key specs."
    /// Only one of the allowed_track_types and content_key_specs values can be specified.
    /// </summary>
    [JsonProperty("content_key_specs")]
    public ContentKeySpec[] ContentKeySpecs { get; set; }

    /// <summary>
    /// Gets or sets policy settings for the license.
    /// In the event this asset has a predefined policy, these specified values are used.
    /// </summary>
    [JsonProperty("policy_overrides")]
    public PolicyOverrides PolicyOverrides { get; set; }
}
```

#### <a name="configure-the-license"></a>Konfigurieren der Lizenz

Verwenden Sie die im vorherigen Abschnitt definierten Klassen, um eine JSON-Zeichenfolge zu erstellen, mit der [WidevineTemplate](/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate) konfiguriert wird:

```csharp
private static ContentKeyPolicyWidevineConfiguration ConfigureWidevineLicenseTempate()
{
    WidevineTemplate template = new WidevineTemplate()
    {
        AllowedTrackTypes = "SD_HD",
        ContentKeySpecs = new ContentKeySpec[]
        {
            new ContentKeySpec()
            {
                TrackType = "SD",
                SecurityLevel = 1,
                RequiredOutputProtection = new OutputProtection()
                {
                    HDCP = "HDCP_V2"
                }
            }
        },
        PolicyOverrides = new PolicyOverrides()
        {
            CanPlay = true,
            CanPersist = true,
            CanRenew = false,
            RentalDurationSeconds = 2592000,
            PlaybackDurationSeconds = 10800,
            LicenseDurationSeconds = 604800,
        }
    };

    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = Newtonsoft.Json.JsonConvert.SerializeObject(template)
    };
    return objContentKeyPolicyWidevineConfiguration;
}
```

## <a name="additional-notes"></a>Zusätzliche Hinweise

* Widevine ist ein von Google Inc. bereitgestellter Dienst, der den Vertragsbedingungen und der Datenschutzrichtlinie von Google, Inc. unterliegt.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das [Schützen mit DRM](protect-with-drm.md).

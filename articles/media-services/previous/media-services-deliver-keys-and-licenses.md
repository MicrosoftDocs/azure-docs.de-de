---
title: Übermitteln von DRM-Lizenzen oder AES-Schlüsseln mithilfe von Azure Media Services | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Azure Media Services verwenden können, um PlayReady- und/oder Widevine-Lizenzen und AES-Schlüssel zu übermitteln, aber die restlichen Aufgaben (Codierung, Verschlüsselung, Streaming) mithilfe Ihrer lokalen Server auszuführen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 8546c2c1-430b-4254-a88d-4436a83f9192
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 73a76162efaf7317c5e2f1668b76325da6f018ce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64868145"
---
# <a name="use-media-services-to-deliver-drm-licenses-or-aes-keys"></a>Übermitteln von DRM-Lizenzen oder AES-Schlüsseln mithilfe von Media Services 

> [!NOTE]
> Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).   > Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).
>

Azure Media Services ermöglicht Ihnen das Erfassen, Codieren, Hinzufügen von Inhaltsschutz und Streamen Ihrer Inhalte. Weitere Informationen finden Sie unter [Verwenden von dynamischer allgemeiner Verschlüsselung mit PlayReady und/oder Widevine](media-services-protect-with-playready-widevine.md). Manche Kunden möchten Media Services nur zum Übermitteln von Lizenzen und/oder Schlüsseln verwenden und das Codieren, Verschlüsseln und Streamen mithilfe eigener, lokaler Server erledigen. Dieser Artikel beschreibt, wie Sie mit Media Services PlayReady- und/oder Widevine-Lizenzen bereitstellen und die restlichen Aufgaben auf Ihren lokalen Servern ausführen können. 

## <a name="overview"></a>Übersicht
Media Services bietet einen Dienst zum Übermitteln von PlayReady- und Widevine-DRM-Lizenzen (Digital Rights Management) und AES-128-Schlüsseln. Mithilfe der von Media Services bereitgestellten APIs können Sie die Rechte und Einschränkungen konfigurieren, die für die DRM-Laufzeit erzwungen werden sollen, wenn ein Benutzer mit DRM geschützte Inhalte wiedergibt. Wenn ein Benutzer die geschützten Inhalte anfordert, fordert die Playeranwendung eine Lizenz vom Media Services-Lizenzdienst an. Wenn die Lizenz autorisiert wird, gibt der Media Services-Lizenzdienst die Lizenz für den Player aus. PlayReady- und Widevine-Lizenzen enthalten den Entschlüsselungsschlüssel, der vom Clientplayer zum Entschlüsseln und Streamen des Inhalts verwendet werden kann.

Media Services unterstützt mehrere Möglichkeiten zur Autorisierung von Benutzern, die Lizenzen oder Schlüssel anfordern. Sie konfigurieren die Autorisierungsrichtlinie für Inhaltsschlüssel. Die Richtlinie kann eine oder mehrere Einschränkungen aufweisen. Die Optionen lauten „Open“ oder „Token“. Eine durch Token eingeschränkte Richtlinie gilt nur zusammen mit einem Token, das von einem Sicherheitstokendienst (Security Token Service, STS) ausgestellt wurde. Media Services unterstützt Token im Simple Web Token-Format (SWT) und im JSON Web Token-Format (JWT).

Das folgende Diagramm zeigt die wichtigsten Schritte, die erfolgen müssen, damit Media Services zum Übermitteln von PlayReady- und/oder Widevine-Lizenzen genutzt werden kann, die restlichen Aufgaben aber auf Ihren lokalen Servern erfolgen können:

![Schützen mit PlayReady](./media/media-services-deliver-keys-and-licenses/media-services-diagram1.png)

## <a name="download-sample"></a>Beispiel herunterladen
Informationen zum Herunterladen des in diesem Artikel beschriebenen Beispiels finden Sie unter [Verwenden von Azure Media Services zum Bereitstellen von PlayReady- und/oder Widevine-Lizenzen mit .NET](https://github.com/Azure/media-services-dotnet-deliver-drm-licenses).

## <a name="create-and-configure-a-visual-studio-project"></a>Erstellen und Konfigurieren eines Visual Studio-Projekts

1. Richten Sie Ihre Entwicklungsumgebung ein, und füllen Sie die Datei „app.config“ mit Verbindungsinformationen, wie unter [Media Services-Entwicklung mit .NET](media-services-dotnet-how-to-use.md) beschrieben.

2. Fügen Sie den **appSettings** in Ihrer Datei „app.config“ die folgenden Elemente hinzu:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```
 
## <a name="net-code-example"></a>Codebeispiel für .NET
Das folgende Codebeispiel zeigt, wie ein allgemeiner symmetrischer Schlüssel erstellt und Lizenzerwerbs-URLs für PlayReady und Widevine abgerufen werden. Zum Konfigurieren Ihres lokalen Servers benötigen Sie einen Inhaltsschlüssel, die Schlüssel-ID und die Lizenzerwerbs-URL. Nach dem Konfigurieren Ihres lokalen Servers können Sie von Ihrem eigenen Streamingserver streamen. Da der verschlüsselte Stream auf einen Media Services-Lizenzserver verweist, fordert Ihr Player eine Lizenz bei Media Services an. Wenn Sie sich für Tokenauthentifizierung entscheiden, überprüft der Media Services-Lizenzserver das Token, das Sie per HTTPS gesendet haben. Wenn das Token gültig ist, übermittelt der Lizenzserver die Lizenz an Ihren Player. Das folgende Codebeispiel zeigt nur, wie ein allgemeiner symmetrischer Schlüssel erstellt und Lizenzerwerbs-URLs für PlayReady oder Widevine abgerufen werden. Wenn Sie AES-128 Schlüssel übermitteln möchten, müssen Sie einen Umschlaginhaltsschlüssel erstellen und eine Schlüsselerwerbs-URL abrufen. Weitere Informationen finden Sie unter [Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselbereitstellungsdiensts](media-services-protect-with-aes128.md).

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
using Newtonsoft.Json;

namespace DeliverDRMLicenses
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly Uri _sampleIssuer =
            new Uri(ConfigurationManager.AppSettings["Issuer"]);
        private static readonly Uri _sampleAudience =
            new Uri(ConfigurationManager.AppSettings["Audience"]);

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = true;
            string tokenTemplateString = null;


            IContentKey key = CreateCommonTypeContentKey();

            // Print out the key ID and Key in base64 string format
            Console.WriteLine("Created key {0} with key value {1} ",
                key.Id, System.Convert.ToBase64String(key.GetClearKeyValue()));

            Console.WriteLine("PlayReady License Key delivery URL: {0}",
                key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));

            Console.WriteLine("Widevine License Key delivery URL: {0}",
                key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine));

            if (tokenRestriction)
                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
            else
                AddOpenAuthorizationPolicy(key);

            Console.WriteLine("Added authorization policy: {0}",
                key.AuthorizationPolicyId);
            Console.WriteLine();
            Console.ReadLine();
        }

        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {

            // Create ContentKeyAuthorizationPolicy with Open restrictions 
            // and create authorization policy          

            List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>
            {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Open",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                            Requirements = null
                        }
            };

            // Configure PlayReady and Widevine license templates.
            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                _context.ContentKeyAuthorizationPolicyOptions.Create("",
                    ContentKeyDeliveryType.PlayReadyLicense,
                        restrictions, PlayReadyLicenseTemplate);

            IContentKeyAuthorizationPolicyOption WidevinePolicy =
                _context.ContentKeyAuthorizationPolicyOptions.Create("",
                    ContentKeyDeliveryType.Widevine,
                    restrictions, WidevineLicenseTemplate);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                        ContentKeyAuthorizationPolicies.
                        CreateAsync("Deliver Common Content Key with no restrictions").
                        Result;


            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
            // Associate the content key authorization policy with the content key.
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;
        }

        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
        {
            string tokenTemplateString = GenerateTokenRequirements();

            List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>
            {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Token Authorization Policy",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                            Requirements = tokenTemplateString,
                        }
            };

            // Configure PlayReady and Widevine license templates.
            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                    ContentKeyDeliveryType.PlayReadyLicense,
                        restrictions, PlayReadyLicenseTemplate);

            IContentKeyAuthorizationPolicyOption WidevinePolicy =
                _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                    ContentKeyDeliveryType.Widevine,
                        restrictions, WidevineLicenseTemplate);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                        ContentKeyAuthorizationPolicies.
                        CreateAsync("Deliver Common Content Key with token restrictions").
                        Result;

            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);

            // Associate the content key authorization policy with the content key
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        static private string GenerateTokenRequirements()
        {
            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

            template.PrimaryVerificationKey = new SymmetricVerificationKey();
            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

            return TokenRestrictionTemplateSerializer.Serialize(template);
        }

        static private string ConfigurePlayReadyLicenseTemplate()
        {
            // The following code configures PlayReady License Template using .NET classes
            // and returns the XML string.

            //The PlayReadyLicenseResponseTemplate class represents the template 
            //for the response sent back to the end user. 
            //It contains a field for a custom data string between the license server 
            //and the application (may be useful for custom app logic) 
            //as well as a list of one or more license templates.

            PlayReadyLicenseResponseTemplate responseTemplate =
                new PlayReadyLicenseResponseTemplate();

            // The PlayReadyLicenseTemplate class represents a license template 
            // for creating PlayReady licenses
            // to be returned to the end users. 
            // It contains the data on the content key in the license 
            // and any rights or restrictions to be 
            // enforced by the PlayReady DRM runtime when using the content key.
            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();

            // Configure whether the license is persistent 
            // (saved in persistent storage on the client) 
            // or non-persistent (only held in memory while the player is using the license).  
            licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

            // AllowTestDevices controls whether test devices can use the license or not.  
            // If true, the MinimumSecurityLevel property of the license
            // is set to 150.  If false (the default), 
            // the MinimumSecurityLevel property of the license is set to 2000.
            licenseTemplate.AllowTestDevices = true;

            // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
            // It grants the user the ability to play back the content subject to the zero or more restrictions 
            // configured in the license and on the PlayRight itself (for playback specific policy). 
            // Much of the policy on the PlayRight has to do with output restrictions 
            // which control the types of outputs that the content can be played over and 
            // any restrictions that must be put in place when using a given output.
            // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
            //then the DRM runtime will only allow the video to be displayed over digital outputs 
            //(analog video outputs won’t be allowed to pass the content).

            // IMPORTANT: These types of restrictions can be very powerful 
            // but can also affect the consumer experience. 
            // If the output protections are configured too restrictive, 
            // the content might be unplayable on some clients. 
            // For more information, see the PlayReady Compliance Rules document.

            // For example:
            //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

            responseTemplate.LicenseTemplates.Add(licenseTemplate);

            return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
        }


        private static string ConfigureWidevineLicenseTemplate()
        {
            var template = new WidevineMessage
            {
                allowed_track_types = AllowedTrackTypes.SD_HD,
                content_key_specs = new[]
                {
                            new ContentKeySpecs
                            {
                                required_output_protection =
                                    new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                                security_level = 1,
                                track_type = "SD"
                            }
                        },
                policy_overrides = new
                {
                    can_play = true,
                    can_persist = true,
                    can_renew = false
                }
            };

            string configuration = JsonConvert.SerializeObject(template);
            return configuration;
        }


        static public IContentKey CreateCommonTypeContentKey()
        {
            // Create envelope encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                                    keyId,
                                    contentKey,
                                    "ContentKey",
                                    ContentKeyType.CommonEncryption);

            return key;
        }

        static private byte[] GetRandomBuffer(int length)
        {
            var returnValue = new byte[length];

            using (var rng =
                new System.Security.Cryptography.RNGCryptoServiceProvider())
            {
                rng.GetBytes(returnValue);
            }

            return returnValue;
        }
    }
}
```

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Weitere Informationen
* [Verwenden von dynamischer allgemeiner Verschlüsselung mit PlayReady und/oder Widevine](media-services-protect-with-playready-widevine.md)
* [Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselübermittlungsdiensts](media-services-protect-with-aes128.md)

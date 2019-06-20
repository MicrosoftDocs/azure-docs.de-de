---
title: Verwenden von dynamischer allgemeiner Verschlüsselung mit PlayReady und/oder Widevine | Microsoft-Dokumentation
description: Sie können Azure Media Services verwenden, um MPEG-DASH-, Smooth Streaming- und HTTP Live Streaming (HLS)-Streams zu übermitteln, die mit Microsoft PlayReady DRM geschützt sind. Außerdem können Sie sie nutzen, um DASH-Daten bereitzustellen, die mit Widevine DRM verschlüsselt wurden. In diesem Thema wird das dynamische Verschlüsseln mit PlayReady- und Widevine-DRM beschrieben.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 548d1a12-e2cb-45fe-9307-4ec0320567a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: c6271b5bb64eacf66c771b84139cead722a2e7b3
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64869392"
---
# <a name="use-playready-andor-widevine-dynamic-common-encryption"></a>Verwenden von dynamischer allgemeiner Verschlüsselung mit PlayReady und/oder Widevine

> [!NOTE]
> Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).   > Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).
>   

## <a name="overview"></a>Übersicht

 Sie können Media Services verwenden, um MPEG-DASH-, Smooth Streaming- und HTTP Live Streaming (HLS)-Streams zu übermitteln, die per [PlayReady Digital Rights Management (DRM)](https://www.microsoft.com/playready/overview/) geschützt sind. Außerdem können Sie verschlüsselte DASH-Streams mit Widevine-DRM-Lizenzen bereitstellen. PlayReady und Widevine sind gemäß der Spezifikation Common Encryption (ISO/IEC 23001-7 CENC) verschlüsselt. Sie können das [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (ab Version 3.5.1) oder die REST-API verwenden, um AssetDeliveryConfiguration für die Verwendung von Widevine zu konfigurieren.

Media Services bietet einen Dienst zum Übermitteln von PlayReady- und Widevine-DMR-Lizenzen. Mithilfe der von Media Services bereitgestellten APIs können Sie die Rechte und Einschränkungen konfigurieren, die für die PlayReady- oder Widevine-DRM-Laufzeit erzwungen werden sollen, wenn ein Benutzer geschützte Inhalte wiedergibt. Wenn ein Benutzer die mit DRM geschützten Inhalte anfordert, fordert die Playeranwendung eine Lizenz vom Media Services-Lizenzdienst an. Wenn die Playeranwendung autorisiert wurde, gibt der Media Services-Lizenzdienst eine Lizenz für den Player aus. Eine PlayReady- oder Widevine-Lizenz enthält den Entschlüsselungsschlüssel, der vom Clientplayer zum Entschlüsseln und Streamen des Inhalts verwendet werden kann.

Sie können auch die folgenden Media Services-Partnerunternehmen zur Unterstützung bei der Bereitstellung von Widevine-Lizenzen nutzen: 

* [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Weitere Informationen finden Sie in den Artikeln zur Integration in [Axinom](media-services-axinom-integration.md) und [castLabs](media-services-castlabs-integration.md).

Media Services unterstützt mehrere Möglichkeiten zur Autorisierung von Benutzern, die Schlüssel anfordern. Die Autorisierungsrichtlinie für Inhaltsschlüssel kann eine oder mehrere Autorisierungseinschränkungen aufweisen: offen oder Token. Eine durch Token eingeschränkte Richtlinie gilt nur zusammen mit einem Token, das von einem Sicherheitstokendienst (Security Token Service, STS) ausgestellt wurde. Media Services unterstützt Token im Format [Simple Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) und [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). 

Weitere Informationen finden Sie unter [Konfigurieren einer Autorisierungsrichtlinie für Inhaltsschlüssel](media-services-protect-with-aes128.md#configure_key_auth_policy).

Damit die dynamische Verschlüsselung genutzt werden kann, müssen Sie über ein Medienobjekt verfügen, das eine Sammlung aus MP4-Dateien mit mehreren Bitraten oder Smooth Streaming-Quelldateien mit mehreren Bitraten enthält. Außerdem müssen Sie die Übermittlungsrichtlinien für das Medienobjekt konfigurieren (weiter unten in diesem Thema beschrieben). Basierend auf dem angegebenen Format in der Streaming-URL stellt der On-Demand-Streaming-Server dann sicher, dass der Datenstrom im ausgewählten Protokoll übermittelt wird. Auf diese Weise werden die Dateien nur in einem Speicherformat gespeichert, und Sie zahlen auch nur für ein Format. Media Services reagiert auf die Anforderung eines Clients jeweils mit der richtigen HTTP-Antwort.

Dieser Artikel ist für Entwickler hilfreich, die an Anwendungen arbeiten, bei denen mit mehreren DRMs, z.B. PlayReady und Widevine, geschützte Medien übermittelt werden. In diesem Artikel erfahren Sie, wie der PlayReady-Lizenzbereitstellungsdienst mit Autorisierungsrichtlinien so konfiguriert wird, dass nur autorisierte Clients PlayReady- oder Widevine-Lizenzen erhalten können. Es wird außerdem gezeigt, wie die dynamische Verschlüsselung mit PlayReady oder Widevine DRM über DASH genutzt wird.

>[!NOTE]
>Beim Erstellen Ihres Media Services-Kontos wird dem Konto ein Standard-Streamingendpunkt mit dem Status „Beendet“ hinzugefügt. Um mit dem Streamen der Inhalte zu beginnen und die dynamische Paketerstellung und dynamische Verschlüsselung zu nutzen, muss der Streamingendpunkt, von dem Sie Inhalte streamen möchten, den Status „Wird ausgeführt“ aufweisen. 

## <a name="download-the-sample"></a>Herunterladen des Beispiels
Sie können das in diesem Artikel beschriebene Beispiel über die Seite mit den [Azure-Beispielen auf GitHub](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) herunterladen.

## <a name="configure-dynamic-common-encryption-and-drm-license-delivery-services"></a>Konfigurieren der dynamischen allgemeinen Verschlüsselung und von DRM-Lizenzbereitstellungsdiensten

Führen Sie die folgenden allgemeinen Schritte beim Schützen Ihrer Medienobjekte mit PlayReady unter Verwendung des Media Services-Lizenzbereitstellungsdiensts sowie der dynamischen Verschlüsselung aus:

1. Erstellen Sie ein Medienobjekt, und laden Sie Dateien in das Medienobjekt hoch.

2. Codieren Sie ein Medienobjekt, das die Sammlung von MP4-Dateien mit adaptiver Bitrate enthält.

3. Erstellen Sie einen Inhaltsschlüssel, und ordnen Sie den Schlüssels dem codierten Medienobjekt zu. In Media Services enthält der Inhaltsschlüssel den Verschlüsselungsschlüssel des Medienobjekts.

4. Konfigurieren Sie eine Autorisierungsrichtlinie für Inhaltsschlüssel. Sie müssen die Autorisierungsrichtlinie für den Inhaltsschlüssel konfigurieren. Der Client muss die Richtlinie erfüllen, bevor der Inhaltsschlüssel an den Client übermittelt wird.

    Beim Erstellen der Autorisierungsrichtlinie für den Inhaltsschlüssel müssen Sie die Bereitstellungsmethode (PlayReady oder Widevine) und die Einschränkungen (offen oder Token) angeben. Außerdem müssen Sie spezifische Informationen zum Typ der Schlüsselbereitstellung angeben, mit denen definiert wird, wie der Schlüssel für den Client bereitgestellt wird ([PlayReady](media-services-playready-license-template-overview.md)- oder [Widevine](media-services-widevine-license-template-overview.md)-Lizenzvorlage).

5. Konfigurieren der Übermittlungsrichtlinie für ein Medienobjekt. Die Konfiguration der Bereitstellungsrichtlinie umfasst auch das Bereitstellungsprotokoll (z.B. „MPEG-DASH“, „HLS“, „Smooth Streaming“ oder „Alle“). Außerdem enthält die Konfiguration den Typ der dynamischen Verschlüsselung (z.B. allgemeine Verschlüsselung) und die URL für den Erwerb der PlayReady- oder Widevine-Lizenz.

    Sie können für jedes Protokoll für das gleiche Medienobjekt jeweils eine andere Richtlinie anwenden. Beispielsweise können Sie die PlayReady-Verschlüsselung auf Smooth/DASH und einen AES Envelope auf HLS anwenden. Alle Protokolle, die nicht in einer Bereitstellungsrichtlinie definiert sind (wenn Sie z.B. eine einzelne Richtlinie hinzufügen, die nur HLS als Protokoll angibt), werden vom Streaming ausgeschlossen. Die einzige Ausnahme ist, wenn Sie überhaupt keine Bereitstellungsrichtlinie für Medienobjekte definiert haben. In diesem Fall sind alle Protokolle ohne Verschlüsselung zulässig.

6. Erstellen eines OnDemand-Locators, um eine Streaming-URL zu erhalten.

Ein vollständiges .NET-Beispiel finden Sie am Ende des Artikels.

In der folgenden Abbildung ist der oben beschriebene Workflow dargestellt. Hier wird das Token zur Authentifizierung verwendet.

![Schützen mit PlayReady](media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

Im weiteren Verlauf dieses Artikels finden Sie ausführliche Beschreibungen, Codebeispiele und Links zu Themen, die Informationen dazu enthalten, wie die oben beschriebenen Aufgaben ausgeführt werden.

## <a name="current-limitations"></a>Aktuelle Einschränkungen
Wenn Sie die Bereitstellungsrichtlinie eines Medienobjekts hinzufügen oder aktualisieren, müssen Sie den zugeordneten Locator löschen und einen neuen Locator erstellen.

Derzeit wird die Verwendung von mehreren Inhaltsschlüsseln nicht unterstützt, wenn Sie für die Verschlüsselung Widevine mit Media Services verwenden. 

## <a name="create-an-asset-and-upload-files-into-the-asset"></a>Erstellen eines Medienobjekts und Hochladen von Dateien in das Medienobjekt
Damit Sie Ihre Videos verwalten, codieren und streamen können, müssen Sie Ihre Inhalte zuerst in Media Services hochladen. Nachdem dies geschehen ist, sind Ihre Inhalte sicher in der Cloud zur weiteren Verarbeitung und für das weitere Streaming gespeichert.

Weitere Informationen finden Sie unter [Hochladen von Dateien in ein Media Services-Konto](media-services-dotnet-upload-files.md).

## <a name="encode-the-asset-that-contains-the-file-to-the-adaptive-bitrate-mp4-set"></a>Codieren Sie ein Medienobjekt, das die Sammlung von MP4-Dateien mit adaptiver Bitrate enthält.
Bei der dynamischen Verschlüsselung erstellen Sie ein Medienobjekt, das eine Sammlung aus MP4-Dateien mit mehreren Bitraten oder Smooth Streaming-Quelldateien mit mehreren Bitraten enthält. Anschließend wird durch den bedarfsgesteuerten Streamingserver auf Basis des in der Manifest- und Fragmentanforderung angegebenen Formats sichergestellt, dass Sie den Datenstrom im ausgewählten Protokoll erhalten. So werden die Dateien nur in einem Speicherformat gespeichert, und Sie zahlen auch nur für ein Format. Media Services reagiert auf die Anforderungen eines Clients jeweils mit der richtigen Antwort. Weitere Informationen finden Sie in der Übersicht unter [Dynamische Paketerstellung](media-services-dynamic-packaging-overview.md).

Informationen zum Codieren finden Sie unter [Codieren eines Medienobjekts mit Media Encoder Standard mit .NET](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a id="create_contentkey"></a>Erstellen eines Inhaltsschlüssels und Zuordnen des Schlüssels zum codierten Medienobjekt
In Media Services enthält der Inhaltsschlüssel den Schlüssel, mit dem ein Medienobjekt verschlüsselt werden soll.

Weitere Informationen finden Sie unter [Erstellen eines Inhaltsschlüssels](media-services-dotnet-create-contentkey.md).

## <a id="configure_key_auth_policy"></a>Konfigurieren einer Autorisierungsrichtlinie für Inhaltsschlüssel
Media Services unterstützt mehrere Möglichkeiten zur Authentifizierung von Benutzern, die Schlüssel anfordern. Sie müssen die Autorisierungsrichtlinie für den Inhaltsschlüssel konfigurieren. Der Client (Player) muss die Richtlinie erfüllen, bevor der Schlüssel an den Client übermittelt wird. Die Autorisierungsrichtlinie für Inhaltsschlüssel kann eine oder mehrere Autorisierungseinschränkungen aufweisen: offen oder Token.

Weitere Informationen finden Sie unter [Konfigurieren von Autorisierungsrichtlinien für Inhaltsschlüssel](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption).

## <a id="configure_asset_delivery_policy"></a>Konfigurieren einer Bereitstellungsrichtlinie für Medienobjekte
Konfigurieren Sie die Übermittlungsrichtlinie für Medienobjekte. Die Konfiguration der Bereitstellungsrichtlinie für Medienobjekte umfasst Folgendes:

* Die DRM-Lizenzerwerbs-URL.
* Das Übermittlungsprotokoll für Medienobjekte (z. B. „MPEG-DASH“, „HLS“, „Smooth Streaming“ oder „Alle“).
* Typ der dynamischen Verschlüsselung (in diesem Fall allgemeine Verschlüsselung).

Weitere Informationen finden Sie unter [Konfigurieren von Übermittlungsrichtlinien für Medienobjekte mit .NET SDK](media-services-dotnet-configure-asset-delivery-policy.md).

## <a id="create_locator"></a>Erstellen eines OnDemand-Streaminglocators, um eine Streaming-URL abzurufen
Sie müssen die Streaming-URL für Smooth Streaming, DASH oder HLS für Ihre Benutzer bereitstellen.

> [!NOTE]
> Wenn Sie die Übermittlungsrichtlinie eines Medienobjekts hinzufügen oder aktualisieren, müssen Sie alle vorhandenen Locators löschen und einen neuen Locator erstellen.
>
>

Anweisungen zum Veröffentlichen eines Medienobjekts und Erstellen einer Streaming-URL finden Sie unter [Erstellen einer Streaming-URL](media-services-deliver-streaming-content.md).

## <a name="get-a-test-token"></a>Abrufen eines Testtokens
Rufen Sie ein Testtoken ab, das auf der Tokeneinschränkung basiert, die für die Schlüsselautorisierungsrichtlinie verwendet wurde.

```csharp
// Deserializes a string containing an XML representation of a TokenRestrictionTemplate
// back into a TokenRestrictionTemplate class instance.
TokenRestrictionTemplate tokenTemplate =
TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

// Generate a test token based on the data in the given TokenRestrictionTemplate.
//The GenerateTestToken method returns the token without the word "Bearer" in front,
//so you have to add it in front of the token string.
string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

Sie können den [Azure Media Services Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html) verwenden, um Ihren Datenstrom zu testen.

## <a name="create-and-configure-a-visual-studio-project"></a>Erstellen und Konfigurieren eines Visual Studio-Projekts

1. Richten Sie Ihre Entwicklungsumgebung ein, und füllen Sie die Datei „app.config“ mit Verbindungsinformationen, wie unter [Media Services-Entwicklung mit .NET](media-services-dotnet-how-to-use.md) beschrieben.

2. Fügen Sie den **appSettings** in Ihrer Datei „app.config“ die folgenden Elemente hinzu:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Beispiel

Im folgenden Beispiel wird die Funktionalität veranschaulicht, die im Media Services SDK für .NET Version 3.5.2 eingeführt wurde. (Sie umfasst vor allem die Möglichkeit zum Definieren einer Widevine-Lizenzvorlage und zum Anfordern einer Widevine-Lizenz von Media Services.)

Überschreiben Sie den Code in Ihrer Datei "Program.cs" mit dem in diesem Abschnitt gezeigten Code.

>[!NOTE]
>Es gilt ein Grenzwert von 1 Millionen Richtlinien für verschiedene Media Services-Richtlinien (z.B. für die Locator-Richtlinie oder für ContentKeyAuthorizationPolicy). Falls Sie immer die gleichen Tage und Zugriffsberechtigungen verwenden, ist es ratsam, dieselbe Richtlinien-ID zu nutzen. Ein Beispiel hierfür ist die Verwendung von Richtlinien für Locators, die für einen längeren Zeitraum vorgesehen sind (Richtlinien ohne Upload). 

Weitere Informationen finden Sie unter [Verwalten von Medienobjekten und verwandten Entitäten mit dem Media Services .NET SDK](media-services-dotnet-manage-entities.md#limit-access-policies).

Stellen Sie sicher, dass die Variablen so aktualisiert werden, dass sie auf die Ordner zeigen, in denen sich Ihre Eingabedateien befinden.

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
using Newtonsoft.Json;

namespace DynamicEncryptionWithDRM
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

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = false;
            string tokenTemplateString = null;

            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
            Console.WriteLine("Uploaded asset: {0}", asset.Id);

            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

            IContentKey key = CreateCommonTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
            Console.WriteLine();

            if (tokenRestriction)
                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
            else
                AddOpenAuthorizationPolicy(key);

            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
            Console.WriteLine();

            CreateAssetDeliveryPolicy(encodedAsset, key);
            Console.WriteLine("Created asset delivery policy. \n");
            Console.WriteLine();

            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
            {
                // Deserializes a string containing an XML representation of a TokenRestrictionTemplate
                // back into a TokenRestrictionTemplate class instance.
                TokenRestrictionTemplate tokenTemplate =
                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note that you need to pass the key ID GUID because 
                // TokenClaim.ContentKeyIdentifierClaim was specified during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            // You can use the https://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
            // Note that DASH works on Internet Explorer 11 (via PlayReady), Microsoft Edge (via PlayReady), and Chrome (via Widevine).

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);

            Console.ReadLine();
        }

        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
        {
            var encodingPreset = "Adaptive Streaming";

            IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                        inputAsset.Name,
                        encodingPreset));

            var mediaProcessors =
            _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

            var latestMediaProcessor =
            mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
            encodeTask.InputAssets.Add(inputAsset);
            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), AssetCreationOptions.StorageEncrypted);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }


        static public IContentKey CreateCommonTypeContentKey(IAsset asset)
        {

            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryption);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }

        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {

            // Create ContentKeyAuthorizationPolicy with open restrictions
            // and create an authorization policy.         

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
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

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
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

            // Associate the content key authorization policy with the content key.
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
            // The following code configures the PlayReady license template by using .NET classes
            // and returns the XML string.

            //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user.
            //It contains a field for a custom data string between the license server and the application
            //(which might be useful for custom app logic) as well as a list of one or more license templates.
            PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

            // The PlayReadyLicenseTemplate class represents a license template you can use to create PlayReady licenses
            // to be returned to end users.
            //It contains the data on the content key in the license and any rights or restrictions to be
            //enforced by the PlayReady DRM runtime when you use the content key.
            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
            //Configure whether the license is persistent (saved in persistent storage on the client)
            //or nonpersistent (held in memory only while the player uses the license).  
            licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

            // AllowTestDevices controls whether test devices can use the license or not.  
            // If true, the MinimumSecurityLevel property of the license
            // is set to 150. If false (the default), the MinimumSecurityLevel property of the license is set to 2,000.
            licenseTemplate.AllowTestDevices = true;

            // You also can configure the PlayRight in the PlayReady license by using the PlayReadyPlayRight class.
            // It grants the user the ability to play back the content subject to the zero or more restrictions
            // configured in the license and on the PlayRight itself (for playback-specific policy).
            // Much of the policy on the PlayRight has to do with output restrictions,
            // which control the types of outputs that the content can be played over and
            // any restrictions that must be put in place when you use a given output.
            // For example, if DigitalVideoOnlyContentRestriction is enabled,
            // the DRM runtime allows the video to be displayed only over digital outputs
            //(analog video outputs aren't allowed to pass the content).

            //IMPORTANT: These types of restrictions can be very powerful but also can affect the consumer experience.
            // If output protections are too restrictive, 
            // content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

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
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
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

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            // Get the PlayReady license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

            // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
            // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
            // WidevineBaseLicenseAcquisitionUrl (used in the following) also tells dynamic encryption
            // to append /? KID =< keyId > to the end of the URL when you create the manifest.
            // As a result, the Widevine license acquisition URL has the KID appended twice,
            // so you need to remove the KID in the URL when you call GetKeyDeliveryUrl.

            Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
            UriBuilder uriBuilder = new UriBuilder(widevineUrl);
            uriBuilder.Query = String.Empty;
            widevineUrl = uriBuilder.Uri;

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
                    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}

            };

            // In this case, we specify only the DASH streaming protocol in the delivery policy.
            // All other protocols are blocked from streaming.
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


            // Add AssetDelivery Policy to the asset.
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        }

        /// <summary>
        /// Gets the streaming origin locator.
        /// </summary>
        /// <param name="assets"></param>
        /// <returns></returns>
        static public string GetStreamingOriginLocator(IAsset asset)
        {

            // Get a reference to the streaming manifest file from the 
            // collection of files in the asset.

            var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                         EndsWith(".ism")).
                         FirstOrDefault();

            // Create a 30-day read-only access policy.
            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

            // Create a locator to the streaming content on an origin.
            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

            // Create a URL to the manifest file.
            return originLocator.Path + assetFile.Name;
        }

        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
            ((IJob)sender).Name,
            e.CurrentState,
            DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
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

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Weitere Informationen

* [CENC mit mehreren DRM-Systemen und Access Control: Referenzentwurf und -implementierung in Azure und Azure Media Services](media-services-cenc-with-multidrm-access-control.md)
* [Configure Widevine packaging with Media Services](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services) (Konfigurieren der Widevine-Paketerstellung mit Media Services)
* [Erste Schritte mit dem Java-Client-SDK für Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use)
* Wenn Sie das aktuelle PHP SDK für Media Services herunterladen möchten, können Sie im [Packagist-Repository](https://packagist.org/packages/microsoft/windowsazure#v0.5.7) nach Version 0.5.7 des Microsoft/WindowsAzure-Pakets suchen. 


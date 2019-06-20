---
title: Schützen von HLS-Inhalten mit Microsoft PlayReady oder Apple FairPlay – Azure | Microsoft Docs
description: Dieses Thema bietet einen Überblick und veranschaulicht, wie Sie Azure Media Services verwenden, um Ihre HLS-Inhalte (HTTP Live Streaming) dynamisch mit Apple FairPlay verschlüsseln. Es zeigt auch, wie Sie den Lizenzbereitstellungsdienst von Media Services verwenden, um FairPlay-Lizenzen an Clients zu übermitteln.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: c30a32466cbac795ef037a3295816e87995ad749
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64868389"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Schützen von HLS-Inhalten mit Apple FairPlay oder Microsoft PlayReady

> [!NOTE]
> Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).   > Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).
>

Mit Azure Media Services können Sie Ihre HLS-Inhalte (HTTP Live Streaming) unter Verwendung der folgenden Formate dynamisch verschlüsseln:  

* **Unverschlüsselter Schlüssel mit AES-128-Umschlag**

    Der gesamte Block wird mit dem **AES-128 CBC**-Modus verschlüsselt. Die Entschlüsselung des Streams wird von iOS- und OS X-Playern nativ unterstützt. Weitere Informationen finden Sie unter [Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselübermittlungsdiensts](media-services-protect-with-aes128.md).
* **Apple FairPlay**

    Die einzelnen Audio- und Videosamples werden mit dem **AES-128 CBC**-Modus verschlüsselt. **FairPlay Streaming** (FPS) ist in die Gerätebetriebssysteme integriert und wird von iOS und Apple TV nativ unterstützt. Safari unter OS X ermöglicht FPS durch Unterstützung der EME-Schnittstelle (Encrypted Media Extensions).
* **Microsoft PlayReady**

Die folgende Abbildung veranschaulicht den Workflow für dynamische **HLS- und FairPlay-Verschlüsselung oder PlayReady-Verschlüsselung**.

![Diagramm des dynamischen Verschlüsselungsworkflows](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

Dieser Artikel veranschaulicht, wie Sie Media Services verwenden, um Ihre HLS-Inhalte dynamisch mit Apple FairPlay verschlüsseln. Es zeigt auch, wie Sie den Lizenzbereitstellungsdienst von Media Services verwenden, um FairPlay-Lizenzen an Clients zu übermitteln.

> [!NOTE]
> Wenn Sie auch Ihre HLS-Inhalte mit PlayReady verschlüsseln möchten, müssen Sie einen gemeinsamen Schlüssel erstellen und ihn mit dem Medienobjekt verknüpfen. Darüber hinaus müssen Sie die Autorisierungsrichtlinie des Inhaltsschlüssels konfigurieren, wie unter [Verwenden von dynamischer allgemeiner Verschlüsselung mit PlayReady und/oder Widevine](media-services-protect-with-playready-widevine.md) beschrieben.
>
>

## <a name="requirements-and-considerations"></a>Anforderungen und Überlegungen

Folgendes ist erforderlich, wenn Sie Media Services verwenden, um mit FairPlay verschlüsselte HLS-Inhalte sowie FairPlay-Lizenzen zu übermitteln:

  * Ein Azure-Konto. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Media Services-Konto. Informationen zum Erstellen eines Media Services-Kontos finden Sie unter [Erstellen eines Azure Media Services-Kontos mithilfe des Azure-Portals](media-services-portal-create-account.md).
  * Registrieren Sie sich für das [Apple Developer Program](https://developer.apple.com/)(Apple-Entwicklerprogramm).
  * Apple setzt voraus, dass der Inhaltsbesitzer über das [Bereitstellungspaket](https://developer.apple.com/contact/fps/)verfügt. Geben Sie an, dass Sie bereits KSM (Key Security Module) mit Media Services implementiert haben, und dass Sie das endgültige FPS-Paket anfordern. Das endgültige FPS-Paket enthält Anweisungen zum Generieren des Zertifikats und zum Abrufen des geheimen Anwendungsschlüssels (ASK). Sie verwenden ASK, um FairPlay zu konfigurieren.
  * Azure Media Services .NET SDK, Version **3.6.0** oder höher.

Folgendes muss seitens der Media Services-Schlüsselbereitstellung festgelegt werden:

  * **App Cert (AC)** : Dies ist eine PFX-Datei, die den privaten Schlüssel enthält. Sie erstellen diese Datei und verschlüsseln sie mit einem Kennwort.

       Wenn Sie eine Richtlinie für die Schlüsselbereitstellung konfigurieren, müssen Sie dieses Kennwort und die PFX-Datei im Base64-Format bereitstellen.

      Die folgenden Schritte beschreiben, wie eine PFX-Zertifikatdatei für FairPlay generiert wird:

    1. Installieren von OpenSSL von https://slproweb.com/products/Win32OpenSSL.html.

        Wechseln Sie zum Ordner, in dem das FairPlay-Zertifikat und andere von Apple übermittelte Dateien enthalten sind.
    2. Führen Sie den folgenden Befehl an der Befehlszeile aus: Dadurch wird die CER-Datei in eine PEM-Datei konvertiert.

        „C:\OpenSSL-Win32\bin\openssl.exe“ x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
    3. Führen Sie den folgenden Befehl an der Befehlszeile aus: Dadurch wird die PEM-Datei in eine PFX-Datei mit dem privaten Schlüssel konvertiert. Das Kennwort für die PFX-Datei wird dann von OpenSSL angefordert.

        „C:\OpenSSL-Win32\bin\openssl.exe“ pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
  * **App Cert-Kennwort**: Das Kennwort zum Erstellen der PFX-Datei.
  * **App Cert-Kennwort-ID:** Sie müssen das Kennwort ähnlich wie andere Media Services-Schlüssel hochladen. Verwenden Sie den Enumerationswert **ContentKeyType.FairPlayPfxPassword**, um die Media Services-ID abzurufen. Diese werden in der Richtlinienoption für die Schlüsselbereitstellung benötigt.
  * **iv:** Dies ist ein zufälliger Wert von 16 Bytes. Er muss dem iv-Wert in der Richtlinie zur Übermittlung von Medienobjekten entsprechen. Sie generieren den iv-Wert und geben ihn in der Richtlinie zur Übermittlung von Medienobjekten sowie in der Richtlinienoption für die Schlüsselbereitstellung an.
  * **ASK**: Dieser Schlüssel wird bei der Generierung der Zertifizierung über das Apple Developer-Portal empfangen. Jedes Entwicklungsteam erhält einen eindeutigen ASK. Speichern Sie eine Kopie des ASK an einem sicheren Ort. Sie müssen den ASK später als FairPlayAsk für Media Services konfigurieren.
  * **ASK-ID:** Diese ID wird abgerufen, wenn Sie ASK in Media Services hochladen. Sie müssen ASK mit dem **ContentKeyType.FairPlayAsk**-Enumerationswert hochladen. Dadurch wird die Media Services-ID zurückgegeben, die Sie beim Festlegen der Richtlinienoption für die Schlüsselbereitstellung verwenden sollten.

Folgendes muss seitens des FPS-Clients festgelegt werden:

  * **App Cert (AC)** : CER-/DER-Datei mit dem öffentlichen Schlüssel, den das Betriebssystem zur Verschlüsselung bestimmter Nutzlasten verwendet. Media Services muss den Schlüssel kennen, da er vom Player benötigt wird. Der Schlüsselbereitstellungsdienst entschlüsselt den Schlüssel mithilfe des entsprechenden privaten Schlüssels.

Um einen über FairPlay verschlüsselten Stream wiederzugeben, rufen Sie zuerst den echten ASK ab, und generieren Sie dann ein echtes Zertifikat. Dieser Prozess erstellt alle drei Teile:

  * DER-Datei
  * PFX-Datei
  * Kennwort für die PFX-Datei

Die folgenden Clients unterstützen HLS mit **AES-128 CBC**-Verschlüsselung: Safari unter OS X, Apple TV, iOS.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>Konfigurieren der dynamischen FairPlay-Verschlüsselung und des Lizenzbereitstellungsdiensts
Die folgenden allgemeinen Schritte dienen zum Schützen Ihrer Medienobjekte mit FairPlay unter Verwendung des Media Services-Lizenzbereitstellungsdiensts sowie der dynamischen Verschlüsselung.

1. Erstellen Sie ein Medienobjekt, und laden Sie Dateien in das Medienobjekt hoch.
2. Codieren Sie ein Medienobjekt, das die Sammlung von MP4-Dateien mit adaptiver Bitrate enthält.
3. Erstellen Sie einen Inhaltsschlüssel, und ordnen Sie den Schlüssels dem codierten Medienobjekt zu.  
4. Konfigurieren der Autorisierungsrichtlinie des Inhaltsschlüssels. Geben Sie Folgendes an:

   * Die Bereitstellungsmethode (in diesem Fall FairPlay)
   * Konfiguration der FairPlay-Richtlinienoptionen; Informationen zum Konfigurieren von FairPlay finden Sie im Beispiel weiter unten in der **ConfigureFairPlayPolicyOptions()** -Methode.

     > [!NOTE]
     > Üblicherweise sollten Sie FairPlay-Richtlinienoptionen nur einmal konfigurieren, da Sie nur einen Satz aus Zertifikat und ASK haben.
     >
     >
   * Einschränkungen (offen oder tokenbasiert)
   * Informationen zum Typ der Schlüsselbereitstellung, der definiert, wie der Schlüssel an den Kunden übermittelt wird
5. Konfigurieren Sie die Übermittlungsrichtlinie für Medienobjekte. Die Richtlinienkonfiguration umfasst Folgendes:

   * Das Übermittlungsprotokoll (HLS)
   * Den Typ der dynamischen Verschlüsselung (Common CBC Encryption)
   * Die Lizenzerwerbs-URL

     > [!NOTE]
     > Wenn Sie einen Stream übermitteln möchten, der mit FairPlay und einem weiteren System zur digitalen Rechteverwaltung (Digital Rights Management, DRM) verschlüsselt wurde, müssen Sie separate Übermittlungsrichtlinien konfigurieren:
     >
     > * Eine IAssetDeliveryPolicy-Richtlinie zum Konfigurieren von DASH (Dynamic Adaptive Streaming over HTTP) mit allgemeiner Verschlüsselung (CENC) (PlayReady + Widevine) sowie Smooth mit PlayReady
     > * Eine weitere IAssetDeliveryPolicy-Richtlinie zum Konfigurieren von FairPlay für HLS
     >
     >
6. Erstellen eines OnDemand-Locators, um eine Streaming-URL zu erhalten.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>Verwenden der FairPlay-Schlüsselübermittlung nach Player-Apps
Sie können Player-Apps mit dem iOS-SDK entwickeln. Damit FairPlay-Inhalte wiedergegeben werden können, müssen Sie das Lizenzaustauschprotokoll implementieren. Dieses Protokoll wird nicht von Apple angegeben. Es ist jeder App freigestellt, wie sie Anforderungen zur Schlüsselübermittlung sendet. Der Media Services-FairPlay-Schlüsselübermittlungsdienst erwartet das SPC als POST-Nachricht mit Verschlüsselung vom Typ „www-form-url“ im folgenden Format:

    spc=<Base64 encoded SPC>

> [!NOTE]
> Azure Media Player unterstützt die FairPlay-Wiedergabe. Weitere Informationen finden Sie in der [Azure Media Player-Dokumentation](https://amp.azure.net/libs/amp/latest/docs/index.html).
>
>

## <a name="streaming-urls"></a>Streaming-URLs
Falls Ihr Asset mit mehreren DRM-Lösungen verschlüsselt wurde, empfiehlt es sich, in der Streaming-URL ein Verschlüsselungstag zu verwenden: (format='m3u8-aapl', encryption='xxx').

Es gelten die folgenden Bedingungen:

* Sie können nur einen einzelnen (oder keinen) Verschlüsselungstyp angegeben.
* Der Verschlüsselungstyp muss nicht in der URL angegeben werden, wenn auf das Medienobjekt nur eine einzelne Verschlüsselung angewendet wurde.
* Beim Verschlüsselungstyp wird die Groß-/Kleinschreibung nicht beachtet.
* Folgende Verschlüsselungstypen können angegeben werden:  
  * **cenc:**  Allgemeine Verschlüsselung (PlayReady oder Widevine)
  * **cbcs-aapl:** FairPlay
  * **cbc:** AES-Umschlagverschlüsselung

## <a name="create-and-configure-a-visual-studio-project"></a>Erstellen und Konfigurieren eines Visual Studio-Projekts

1. Richten Sie Ihre Entwicklungsumgebung ein, und füllen Sie die Datei „app.config“ mit Verbindungsinformationen, wie unter [Media Services-Entwicklung mit .NET](media-services-dotnet-how-to-use.md) beschrieben. 
2. Fügen Sie den **appSettings** in Ihrer Datei „app.config“ die folgenden Elemente hinzu:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Beispiel

Das folgende Beispiel veranschaulicht die Möglichkeit, Media Services zum Übermitteln von Inhalten zu verwenden, die mit FairPlay verschlüsselt wurden. Diese Funktionalität wurde im Azure Media Services SDK für .NET, Version 3.6.0, eingeführt. 

Überschreiben Sie den Code in Ihrer Datei "Program.cs" mit dem in diesem Abschnitt gezeigten Code.

>[!NOTE]
>Es gilt ein Grenzwert von 1.000.000 Richtlinien für verschiedene AMS-Richtlinien (z.B. für die Locator-Richtlinie oder für ContentKeyAuthorizationPolicy). Wenn Sie immer die gleichen Tage/Zugriffsberechtigungen verwenden, z.B. Richtlinien für Locator, die für einen längeren Zeitraum vorgesehen sind (Richtlinien ohne Upload), sollten Sie dieselbe Richtlinien-ID verwenden. Weitere Informationen dazu finden Sie in [diesem Artikel](media-services-dotnet-manage-entities.md#limit-access-policies).

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
using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
using Newtonsoft.Json;
using System.Security.Cryptography.X509Certificates;

namespace DynamicEncryptionWithFairPlay
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

            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
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
                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                // back into a TokenRestrictionTemplate class instance.
                TokenRestrictionTemplate tokenTemplate =
                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note, you need to pass the key id Guid because we specified
                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);

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

            IJob job = _context.Jobs.Create(String.Format("Encoding {0}", inputAsset.Name));

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

        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
        {
            // Create HLS SAMPLE AES encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryptionCbcs);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }


        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {
            // Create ContentKeyAuthorizationPolicy with Open restrictions
            // and create authorization policy          

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Open",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                        Requirements = null
                    }
                    };


            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();

            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
            ContentKeyDeliveryType.FairPlay,
            restrictions,
            FairPlayConfiguration);


            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

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

            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();


            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                   ContentKeyDeliveryType.FairPlay,
                   restrictions,
                   FairPlayConfiguration);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        private static string ConfigureFairPlayPolicyOptions()
        {
            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK.
            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
            byte[] askBytes = Guid.NewGuid().ToByteArray();
            var askId = Guid.NewGuid();
            // Key delivery retrieves askKey by askId and uses this key to generate the response.
            IContentKey askKey = _context.ContentKeys.Create(
                        askId,
                        askBytes,
                        "askKey",
                        ContentKeyType.FairPlayASk);

            //Customer password for creating the .pfx file.
            string pfxPassword = "<customer password for creating the .pfx file>";
            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
            var pfxPasswordId = Guid.NewGuid();
            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                        pfxPasswordId,
                        pfxPasswordBytes,
                        "pfxPasswordKey",
                        ContentKeyType.FairPlayPfxPassword);

            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
            byte[] iv = Guid.NewGuid().ToByteArray();

            //Specify the .pfx file created by the customer.
            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);

            string FairPlayConfiguration =
            Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                appCert,
                pfxPassword,
                pfxPasswordId,
                askId,
                iv);

            return FairPlayConfiguration;
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

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();

            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);

            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);

            // Get the FairPlay license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);

            // The reason the below code replaces "https://" with "skd://" is because
            // in the IOS player sample code which you obtained in Apple developer account,
            // the player only recognizes a Key URL that starts with skd://.
            // However, if you are using a customized player,
            // you can choose whatever protocol you want.
            // For example, "https".

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
            };

            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            assetDeliveryPolicyConfiguration);

            // Add AssetDelivery Policy to the asset
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

            // Create a 30-day readonly access policy.
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

## <a name="next-steps-media-services-learning-paths"></a>Nächste Schritte: Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

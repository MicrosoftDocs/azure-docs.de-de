---
title: Erstellen eines Mandantenmodells (Vorschau) – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Generieren Sie automatisch ein sicheres, konformes Mandantenmodell (Custom Speech mit Microsoft 365-Daten), das Ihre Microsoft 365-Daten nutzt, um eine optimale Spracherkennung für organisationsspezifische Begriffe bereitzustellen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 48cde51ee9941f705aa848d121c419a8f0c9ad1a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013713"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>Tutorial: Erstellen eines Mandantenmodells (Vorschau)

Ein Mandantenmodell (Custom Speech mit Microsoft 365-Daten) ist ein auswählbarer Dienst für Microsoft 365-Unternehmenskunden, der automatisch ein benutzerdefiniertes Spracherkennungsmodell aus den Microsoft 365-Daten Ihrer Organisation generiert. Das Modell ist für technische und andere Fachbegriffe sowie für Personennamen optimiert und zeichnet sich durch hohe Sicherheit und Konformität aus.

> [!IMPORTANT]
> Wenn Ihre Organisation sich über den Mandantenmodelldienst registriert, kann der Speech-Dienst möglicherweise auf das Sprachmodell Ihrer Organisation zugreifen. Das Modell wird anhand von E-Mails und Dokumenten der öffentlichen Microsoft 365-Gruppe erstellt, die von allen Personen in Ihrer Organisation eingesehen werden können. Der Administrator Ihrer Organisation kann die Nutzung des organisationsweiten Sprachmodells über das Verwaltungsportal aktivieren und deaktivieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Registrieren für das Mandantenmodell über das Microsoft 365 Admin Center
> * Abrufen eines Abonnementschlüssels für Speech
> * Erstellen eines Mandantenmodells
> * Bereitstellen eines Mandantenmodells
> * Verwenden Ihres Mandantenmodells mit dem Speech SDK

## <a name="enroll-in-the-tenant-model-service"></a>Registrieren beim Mandantenmodelldienst

Bevor Sie Ihr Mandantenmodell bereitstellen können, müssen Sie sich beim Mandantenmodelldienst registrieren. Die Registrierung erfolgt im Microsoft 365 Admin Center und kann nur durch Ihren Administrator durchgeführt werden.

1. Melden Sie sich beim [Microsoft 365 Admin Center](https://admin.microsoft.com) an.

1. Wählen Sie im linken Bereich die Option **Einstellungen** und dann im geschachtelten Menü die Option **Einstellungen** aus. Wählen Sie anschließend im Hauptfenster **Azure Speech-Dienste** aus.

   ![Der Bereich „Dienste und Add-Ins“](media/tenant-language-model/tenant-language-model-enrollment.png)

1. Aktivieren Sie das Kontrollkästchen **Organisationsweites Sprachmodell zulassen**, und wählen Sie dann **Änderungen speichern** aus.

   ![Der Bereich „Azure Speech-Dienste“](media/tenant-language-model/tenant-language-model-enrollment-2.png)

So deaktivieren Sie die Mandantenmodellinstanz:
1. Wiederholen Sie die Schritte 1 und 2.
1. Deaktivieren Sie das Kontrollkästchen **Organisationsweites Sprachmodell zulassen**, und wählen Sie dann **Änderungen speichern** aus.

## <a name="get-a-speech-subscription-key"></a>Abrufen eines Abonnementschlüssels für Speech

Um Ihr Mandantenmodell mit dem Speech SDK zu verwenden, benötigen Sie eine Speech-Ressource und den zugehörigen Abonnementschlüssel.

1. Melden Sie sich beim [Azure-Portal](https://aka.ms/azureportal) an.
1. Wählen Sie **Ressource erstellen**.
1. Geben Sie im **Suchfeld** den Suchbegriff **Speech** ein.
1. Wählen Sie in der Ergebnisliste **Speech** und dann **Erstellen** aus.
1. Folgen Sie den Anweisungen auf dem Bildschirm, um Ihre Ressource zu erstellen. Stellen Sie Folgendes sicher:
   * **Standort** ist entweder auf **eastus** oder **westus** festgelegt.
   * **Tarif** ist auf **S0** festgelegt.
1. Klicken Sie auf **Erstellen**.

   Ihre Ressource wird binnen weniger Minuten erstellt. Den Abonnementschlüssel finden Sie im Abschnitt **Übersicht** für Ihre Ressource.

## <a name="create-a-language-model"></a>Erstellen eines Sprachmodells

Nachdem Ihr Administrator das Mandantenmodell für Ihre Organisation aktiviert hat, können Sie ein Sprachmodell erstellen, das auf Ihren Microsoft 365-Daten basiert.

1. Melden Sie sich bei [Speech Studio](https://speech.microsoft.com/) an.
1. Klicken Sie oben rechts auf **Einstellungen** (Zahnradsymbol), und wählen Sie dann **Einstellungen für Mandantenmodell** aus.

   ![Der Link zu den Einstellungen für das Mandantenmodell](media/tenant-language-model/tenant-language-settings.png)

   Speech Studio informiert Sie in einer Meldung darüber, ob Sie dazu berechtigt sind, ein Mandantenmodell zu erstellen.

   > [!NOTE]
   > Enterprise-Kunden in Nordamerika sind berechtigt, ein Mandantenmodell (Englisch) zu erstellen. Für Kunden vom Typ „Kunden-Lockbox“, „Kundenschlüssel“ oder „Office 365 Government“ ist dieses Feature nicht verfügbar. Informationen dazu, ob Ihr Kundenkonto den Typ „Kunden-Lockbox“ oder „Kundenschlüssel“ aufweist, finden Sie hier:
   > * [Kunden-Lockbox](/microsoft-365/compliance/customer-lockbox-requests)
   > * [Kundenschlüssel](/microsoft-365/compliance/customer-key-overview)
   > * [Office 365 Government](https://www.microsoft.com/microsoft-365/government)

1. Wählen Sie **Aktivieren** aus.

   Wenn Ihr Mandantenmodell bereit ist, erhalten Sie per E-Mail eine Bestätigung mit weiteren Anweisungen.

## <a name="deploy-your-tenant-model"></a>Bereitstellen Ihres Mandantenmodells

Wenn Ihre Mandantenmodellinstanz bereit ist, stellen Sie diese folgendermaßen bereit:

1. Klicken Sie in der Bestätigungs-E-Mail auf die Schaltfläche **Modell anzeigen**, oder melden Sie sich bei [Speech Studio](https://speech.microsoft.com/) an.
1. Klicken Sie oben rechts auf **Einstellungen** (Zahnradsymbol), und wählen Sie dann **Einstellungen für Mandantenmodell** aus.

   ![Der Link zu den Einstellungen für das Mandantenmodell](media/tenant-language-model/tenant-language-settings.png)

1. Klicken Sie auf **Bereitstellen**.

   Wenn Ihr Modell bereitgestellt wurde, ändert sich der Status zu *Bereitgestellt*.

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>Verwenden Ihres Mandantenmodells mit dem Speech SDK

Nachdem Sie Ihr Modell bereitgestellt haben, können Sie es mit dem Speech SDK verwenden. In diesem Abschnitt verwenden Sie den Beispielcode, um den Speech-Dienst mithilfe der Azure Active Directory-Authentifizierung aufzurufen.

Schauen wir uns den Code an, mit dem Sie das Speech SDK in C# aufrufen. In diesem Beispiel führen Sie die Spracherkennung mithilfe Ihres Mandantenmodells durch. Diese Anleitung setzt voraus, dass Ihre Plattform bereits eingerichtet ist. Hilfe bei der Einrichtung finden Sie unter [Schnellstart: Erkennen von Sprache, C# (.NET Core)](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore).

Kopieren Sie diesen Code in Ihr Projekt:

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // ServiceApplicationId is a fixed value. No need to change it.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

Als Nächstes müssen Sie das Projekt über die Befehlszeile neu erstellen und ausführen. Bevor Sie den Befehl ausführen, aktualisieren Sie einige Parameter, indem Sie folgendermaßen vorgehen:

1. Ersetzen Sie `<Username>` und `<Password>` durch die Werte eines gültigen Mandantenbenutzers.
1. Ersetzen Sie `<Subscription-Key>` durch den Abonnementschlüssel Ihrer Speech-Ressource. Diesen Wert finden Sie im [Azure-Portal](https://aka.ms/azureportal) im Abschnitt **Übersicht** für Ihre Speech-Ressource.
1. Ersetzen Sie `<Endpoint-Uri>` durch den folgenden Endpunkt. Achten Sie darauf, dass Sie `{your region}` durch die Region ersetzen, in der Ihre Speech-Ressource erstellt wurde. Diese Regionen werden unterstützt: `westus`, `westus2` und `eastus`. Informationen zu Ihrer Region finden Sie im [Azure-Portal](https://aka.ms/azureportal) im Abschnitt **Übersicht** Ihrer Speech-Ressource.
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. Führen Sie den folgenden Befehl aus:

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

In diesem Tutorial haben Sie erfahren, wie Sie Microsoft 365-Daten verwenden, um ein benutzerdefiniertes Spracherkennungsmodell mit dem Speech SDK zu erstellen, bereitzustellen und zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

* [Speech Studio](https://speech.microsoft.com/)
* [Speech SDK](speech-sdk.md)
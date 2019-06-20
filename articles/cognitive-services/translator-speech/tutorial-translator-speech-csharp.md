---
title: 'Tutorial: Sprachübersetzungs-API (C#)'
titleSuffix: Azure Cognitive Services
description: Übersetzen Sie Text mit der Sprachübersetzungs-API in Echtzeit.
services: cognitive-services
author: v-jerkin
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: tutorial
ms.date: 3/5/2018
ms.author: v-jerkin
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 68457367987305b6926f8f885a226cfcedd23873
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65860314"
---
# <a name="tutorial-translator-speech-application-in-c"></a>Tutorial: Sprachübersetzungsanwendung in C#

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

In diesem Tutorial lernen Sie ein interaktives Sprachübersetzungstool kennen, das die Sprachübersetzungs-API (ein Teil von Azure Cognitive Services) verwendet. Sie lernen Folgendes:

> [!div class="checklist"]
> * Anfordern einer Liste mit Sprachen, die der Dienst unterstützt
> * Aufzeichnen von Audio und Übertragen der Daten an den Dienst
> * Empfangen und Anzeigen von Übersetzungen des Gesprochenen in Textform
> * Optional: Wiedergeben einer gesprochenen Version der Übersetzung (Sprachsynthese)

Eine Visual Studio-Projektmappendatei für diese Anwendung finden Sie auf [GitHub](https://github.com/MicrosoftTranslator/SpeechTranslator).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial können Sie eine beliebige Edition von Visual Studio 2019 (einschließlich Community Edition) verwenden.

Die Visual Studio-Projektmappe erstellt auch ein Installationsprogramm für die Anwendung. Um diese Funktion nutzen zu können, benötigen Sie das [WiX-Toolset](http://wixtoolset.org/) und die [WiX-Toolset-Erweiterung für Visual Studio](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension).

Sie benötigen auch einen Abonnementschlüssel für den Sprachübersetzungsdienst. Diesen können Sie über das Dashboard von Microsoft Azure abrufen. Im Rahmen des verfügbaren kostenlosen Tarifs können Sie pro Monat bis zu zehn Stunden gesprochene Sprache kostenlos übersetzen lassen. Dieser Tarif ist für das vorliegende Tutorial ausreichend.

Die [JSON.NET-Bibliothek](https://www.newtonsoft.com/json) (von Newtonsoft) ist ebenfalls erforderlich. Diese Assembly wird von NuGet automatisch installiert, wenn in den Visual Studio-Optionen beide Kontrollkästchen für die Paketwiederherstellung aktiviert sind.

## <a name="trying-the-translation-app"></a>Ausprobieren der Übersetzungs-App

Öffnen Sie in Visual Studio die Projektmappe für die Sprachübersetzung (`SpeechTranslator.sln`), und drücken Sie F5, um die Anwendung zu starten.  Das Hauptfenster der Anwendung wird angezeigt.

![[Hauptfenster der Sprachübersetzung]](media/speech-translator-main-window.png)

Wenn Sie die Anwendung zum ersten Mal ausführen, klicken Sie im Menü **Einstellungen** auf **Kontoeinstellungen**, um das hier gezeigte Fenster zu öffnen.

![[Hauptfenster der Sprachübersetzung]](media/speech-translator-settings-window.png)

Fügen Sie in diesem Fenster Ihren Abonnementschlüssel für die Sprachübersetzung ein, und klicken Sie dann auf **Speichern**. Der Schlüssel wird zwischen den Ausführungen gespeichert.

Wählen Sie im Hauptfenster die gewünschten Geräte für die Audioeingabe und die Audioausgabe sowie die Ausgangs- und die Zielsprache aus. Wenn die Übersetzung als Audio ausgegeben werden soll, vergewissern Sie sich, dass das Kontrollkästchen **TTS** (Text-To-Speech, Sprachsynthese) aktiviert ist. Falls bereits während des Sprechens spekulative Teilübersetzungen angezeigt werden sollen, aktivieren Sie die Option **Partial Results** (Teilergebnisse).

Klicken Sie abschließend auf **Start** (Starten), um mit der Übersetzung zu beginnen. Sagen Sie etwas, das übersetzt werden soll, und sehen Sie dabei zu, wie der erkannte Text und die entsprechende Übersetzung im Fenster angezeigt werden. Wenn Sie die TTS-Option aktiviert haben, wird die Übersetzung auch als Audio ausgegeben.

## <a name="obtaining-supported-languages"></a>Abrufen der unterstützten Sprachen

Zum Zeitpunkt der Artikelerstellung unterstützt der Sprachübersetzungsdienst mehr als fünf Dutzend Sprachen für die Textübersetzung. Die Sprachübersetzung wird für eine geringere Anzahl von Sprachen unterstützt. Für solche Sprachen müssen sowohl die Transkription (Spracherkennung) als auch die Sprachsynthese (für die Sprachausgabe) unterstützt werden.

Mit anderen Worten: Für die Sprachübersetzung muss eine Ausgangssprache mit Transkriptionsunterstützung verwendet werden. Bei der Ausgabesprache kann es sich um eine beliebige Sprache handeln, für die die Textübersetzung unterstützt wird (vorausgesetzt, Sie möchten das Ergebnis in Textform erhalten). Wenn Sie die Sprachausgabe nutzen möchten, muss für die Zielsprache der Übersetzung die Sprachsynthese unterstützt werden.

Microsoft fügt ggf. nach und nach Unterstützung für neue Sprachen hinzu. Aus diesem Grund sollten Sie die unterstützten Sprachen in Ihrer Anwendung nicht hartcodieren. Stattdessen stellt die Sprachübersetzungs-API einen Sprachenendpunkt (Languages) bereit, von dem Sie die unterstützten Sprachen zur Laufzeit abrufen können. Sie können eine oder mehrere Listen von Sprachen erhalten:

| | |
|-|-|
|`speech`|Die Sprachen, die für die Sprachtranskription unterstützt werden. Diese Sprachen können als Ausgangssprache für die Sprachübersetzung verwendet werden.|
|`text`|Die Sprachen, die für reine Textübersetzungen unterstützt werden. Diese Sprachen können bei Verwendung der Textausgabe als Zielsprache für die Sprachübersetzung verwendet werden.|
|`tts`|Die für die Sprachsynthese unterstützten Stimmen, die jeweils einer bestimmten Sprache zugeordnet werden. Diese können bei Verwendung der Sprachsynthese als Zielsprache für die Sprachübersetzung verwendet werden. Bestimmte Sprachen werden möglicherweise von mehreren Stimmen unterstützt.|

Für den Endpunkt „Languages“ ist kein Abonnementschlüssel erforderlich, und er kann unabhängig von Ihrem Kontingent verwendet werden. Er hat den URI `https://dev.microsofttranslator.com/languages` und gibt Ergebnisse im JSON-Format zurück.

Die hier gezeigte Methode `UpdateLanguageSettingsAsync()` in `MainWindow.xaml.cs` ruft den Endpunkt „Languages“ auf, um die Liste mit den unterstützten Sprachen abzurufen.

```csharp
private async Task UpdateLanguageSettingsAsync()
{
    Uri baseUri = new Uri("https://" + baseUrl);
    string fullUriString = "/Languages?api-version=1.0&scope=text,speech,tts";
    if (MenuItem_Experimental.IsChecked) fullUriString += "&flight=experimental";            
    Uri fullUri = new Uri(baseUri, fullUriString);

    using (HttpClient client = new HttpClient()) //'client' is the var - using statement ensures the dispose method is used even after an exception.
    {
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, fullUri);

        // get language names for current UI culture:
        request.Headers.Add("Accept-Language", CultureInfo.CurrentUICulture.TwoLetterISOLanguageName);

        // add a client-side trace Id. In case of issues, one can contact support and provide this:
        //string traceId = "SpeechTranslator" + Guid.NewGuid().ToString();
        //request.Headers.Add("X-ClientTraceId", traceId);
        //Debug.Print("TraceId: {0}", traceId);

        client.Timeout = TimeSpan.FromMilliseconds(10000);
        HttpResponseMessage response = await client.SendAsync(request); //make the async call to the web using the client var and passing the built up URI
        response.EnsureSuccessStatusCode(); //causes exception if the return is false

        Debug.Print("Request Id returned: {0}", GetRequestId(response));

        //create dictionaries to hold the language specific data
        spokenLanguages = new Dictionary<string, string>();
        fromLanguages = new Dictionary<string, string>();
        textLanguages = new Dictionary<string, string>();
        isLTR = new Dictionary<string, bool>();
        voices = new Dictionary<string, List<TTsDetail>>();

        JObject jResponse = JObject.Parse(await response.Content.ReadAsStringAsync()); //get the json from the async call with the response var created above, parse it and put it in a var called jResponse - JObject is a newton class

        //Gather the set of TTS voices
        foreach (JProperty jTts in jResponse["tts"])
        {
            JObject ttsDetails = (JObject)jTts.Value;

            string code = jTts.Name;
            string language = ttsDetails["language"].ToString();
            string displayName = ttsDetails["displayName"].ToString();
            string gender = ttsDetails["gender"].ToString();

            if (!voices.ContainsKey(language)) //check dictionary for a specific key value
            {
                voices.Add(language, new List<TTsDetail>()); //add to the dictionary the locale key and a ttsDetail object
            }

            voices[language].Add(new TTsDetail() { Code = code, DisplayName = string.Format("{0} ({1})", displayName, gender) });
        }

        // Gather the set of speech translation languages
        foreach (JProperty jSpeech in jResponse["speech"])
        {
            JObject languageDetails = (JObject)jSpeech.Value;
            string code = jSpeech.Name;
            string simplecode = languageDetails["language"].ToString();
            string displayName = languageDetails["name"].ToString();
            spokenLanguages.Add(code, displayName);
            fromLanguages.Add(code,simplecode);
        }

        spokenLanguages = spokenLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        FromLanguage.Items.Clear();
        foreach (var language in spokenLanguages)
        {
            FromLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key});
        }

        // Gather the set of text translation languages
        foreach (JProperty jText in jResponse["text"])
        {
            JObject languageDetails = (JObject)jText.Value;
            string code = jText.Name;
            string displayName = languageDetails["name"].ToString();
            textLanguages.Add(code, displayName);

            string direction = languageDetails["dir"].ToString().ToLowerInvariant();
            bool LTR = true;
            if (direction.ToLowerInvariant() == "rtl") LTR = false;
            isLTR.Add(code, LTR);
        }

        textLanguages = textLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        ToLanguage.Items.Clear();
        foreach (var language in textLanguages)
        {
            ToLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key });
        }

        if (Properties.Settings.Default.FromLanguageIndex >= 0) FromLanguage.SelectedIndex = Properties.Settings.Default.FromLanguageIndex;
        else
        {
            for(int i=0; i < FromLanguage.Items.Count; ++i)
            {
                ComboBoxItem item = (ComboBoxItem)FromLanguage.Items[i];
                if(CultureInfo.CurrentUICulture.Name.Equals((string)item.Tag, StringComparison.OrdinalIgnoreCase))
                {
                    FromLanguage.SelectedIndex = i;
                }
            }
        }
        if (Properties.Settings.Default.ToLanguageIndex >= 0) ToLanguage.SelectedIndex = Properties.Settings.Default.ToLanguageIndex;
        else
        {
            Random rnd = new Random(DateTime.Now.Millisecond);
            ToLanguage.SelectedIndex = (rnd.Next() % textLanguages.Count);
        }
    }
}
```

Diese Methode erstellt zunächst eine HTTP-Anforderung für den Endpunkt „Languages“, um alle drei Sprachenlisten (`text`, `speech` und `tts`) anzufordern.

Der Endpunkt „Languages“ bestimmt anhand des Headers `Accept-Languages` der Anforderung die Sprache, in der die Namen der Sprachen angezeigt werden. Die Sprache „Deutsch“ ist für Englisch sprechende Benutzer beispielsweise „German“ und heißt auf Spanisch „Alemán“. Diese Unterschiede werden in der Sprachenliste berücksichtigt. Für diesen Header wird die Standardsprache des Systems verwendet.

Nachdem die Anforderung gesendet und die JSON-Antwort empfangen wurde, wird die Antwort analysiert und in interne Datenstrukturen aufgeteilt. Auf der Grundlage dieser Strukturen werden dann die Menüs für die Ausgangs- und Zielsprache erstellt.

Da die verfügbaren Stimmen davon abhängen, welche Zielsprache der Benutzer auswählt, kann das Menü für die Stimme noch nicht eingerichtet werden. Die verfügbaren Stimmen für die einzelnen Sprachen werden daher zur späteren Verwendung gespeichert. Der Handler `ToLanguage_SelectionChanged` (in der gleichen Quelldatei) aktualisiert das Stimmenmenü später durch Aufrufen von `UpdateVoiceComboBox()`, wenn der Benutzer eine Zielsprache auswählt.

Wenn der Benutzer die Anwendung zum ersten Mal ausführt, wird eine zufällig ausgewählte Zielsprache festgelegt. (Die Menüeinstellungen werden sitzungsübergreifend gespeichert.)

## <a name="authenticating-requests"></a>Authentifizieren von Anforderungen

Für die Authentifizierung beim Microsoft-Sprachübersetzungsdienst müssen Sie Ihren Azure-Abonnementschlüssel im Header als Wert für `Ocp-Apim-Subscription-Key` (in der Verbindungsanforderung) senden.

## <a name="translation-overview"></a>Übersicht über die Übersetzung

Die Übersetzungs-API (WebSockets-Endpunkt: `wss://dev.microsofttranslator.com/speech/translate`) akzeptiert zu übersetzende Audiodaten im WAVE-Format (Mono, 16 kHz, 16 Bit mit Vorzeichen). Der Dienst gibt eine oder mehrere JSON-Antworten zurück, die sowohl den erkannten als auch den übersetzten Text enthalten. Wenn eine Sprachsynthese angefordert wurde, wird eine Audiodatei gesendet.

Der Benutzer wählt die Audioquelle über das Dropdownmenü „Microphone/File Input“ (Eingabe: Mikrofon/Datei) aus. Die Audiodaten können von einem Audiogerät (etwa von einem Mikrofon) oder aus einer `.WAV`-Datei stammen.

Die Methode `StartListening_Click` wird aufgerufen, wenn der Benutzer auf die Startschaltfläche klickt. Dieser Ereignishandler ruft wiederum `Connect()` auf, um mit der Audioübermittlung an den Dienst-API-Endpunkt zu beginnen. Die Methode `Connect()` führt die folgenden Aufgaben aus:


> [!div class="checklist"]
> * Abrufen und Überprüfen der Benutzereinstellungen aus dem Hauptfenster
> * Initialisieren der Eingabe- und Ausgabestreams für die Audiodaten
> * Aufrufen von `ConnectAsync()` zur Behandlung der restlichen Vorgänge

`ConnectAsync()` ist wiederum für folgende Aufgaben zuständig:

> [!div class="checklist"]
> * Authentifizieren mit dem Azure-Abonnementschlüssel im Header `Ocp-Apim-Subscription-Key`
> * Erstellen einer `SpeechClient`-Instanz (in `SpeechClient.cs`) für die Kommunikation mit dem Dienst
> * Initialisieren von `TextMessageDecoder`- und `BinaryMessageDecoder`-Instanzen (siehe `SpeechResponseDecoder.cs`) zum Behandeln der Antworten
> * Senden der Audiodaten über die `SpeechClient`-Instanz an den Sprachübersetzungsdienst
> * Empfangen und Verarbeiten der Übersetzungsergebnisse

`SpeechClient` hat etwas weniger zu tun:

> [!div class="checklist"]
> * Herstellen einer WebSocket-Verbindung mit dem Sprachübersetzungsdienst
> * Senden von Audiodaten und Empfangen von Antworten über den Socket

## <a name="a-closer-look"></a>Ausführlichere Betrachtung

Damit sollte nun klarer sein, wie Komponenten der Anwendung zusammenarbeiten, um die Übersetzungsanforderung abzuwickeln. In diesem Abschnitt gehen wir anhand von Code näher auf die relevanten Komponenten ein.

Im Anschluss finden Sie eine partielle Version von `Connect()`, die die Einrichtung der Audiodatenströme veranschaulicht:

```csharp
private void Connect()
{
    if (this.currentState != UiState.ReadyToConnect) return;

    Stopwatch watch = Stopwatch.StartNew();
    UpdateUiState(UiState.Connecting);

    // Omitted: code to validate UI settings

    string tag = ((ComboBoxItem)Mic.SelectedItem).Tag as string;
    string audioFileInputPath = null;
    if (tag == "File")
    {
        audioFileInputPath = this.AudioFileInput.Text;
        foreach (string currFile in audioFileInputPath.Split('|'))
        {
            if (!File.Exists(currFile))
            {
                SetMessage(String.Format($"Invalid audio source: selected file {currFile} does not exist."), "", MessageKind.Error);
                UpdateUiState(UiState.ReadyToConnect);
                return;
            }
        }
    }
    bool shouldSuspendInputAudioDuringTTS = this.CutInputAudioCheckBox.IsChecked.HasValue ? this.CutInputAudioCheckBox.IsChecked.Value : false;

    correlationId = Guid.NewGuid().ToString("D").Split('-')[0].ToUpperInvariant();

    // Setup speech translation client options
    SpeechClientOptions options;

    string voicename = "";
    if (this.Voice.SelectedItem != null)
    {
        voicename = ((ComboBoxItem)this.Voice.SelectedItem).Tag.ToString();
    }
    options = new SpeechTranslateClientOptions()
    {
        TranslateFrom = ((ComboBoxItem)this.FromLanguage.SelectedItem).Tag.ToString(),
        TranslateTo = ((ComboBoxItem)this.ToLanguage.SelectedItem).Tag.ToString(),
        Voice = voicename,
    };

    options.Hostname = baseUrl;
    options.AuthHeaderKey = "Authorization";
    options.AuthHeaderValue = ""; // set later in ConnectAsync.
    options.ClientAppId = new Guid("EA66703D-90A8-436B-9BD6-7A2707A2AD99");
    options.CorrelationId = this.correlationId;
    options.Features = GetFeatures().ToString().Replace(" ", "");
    options.Profanity = ((SpeechClient.ProfanityFilter)Enum.Parse(typeof(SpeechClient.ProfanityFilter), GetProfanityLevel(), true)).ToString();
    options.Experimental = MenuItem_Experimental.IsChecked;

    // Setup player and recorder but don't start them yet.
    WaveFormat waveFormat = new WaveFormat(16000, 16, 1);

    // WaveProvider for incoming TTS
    // We use a rather large BufferDuration because we need to be able to hold an entire utterance.
    // TTS audio is received in bursts (faster than real-time).
    textToSpeechBytes = 0;
    playerTextToSpeechWaveProvider = new BufferedWaveProvider(waveFormat);
    playerTextToSpeechWaveProvider.BufferDuration = TimeSpan.FromMinutes(5);

    ISampleProvider sampleProvider = null;
    if (audioFileInputPath != null)
    {
        // Setup mixing of audio from input file and from TTS
        playerAudioInputWaveProvider = new BufferedWaveProvider(waveFormat);
        var srce1 = new Pcm16BitToSampleProvider(playerTextToSpeechWaveProvider);
        var srce2 = new Pcm16BitToSampleProvider(playerAudioInputWaveProvider);
        var mixer = new MixingSampleProvider(srce1.WaveFormat);
        mixer.AddMixerInput(srce1);
        mixer.AddMixerInput(srce2);
        sampleProvider = mixer;
    }
    else
    {
        recorder = new WaveIn();
        recorder.DeviceNumber = (int)((ComboBoxItem)Mic.SelectedItem).Tag;
        recorder.WaveFormat = waveFormat;
        recorder.DataAvailable += OnRecorderDataAvailable;
        sampleProvider = playerTextToSpeechWaveProvider.ToSampleProvider();
    }

    if (!batchMode)
    {
        player = new WaveOut();
        player.DeviceNumber = (int)((ComboBoxItem)Speaker.SelectedItem).Tag;
        player.Init(sampleProvider);
    }

    this.audioBytesSent = 0;

    string logAudioFileName = null;
    if (LogSentAudio.IsChecked|| LogReceivedAudio.IsChecked)
    {
        string logAudioPath = System.IO.Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.ApplicationData), Properties.Settings.Default.OutputDirectory);
        try
        {
            Directory.CreateDirectory(logAudioPath);
        }
        catch
        {
            this.AddItemToLog(string.Format("Could not create folder {0}", logAudioPath));
        }

        if (LogSentAudio.IsChecked)
        {
            logAudioFileName = System.IO.Path.Combine(logAudioPath, string.Format("audiosent_{0}.wav", this.correlationId));
        }

        if (LogReceivedAudio.IsChecked)
        {
            string fmt = System.IO.Path.Combine(logAudioPath, string.Format("audiotts_{0}_{{0}}.wav", this.correlationId));
            this.audioReceived = new BinaryMessageDecoder(fmt);
        }
    }
}
```

Ein erheblicher Teil von `Connect()` dreht sich um die Erstellung einer `SpeechClientOptions`-Instanz (siehe `SpeechClientOptions.cs`), um die Optionen für die Übersetzung zu speichern. Die Optionen umfassen die benötigten Informationen für die Verbindungsherstellung mit dem Dienst (etwa den Authentifizierungsschlüssel und den Hostnamen) sowie die Features, die für die Übersetzung verwendet werden. Die Felder hier sind den Headerfeldern und HTTP-Parametern zugeordnet, die von der [Sprachübersetzungs-API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference) verfügbar gemacht werden.

`Connect()` erstellt und initialisiert auch das Audioeingabegerät (Variable `sampleProvider`), das als Quelle für das Gesprochene dient, das übersetzt werden soll. Bei diesem Gerät handelt es sich entweder um ein Hardwareeingabegerät (etwa ein Mikrofon) oder um eine Datei, die WAVE-Audiodaten enthält.

Hier sehen Sie die Methode `ConnectAsync()`, die die Klasse `speechClient` instanziiert und anonyme Funktionen zum Behandeln von textbasierten und binären Antworten des Diensts einbindet.

```csharp
private async Task ConnectAsync(SpeechClientOptions options, bool suspendInputAudioDuringTTS)
{
    await ADMAuthenticate(options);

    TextMessageDecoder textDecoder;

    s2smtClient = new SpeechClient((SpeechTranslateClientOptions)options, CancellationToken.None);

    s2smtClient.OnBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnEndOfBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnTextData += (c, a) => { textDecoder.AppendData(a); lastReceivedPacketTick = DateTime.Now.Ticks; };
    s2smtClient.OnEndOfTextData += (c, a) =>
    {
        textDecoder.AppendData(a);
        lastReceivedPacketTick = DateTime.Now.Ticks;
        textDecoder
            .Decode()
            .ContinueWith(t =>
            {
                if (t.IsFaulted)
                {
                    Log(t.Exception, "E: Failed to decode incoming text message.");
                }
                else
                {
                    object msg = t.Result;
                    if (msg.GetType() == typeof(FinalResultMessage))
                    {
                        // omitted: code to process final binary result
                    }
                    if (msg.GetType() == typeof(PartialResultMessage))
                    {
                        // omitted: code to process partial binary result
                    }
                }
            });
    };
    s2smtClient.Failed += (c, ex) =>
    {
        Log(ex, "E: SpeechTranslation client reported an error.");
    };
    s2smtClient.Disconnected += (c, ea) =>
    {
        SafeInvoke(() =>
        {
            // We only care to react to server disconnect when our state is Connected.
            if (currentState == UiState.Connected)
            {
                Log("E: Connection has been lost.");
                Log($"E: Errors (if any): \n{string.Join("\n", s2smtClient.Errors)}");
                Disconnect();
            }
        });
    };
    await s2smtClient.Connect();
}
```

Nach der Authentifizierung erstellt die Methode die `SpeechClient`-Instanz. Wenn Binär- und Textantworten eingehen, ruft die Klasse `SpeechClient` (in `SpeechClient.cs`) Ereignishandler auf. Weitere Handler werden aufgerufen, wenn ein Verbindungsfehler auftritt oder die Verbindung getrennt wird.

Bei den Binärdaten handelt es sich um Audiodaten (Ausgabe der Sprachsynthese), die vom Dienst gesendet werden, wenn TTS aktiviert ist. Bei den Textdaten handelt es sich um eine partielle oder vollständige Übersetzung des gesprochenen Texts. Nach der Instanziierung bindet die Methode also Funktionen zur Behandlung dieser Nachrichten ein: Audio (durch Speicherung zur späteren Wiedergabe) und Text (durch Anzeige im Fenster).

## <a name="next-steps"></a>Nächste Schritte

Dieses Codebeispiel ist eine Anwendung mit umfangreichen Features zur Veranschaulichung der Verwendung der Sprachübersetzungs-API. Es ist daher etwas komplex. Sie sind nun mit den wichtigsten Aspekten vertraut. Für den Rest empfiehlt es sich ggf., verschiedene Breakpoints in Visual Studio festzulegen und den Übersetzungsprozess Schritt für Schritt nachzuvollziehen. Wenn Sie die Beispielanwendung verstanden haben, können Sie den Sprachübersetzungsdienst in Ihren eigenen Anwendungen nutzen.

> [!div class="nextstepaction"]
> [Referenz für die Sprachübersetzungs-API von Microsoft](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)

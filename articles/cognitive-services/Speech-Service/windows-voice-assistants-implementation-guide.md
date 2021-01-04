---
title: 'Sprachassistenten unter Windows: Richtlinien zur Implementierung der Anzeige oberhalb des Sperrbildschirms'
titleSuffix: Azure Cognitive Services
description: Enthält eine Anleitung zum Implementieren der Sprachaktivierung und der Funktionen für die Anzeige oberhalb des Sperrbildschirms für eine Sprach-Agent-Anwendung.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.custom: devx-track-csharp
ms.openlocfilehash: 0503e0bf2fe152296ca6890e14503d05bd3bbeef
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024771"
---
# <a name="implementing-voice-assistants-on-windows"></a>Implementieren von Sprachassistenten unter Windows

In dieser Anleitung werden wichtige Implementierungsdetails für die Erstellung eines Sprachassistenten unter Windows beschrieben.

## <a name="implementing-voice-activation"></a>Implementieren der Sprachaktivierung

Nachdem Sie die [Umgebung eingerichtet](how-to-windows-voice-assistants-get-started.md) und sich mit der [Funktionsweise der Sprachaktivierung](windows-voice-assistants-overview.md#how-does-voice-activation-work) vertraut gemacht haben, können Sie damit beginnen, die Sprachaktivierung für Ihren eigenen Sprachassistenten zu implementieren.

### <a name="registration"></a>Registrierung

#### <a name="ensure-that-the-microphone-is-available-and-accessible-then-monitor-its-state"></a>Sicherstellen der Verfügbarkeit und Zugänglichkeit des Mikrofons und Überwachen des Status

Bei MVA muss ein Mikrofon vorhanden und zugänglich sein, damit eine Sprachaktivierung erkannt werden kann. Verwenden Sie die Klassen [AppCapability](/uwp/api/windows.security.authorization.appcapabilityaccess.appcapability?view=winrt-18362), [DeviceWatcher](/uwp/api/windows.devices.enumeration.devicewatcher?view=winrt-18362) und [MediaCapture](/uwp/api/windows.media.capture.mediacapture?view=winrt-18362), um den Datenschutz in Bezug auf den Zugriff auf das Mikrofon, das Vorhandensein des Geräts und den Gerätestatus (z. B. Lautstärke und Stummschaltung) zu überprüfen.

### <a name="register-the-application-with-the-background-service"></a>Registrieren der Anwendung beim Hintergrunddienst

Damit MVA die Anwendung im Hintergrund starten kann, muss sie beim Hintergrunddienst registriert sein. Einen vollständigen Leitfaden zur Registrierung beim Hintergrunddienst finden Sie [hier](/windows/uwp/launch-resume/register-a-background-task).

### <a name="unlock-the-limited-access-feature"></a>Entsperren des Features für eingeschränkten Zugriff

Verwenden Sie den von Microsoft bereitgestellten Schlüssel für das Feature für eingeschränkten Zugriff, um die Funktion für den Sprachassistenten zu entsperren. Nutzen Sie hierfür die [LimitedAccessFeature](/uwp/api/windows.applicationmodel.limitedaccessfeatures?view=winrt-18362)-Klasse aus dem Windows SDK.

### <a name="register-the-keyword-for-the-application"></a>Registrieren des Schlüsselworts für die Anwendung

Die Anwendung muss sich selbst, das Schlüsselwortmodell und die zugehörige Sprache bei Windows registrieren.

Rufen Sie zunächst das Erkennungsmodul für Schlüsselwörter ab. In diesem Beispielcode rufen wir das erste Erkennungsmodul ab, aber Sie können über `configurableDetectors` auch ein bestimmtes Erkennungsmodul auswählen.

```csharp
private static async Task<ActivationSignalDetector> GetFirstEligibleDetectorAsync()
{
    var detectorManager = ConversationalAgentDetectorManager.Default;
    var allDetectors = await detectorManager.GetAllActivationSignalDetectorsAsync();
    var configurableDetectors = allDetectors.Where(candidate => candidate.CanCreateConfigurations
        && candidate.Kind == ActivationSignalDetectorKind.AudioPattern
        && (candidate.SupportedModelDataTypes.Contains("MICROSOFT_KWSGRAPH_V1")));

    if (configurableDetectors.Count() != 1)
    {
        throw new NotSupportedException($"System expects one eligible configurable keyword spotter; actual is {configurableDetectors.Count()}.");
    }

    var detector = configurableDetectors.First();

    return detector;
}
```

Rufen Sie nach dem Abrufen des ActivationSignalDetector-Objekts die zugehörige `ActivationSignalDetector.CreateConfigurationAsync`-Methode mit Signal-ID, Modell-ID und Anzeigename ab, um Ihr Schlüsselwort zu registrieren und das `ActivationSignalDetectionConfiguration`-Element Ihrer Anwendung abzurufen. Bei der Signal- und der Modell-ID sollte es sich um GUIDs handeln, die vom Entwickler festgelegt und für ein Schlüsselwort dann nicht mehr geändert werden.

### <a name="verify-that-the-voice-activation-setting-is-enabled"></a>Überprüfen, ob die Einstellung für die Sprachaktivierung aktiviert ist

Für die Verwendung der Sprachaktivierung muss ein Benutzer sie für sein System und seine Anwendung aktivieren. Sie finden die entsprechende Einstellung in den Windows-Einstellungen unter den „Datenschutzeinstellungen für die Sprachaktivierung“. Verwenden Sie die Instanz von `ActivationSignalDetectionConfiguration`, die Sie beim Registrieren des Schlüsselworts genutzt haben, um den Status der Einstellung für die Sprachaktivierung in Ihrer Anwendung zu überprüfen. Das Feld [AvailabilityInfo](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-uwp/UWPVoiceAssistantSample/UIAudioStatus.cs#L128) unter `ActivationSignalDetectionConfiguration` enthält einen Enumerationswert, der den Status der Einstellung für die Sprachaktivierung beschreibt.

### <a name="retrieve-a-conversationalagentsession-to-register-the-app-with-the-mva-system"></a>Abrufen von „ConversationalAgentSession“ zum Registrieren der App beim MVA-System

`ConversationalAgentSession` ist eine Klasse im Windows SDK, mit der Ihre App den App-Status (Leerlauf, Erkennen, Lauschen, Arbeiten, Sprechen) für Windows aktualisieren und Ereignisse empfangen kann, z. B. Aktivierungserkennung und Systemstatusänderungen wie das Sperren des Bildschirms. Das Abrufen einer Instanz von „AgentSession“ dient auch zum Registrieren der Anwendung bei Windows als „aktivierbar per Sprache“. Die bewährte Methode besteht darin, einen Verweis auf `ConversationalAgentSession` zu verwenden. Verwenden Sie die `ConversationalAgentSession.GetCurrentSessionAsync`-API, um die Sitzung abzurufen.

### <a name="listen-to-the-two-activation-signals-the-onbackgroundactivated-and-onsignaldetected"></a>Lauschen auf die beiden Aktivierungssignale: „OnBackgroundActivated“ und „OnSignalDetected“

Windows signalisiert Ihrer App, wenn sie auf eine der beiden Arten ein Schlüsselwort erkennt. Falls die App nicht aktiv ist (kein Verweis auf eine nicht geschlossene Instanz von `ConversationalAgentSession` vorhanden), wird Ihre App gestartet und die „OnBackgroundActivated“-Methode in der Datei „App.xaml.cs“ Ihrer Anwendung aufgerufen. Wenn das Feld `BackgroundActivatedEventArgs.TaskInstance.Task.Name` der Ereignisargumente mit der Zeichenfolge „AgentBackgroundTrigger“ übereinstimmt, wurde der Start der Anwendung per Sprachaktivierung ausgelöst. Die Anwendung muss diese Methode außer Kraft setzen und eine Instanz von „ConversationalAgentSession“ abrufen, um für Windows zu signalisieren, dass sie jetzt aktiv ist. Wenn die Anwendung aktiv ist, wird von Windows das Auftreten einer Sprachaktivierung signalisiert, indem das Ereignis `ConversationalAgentSession.OnSignalDetected` verwendet wird. Fügen Sie diesem Ereignis einen Ereignishandler hinzu, sobald Sie `ConversationalAgentSession` abgerufen haben.

## <a name="keyword-verification"></a>Schlüsselwortüberprüfung

Nachdem eine Sprach-Agent-Anwendung per Sprache aktiviert wurde, wird im nächsten Schritt überprüft, ob die Schlüsselworterkennung gültig war. Windows verfügt nicht über eine Lösung zur Überprüfung von Schlüsselwörtern, aber Sprachassistenten können auf die Audiodaten über die hypothetische Aktivierung zugreifen und die Überprüfung selbst durchführen.

### <a name="retrieve-activation-audio"></a>Abrufen der Audiodaten für die Aktivierung

Erstellen Sie ein [AudioGraph](/uwp/api/windows.media.audio.audiograph)-Element, und übergeben Sie es an das `CreateAudioDeviceInputNodeAsync`-Element von `ConversationalAgentSession`. Der Audiopuffer des Graphen wird geladen, und die Audiodaten *beginnen ca. drei Sekunden vor der Erkennung des Schlüsselworts*. Dieser Vorabzeitraum wird für die Audiodaten verwendet, um unterschiedliche Schlüsselwortlängen und Sprechgeschwindigkeiten abzudecken. Verarbeiten Sie anschließend das [QuantumStarted](/uwp/api/windows.media.audio.audiograph.quantumstarted?view=winrt-18362)-Ereignis des Audiographen, um die Audiodaten abzurufen.

```csharp
var inputNode = await agentSession.CreateAudioDeviceInputNodeAsync(audioGraph);
var outputNode = inputGraph.CreateFrameOutputNode();
inputNode.AddOutgoingConnection(outputNode);
audioGraph.QuantumStarted += OnQuantumStarted;
```

Hinweis: Dieser Vorabzeitraum mit Audiodaten im Audiopuffer kann dazu führen, dass für die Schlüsselwortüberprüfung ein Fehler auftritt. Sie können dieses Problem beheben, indem Sie den Anfang der Daten im Audiopuffer abschneiden, bevor Sie die Audiodaten an die Schlüsselwortüberprüfung absenden. Dieses Abschneiden der Anfangsdaten sollte auf jeden Assistenten speziell zugeschnitten werden.

### <a name="launch-in-the-foreground"></a>Starten im Vordergrund

Wenn die Überprüfung des Schlüsselworts erfolgreich ist, muss die Anwendung in den Vordergrund verschoben werden, damit die Benutzeroberfläche sichtbar ist. Rufen Sie die `ConversationalAgentSession.RequestForegroundActivationAsync`-API auf, um Ihre Anwendung in den Vordergrund zu verschieben.

### <a name="transition-from-compact-view-to-full-view"></a>Übergang von der Kompakt- zur Vollansicht

Wenn Ihre Anwendung erstmalig per Sprache aktiviert wird, wird sie in der Kompaktansicht gestartet. Informationen zu den verschiedenen Ansichten und den entsprechenden Übergängen für Sprachassistenten unter Windows finden Sie unter [Entwurfsleitfaden für Sprachaktivierung (Vorschau)](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview).

Verwenden Sie die ApplicationView-API `TryEnterViewModeAsync`, um den Übergang von der Kompaktansicht zur vollständigen App-Ansicht durchzuführen:

```csharp
var appView = ApplicationView.GetForCurrentView();
await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);
```

## <a name="implementing-above-lock-activation"></a>Implementieren der Aktivierung der Anzeige oberhalb des Sperrbildschirms

Mit den folgenden Schritten wird für einen Sprachassistenten unter Windows die Anzeige oberhalb des Sperrbildschirms aktiviert. Sie enthalten zudem Verweise auf Beispielcode und Richtlinien zur Verwaltung des Anwendungslebenszyklus.

Informationen zum Entwerfen der Anzeige oberhalb des Sperrbildschirms finden Sie im [Leitfaden zu den bewährten Methoden](windows-voice-assistants-best-practices.md).

Wenn eine App eine Ansicht oberhalb des Sperrbildschirms anzeigt, wird dies als „Kioskmodus“ bezeichnet. Weitere Informationen zur Implementierung einer App mit Kioskmodus finden Sie in der [Dokumentation zum Kioskmodus](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access).

### <a name="transitioning-above-lock"></a>Übergang zur Anzeige oberhalb des Sperrbildschirms

Eine Aktivierung oberhalb des Sperrbildschirms ähnelt einer Aktivierung unterhalb des Sperrbildschirms. Wenn keine aktiven Instanzen der Anwendung vorhanden sind, wird im Hintergrund eine neue Instanz gestartet, und `OnBackgroundActivated` in „App.xaml.cs“ wird aufgerufen. Falls eine Instanz der Anwendung vorhanden ist, erhält diese Instanz über das Ereignis `ConversationalAgentSession.SignalDetected` eine Benachrichtigung.

Wenn die Anwendung nicht bereits oberhalb des Sperrbildschirms angezeigt wird, muss sie `ConversationalAgentSession.RequestForegroundActivationAsync` aufrufen. Hierdurch wird die `OnLaunched`-Methode in „App.xaml.cs“ ausgelöst, und es sollte ein Navigationsvorgang zu der Ansicht erfolgen, die oberhalb des Sperrbildschirms angezeigt werden soll.

### <a name="detecting-lock-screen-transitions"></a>Erkennen von Sperrbildschirm-Übergängen

Die Bibliothek „ConversationalAgent“ im Windows SDK verfügt über eine API, über die leicht auf den Status des Sperrbildschirms und die zugehörigen Änderungen zugegriffen werden kann. Verwenden Sie das Feld `ConversationalAgentSession.IsUserAuthenticated`, um den aktuellen Status des Sperrbildschirms zu ermitteln. Fügen Sie zum Erkennen von Änderungen im gesperrten Zustand dem `SystemStateChanged`-Ereignis des `ConversationalAgentSession`-Objekts einen Ereignishandler hinzu. Es wird jeweils ausgelöst, wenn der Bildschirm aus dem nicht gesperrten in den gesperrten Zustand wechselt (oder umgekehrt). Wenn der Wert der Ereignisargumente `ConversationalAgentSystemStateChangeType.UserAuthentication` lautet, hat sich der Status des Bildschirmstatus geändert.

```csharp
conversationalAgentSession.SystemStateChanged += (s, e) =>
{
    if (e.SystemStateChangeType == ConversationalAgentSystemStateChangeType.UserAuthentication)
    {
        // Handle lock state change
    }
};
```

### <a name="detecting-above-lock-activation-user-preference"></a>Erkennen der vom Benutzer bevorzugten Einstellung für die Aktivierung der Anzeige oberhalb des Sperrbildschirms

Der Anwendungseintrag auf der Seite mit den Datenschutzeinstellungen für die Sprachaktivierung enthält einen Umschalter für die Funktion zum Anzeigen oberhalb des Sperrbildschirms. Damit Ihre App oberhalb des Sperrbildschirms gestartet werden kann, muss der Benutzer diese Einstellung aktivieren. Der Status der Berechtigungen für die Anzeige oberhalb des Sperrbildschirms wird ebenfalls im „ActivationSignalDetectionConfiguration“-Objekt gespeichert. Der Status von „AvailabilityInfo.HasLockScreenPermission“ spiegelt wider, ob der Benutzer seine Einwilligung für die Anzeige oberhalb des Sperrbildschirms erteilt hat. Wenn diese Einstellung deaktiviert ist, kann eine Sprachanwendung den Benutzer auffordern, unter dem Link „ms-settings:privacy-voiceactivation“ zur entsprechenden Seite mit den Einstellungen zu navigieren. Die Seite enthält eine Anleitung zur Aktivierung der Anzeige oberhalb des Sperrbildschirms für die Anwendung.

## <a name="closing-the-application"></a>Schließen der Anwendung

Verwenden Sie die `WindowService.CloseWindow()`-API, um die Anwendung richtig programmgesteuert zu schließen, während sie oberhalb oder unterhalb des Sperrbildschirms angezeigt wird. Hierdurch werden alle UWP-Lebenszyklusmethoden ausgelöst, z. B. OnSuspend, damit die Anwendung das `ConversationalAgentSession`-Objekt vor dem Schließen verwerfen kann.

> [!NOTE]
> Die Anwendung kann geschlossen werden, ohne dass die [unterhalb des Sperrbildschirms angezeigte Instanz](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access#add-a-way-out-of-assigned-access-) geschlossen wird. In diesem Fall muss die Anzeige oberhalb des Sperrbildschirms „bereinigt“ werden. Hierbei wird sichergestellt, dass nach dem Entsperren des Bildschirms keine Ereignishandler oder Aufgaben vorhanden sind, die versuchen, die oberhalb des Sperrbildschirms angezeigte Ansicht zu manipulieren.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [UWP Voice Assistant-Beispiel-App mit Beispielen und Codeübersichten](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)
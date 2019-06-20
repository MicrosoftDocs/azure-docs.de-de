---
title: Versionshinweise – Speech Services
titlesuffix: Azure Cognitive Services
description: Hier finden Sie eine fortlaufende Liste von veröffentlichten Features, Verbesserungen, Fehlerbehebungen und bekannten Problemen bei Azure Speech Services.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: f22b0fcac6099482addfcf56a20e0e828866326e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65606348"
---
# <a name="release-notes"></a>Versionshinweise

## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

Dies ist ein Fehlerbehebungsrelease und betrifft nur das native/verwaltete SDK. Es betrifft nicht die JavaScript-Version des SDK.

**Fehlerbehebungen**

* Fehlerbehebung bei FromSubscription bei Verwendung mit Unterhaltungstranskription.
* Fehlerbehebung bei der Schlüsselwortsuche für den virtuellen Voice-First-Assistenten.


## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0: Release von Mai 2019

**Neue Features**

* Die Funktionalität für das Aktivierungswort (Keyword Spotting/KWS) ist jetzt für Windows und Linux verfügbar. Die KWS-Funktionalität kann u. U. mit jedem Mikrofontyp verwendet werden, offiziell wird KWS derzeit jedoch nur für die Mikrofonarrays in der Azure Kinect DK-Hardware oder im Speech Devices SDK unterstützt.
* Begriffshinweisfunktionalität ist über das SDK verfügbar. Weitere Informationen finden Sie [hier](how-to-phrase-lists.md).
* Unterhaltungstranskriptionsfunktionalität ist über das SDK verfügbar. Klicken Sie [hier](conversation-transcription-service.md).
* Unterstützung für virtuelle Voice-First-Assistenten über den Direct Line Speech-Kanal wurde hinzugefügt.

**Beispiele**

* Beispiele für neue Funktionen oder neue Dienste, die vom SDK unterstützt werden, wurden hinzugefügt.

**Verbesserungen/Änderungen**

* Verschiedene Erkennungseigenschaften wurden hinzugefügt, um das Dienstverhalten oder Dienstergebnisse anzupassen (z. B. Maskieren von Obszönitäten).
* Sie können die Erkennung jetzt über die Standardkonfigurationseigenschaften konfigurieren, auch wenn Sie den Erkenner `FromEndpoint` erstellt haben.
* Objective-C: „SPXSpeechConfiguration“ wurde durch die `OutputFormat`-Eigenschaft ergänzt.
* Das SDK unterstützt jetzt Debian 9 als Linux-Distribution.

**Fehlerbehebungen**

* Es wurde ein Problem behoben, bei dem die Sprecherressource in der Sprachsynthese zu früh zerstört wurde.
## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

Dies ist ein Fehlerbehebungsrelease und betrifft nur das native/verwaltete SDK. Es betrifft nicht die JavaScript-Version des SDK.

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

Dieses Release gilt nur für JavaScript. Es wurden keine Features hinzugefügt. Die folgenden Fehler wurden behoben:

* Verhindern Sie das Laden von „https-proxy-agent“ durch Webpack.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0: Release von April 2019

**Neue Features** 

* Das SDK unterstützt jetzt den Text-Sprach-Dienst als Betaversion. Dies wird unter Windows- und Linux-Desktops für C++ und C# unterstützt. Weitere Informationen finden Sie in der [Übersicht über die Sprachsynthese](text-to-speech.md#get-started-with-text-to-speech).
* Das SDK unterstützt jetzt MP3- und Opus/OGG-Audiodateien als Streameingabedateien. Dieses Feature steht nur unter Linux mit C++ und C# zur Verfügung und befindet sich derzeit in der Betaversion (weitere Details finden Sie [hier](how-to-use-codec-compressed-audio-input-streams.md)).
* Das Speech SDK für Java, .NET Core, C++ und Objective-C unterstützt nun auch macOS. Die Objective-C-Unterstützung für macOS befindet sich derzeit in der Betaphase.
* iOS: Das Speech SDK für iOS (Objective-C) wird jetzt auch als ein CocoaPod veröffentlicht.
* JavaScript: Unterstützung von nicht standardisierten Mikrofonen als Eingabegeräte.
* JavaScript: Proxyunterstützung für Node.js.

**Beispiele**

* Beispiele für die Verwendung des Speech SDK mit C++ und Objective-C unter macOS wurden hinzugefügt.
* Beispiele zur Veranschaulichung der Verwendung des Text-zu-Sprache-Diensts wurden hinzugefügt.

**Verbesserungen/Änderungen**

* Python: Zusätzliche Eigenschaften der Erkennungsergebnisse werden jetzt über die `properties`-Eigenschaft verfügbar gemacht.
* Zur weiteren Unterstützung beim Entwickeln und Debuggen können Sie die Informationen aus SDK-Protokollierung und Diagnose in eine Protokolldatei umleiten (weitere Details finden Sie [hier](how-to-use-logging.md)).
* JavaScript: Verbesserte Prozessleistung bei Audiodaten.

**Fehlerbehebungen**

* Mac/iOS: Ein Fehler, der zu einer langen Wartezeit geführt hat, wenn keine Verbindung mit Speech Services hergestellt werden konnte, wurde behoben.
* Python: verbesserte Fehlerbehandlung für Argumente in Python-Rückrufen.
* JavaScript: Ein Fehler bei Statusmeldungen nach dem Ende der Spracheingabe mit RequestSession wurde behoben.

## <a name="speech-sdk-131-2019-february-refresh"></a>Sprach-SDK 1.3.1: Aktualisierung von Februar 2019

Dies ist ein Fehlerbehebungsrelease und betrifft nur das native/verwaltete SDK. Es betrifft nicht die JavaScript-Version des SDK.

**Fehlerbehebung**

* Korrigiert einen Speicherverlust bei der Verwendung von Mikrofoneingabe. Streambasierte oder Dateieingaben sind nicht betroffen.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0: Version von Februar 2019

**Neue Features**

* Das Speech SDK unterstützt die Auswahl des Eingangsmikrofons über die AudioConfig-Klasse. Dadurch können Sie Audiodaten über ein anderes als das Standardmikrofon an den Speech-Dienst streamen. Weitere Informationen finden Sie in der Dokumentation, in der die [Auswahl eines Audioeingabegeräts](how-to-select-audio-input-devices.md) beschrieben wird. Für JavaScript ist diese Funktion noch nicht verfügbar.
* Das Speech SDK unterstützt jetzt Unity in einer Betaversion. Senden Sie uns Feedback über den Abschnitt „Issue“ im [GitHub-Beispielrepository](https://aka.ms/csspeech/samples). Dieses Release unterstützt Unity unter Windows x86 und x64 (Desktopanwendungen oder Anwendungen der universellen Windows-Plattform) und unter Android (ARM32/64, x86). Weitere Informationen finden Sie in unserem [Unity-Schnellstart](quickstart-csharp-unity.md).
* Die Datei `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (im Lieferumfang von früheren Releases enthalten) ist nicht mehr erforderlich. Die Funktion ist jetzt in das Core-SDK integriert.


**Beispiele**

Die folgenden neuen Inhalte stehen in unserem [Beispielrepository](https://aka.ms/csspeech/samples) zur Verfügung:

* Weitere Beispiele für AudioConfig.FromMicrophoneInput.
* Weitere Python-Beispiele für Absichtserkennung und Übersetzung.
* Weitere Beispiele für die Verwendung des Verbindungsobjekts in iOS.
* Weitere Java-Beispiele für die Übersetzung mit Audioausgabe.
* Neues Beispiel für die Verwendung der [REST-API zur Batchtranskription](batch-transcription.md).

**Verbesserungen/Änderungen**

* Python
  * Verbesserte Parameterüberprüfung und Fehlermeldungen in SpeechConfig.
  * Hinzugefügte Unterstützung für das Verbindungsobjekt.
  * Unterstützung für 32-Bit-Python (x86) unter Windows.
  * Das Speech SDK für Python befindet sich nicht mehr in der Betaversion.
* iOS
  * Das SDK wird jetzt für das iOS SDK, Version 12.1, erstellt.
  * Das SDK unterstützt jetzt die iOS-Versionen 9.2 und höher.
  * Verbesserte Referenzdokumentation und Korrektur mehrerer Eigenschaftsnamen.
* JavaScript
  * Hinzugefügte Unterstützung für das Verbindungsobjekt.
  * Hinzugefügte Typdefinitionsdateien für JavaScript-Pakete
  * Anfangsunterstützung und Implementierung für Phrasenhinweise.
  * Rückgabe der Eigenschaftensammlung mit Dienst-JSON für die Erkennung.
* Windows-DLLs enthalten jetzt eine Versionsressource.
* Wenn Sie eine `FromEndpoint`-Erkennung erstellen, können Sie der Endpunkt-URL direkt Parameter hinzufügen. Mithilfe von `FromEndpoint` können Sie die Erkennung nicht über die Standardkonfigurationseigenschaften konfigurieren.

**Fehlerbehebungen**

* Leere Angaben für Proxybenutzername und Proxykennwort wurden nicht ordnungsgemäß behandelt. Wenn Sie in diesem Release den Proxybenutzernamen und das Proxykennwort auf eine leere Zeichenfolge festlegen, werden diese bei der Verbindungsherstellung mit dem Proxy nicht übermittelt.
* Vom SDK erstellte SessionId-Angaben wurden&nbsp;für einige Sprachen/Umgebungen nicht immer wirklich zufällig gewählt. Es wurde eine Initialisierung des Zufallsgenerators hinzugefügt, um dieses Problem zu beheben.
* Verbesserte Verarbeitung des Autorisierungstokens. Wenn Sie ein Autorisierungstoken verwenden möchten, geben Sie es in SpeechConfig an, und lassen Sie den Abonnementschlüssel leer. Erstellen Sie die Erkennung dann wie gewohnt.
* In einigen Fällen wurde das Verbindungsobjekt nicht ordnungsgemäß freigegeben. Dieses Problem wurde behoben.
* Das JavaScript-Beispiel wurde korrigiert, um die Audioausgabe für die Übersetzungssynthese auch in Safari zu unterstützen.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Dieses Release gilt nur für JavaScript. Es wurden keine Features hinzugefügt. Die folgenden Fehler wurden behoben:

* Ende des Datenstroms wird bei turn.end und nicht bei speech.end ausgelöst.
* In Audiopump wurde der Fehler behoben, dass der nächste Sendevorgang nicht geplant wurde, wenn beim aktuellen Sendevorgang ein Fehler auftrat.
* Die kontinuierliche Erkennung mit Authentifizierungstoken wurde korrigiert.
* Programmfehlerbehebung für verschiedene Erkennungen/Endpunkte.
* Verbesserungen bei der Dokumentation.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0: Release von Dezember 2018

**Neue Features**

* Python
  * Die Betaversion der Python-Unterstützung (ab 3.5) ist mit diesem Release verfügbar. Weitere Informationen finden Sie hier (quickstart-python.md).
* JavaScript
  * Das Speech SDK für JavaScript wird jetzt als Open-Source-Code bereitgestellt. Der Quellcode steht auf [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js)zur Verfügung.
  * Node.js wird jetzt unterstützt. Weitere Informationen finden Sie [hier](quickstart-js-node.md).
  * Die Längenbeschränkung für Audiositzungen wurde entfernt. Die Verbindungswiederherstellung erfolgt automatisch im Hintergrund.
* Verbindungsobjekt
  * Über die Erkennung können Sie auf ein Verbindungsobjekt zugreifen. Mit diesem Objekt können Sie die Dienstverbindung explizit initiieren und Verbindungsherstellungs- und Verbindungstrennungsereignisse abonnieren.
    (Für JavaScript und Python ist diese Funktion noch nicht verfügbar.)
* Unterstützung von Ubuntu 18.04
* Android
  * ProGuard-Unterstützung während der APK-Generierung aktiviert

**Verbesserungen**

* Verbesserungen bei der internen Threadverwendung (weniger Threads, Sperren, Mutexe)
* Verbesserte Fehlerberichterstellung/-informationen. In einigen Fällen wurden Fehlermeldungen nicht ordnungsgemäß verteilt.
* Entwicklungsabhängigkeiten in JavaScript wurden für die Verwendung aktueller Module aktualisiert.

**Fehlerbehebungen**

* Arbeitsspeicherverluste aufgrund eines Typenkonflikts in „RecognizeAsync“ behoben
* In einigen Fällen sind Ausnahmen verloren gegangen.
* Behebung des Arbeitsspeicherverlusts in Übersetzungsereignisargumenten
* Sperrproblem bei der Verbindungswiederherstellung in langen Sitzungen behoben
* Problem behoben, dass dazu führen konnte, dass das Endergebnis für fehlerhafte Übersetzungen verpasst wird.
* C#: Wenn im Hauptthread nicht auf einen asynchronen Vorgang gewartet wurde, konnte es vorkommen, dass die Erkennung vor Abschluss der asynchronen Aufgabe entfernt wird.
* Java: Problem behoben, das zum Absturz des virtuellen Java-Computers geführt hat
* Objective-C: Enumerationszuordnung korrigiert (anstelle von „RecognizingIntent“ wurde „RecognizedIntent“ zurückgegeben)
* JavaScript: Standardausgabeformat in „SpeechConfig“ auf „einfach“ festgelegt
* JavaScript: Beseitigung der Inkonsistenz zwischen Eigenschaften des Konfigurationsobjekts in JavaScript und anderen Sprachen

**Beispiele**

* Mehrere Beispiele aktualisiert und korrigiert (z.B. die Ausgabestimmen für die Übersetzung).
* Node.js-Beispiele zum [Beispielrepository](https://aka.ms/csspeech/samples) hinzugefügt

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Neue Features**

* Unterstützung für Android x86/x64.
* Proxyunterstützung: Im SpeechConfig-Objekt können Sie jetzt eine Funktion aufrufen, um die Proxyinformationen (Hostname, Port, Benutzername und Kennwort) festzulegen. Dieses Feature ist in iOS noch nicht verfügbar.
* Verbesserte Fehlercodes und Meldungen. Wenn eine Erkennung einen Fehler zurückgab, wurde dadurch bereits `Reason` (im abgebrochenen Ereignis) oder `CancellationDetails` (im Erkennungsergebnis) auf `Error` festgelegt. Das abgebrochene Ereignis enthält jetzt zwei zusätzliche Member: `ErrorCode` und `ErrorDetails`. Wenn der Server zusätzliche Fehlerinformationen mit dem Fehler zurückgibt, sind diese jetzt in den neuen Membern verfügbar.

**Verbesserungen**

* In der Konfiguration der Erkennung wurde eine zusätzliche Überprüfung hinzugefügt, und es wurde eine zusätzliche Fehlermeldung hinzugefügt.
* Die Verarbeitung von langen Pausen mitten in einer Audiodatei wurde verbessert.
* NuGet-Paket: Für .NET Framework-Projekte wird die Erstellung mit AnyCPU-Konfiguration verhindert.

**Fehlerbehebungen**

* In Erkennungen wurden verschiedene Ausnahmen behoben. Darüber hinaus werden Ausnahmen abgefangen und in abgebrochene Ereignisse konvertiert.
* Ein Arbeitsspeicherverlust in der Eigenschaftenverwaltung wurde behoben.
* Es wurde ein Fehler behoben, bei dem eine Audioeingabedatei zum Absturz der Erkennung führen konnte.
* Es wurde ein Fehler behoben, bei dem nach dem Ereignis zum Beenden einer Sitzung weiter Ereignisse empfangen werden konnten.
* Einige Racebedingungen im Threading wurden korrigiert.
* Ein iOS-Kompatibilitätsproblem wurde behoben, das zu einem Absturz führen konnte.
* Verbesserungen bei der Stabilität für die Android-Mikrofonunterstützung.
* Es wurde ein Fehler behoben, bei dem eine Erkennung in JavaScript die Erkennungssprache ignorierte.
* Es wurde ein Fehler behoben, der (in einigen Fällen) das Festlegen der EndpointId in JavaScript verhinderte.
* Die Parameterreihenfolge in AddIntent in JavaScript wurde geändert, und es wurde eine fehlende JavaScript-Signatur für AddIntent hinzugefügt.

**Beispiele**

* Dem [Beispielrepository](https://aka.ms/csspeech/samples) wurden C++- und C#-Beispiele für die Verwendung von Pull- und Pushstreams hinzugefügt.

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

Verbesserte Zuverlässigkeit und Fehlerbehebungen:

* Ein potenziell schwerwiegender Fehler aufgrund einer Racebedingung bei der Löscherkennung wurde behoben.
* Ein potenziell schwerwiegender Fehler bei einer nicht festgelegten Eigenschaft wurde behoben.
* Zusätzliche Fehler- und Parameterüberprüfungen wurden hinzugefügt.
* Objective-C: Ein potenziell schwerwiegender Fehler durch Namensüberschreibungen in NSString wurde behoben.
* Objective-C: Sichtbarkeit der API wurde angepasst
* JavaScript: Korrektur in Bezug auf Ereignisse und deren Nutzlasten.
* Verbesserungen bei der Dokumentation.

Im [Beispielrepository](https://aka.ms/csspeech/samples) wurde ein neues Beispiel für JavaScript hinzugefügt.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: Release von September 2018

**Neue Features**

* Unterstützung für Objective-C unter iOS. Sehen Sie sich unseren [Objective-C-Schnellstart für iOS](quickstart-objectivec-ios.md) an.
* Unterstützung für JavaScript im Browser. Sehen Sich unseren [JavaScript-Schnellstart](quickstart-js-browser.md) an.

**Wichtige Änderungen**

* Mit diesem Release werden einige Breaking Changes eingeführt.
  Ausführliche Informationen finden Sie auf [dieser Seite](https://aka.ms/csspeech/breakingchanges_1_0_0).

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: Release von August 2018

**Neue Features**

* Mit dem Speech SDK erstellte UWP-Apps erfüllen nun die Anforderungen des Windows App Certification Kit (WACK).
  Sehen Sie sich den [UWP-Schnellstart](quickstart-csharp-uwp.md) an.
* Unterstützung für .NET Standard 2.0 unter Linux (Ubuntu 16.04 x 64)
* Experimentell: Unterstützung für Java 8 unter Windows (64 Bit) und Linux (Ubuntu 16.04 x64).
  Sehen Sie sich den [Schnellstart zur Java Runtime Environment](quickstart-java-jre.md) an.

**Funktionale Änderung**

* Es werden weitere Detailinformationen zu Verbindungsfehlern verfügbar gemacht.

**Wichtige Änderungen**

* In Java (Android) erfordert die `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate`-Funktion keinen Path-Parameter mehr. Der Pfad wird nun auf allen unterstützten Plattformen automatisch erkannt.
* Der get-Accessor der `EndpointUrl`-Eigenschaft in Java und C# wurde entfernt.

**Fehlerbehebungen**

* In Java werden die Ergebnisse der Audiosynthese in der Übersetzungserkennung jetzt implementiert.
* Ein Problem wurde behoben, das inaktive Threads und eine erhöhte Anzahl von offenen und nicht verwendeten Sockets verursachen konnte.
* Ein Problem wurde behoben, das dazu führen konnte, dass lange ausgeführte Erkennungen während der Übertragung beendet wurden.
* Eine Racebedingung beim Herunterfahren der Erkennung wurde behoben.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: Release von Juli 2018

**Neue Features**

* Unterstützung für Android-Plattform (API 23: Android 6.0 Marshmallow oder höher). Sehen Sie sich den [Android-Schnellstart](quickstart-java-android.md) an.
* Unterstützung für .NET Standard 2.0 unter Windows. Sehen Sie sich den [.NET Core-Schnellstart](quickstart-csharp-dotnetcore-windows.md) an.
* Experimentell: Unterstützung für UWP unter Windows (Version 1709 oder höher).
  * Sehen Sie sich den [UWP-Schnellstart](quickstart-csharp-uwp.md) an.
  * Hinweis: Mit dem Speech SDK erstellte UWP-Apps erfüllen die Anforderungen des Windows App Certification Kit (WACK) noch nicht.
* Unterstützung einer lang andauernden Erkennung mit automatischer erneuter Verbindungsherstellung.

**Funktionale Änderungen**

* `StartContinuousRecognitionAsync()` unterstützt eine lang andauernde Erkennung.
* Das Erkennungsergebnis enthält mehr Felder. Versatz vom Audiobeginn und Dauer (beides in Takten) des erkannten Texts und weitere Werte, die den Erkennungsstatus darstellen, z.B. `InitialSilenceTimeout` und `InitialBabbleTimeout`.
* Unterstützung für AuthorizationToken zum Erstellen von Factoryinstanzen.

**Wichtige Änderungen**

* Erkennungsereignisse: Der NoMatch-Ereignistyp wurde mit dem Error-Ereignis zusammengeführt.
* SpeechOutputFormat in C# wurde in OutputFormat umbenannt, um mit C++ konsistent zu bleiben.
* Der Rückgabetyp einiger Methoden der `AudioInputStream`-Schnittstelle wurde geringfügig geändert:
   * In Java gibt die `read`-Methode jetzt `long` anstelle von `int` zurück.
   * In C# gibt die `Read`-Methode jetzt `uint` anstelle von `int` zurück.
   * In C++ geben die `Read`- und die `GetFormat`-Methoden jetzt `size_t` anstelle von `int` zurück.
* C++: Instanzen von Audioeingabestreams können jetzt nur als `shared_ptr` übergeben werden.

**Fehlerbehebungen**

* Korrektur falscher Rückgabewerte im Ergebnis, wenn bei `RecognizeAsync()` ein Timeout auftritt.
* Die Abhängigkeit von Media Foundation-Bibliotheken für Windows wurde entfernt. Das SDK verwendet jetzt die Core Audio-APIs.
* Korrektur der Dokumentation: Eine Seite [Regionen](regions.md) wurde hinzugefügt, um die unterstützten Regionen zu beschreiben.

**Bekanntes Problem**

* Das Speech SDK für Android meldet die Ergebnisse der Sprachsynthese für Übersetzungen nicht. Dieses Problem wird im nächsten Release behoben.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: Release von Juni 2018

**Funktionale Änderungen**

- AudioInputStream

  Eine Erkennung kann jetzt einen Stream als Audioquelle nutzen. Weitere Informationen finden Sie in der zugehörigen [Schrittanleitung](how-to-use-audio-input-streams.md).

- Detailliertes Ausgabeformat

  Beim Erstellen von `SpeechRecognizer` können Sie das Ausgabeformat `Detailed` oder `Simple` anfordern. `DetailedSpeechRecognitionResult` enthält eine Zuverlässigkeitsbewertung, erkannten Text, eine lexikalische Rohform, eine normalisierte Form und eine normalisierte Form mit maskierten anstößigen Ausdrücken.

**Wichtige Änderung**

- Änderung von `SpeechRecognitionResult.RecognizedText` in `SpeechRecognitionResult.Text` in C#.

**Fehlerbehebungen**

- Ein mögliches Rückrufproblem auf USP-Ebene beim Herunterfahren wurde behoben.

- Wenn eine Audioeingabedatei von einer Erkennung genutzt wurde, wurde das Dateihandle länger als erforderlich gespeichert.

- Mehrere Deadlocks zwischen dem Nachrichtensystem und der Erkennung wurden entfernt.

- Ein `NoMatch`-Ergebnis wird ausgelöst, wenn bei der Antwort vom Dienst ein Timeout auftritt.

- Die Media Foundation-Bibliotheken unter Windows werden verzögert geladen. Diese Bibliothek ist nur für die Mikrofoneingabe erforderlich.

- Die Uploadgeschwindigkeit für Audiodaten ist auf das Doppelte der ursprünglichen Audiogeschwindigkeit beschränkt.

- C# .NET-Assemblys haben unter Windows nun einen starken Namen.

- Korrektur der Dokumentation: `Region` ist eine erforderliche Information zum Erstellen einer Erkennung.

Weitere Beispiele wurden hinzugefügt und werden regelmäßig aktualisiert. Die Sammlung der aktuellsten Beispiele finden Sie im [GitHub-Repository mit Beispielen für das Speech SDK](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: Release von Mai 2018

Dieses Release ist erste öffentliche Vorschauversion des Cognitive Services Speech SDK.

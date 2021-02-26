---
title: Versionshinweise – Spracherkennungsdienst
titleSuffix: Azure Cognitive Services
description: Hier finden Sie eine fortlaufende Liste der veröffentlichten Features, Verbesserungen, Fehlerbehebungen und bekannten Problemen des Speech-Diensts.
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: 4393607d6714bc4c1b10ac89d5ac69c173f8fef4
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257320"
---
# <a name="speech-service-release-notes"></a>Versionshinweise zum Speech-Dienst

## <a name="speech-sdk-1150-2021-january-release"></a>Speech-SDK 1.15.0: Release von Januar 2021

**Hinweis**: Für das Speech SDK unter Windows muss das freigegebene Microsoft Visual C++ Redistributable für Visual Studio 2015, 2017 und 2019 installiert sein. Sie können sie [hier](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)herunterladen.

**Zusammenfassung der Highlights**
- Der geringere Arbeitsspeicher und Speicherbedarf des Datenträgers machen das SDK effizienter.
- Es sind Ausgabeformate mit höherer Genauigkeit für die private Vorschau der benutzerdefinierten neuronalen Stimme verfügbar.
- Die Absichtserkennung kann jetzt mehr als nur die höchste Absicht abrufen und zurückgeben, sodass Sie eine separate Bewertung der Absicht Ihres Kunden durchführen können.
- Das Einrichten Ihres Sprach-Assistenten oder Bots ist nun einfacher, und Sie können das Zuhören sofort beenden und die Reaktionen auf Fehler besser steuern.
- Die Geräteleistung wurde verbessert, da die Komprimierung optional ist.
- Die Verwendung des Speech-SDK unter Windows ARM bzw. ARM64 ist möglich.
- Das Debuggen auf niedriger Ebene wurde verbessert.
- Das Feature zur Bewertung der Aussprache ist jetzt in größerem Umfang verfügbar.
- Es gibt verschiedene Fehlerbehebungen für von unseren geschätzten Kunden auf GitHub gekennzeichneten Issues. VIELEN DANK! Es wäre schön, wenn Sie uns weiter Feedback senden würden.

**Verbesserungen**
- Das Speech-SDK ist jetzt effizienter und einfacher zu verwenden. Es wurde ein Multirelease gestartet, um die Speicherauslastung und den Speicherbedarf des Speech-SDK zu reduzieren. Im ersten Schritt wurden erhebliche Änderungen an der Dateigröße in freigegebenen Bibliotheken vorgenommen. Im Vergleich zum Release 1.14:
  - Die 64-Bit-UWP-kompatiblen Windows-Bibliotheken sind etwa 30 Prozent kleiner.
  - Die 32-Bit-Windows-Bibliotheken wurden noch nicht optimiert.
  - Linux-Bibliotheken sind 20 bis 25 Prozent kleiner.
  - Android-Bibliotheken sind 3 bis 5 Prozent kleiner.

**Neue Features**
- **All**: Für die private Vorschau der benutzerdefinierten neuronalen Stimme über die TTS-Sprachsynthese-API sind neue 48-kHz-Ausgabeformate verfügbar: Audio48Khz192KBitRateMonoMp3, audio-48khz-192kbitrate-mono-mp3, Audio48Khz96KBitRateMonoMp3, audio-48khz-96kbitrate-mono-mp3, Raw48Khz16BitMonoPcm, raw-48khz-16bit-mono-pcm, Riff48Khz16BitMonoPcm, riff-48khz-16bit-mono-pcm.
- **All**: Custom Voice ist ebenfalls einfacher zu verwenden. Die Unterstützung für das Einstellen von Custom Voice über `EndpointId` ([C++](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setendpointid), [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.endpointid?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EndpointId), [Java](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setendpointid?view=azure-java-stable#com_microsoft_cognitiveservices_speech_SpeechConfig_setEndpointId_String_), [JavaScript](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#endpointId), [Objective-C](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#endpointid), [Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#endpoint-id)) wurde hinzugefügt. Vor dieser Änderung mussten Custom Voice-Benutzer die Endpunkt-URL über die `FromEndpoint`-Methode festlegen. Kunden können nun die `FromSubscription`-Methode wie bei öffentlichen Stimmen verwenden und dann die Bereitstellungs-ID angeben, indem sie `EndpointId` festlegen. Dadurch wird das Einrichten von benutzerdefinierten Stimmen vereinfacht. 
- **C++/C#/Java/Objective-C/Python:** Fragen Sie mehr als nur die höchste Absicht von `IntentRecognizer` ab. Jetzt wird das Konfigurieren des JSON-Ergebnisses über die `LanguageUnderstandingModel FromEndpoint`-Methode mithilfe des `verbose=true`-URI-Parameters unterstützt, das alle Absichten und nicht nur die Absicht mit der höchsten Bewertung enthält. Dies bezieht sich auf das [GitHub-Issue 880](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/880). Die aktualisierte Dokumentation finden Sie [hier](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/intent-recognition/#add-a-languageunderstandingmodel-and-intents).
- **C++/C#/Java:** Sie können Ihren Sprach-Assistenten oder Bot dazu bringen, dass er das Zuhören sofort beendet. `DialogServiceConnector` ([C++](https://docs.microsoft.com/cpp/cognitive-services/speech/dialog-dialogserviceconnector), [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector?view=azure-dotnet), [Java](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector?view=azure-java-stable)) verfügt jetzt über eine `StopListeningAsync()`-Methode für die gemeinsame Verwendung mit `ListenOnceAsync()`. Dadurch wird die Audioaufzeichnung sofort beendet und ordnungsgemäß auf das Ergebnis gewartet, sodass sich dies perfekt für Szenarios mit der Schaltfläche „Jetzt Beenden“ eignet.
- **C++/C#/Java/JavaScript:** Sorgen Sie dafür, dass Ihr Sprach-Assistent oder Bot besser auf zugrunde liegende Systemfehler reagiert. `DialogServiceConnector` ([C++](https://docs.microsoft.com/cpp/cognitive-services/speech/dialog-dialogserviceconnector), [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector?view=azure-dotnet), [Java](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector?view=azure-java-stable), [JavaScript](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector?view=azure-node-latest)) verfügt jetzt über einen neuen `TurnStatusReceived`-Ereignishandler. Diese optionalen Ereignisse entsprechen allen [`ITurnContext`](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.iturncontext?view=botbuilder-dotnet-stable)-Auflösungen im Zusammenhang mit dem Bot und melden ggf. Ausführungsfehler (z. B. als Ergebnis eines Ausnahmefehlers, Timeouts oder Netzwerkfehlers zwischen Direct Line Speech und dem Bot). `TurnStatusReceived` erleichtert das Reagieren auf Fehlerbedingungen. Wenn ein Bot beispielsweise zu viel Zeit für eine Back-End-Datenbankabfrage benötigt (z. B. bei der Suche nach einem Produkt), kann dem Client mit `TurnStatusReceived` und einer Nachricht wie „Entschuldigung, ich habe das nicht verstanden. Probieren Sie es später noch mal.“ mitgeteilt werden, dass er die Aufforderung später noch mal durchführen soll.
- **C++/C#** : Verwenden Sie das Speech-SDK auf mehreren Plattformen. Das [NuGet-Paket für das Speech-SDK](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) unterstützt jetzt native Windows ARM-/ARM64-Desktopbinärdateien (UWP wurde bereits unterstützt), damit das Speech-SDK für mehr Computertypen verwendet werden kann.
- **Java**: [`DialogServiceConnector`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector?view=azure-java-stable) verfügt jetzt über eine `setSpeechActivityTemplate()`-Methode, die zuvor versehentlich von der Sprache ausgeschlossen wurde. Dies entspricht dem Festlegen der `Conversation_Speech_Activity_Template`-Eigenschaft und erfordert, dass alle zukünftigen Bot Framework-Aktivitäten, die vom Direct Line Speech-Dienst stammen, den bereitgestellten Inhalt in ihre JSON-Nutzdaten zusammenführen.
- **Java**: Das Debuggen auf niedriger Ebene wurde verbessert. Die [`Connection`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.connection?view=azure-java-stable)-Klasse verfügt jetzt ähnlich wie andere Programmiersprachen (C++, C#) über ein `MessageReceived`-Ereignis. Dieses Ereignis ermöglicht den Zugriff auf vom Dienst eingehende Daten auf niedriger Ebene und kann bei der Diagnose und beim Debuggen hilfreich sein.
- **JavaScript:** Das Einrichten von Sprach-Assistenten und Bots über die [`BotFrameworkConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/botframeworkconfig)-Klasse wird einfacher, da diese nun über die Factorymethoden `fromHost()` und `fromEndpoint()` verfügt, die die Verwendung von benutzerdefinierten Dienstidentifizierungen im Vergleich zum manuellen Festlegen von Eigenschaften vereinfachen. Die optionale Angabe von `botId` wurde für die Verwendung eines nicht dem Standard entsprechenden Bots in den Konfigurationsfactorys ebenfalls standardisiert.
- **JavaScript:** Die Geräteleistung wurde durch das Hinzufügen der Zeichenfolgensteuerungseigenschaft für die WebSocket-Komprimierung verbessert. Aus Leistungsgründen wurde die WebSocket-Komprimierung standardmäßig deaktiviert. Diese kann für Szenarios mit geringer Bandbreite erneut aktiviert werden. Ausführlichere Informationen finden Sie [hier](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/propertyid). Dies bezieht sich auf das [GitHub-Issue 242](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/242).
- **JavaScript:** Die Unterstützung für die Bewertung der Aussprache wurde hinzugefügt, um die Auswertung der Aussprache zu ermöglichen. Den Schnellstart finden Sie [hier](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-pronunciation-assessment?pivots=programming-language-javascript).

**Fehlerbehebungen**
- **Alle** (mit Ausnahme von JavaScript): Es wurde eine Regression in Version 1.14 korrigiert, bei der das Erkennungsmodul zu viel Speicher belegt hat.
- **C++:** Es wurde ein Problem mit der automatischen Speicherbereinigung mit `DialogServiceConnector` behoben, auf das sich das [GitHub-Issue 794](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/794) bezieht.
- **C#** : Es wurde ein Problem mit dem Herunterfahren des Threads behoben, das dazu geführt hat, dass Objekte beim Verwerfen ungefähr eine Sekunde blockiert wurden.
- **C++/C#/Java:** Es wurde eine Ausnahme korrigiert, die verhindert, dass eine Anwendung das Sprachautorisierungstoken oder die Aktivitätsvorlage mehr als einmal auf einem `DialogServiceConnector` festlegt.
- **C++/C#/Java:** Es wurde ein Problem behoben, das dazu geführt hat, dass das Erkennungsmodul aufgrund einer Racebedingung beim Löschen abgestürzt ist.
- **JavaScript**: [`DialogServiceConnector`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector) hat den optionalen `botId`-Parameter, der in den Factorys von `BotFrameworkConfig` angegebenen wurde, zuvor nicht berücksichtigt. Dadurch ist es notwendig, den Abfragezeichenfolgenparameter `botId` manuell festzulegen, um einen nicht dem Standard entsprechenden Bot zu verwenden. Der Fehler wurde korrigiert, und `botId`-Werte, die in den Factorys von `BotFrameworkConfig` bereitgestellt werden, werden einschließlich der neuen Ergänzungen `fromHost()` und `fromEndpoint()` berücksichtigt und verwendet. Dies gilt auch für den `applicationId`-Parameter für `CustomCommandsConfig`.
- **JavaScript:** Das [GitHub-Issue 881](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/881) wurde behoben, sodass das Erkennungsmodul Objekten wiederverwenden kann.
- **JavaScript:** Es wurde ein Problem behoben, bei dem das SKD mehrmals in einer TTS-Sitzung `speech.config` gesendet wurde und somit Bandbreite verschwendet hat.
- **JavaScript:** Die Fehlerbehandlung bei der Mikrofonautorisierung wurde vereinfacht, sodass mehr beschreibende Meldungen angezeigt werden können, wenn ein Benutzer die Mikrofoneingabe im Browser nicht zugelassen hat.
- **JavaScript:** Das [GitHub-Issue 249](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/249) wurde behoben, bei dem Typfehler in `ConversationTranslator` und `ConversationTranscriber` einen Kompilierungsfehler für TypeScript-Benutzer verursacht haben.
- **Objective-C:** Es wurde ein Problem behoben, bei dem der GStreamer-Build für iOS in Xcode 11.4 nicht ausgeführt werden konnte. Das [GitHub-Issue 911](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/911) bezieht sich auf dieses Problem.
- **Python**: Das [GitHub-Issue 870](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/870) wurde behoben, indem „DeprecationWarning: the imp module is deprecated in favour of importlib“ (DeprecationWarning: Das imp-Modul für importlib ist veraltet.) entfernt wurde.

**Beispiele**
- Das [„from-file“-Beispiel für den JavaScript-Browser](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/quickstart/javascript/browser/from-file/index.html) verwendet jetzt Dateien für die Spracherkennung. [GitHub-Issue 884](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/884) bezieht sich auf dieses Problem.

## <a name="speech-cli-also-known-as-spx-2021-january-release"></a>Speech-Befehlszeilenschnittstelle (auch als SPX bezeichnet): Release von Januar 2021

**Neue Features**
- Die Speech-CLI ist jetzt als [NuGet-Paket](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech.CLI/) verfügbar und kann über die .NET-CLI als globales .NET-Tool installiert werden, das Sie über die Shell oder die Befehlszeile aufrufen können.
- Das [DevOps-Vorlagenrepository für Custom Speech](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) wurde aktualisiert, um die Speech-CLI für Custom Speech-Workflows zu verwenden.

**Abgekürzte Tests aufgrund von COVID-19:** Da unsere Entwickler aufgrund der fortwährenden Pandemie weiterhin von zu Hause aus arbeiten müssen, wurden die manuellen Überprüfungsskripts aus den Zeiten vor der Pandemie erheblich reduziert. Es wird auf weniger Geräten mit weniger Konfigurationen getestet, und die Wahrscheinlichkeit, dass umgebungsspezifische Fehler nicht erkannt werden, ist möglicherweise höher. Dennoch werden weiterhin viele verschiedene Automatisierungsansätze für die Überprüfung verwendet. Falls wir doch entgegen aller Wahrscheinlichkeit etwas übersehen haben sollten, informieren Sie uns bitte auf [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Bleiben Sie gesund!

## <a name="text-to-speech-2020-december-release"></a>Sprachsyntheserelease von Dezember 2020

**Neue neuronale Stimmen in der allgemeinen Verfügbarkeit und in der Vorschau**

51 neue Stimmen wurden veröffentlicht, sodass nun insgesamt 129 neuronale Stimmen in 54 Sprachen/Gebietsschemas vorhanden sind:

- **46 neue Stimmen in allgemein verfügbaren Gebietsschemas:** Shakir in `ar-EG` Arabisch (Ägypten), Hamed in `ar-SA` Arabisch (Saudi-Arabien), Borislav in `bg-BG` Bulgarisch (Bulgarien), Joana in `ca-ES` Katalanisch (Spanien), Antonin in `cs-CZ` Tschechisch (Tschechische Republik), Jeppe in `da-DK` Dänisch (Dänemark), Jonas in `de-AT` Deutsch (Österreich), Jan in `de-CH` Deutsch (Schweiz), Nestoras in `el-GR` Griechisch (Griechenland), Liam in `en-CA` Englisch (Kanada), Connor in `en-IE` Englisch (Irland), Madhur in `en-IN` Hindi (Indien), Mohan in `en-IN` Telugu (Indien), Prabhat in `en-IN` Englisch (Indien), Valluvar in `en-IN` Tamil (Indien), Enric in `es-ES` Katalanisch (Spanien), Kert in `et-EE` Estnisch (Estland), Harri in `fi-FI` Finnisch (Finnland), Selma in `fi-FI` Finnisch (Finnland), Fabrice in `fr-CH` Französisch (Schweiz), Colm in `ga-IE` Irisch (Irland), Avri in `he-IL` Hebräisch (Israel), Srecko in `hr-HR` Kroatisch (Kroatien), Tamas in `hu-HU` Ungarisch (Ungarn), Gadis in `id-ID` Indonesisch (Indonesien), Leonas in `lt-LT` Litauisch (Litauen), Nils in `lv-LV` Lettisch (Lettland), Osman in `ms-MY` Malaysisch (Malaysia), Joseph in `mt-MT` Maltesisch (Malta), Finn in `nb-NO` Norwegisch, Bokmål (Norwegen), Pernille in `nb-NO` Norwegisch, Bokmål (Norwegen), Fenna in `nl-NL` Niederländisch (Niederlande), Maarten in `nl-NL` Niederländisch (Niederlande), Agnieszka in `pl-PL` Polnisch (Polen), Marek in `pl-PL` Polnisch (Polen), Duarte in `pt-BR` Portugiesisch (Brasilien), Raquel in `pt-PT` Portugiesisch (Portugal), Emil in `ro-RO` Rumänisch (Rumänien), Dmitry in `ru-RU` Russisch (Russland), Svetlana in `ru-RU` Russisch (Russland), Lukas in `sk-SK` Slowakisch (Slowakei), Rok in `sl-SI` Slowenisch (Slowenien), Mattias in `sv-SE` Schwedisch (Schweden), Sofie in `sv-SE` Schwedisch (Schweden), Niwat in `th-TH` Thai (Thailand), Ahmet in `tr-TR` Türkisch (Türkei), NamMinh in `vi-VN` Vietnamesisch (Vietnam), HsiaoChen in `zh-TW` Taiwanesisches Mandarin (Taiwan), YunJhe in `zh-TW` Taiwanesisches Mandarin (Taiwan), HiuMaan in `zh-HK` Chinesisch Kantonesisch (Hongkong), WanLung in `zh-HK` Chinesisch Kantonesisch (Hongkong).

- **5 neue Stimmen in Gebietsschemas in der Vorschau:** Kert in `et-EE` Estnisch (Estland), Colm in `ga-IE` Irisch (Irland), Nils in `lv-LV` Lettisch (Lettland), Leonas in `lt-LT` Litauisch (Litauen), Joseph in `mt-MT` Maltesisch (Malta).

Mit diesem Release werden nun insgesamt 129 neuronale Stimmen in 54 Sprachen/Gebietsschemas unterstützt. Darüber hinaus sind mehr als 70 Standardstimmen in 49 Sprachen/Gebietsschemas verfügbar. Eine vollständige Liste finden Sie unter [Sprachunterstützung](language-support.md#text-to-speech).

**Updates für die Audioinhaltserstellung**
- Die Benutzeroberfläche für die Stimmenauswahl mit Stimmenkategorien und ausführlichen Beschreibungen wurde verbessert. 
- Die Intonation für alle neuronalen Stimmen wurde für verschiedene Sprachen optimiert.
- Die Benutzeroberflächenlokalisierung basierend auf der Sprache des Browsers wurde automatisiert.
- `StyleDegree`-Steuerelemente für alle neuronalen Stimmen für `zh-CN`.
Sie können die neuen Features im [Audioinhaltserstellungs-Tool](https://speech.microsoft.com/audiocontentcreation) testen. 

**Updates für zh-CN-Stimmen**
- Alle neuronalen Stimmen für `zh-CN` wurden mit Unterstützung von Englisch aktualisiert.
- Alle neuronalen Stimmen für `zh-CN` unterstützen nun Anpassung der Intonation. SSML oder das Audioinhaltserstellungs-Tool können zum Anpassen der Intonation verwendet werden.
- Alle neuronalen Stimmen für `zh-CN` mit mehreren Stilen wurden zur Unterstützung des `StyleDegree`-Steuerelements aktualisiert. Die Intensität der Emotionen (weich oder stark) ist anpassbar.
- `zh-CN-YunyeNeural` wurde zur Unterstützung mehrerer Stile aktualisiert, die verschiedene Emotionen widerspiegeln können.

## <a name="text-to-speech-2020-november-release"></a>Sprachsyntheserelease von November 2020

**Neue Gebietsschemas und Stimmen in der Vorschau**
- **Fünf neue Stimmen und Sprachen** wurden zum Portfolio der neuronalen Sprachsynthese hinzugefügt. Sie lauten wie folgt: Grace in Maltesisch (Malta), Ona in Litauisch (Litauen), Anu in Estnisch (Estland), Orla in Irisch (Irland) und Everita in Lettisch (Lettland).
- **Fünf neue `zh-CN`-Stimmen mit Unterstützung mehrerer Stile und Rollen:** Xiaohan, Xiaomo, Xiaorui, Xiaoxuan und Yunxi.

> Diese Stimmen sind in drei Azure-Regionen in der öffentlichen Vorschau verfügbar: „USA, Osten“, „Asien, Südosten“ und „Europa, Westen“.

**Der neuronale Sprachsynthesecontainer ist allgemein Verfügbar**
- Mit dem neuronalen Sprachsynthesecontainer können Entwickler die Sprachsynthese mit den natürlichsten digitalen Stimmen für spezifische Sicherheits- und Datengovernanceanforderungen in ihren eigenen Umgebungen ausführen. Erfahren Sie, [wie Sie Sprachsynthesecontainer installieren](speech-container-howto.md). 

**Neue Features**
- **Custom Voice:** ermöglicht Benutzern das Kopieren eines Stimmmodells aus einer Region in eine andere (das Anhalten und Fortsetzen des Endpunkts wird unterstützt). Rufen Sie das [Portal](https://speech.microsoft.com/customvoice) auf.
- Unterstützung des [SSML-Tags „silence“](speech-synthesis-markup.md#add-silence) 
- Allgemeine Verbesserungen bei der Stimmenqualität der Sprachsynthese: Die Genauigkeit der Aussprache auf Wortebene in nb-NO wurde verbessert. Aussprachefehler wurden um 53 % verringert.

> Weitere Informationen finden Sie in [diesem Techblog](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-previews-five-new-languages-with/ba-p/1907604).

## <a name="text-to-speech-2020-october-release"></a>Sprachsynthese: Release Oktober 2020

**Neue Features**
- Jenny unterstützt einen neuen `newscast`-Stil. Weitere Informationen finden Sie unter [Verwenden der Sprachstile in SSML](speech-synthesis-markup.md#adjust-speaking-styles).
- **Für neuronale Stimmen wurde ein Upgrade auf einen HiFiNet-Vocoder durchgeführt, der eine höhere Klangtreue und eine höhere Synthesegeschwindigkeit aufweist**. Dies kommt Kunden zugute, deren Szenario auf HiFi-Audio oder lange Interaktionen beruht, einschließlich Videosynchronisation, Hörbücher oder Onlinelernmaterialien. [Erfahren Sie mehr über die Story, und hören Sie sich die Sprachbeispiele in unserem Tech Community-Blogbeitrag an.](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860)
- **[Custom Voice](https://speech.microsoft.com/customvoice) & [Audioinhaltserstellungs-Studio](https://speech.microsoft.com/audiocontentcreation) wurde für 17 Gebietsschemas lokalisiert**. Benutzer können die Benutzeroberfläche für eine benutzerfreundlichere Umgebung leicht in eine lokale Sprache wechseln.   
- **Audioinhaltserstellung**: Es wurde die Stilgradsteuerung für XiaoxiaoNeural hinzugefügt. Das Feature der angepassten Unterbrechung wurde optimiert, um inkrementelle Unterbrechungen von 50 ms einzuschließen. 

**Allgemeine Verbesserungen bei der Stimmenqualität der Sprachsynthese**
- Die Genauigkeit der Aussprache auf Wortebene wurde in `pl-PL` (Verringerung der Fehlerrate: 51 %) und `fi-FI` (Verringerung der Fehlerrate: 58 %) verbessert.
- Das Lesen einzelner Wörter für `ja-JP` wurde für das Wörterbuchszenario verbessert. Aussprachefehler wurden um 80 % verringert.
- `zh-CN-XiaoxiaoNeural`: Die Sprachqualität von „Sentiment/CustomerService/Newscast/Cheerful/Angry style“ wurde verbessert.
- `zh-CN`: Die Erhua-Aussprache und der helle Ton wurden verbessert und der Raumsatzrhythmus optimiert, was die Verständlichkeit erheblich verbessert. 

## <a name="speech-sdk-1140-2020-october-release"></a>Speech SDK 1.14.0: Release vom Oktober 2020

**Hinweis**: Für das Speech SDK unter Windows muss das freigegebene Microsoft Visual C++ Redistributable für Visual Studio 2015, 2017 und 2019 installiert sein. Sie können sie [hier](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)herunterladen.

**Neue Features**
- **Linux:** Unterstützung für Debian 10 und Ubuntu 20.04 LTS wurde hinzugefügt.
- **Python/Objective-C**: Die Unterstützung für die `KeywordRecognizer`-API wurde hinzugefügt. Die Dokumentation finden Sie [hier](./custom-keyword-basics.md).
- **C++/Java/C#** : Die Unterstützung zum Festlegen beliebiger `HttpHeader`-Schlüssel/-Werte über `ServicePropertyChannel::HttpHeader` wurde hinzugefügt.
- **JavaScript:** Die Unterstützung für die `ConversationTranscriber`-API wurde hinzugefügt. Die zugehörige Dokumentation finden Sie [hier](./how-to-use-conversation-transcription.md?pivots=programming-language-javascript). 
- **C++/C#** : Die neue `AudioDataStream FromWavFileInput`-Methode (zum Lesen von WAV-Dateien) wurde [hier (C++)](/cpp/cognitive-services/speech/audiodatastream) und [hier (C#)](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream) hinzugefügt.
-  **C++/C#/Java/Python/Objective-C/Swift**: Es wurde eine `stopSpeakingAsync()`-Methode zum Beenden der Sprachsynthese hinzugefügt. Die Referenzdokumentation finden Sie [hier (C++)](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace), [hier (C#)](/dotnet/api/microsoft.cognitiveservices.speech), [hier (Java)](/java/api/com.microsoft.cognitiveservices.speech), [hier (Python)](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech) und [hier (Objective-C/Swift)](/objectivec/cognitive-services/speech/).
- **C#, C++, Java**: Es wurde eine `FromDialogServiceConnector()`-Funktion zur Klasse `Connection` hinzugefügt, mit der Ereignisse für `DialogServiceConnector` zum Herstellen oder Aufheben von Verbindungen überwacht werden können. Die Referenzdokumentation finden Sie [hier (C#)](/dotnet/api/microsoft.cognitiveservices.speech.connection), [hier (C++)](/cpp/cognitive-services/speech/connection) und [hier (Java)](/java/api/com.microsoft.cognitiveservices.speech.connection).
- **C++/C#/Java/Python/Objective-C/Swift**: Die Unterstützung für die Aussprachebewertung wurde hinzugefügt. Diese bewertet die Aussprache und gibt den Rednern Feedback zur Genauigkeit und zum Redefluss der gesprochenen Audioinformationen. Lesen Sie die [Dokumentation](how-to-pronunciation-assessment.md).

**Wichtige Änderung**
- **JavaScript:** Der Rückgabetyp von PullAudioOutputStream.read() wurde von einer internen Zusage in eine native JavaScript-Zusage geändert.

**Fehlerbehebungen**
- **All**: Die 1.13-Regression wurde in `SetServiceProperty` behoben, bei der Werte mit bestimmten Zeichen ignoriert wurden.
- **C#** : Windows-Konsolenbeispiele in Visual Studio 2019 wurden behoben, in denen bei der Suche von nativen DLLs Fehler aufgetreten sind.
- **C#** : Der Absturz bei der Arbeitsspeicherverwaltung wurde behoben, wenn ein Datenstrom als `KeywordRecognizer`-Eingabe verwendet wurde.
- **ObjectiveC/Swift**: Der Absturz bei der Arbeitsspeicherverwaltung wurde behoben, wenn ein Datenstrom als Eingabe des Erkennungsmoduls verwendet wurde.
- **Windows**: Es wurde ein Problem mit der Koexistenz von BT HFP/A2DP auf der universellen Windows-Plattform behoben.
- **JavaScript:** Die Zuordnung von Sitzungs-IDs wurde behoben, um die Protokollierung zu verbessern und bei internen Debug-/Dienstkorrelationen zu helfen.
- **JavaScript:** Es wurde eine Fehlerbehebung für `DialogServiceConnector` hinzugefügt, die `ListenOnce`-Aufrufe nach dem Ausführen des ersten Aufrufs deaktiviert.
- **JavaScript:** Es wurde ein Problem behoben, bei dem die Ergebnisausgabe immer nur „simple“ (einfach) ergibt.
- **JavaScript:** Ein Problem bei der fortlaufenden Erkennung wurde in Safari unter macOS behoben.
- **JavaScript:** Es wurde eine Risikominderung für die CPU-Last für das Szenario mit hohem Anforderungsdurchsatz durchgeführt.
- **JavaScript:** Der Zugriff auf Details des Ergebnisses der Sprachprofilregistrierung wurde zugelassen.
- **JavaScript:** Ein Fehler bei der fortlaufenden Erkennung in `IntentRecognizer` wurde behoben.
- **C++/C#/Java/Python/Swift/ObjectiveC**: Eine falsche URL für „australiaeast“ und „brazilsouth“ in `IntentRecognizer` wurde behoben.
- **C++/C#** : Es wurde `VoiceProfileType` als Argument beim Erstellen eines `VoiceProfile`-Objekts hinzugefügt.
- **C++/C#/Java/Python/Swift/ObjectiveC**: Es wurde ein Problem für das potenzielle `SPX_INVALID_ARG` beim Versuch behoben, `AudioDataStream` von einer angegebenen Position zu lesen.
- **IOS**: Es wurde der Absturz bei der Spracherkennung unter Unity behoben.

**Beispiele**
- **ObjectiveC**: Ein Beispiel für die Schlüsselworterkennung wurde [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/speech-samples) hinzugefügt.
- **C#/JavaScript**: Ein Schnellstart für die Unterhaltungstranskription wurde [hier (C#)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/conversation-transcription) und [hier (JavaScript)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/conversation-transcription) hinzugefügt.
- **C++/C#/Java/Python/Swift/ObjectiveC**: [Hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples) wurde ein Beispiel für die Bewertung der Aussprache hinzugefügt.
- **Xamarin**: Der Schnellstart wurde [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/xamarin) auf die neueste Visual Studio-Vorlage aktualisiert.

**Bekanntes Problem**
- Das DigiCert Global Root G2-Zertifikat wird in HoloLens 2 und Android 4.4 (KitKat) nicht standardmäßig unterstützt und muss zum System hinzugefügt werden, damit das Speech SDK funktioniert. Das Zertifikat wird in naher Zukunft den Betriebssystemimages von HoloLens 2 hinzugefügt werden. Kunden von Android 4.4 müssen das aktualisierte Zertifikat dem System hinzufügen.

**Abgekürzte Tests aufgrund von COVID-19:** Da wir in den letzten Wochen remote gearbeitet haben, konnten wir die manuellen Tests zur Überprüfung nicht im gewohnten Umfang durchführen. Wir haben keine Änderungen vorgenommen, die möglicherweise zu Kompatibilitätsproblemen geführt hätten, und alle unsere automatisierten Tests wurden bestanden. Falls wir doch entgegen aller Wahrscheinlichkeit etwas übersehen haben sollten, informieren Sie uns bitte auf [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Bleiben Sie gesund!

## <a name="speech-cli-also-known-as-spx-2020-october-release"></a>Speech-Befehlszeilenschnittstelle (auch als SPX bezeichnet): Release vom Oktober 2020
SPX ist die Befehlszeilenschnittstelle, um den Azure Speech-Dienst ohne das Schreiben von Code zu verwenden. Laden Sie die neueste Version [hier](./spx-basics.md) herunter. <br>

**Neue Features**
- `spx csr dataset upload --kind audio|language|acoustic` – Erstellen Sie Datasets aus lokalen Daten, nicht nur aus URLs.
- `spx csr evaluation create|status|list|update|delete` – Vergleichen Sie neue Modelle mit grundlegenden Tatsachen/anderen Modellen.
- `spx * list` – Unterstützt die nicht ausgelagerte Umgebung (erfordert kein --top X --skip X).
- `spx * --http header A=B` – Unterstützen Sie benutzerdefinierte Header (zur benutzerdefinierten Authentifizierung zu Office hinzugefügt). 
- `spx help` – Verbesserter Text und farbcodierter Graviszeichentext (blau).

## <a name="text-to-speech-2020-september-release"></a>Sprachsynthese: Release September 2020

### <a name="new-features"></a>Neue Funktionen

* **Neuronales Text-to-Speech** 
    * **Erweitert, um 18 neue Sprachen/Gebietsschemas zu unterstützen.** Dazu gehören Bulgarisch, Tschechisch, Deutsch (Österreich), Deutsch (Schweiz), Griechisch, Englisch (Irland), Französisch (Schweiz), Hebräisch, Kroatisch, Ungarisch, Indonesisch, Malaiisch, Rumänisch, Slowakisch, Slowenisch, Tamil, Telugu und Vietnamesisch. 
    * **Wir haben 14 neue Stimmen veröffentlicht, um die Vielfalt in den vorhandenen Sprachen zu erhöhen.** Weitere Informationen finden Sie in der [vollständigen Liste der Sprachen und Stimmen](language-support.md#neural-voices).
    * **Neue Sprechweisen für `en-US`- und `zh-CN`-Stimmen.** Jenny, die neue Stimme auf Englisch (USA), unterstützt Chatbot-, Kundendienst- und Assistentenstile. 10 neue Sprechweisen sind mit unserer zh-CN-Stimme „XiaoXiao“ verfügbar. Darüber hinaus unterstützt die neuronale Stimme von XiaoXiao die `StyleDegree`-Optimierung. Weitere Informationen finden Sie unter [Verwenden der Sprachstile in SSML](speech-synthesis-markup.md#adjust-speaking-styles).

* **Container: Es wurde ein neuronaler TTS-Container in der öffentlichen Vorschau mit 16 Stimmen in 14 Sprachen veröffentlicht.** Weitere Informationen finden Sie unter [Bereitstellen von Speech-Containern für neuronales TTS](speech-container-howto.md).  

Lesen Sie die [vollständige Ankündigung der TTS-Updates für Ignite 2020](https://techcommunity.microsoft.com/t5/azure-ai/ignite-2020-neural-tts-updates-new-language-support-more-voices/ba-p/1698544). 

## <a name="text-to-speech-2020-august-release"></a>Release der Sprachsynthese vom August 2020

### <a name="new-features"></a>Neue Funktionen

* **Neuronale Sprachsynthese: Neuer Sprachstil für die Aria-Stimme in `en-US`:** AriaNeural kann wie ein Nachrichtensprecher beim Lesen der Nachrichten klingen. Der Stil „newscast-formal“ klingt seriöser, während der Stil „newscast-casual“ lockerer und informell klingt. Weitere Informationen finden Sie unter [Verwenden der Sprachstile in SSML](speech-synthesis-markup.md).

* **Custom Voice: Release eines neuen Features zur automatischen Überprüfung der Trainingsdatenqualität:** Wenn Sie Ihre Daten hochladen, untersucht das System verschiedene Aspekte Ihrer Audio- und Transkriptdaten und behebt oder filtert automatisch Probleme, um die Qualität des Sprachmodells zu verbessern. Dies umfasst die Lautstärke Ihrer Audiodaten, den Rauschpegel, die Aussprachegenauigkeit, die Ausrichtung der Sprache mit dem normalisierten Text, die Stille in den Audiodaten sowie das Audio- und Skriptformat. 

* **Audioinhaltserstellung: Neue Features für leistungsstärkere Sprachoptimierungs- und Audioverwaltungsfunktionen:**

    * Aussprache: Feature zur Optimierung der Aussprache wurde mit dem aktuellen Phonemsatz aktualisiert. Sie können das richtige Phonemelement aus der Bibliothek auswählen und die Aussprache der ausgewählten Wörter verfeinern. 

    * Herunterladen: Die Audiofeatures „Herunterladen“ und „Exportieren“ wurde verbessert, um das Generieren von Audiodaten nach Absatz zu unterstützen. Sie können den Inhalt in derselben Datei oder in SSML bearbeiten, während Sie mehrere Audioausgaben erzeugen. Die Dateistruktur von „Herunterladen“ wurde ebenfalls optimiert. Sie können jetzt problemlos alle Audiodateien in einem Ordner erhalten. 

    * Taskstatus: Die Funktion zum Exportieren mehrerer Dateien wurde verbessert. Wenn beim Exportieren von mehreren Dateien in der Vergangenheit ein Fehler bei einer der Dateien aufgetreten ist, ist der gesamte Task fehlgeschlagen. Nun werden alle anderen Dateien erfolgreich exportiert. Der Taskbericht wurde um mehr Details und strukturierte Informationen erweitert. Sie können die Protokolle nun mithilfe des Berichts auf alle fehlerhaften Dateien und Sätze überprüfen. 

    * SSML-Dokumentation: Ein Link zur SSML-Dokumentation wurde bereitgestellt, damit Sie die Regeln zur Verwendung der Optimierungsfeatures überprüfen können.

* **Die Voice List-API wurde aktualisiert, sodass nun ein benutzerfreundlicher Anzeigename und die unterstützten Sprachstile für neuronale Stimmen enthalten sind.**

### <a name="general-tts-voice-quality-improvements"></a>Allgemeine Verbesserungen bei der Stimmenqualität der Sprachsynthese

* Der Prozentsatz an Aussprachefehlern für `ru-RU` (Fehlerrate wurde um 56 % reduziert) und `sv-SE` (Fehlerrate wurde um 49 % reduziert) wurde reduziert.

* Das Lesen von Wörtern mit Polyphonie von neuronalen Stimmen in `en-US` wurde um 40 % verbessert. Beispiele für Wörter mit Polyphonie sind „read“, „live“, „content“, „record“ und „object“. 

* Die Natürlichkeit der Betonung von Fragen in `fr-FR` wurde verbessert. MOS-Erhöhung (Mean Opinion Score): +0,28

* Die Vocoder für die folgenden Stimmen wurden mit Genauigkeitsverbesserungen und allgemeiner Leistungsverbesserung um 40 % aktualisiert.

    | Gebietsschema | Sprache |
    |---|---|    
    | `en-GB` | Mia |
    | `es-MX` | Dalia |
    | `fr-CA` | Sylvie |
    | `fr-FR` | Denise |
    | `ja-JP` | Nanami |
    | `ko-KR` | Sun-Hi |

### <a name="bug-fixes"></a>Behebung von Programmfehlern

* Einige Fehler mit dem Audioinhaltserstellungs-Tool wurden behoben. 
    * Ein Problem mit der automatischen Aktualisierung wurde behoben. 
    * Probleme mit Sprachstilen in zh-CN in der Region „Asien, Südosten“ wurden behoben.
    * Ein Stabilitätsproblem, einschließlich eines Exportfehlers mit dem Tag „break“, sowie Satzzeichenfehler wurden behoben.

## <a name="new-speech-to-text-locales-2020-august-release"></a>Neue Gebietsschemas für die Spracherkennung: Release vom August 2020
Im August wurden 26 neue Gebietsschemas für die Spracherkennung veröffentlicht: 2 europäische Sprachen (`cs-CZ` und `hu-HU`), 5 englische Gebietsschemas und 19 spanische Gebietsschemas, die die meisten Länder in Südamerika abdecken. Im Folgenden finden Sie eine Liste der neuen Gebietsschemas. Eine vollständige Liste der Sprachen finden Sie [hier](./language-support.md).

| Gebietsschema  | Sprache                          |
|---------|-----------------------------------|
| `cs-CZ` | Tschechisch (Tschechische Republik)            | 
| `en-HK` | Englisch (Hongkong)               | 
| `en-IE` | Englisch (Irland)                 | 
| `en-PH` | Englisch (Philippinen)             | 
| `en-SG` | Englisch (Singapur)               | 
| `en-ZA` | Englisch (Südafrika)            | 
| `es-AR` | Spanisch (Argentinien)               | 
| `es-BO` | Spanisch (Bolivien)                 | 
| `es-CL` | Spanisch (Chile)                   | 
| `es-CO` | Spanisch (Kolumbien)                | 
| `es-CR` | Spanisch (Costa Rica)              | 
| `es-CU` | Spanisch (Kuba)                    | 
| `es-DO` | Spanisch (Dominikanische Republik)      | 
| `es-EC` | Spanisch (Ecuador)                 | 
| `es-GT` | Spanisch (Guatemala)               | 
| `es-HN` | Spanisch (Honduras)                | 
| `es-NI` | Spanisch (Nicaragua)               | 
| `es-PA` | Spanisch (Panama)                  | 
| `es-PE` | Spanisch (Peru)                    | 
| `es-PR` | Spanisch (Puerto Rico)             | 
| `es-PY` | Spanisch (Paraguay)                | 
| `es-SV` | Spanisch (El Salvador)             | 
| `es-US` | Spanisch (USA)                     | 
| `es-UY` | Spanisch (Uruguay)                 | 
| `es-VE` | Spanisch (Venezuela)               | 
| `hu-HU` | Ungarisch (Ungarn)               | 


## <a name="speech-sdk-1130-2020-july-release"></a>Speech SDK 1.13.0: Release 2020-July

**Hinweis**: Für das Speech SDK unter Windows muss das freigegebene Microsoft Visual C++ Redistributable für Visual Studio 2015, 2017 und 2019 installiert sein. Sie können die Software [hier](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) herunterladen und installieren.

**Neue Features**
- **C#** : Unterstützung für asynchrone Unterhaltungstranskription hinzugefügt. Die zugehörige Dokumentation finden Sie [hier](./how-to-async-conversation-transcription.md).  
- **JavaScript:** Unterstützung für Sprechererkennung für [Browser](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/speaker-recognition) und [Node.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/speaker-recognition) hinzugefügt.
- **JavaScript:** Unterstützung für automatische Spracherkennung/Sprach-ID hinzugefügt. Die zugehörige Dokumentation finden Sie [hier](./how-to-automatic-language-detection.md?pivots=programming-language-javascript).
- **Objective-C:** Unterstützung für die [Konversation von mehreren Geräten](./multi-device-conversation.md) und [Unterhaltungstranskription](./conversation-transcription.md) hinzugefügt. 
- **Python**: Unterstützung für komprimierte Audiodaten für Python unter Windows und Linux hinzugefügt. Die zugehörige Dokumentation finden Sie [hier](./how-to-use-codec-compressed-audio-input-streams.md). 

**Fehlerbehebungen**
- **All**: Es wurde ein Problem behoben, durch das der KeywordRecognizer die Streams nach einer Erkennung nicht weiterleitete.
- **All**: Es wurde ein Problem behoben, durch das der aus einem KeywordRecognitionResult abgeleitete Stream nicht das Schlüsselwort enthielt.
- **All**: Es wurde ein Problem behoben, durch das SendMessageAsync die Nachricht nicht wirklich über das Netzwerk gesendet hat, nachdem die Benutzer darauf warteten.
- **All**: Es wurde ein Absturz in den Sprechererkennungs-APIs korrigiert, wenn Benutzer VoiceProfileClient::SpeakerRecEnrollProfileAsync mehrfach aufgerufen haben und nicht darauf warteten, dass die Aufrufe beendet wurden.
- **All**: Die Aktivierung der Dateiprotokollierung in der VoiceProfileClient- und der SpeakerRecognizer-Klasse wurde korrigiert.
- **JavaScript:** Es wurde ein [Problem](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/74) mit der Drosselung behoben, wenn der Browser minimiert wird.
- **JavaScript:** Es wurde ein [Problem](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/78) mit einem Arbeitsspeicherverlust in Streams behoben.
- **JavaScript:** Zwischenspeicherung für OCSP-Antworten von Node.js hinzugefügt.
- **Java**: Es wurde ein Problem behoben, durch das BigInteger-Felder immer „0“ zurückgaben.
- **iOS:** Es wurde ein [Problem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/702) beim Veröffentlichen von Apps, die auf dem Speech SDK basieren, im iOS App Store behoben.

**Beispiele**
- **C++:** Beispielcode für Sprechererkennung [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp) hinzugefügt.

**Abgekürzte Tests aufgrund von COVID-19:** Da wir in den letzten Wochen remote gearbeitet haben, konnten wir die manuellen Tests zur Überprüfung nicht im gewohnten Umfang durchführen. Wir haben keine Änderungen vorgenommen, die möglicherweise zu Kompatibilitätsproblemen geführt hätten, und alle unsere automatisierten Tests wurden bestanden. Falls wir doch entgegen aller Wahrscheinlichkeit etwas übersehen haben sollten, informieren Sie uns bitte auf [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Bleiben Sie gesund!

## <a name="text-to-speech-2020-july-release"></a>Sprachsynthese: Release Juli 2020

### <a name="new-features"></a>Neue Funktionen

* **Neuronale Sprachsynthese: 15 neue neuronale Stimmen:** Die neu hinzugefügten Stimmen im Portfolio der neuronalen Sprachsynthese sind die folgenden: Salma für `ar-EG` Arabisch (Ägypten), Zariyah für `ar-SA` Arabisch (Saudi-Arabien), Alba für `ca-ES` Katalanisch (Spanien), Christel für `da-DK` Dänisch (Dänemark), Neerja für `es-IN` Englisch (Indien), Noora für `fi-FI` Finnisch (Finnland), Swara für `hi-IN` Hindi (Indien), Colette für `nl-NL` Niederländisch (Niederlande), Zofia für `pl-PL` Polnisch (Polen), Fernanda für `pt-PT` Portugiesisch (Portugal), Dariya für `ru-RU` Russisch (Russland), Hillevi für `sv-SE` Schwedisch (Schweden), Achara für `th-TH` Thai (Thailand), HiuGaai für `zh-HK` Chinesisch (Kantonesisch, traditionell) und HsiaoYu für `zh-TW` Chinesisch (Taiwanesisches Mandarin). [Hier](./language-support.md#neural-voices) finden Sie alle unterstützten Sprachen.  

* **Custom Voice: Optimierung der Stimmentests mit dem Trainingsablauf zur Vereinfachung für den Benutzer:** Mit dem neuen Testingfeature wird jede Stimme automatisch durch eine vordefinierte Gruppe von Tests getestet, die für jede Sprache so optimiert wurde, dass sie allgemeine Szenarios sowie Sprachassistentszenarios abdeckt. Diese Testgruppen wurden sorgfältig ausgewählt und getestet, sodass sie typische Anwendungsfälle und Phoneme der Sprache enthalten. Benutzer können beim Trainieren eines Modells zudem noch immer auch ihre eigenen Testskripts hochladen.

* **Audioinhaltserstellung: Es werden einige neue Features eingeführt, die leistungsstärkere Sprachoptimierungs- und Audioverwaltungsfunktionen bereitstellen.**

    * `Pitch`, `rate` und `volume` werden erweitert, sodass die Optimierung durch einen vordefinierten Wert, z. B. „Langsam, „Mittel“ und „Schnell“, unterstützt wird. Benutzer können nun ganz einfach einen Wert für die „Konstante“ für Ihre Audiobearbeitung auswählen.

    ![Audiooptimierung](media/release-notes/audio-tuning.png)

    * Benutzer können nun die `Audio history`-Einträge für ihre Arbeitsdatei prüfen. Mit diesem Feature können Benutzer einfach alle im Zusammenhang mit einer Arbeitsdatei generierten Audiodaten nachverfolgen. Sie können die Verlaufsversion prüfen und die Qualität vergleichen, während sie optimieren. 

    ![Audioverlauf](media/release-notes/audio-history.png)

    * Das `Clear`-Feature ist jetzt flexibler. Benutzer können einen bestimmten Optimierungsparameter löschen, wobei andere Parameter für den ausgewählten Inhalt verfügbar bleiben.  

    * Auf der [Startseite](https://speech.microsoft.com/audiocontentcreation) wurde ein Videotutorial hinzugefügt, um Benutzern mit den ersten Schritten mit der Stimmenoptimierung und der Audioverwaltung der Sprachsynthese zu helfen. 

### <a name="general-tts-voice-quality-improvements"></a>Allgemeine Verbesserungen bei der Stimmenqualität der Sprachsynthese

* Der Vocoder der Sprachsynthese wurde verbessert und weist nun eine höhere Genauigkeit und eine geringere Wartezeit auf.

    * Elsa (`it-IT`) wurde auf einen neuen Vocoder aktualisiert, wodurch sich die Stimmenqualität, dargestellt durch den CMOS (Comparative Mean Opinion Score), um +0,464 verbessert hat, die Synthese 40 % schneller erfolgt und sich die Wartezeit beim ersten Byte um 30 % verringert hat. 
    * Xiaoxiao (`zh-CN`) wurde auf den neuen Vocoder aktualisiert, der eine Verbesserung des CMOS von +0148 für die allgemeine Domäne, +0,348 für den Nachrichtensprecherstil und +0,195 für den lyrischen Stil bewirkt. 

* Die Sprachmodelle für `de-DE` und `ja-JP` wurden aktualisiert, sodass die Ausgabe der Sprachsynthese natürlicher erscheint.
    
    * Katja (`de-DE`) wurde mit der neuesten Rhythmusmodellierungsmethode aktualisiert und weist eine Verbesserung des MOS (Mean Opinion Score) von +0,13 auf. 
    * Nanami (`ja-JP`) wurde mit einem neuen Rhythmusmodell für den Tonhöhenakzent aktualisiert und weist eine Verbesserung des MOS (Mean Opinion Score) von +0,19 auf.  

* Die Genauigkeit der Aussprache auf Wortebene wurde in fünf Sprachen verbessert.

    | Sprache | Reduzierung der Aussprachefehler |
    |---|---|
    | `en-GB` | 51 % |
    | `ko-KR` | 17 % |
    | `pt-BR` | 39% |
    | `pt-PT` | 77 % |
    | `id-ID` | 46 % |

### <a name="bug-fixes"></a>Behebung von Programmfehlern

* Lesen von Währungen
    * Das Problem mit dem Lesen von Währungen bei `es-ES` und `es-MX` wurde behoben.
     
    | Sprache | Eingabe | Vorgelesenes nach Verbesserung |
    |---|---|---|
    | `es-MX` | 1,58 USD | un peso cincuenta y ocho centavos |
    | `es-ES` | 1,58 USD | un dólar cincuenta y ocho centavos |

    * Negative Währungen (z. B. „–325 €“) werden für folgende Sprachen unterstützt: `en-US`, `en-GB`, `fr-FR`, `it-IT`, `en-AU`, `en-CA`.

* Das Lesen von Adressen in `pt-PT` wurde verbessert.
* Die Ausspracheprobleme der Wörter „for“ und „four“ bei Natasha (`en-AU`) und Libby (`en-UK`) wurden behoben.  
* Fehler im Tool „Audio Content Creation“ wurden behoben:
    * Die zusätzliche unerwartete Pause nach dem zweiten Absatz wurde behoben.  
    * Das Feature „No break“ (keine Unterbrechung) wurde nach einem Regressionsfehler wieder hinzugefügt. 
    * Das zufällige Aktualisierungsproblem von Speech Studio wurden behoben.  

### <a name="samplessdk"></a>Beispiele/SDK

* JavaScript: Das Wiedergabeproblem in Firefox und Safari unter macOS und iOS wurde behoben. 

## <a name="speech-sdk-1121-2020-june-release"></a>Speech SDK 1.12.1: Release von Juni 2020
**Speech-Befehlszeilenschnittstelle (auch als SPX bezeichnet)**
-   Hinzugefügte Suchfeatures für die Hilfe in der Befehlszeilenschnittstelle:
    -   `spx help find --text TEXT`
    -   `spx help find --topic NAME`
-   Für die neu bereitgestellten APIs der Version 3.0 für Batch und Custom Speech aktualisiert:
    -   `spx help batch examples`
    -   `spx help csr examples`

**Neue Features**
-   **C\#, C++:** Sprechererkennung (Vorschauversion): Dieses Feature ermöglicht die Sprecheridentifikation („Wer spricht?“) und Sprecherüberprüfung („Ist der Sprecher die angegebene Person?“). Beginnen Sie mit der [Übersicht](./speaker-recognition-overview.md), und lesen Sie den Artikel [Grundlagen zur Sprechererkennung](./get-started-speaker-recognition.md) oder die [API-Referenzdokumentation](/rest/api/speakerrecognition/).

**Fehlerbehebungen**
-   **C\#, C++:** Die Mikrofonaufzeichnung funktionierte in 1.12 bei der Sprechererkennung nicht. Dies wurde behoben.
-   **JavaScript:** Fehler der Sprachsynthese in Firefox und Safari unter macOS und iOS wurden behoben.
-   Ein Fehler wurde behoben, bei dem es durch eine Zugriffsverletzung der Windows-Anwendungsüberprüfung bei der Unterhaltungstranskription von 8-Kanal-Datenströmen zu einem Absturz kam.
-   Es wurde ein Fehler behoben, bei dem es durch eine Zugriffsverletzung der Windows-Anwendungsüberprüfung bei der Konversationsübersetzung von mehreren Geräten zu einem Absturz kam.

**Beispiele**
-   **C#** : [Codebeispiel](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) für die Sprechererkennung.
-   **C++:** [Codebeispiel](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) für die Sprechererkennung.
-   **Java**: [Codebeispiel](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/intent-recognition) für die Absichtserkennung unter Android. 

**Abgekürzte Tests aufgrund von COVID-19:** Da wir in den letzten Wochen remote gearbeitet haben, konnten wir die manuellen Tests zur Überprüfung nicht im gewohnten Umfang durchführen. Wir haben keine Änderungen vorgenommen, die möglicherweise zu Kompatibilitätsproblemen geführt hätten, und alle unsere automatisierten Tests wurden bestanden. Falls wir doch entgegen aller Wahrscheinlichkeit etwas übersehen haben sollten, informieren Sie uns bitte auf [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Bleiben Sie gesund!


## <a name="speech-sdk-1120-2020-may-release"></a>Speech SDK 1.12.0: Release von Mai 2020
**Speech-Befehlszeilenschnittstelle (auch als SPX bezeichnet)**
- **SPX** ist ein neues Befehlszeilentool, mit dem Sie Aktionen wie Erkennung, Synthese, Übersetzung, Batch-Transkription und benutzerdefinierte Sprachverwaltung über die Befehlszeile ausführen können. Verwenden Sie es zum Testen des Speech-Diensts oder zum Erstellen von Skripts für die auszuführenden Aufgaben des Speech-Diensts. Das Tool steht [hier](./spx-overview.md) zum Download zur Verfügung. Dort finden Sie auch die Dokumentation.

**Neue Features**
- **Go**: Neue Unterstützung der Sprache Go für [Spracherkennung](./get-started-speech-to-text.md?pivots=programming-language-go) und [benutzerdefinierten Sprachassistenten](./quickstarts/voice-assistants.md?pivots=programming-language-go). Ihre Entwicklungsumgebung können Sie [hier](./quickstarts/setup-platform.md?pivots=programming-language-go) einrichten. Beispielcode finden Sie weiter unten im Abschnitt „Beispiele“. 
- **JavaScript:** Browserunterstützung für Sprachsynthese hinzugefügt. Die zugehörige Dokumentation finden Sie [hier](./get-started-text-to-speech.md?pivots=programming-language-JavaScript).
- **C++, C#, Java:** Unterstützung des neuen `KeywordRecognizer`-Objekts sowie neuer APIs unter Windows, Android, Linux und iOS. Lesen Sie die [Dokumentation](./custom-keyword-overview.md). Beispielcode finden Sie weiter unten im Abschnitt „Beispiele“. 
- **Java**: Konversation mit mehreren Geräten mit Übersetzungsunterstützung hinzugefügt. Die zugehörige Referenzdokumentation finden Sie [hier](/java/api/com.microsoft.cognitiveservices.speech.transcription).

**Verbesserungen und Optimierungen**
- **JavaScript:** Mikrofonimplementierung für Browser optimiert, um die Genauigkeit bei der Spracherkennung zu verbessern.
- **Java**: Bindungen mit direkter JNI-Implementierung ohne SWIG wurden umgestaltet. Durch diese Änderung wird die Bindungsgröße aller für Windows, Android, Linux und Mac verwendeten Java-Pakete um das Zehnfache verringert und die weitere Entwicklung der Speech SDK-Java-Implementierung vereinfacht.
- **Linux:** Die unterstützende [Dokumentation](./speech-sdk.md?tabs=linux) wurde mit den neuesten RHEL 7-spezifischen Anmerkungen aktualisiert.
- Die Verbindungslogik wurde verbessert, um im Falle von Dienst- oder Netzwerkfehlern mehrere Verbindungsversuche zu unternehmen.
- Die Speech-Schnellstartseite [portal.azure.com](https://portal.azure.com) wurde aktualisiert, um Entwickler beim nächsten Schritt für die Azure Speech-Journey zu unterstützen.

**Fehlerbehebungen**
- **C#, Java:** Ein [Problem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/587) beim Laden von SDK-Bibliotheken in Linux ARM wurde behoben (sowohl für die 32-Bit- als auch für die 64-Bit-Version).
- **C#** : Das explizite Löschen nativer Handles für die TranslationRecognizer-, IntentRecognizer- und Connection-Objekte wurde korrigiert.
- **C#** : Für das ConversationTranscriber-Objekt wurde die Lebensdauerverwaltung für Audioeingaben korrigiert.
- Es wurde ein Problem behoben, bei dem der Grund für das `IntentRecognizer`-Ergebnis nicht ordnungsgemäß festgelegt wurde, wenn Absichten aus einfachen Ausdrücken erkannt wurden.
- Es wurde ein Problem behoben, bei dem das `SpeechRecognitionEventArgs`-Ergebnisoffset nicht ordnungsgemäß festgelegt wurde.
- Es wurde eine Racebedingung behoben, bei der vom SDK versucht wurde, eine Netzwerknachricht zu senden, bevor die WebSocket-Verbindung hergestellt wurde. Dies war für `TranslationRecognizer` beim Hinzufügen von Teilnehmern reproduzierbar.
- Es wurden Arbeitsspeicherverluste in der Schlüsselworterkennungs-Engine korrigiert.

**Beispiele**
- **Go**: Schnellstartanleitungen für [Spracherkennung](./get-started-speech-to-text.md?pivots=programming-language-go) und [benutzerdefinierten Sprachassistenten](./quickstarts/voice-assistants.md?pivots=programming-language-go) hinzugefügt. Beispielcode finden Sie [hier](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples). 
- **JavaScript:** Schnellstartanleitungen für [Sprachsynthese](./get-started-text-to-speech.md?pivots=programming-language-javascript), [Übersetzung](./get-started-speech-translation.md?pivots=programming-language-csharp&tabs=script) und [Absichtserkennung](./quickstarts/intent-recognition.md?pivots=programming-language-javascript) hinzugefügt.
- Schlüsselworterkennungsbeispiele für [C\#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer) und [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer) (Android).  

**Abgekürzte Tests aufgrund von COVID-19:** Da wir in den letzten Wochen remote gearbeitet haben, konnten wir die manuellen Tests zur Überprüfung nicht im gewohnten Umfang durchführen. Wir haben keine Änderungen vorgenommen, die möglicherweise zu Kompatibilitätsproblemen geführt hätten, und alle unsere automatisierten Tests wurden bestanden. Falls wir etwas übersehen haben sollten, informieren Sie uns bitte auf [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Bleiben Sie gesund!

## <a name="speech-sdk-1110-2020-march-release"></a>Speech SDK 1.11.0: Release von März 2020
**Neue Features**
- Linux: Unterstützung für Red Hat Enterprise Linux (RHEL)/CentOS 7 x64 mit [Anweisungen](./how-to-configure-rhel-centos-7.md) zum Konfigurieren des Systems für Speech SDK hinzugefügt.
- Linux: Unterstützung für .NET Core C# unter Linux ARM32 und ARM64 hinzugefügt. Weitere Informationen finden Sie [hier](./speech-sdk.md?tabs=linux). 
- C#, C++: `UtteranceId` in `ConversationTranscriptionResult` hinzugefügt. Dies ist eine konsistente ID für alle Spracherkennungs-Zwischenergebnisse und -Endergebnisse. Ausführlichere Informationen für [C#](/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult) und [C++](/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult).
- Python: Unterstützung für `Language ID` wurde hinzugefügt. Siehe „speech_sample.py“ im [GitHub-Repository](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
- Windows: Unterstützung für komprimierte Audioeingabeformate auf der Windows-Plattform für alle Win32-Konsolenanwendungen hinzugefügt. Ausführlichere Informationen finden Sie [hier](./how-to-use-codec-compressed-audio-input-streams.md). 
- JavaScript: Unterstützung von Sprachsynthese (Text-to-Speech) in NodeJS. [Hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech)erhalten Sie weitere Informationen. 
- JavaScript: Fügen Sie neue APIs hinzu, um die Überprüfung aller gesendeten und empfangenen Nachrichten zu ermöglichen. [Hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript)erhalten Sie weitere Informationen. 
        
**Fehlerbehebungen**
- C#, C++: Es wurde ein Problem behoben, sodass `SendMessageAsync` jetzt binäre Nachrichten als binären Typ sendet. Ausführlichere Informationen für [C#](/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_) und [C++](/cpp/cognitive-services/speech/connection).
- C#, C++: Es wurde das Problem behoben, dass die Verwendung des `Connection MessageReceived`-Ereignisses einen Absturz verursachen kann, wenn `Recognizer` vor dem `Connection`-Objekt verworfen wird. Ausführlichere Informationen für [C#](/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived) und [C++](/cpp/cognitive-services/speech/connection#messagereceived).
- Android: Die Audiopuffergröße des Mikrofons wurde von 800 ms auf 100 ms verringert, um die Wartezeit zu reduzieren.
- Android: Es wurde ein [Problem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) beim x86-Android-Emulator in Android Studio behoben.
- JavaScript: Unterstützung für Regionen in China mit der `fromSubscription`-API hinzugefügt. Ausführlichere Informationen finden Sie [hier](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#fromsubscription-string--string-). 
- JavaScript: Fügen Sie weitere Fehlerinformationen zu Verbindungsfehlern aus NodeJS hinzu.
        
**Beispiele**
- Unity: Problem bei öffentlichem Absichtserkennungsbeispiel ist behoben, bei dem der LUIS-JSON-Import fehlgeschlagen ist. Ausführlichere Informationen finden Sie [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369).
- Python: Beispiel für `Language ID` hinzugefügt. Ausführlichere Informationen finden Sie [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py).
    
**Abgekürzte Tests aufgrund von COVID-19:** Da wir in den letzten Wochen remote gearbeitet haben, konnten wir die manuellen Tests zur Geräteüberprüfung nicht im gewohnten Umfang durchführen. Beispielsweise konnten die Mikrofoneingabe und Lautsprecherausgabe unter Linux, iOS und macOS nicht getestet werden. Wir haben keine Änderungen vorgenommen, die möglicherweise zu Beschädigungen auf diesen Plattformen geführt haben, und alle unsere automatisierten Tests wurden bestanden. Falls wir doch entgegen aller Wahrscheinlichkeit etwas übersehen haben sollten, informieren Sie uns bitte auf [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br> Vielen Dank für Ihre Unterstützung. Fragen können Sie wie immer auf [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) oder in [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/731) stellen. Auf diesen Plattformen können Sie auch Feedback geben.<br>
Bleiben Sie gesund!

## <a name="speech-sdk-1100-2020-february-release"></a>Speech SDK 1.10.0: Release von Februar 2020

**Neue Features**

 - Python-Pakete zur Unterstützung des neuen Python-Release 3.8 hinzugefügt
 - x64-Unterstützung für Red Hat Enterprise Linux (RHEL)/CentOS 8 (C++, C#, Java, Python)
   > [!NOTE] 
   > Kunden müssen OpenSSL wie [hier beschrieben](./how-to-configure-openssl-linux.md) konfigurieren.
 - Linux ARM32-Unterstützung für Debian und Ubuntu
 - Von „DialogServiceConnector“ wird jetzt der optionale Parameter „bot ID“ für „BotFrameworkConfig“ unterstützt. Dieser Parameter ermöglicht die Verwendung mehrerer Direct Line Speech-Bots mit einer einzelnen Azure Speech-Ressource. Ohne Angabe des Parameters wird der (auf der Direct Line Speech-Kanalkonfigurationsseite festgelegte) Standardbot verwendet.
 - „DialogServiceConnector“ verfügt nun über die Eigenschaft „SpeechActivityTemplate“. Der Inhalt dieser JSON-Zeichenfolge wird von Direct Line Speech verwendet, um ein breites Spektrum an unterstützten Feldern in allen Aktivitäten vorab aufzufüllen, die einen Direct Line Speech-Bot erreichen. Hierzu zählen auch Aktivitäten, die als Reaktion auf Ereignisse automatisch generiert werden (beispielsweise Spracherkennung).
 - Von der Sprachsynthese wird nun der Abonnementschlüssel für die Authentifizierung verwendet. Dadurch verringert sich die Wartezeit für das erste Byte des ersten Syntheseergebnisses nach der Erstellung eines Synthesizers.
 - Verringerung der durchschnittlichen Wortfehlerrate um 18,6 Prozent dank aktualisierter Spracherkennungsmodelle für 19 Gebietsschemas (es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, ca-ES, zh-TW, th-TH, pt-PT, tr-TR). Die neuen Modelle führen zu erheblichen Verbesserungen in verschiedenen Bereichen. Hierzu zählen unter anderem Diktat, Callcentertranskription und Videoindizierung.

**Fehlerbehebungen**

 - Fehler behoben, der dazu führte, dass von der Unterhaltungstranskription in Java-APIs nicht ordnungsgemäß gewartet wurde 
 - Xamarin-bezogenes [GitHub-Problem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363) mit dem Android-x86-Emulator behoben
 - Fehlende (Get|Set)Property-Methoden zu „AudioConfig“ hinzugefügt
 - Fehler bei der Sprachsynthese behoben, der dazu führte, dass der Audiodatenstrom (audioDataStream) im Falle eines Verbindungsfehlers nicht beendet werden konnte
 - Die Verwendung eines Endpunkts ohne Region hatte USP-Fehler für die Konversationsübersetzung zur Folge.
 - Für die ID-Generierung in universellen Windows-Anwendungen wird nun ein Algorithmus für eine angemessen eindeutige GUID verwendet. Zuvor wurde ungewollt standardmäßig eine Stubimplementierung verwendet, die bei umfangreichen Interaktionen häufig zu Konflikten führte.
 
 **Beispiele**
 
 - Unity-Beispiel für die Verwendung des Speech SDK mit [Unity-Mikrofon und Pushmodusstreaming](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)

**Weitere Änderungen**

 - [OpenSSL-Konfigurationsdokumentation für Linux aktualisiert](./how-to-configure-openssl-linux.md)

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0: Release 2020-January

**Neue Features**

- Mehrgerätekonversation: Verbinden Sie mehrere Geräte mit derselben sprach- oder textbasierten Konversation, und übersetzen Sie optional die zwischen ihnen gesendeten Nachrichten. Weitere Informationen finden Sie in [diesem Artikel](multi-device-conversation.md). 
- Unterstützung für die Schlüsselworterkennung wurde für das AAR-Paket für Android und für x86- und x64-Versionen hinzugefügt. 
- Objective-C: Methoden `SendMessage` und `SetMessageProperty` wurden dem `Connection`-Objekt hinzugefügt. Die zugehörige Dokumentation finden Sie [hier](/objectivec/cognitive-services/speech/spxconnection).
- Die TTS-API in C++ unterstützt jetzt `std::wstring` als Texteingabe für die Synthese. Dadurch ist es nicht mehr erforderlich, den Typ wstring vor der Übergabe an das SDK in string zu konvertieren. Ausführlichere Informationen finden Sie [hier](/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync). 
- C#: [Sprach-ID](./how-to-automatic-language-detection.md?pivots=programming-language-csharp) und [Ausgangssprachenkonfiguration](./how-to-specify-source-language.md?pivots=programming-language-csharp) sind jetzt verfügbar.
- JavaScript: Dem `Connection`-Objekt wurde eine Funktion für die Weiterleitung benutzerdefinierter Nachrichten vom Speech-Dienst als Rückruf von `receivedServiceMessage` zu hinzugefügt.
- JavaScript: Unterstützung für `FromHost API` wurde hinzugefügt, um die Verwendung mit lokalen Containern und Sovereign Clouds zu vereinfachen Die zugehörige Dokumentation finden Sie [hier](speech-container-howto.md).
- JavaScript: `NODE_TLS_REJECT_UNAUTHORIZED` wird nun dank eines Beitrags von [orgads](https://github.com/orgads) berücksichtigt. Ausführlichere Informationen finden Sie [hier](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75).

**Wichtige Änderungen**

- `OpenSSL` wurde auf Version 1.1.1b aktualisiert und ist statisch mit der Kernbibliothek des Speech SDK für Linux verknüpft. Dies kann zu einer Unterbrechung führen, wenn `OpenSSL` für Ihren Posteingang nicht im Verzeichnis `/usr/lib/ssl` im System installiert wurde. In [unserer Dokumentation](how-to-configure-openssl-linux.md) in den Dokumenten zum Speech SDK finden Sie Möglichkeiten, wie Sie das Problem umgehen können.
- Wir haben den in C# für `WordLevelTimingResult.Offset` zurückgegebenen Datentyp von `int` in `long` geändert, um den Zugriff auf `WordLevelTimingResults` zu ermöglichen, wenn Sprachdaten länger als 2 Minuten sind.
- `PushAudioInputStream` und `PullAudioInputStream` senden nun WAV-Headerinformationen an den Speech-Dienst basierend auf dem `AudioStreamFormat`, das bei der Erstellung optional angegeben werden kann. Kunden müssen nun das [unterstützte Audioeingabeformat](how-to-use-audio-input-streams.md) verwenden. Alle anderen Formate führen zu weniger guten Erkennungsergebnissen oder anderen Problemen. 

**Fehlerbehebungen**

- Weitere Informationen finden Sie im obigen `OpenSSL`-Update unter „Wichtige Änderungen“. Wir haben sowohl einen zeitweiligen Absturz als auch ein Leistungsproblem (Sperrkonflikte bei hoher Auslastung) in Linux und Java korrigiert. 
- Java: Es wurden Verbesserungen am Objektabschluss in Szenarien mit hoher Parallelität vorgenommen.
- Das NuGet-Paket wurde umstrukturiert. Wir haben die drei Kopien von `Microsoft.CognitiveServices.Speech.core.dll` und `Microsoft.CognitiveServices.Speech.extension.kws.dll` im Ordner „lib“ entfernt, sodass das NuGet-Paket nun kleiner ist und schneller heruntergeladen werden kann. Außerdem haben wir Header hinzugefügt, die zum Kompilieren einiger nativer C++-Apps benötigt werden.
- Die korrigierten Schnellstartbeispiele finden Sie [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp). Diese wurden ohne Anzeige der Ausnahme „Mikrofon wurde nicht gefunden“ unter Linux, macOS und Windows beendet.
- Ein SDK-Absturz bei langen Spracherkennungsergebnissen für bestimmte Codepfade wie in [diesem Beispiel](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp) wurde korrigiert.
- Ein Fehler bei der SDK-Bereitstellung in Azure-Web-App-Umgebungen wurde behoben, um [dieses Kundenproblem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396) zu beseitigen.
- Ein TTS-Fehler bei der Verwendung mehrerer `<voice>`- oder `<audio>`-Tags wurde behoben, um [dieses Kundenproblems](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433) zu beseitigen. 
- Ein TTS 401-Fehler beim Wiederherstellen des SDK nach dem Anhalten wurde behoben.
- JavaScript: Ein zirkulärer Import von Audiodaten wurde dank eines Beitrags von [euirim](https://github.com/euirim) korrigiert. 
- JavaScript: Unterstützung für das Festlegen von Diensteigenschaften wurde wie in 1.7 hinzugefügt.
- JavaScript: Ein Problem wurde behoben, bei dem ein Verbindungsfehler zu kontinuierlichen erfolglosen WebSocket-Verbindungsversuchen führen konnte.

**Beispiele**

- Es wurde ein [Beispiel für die Schlüsselworterkennung für Android](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo) hinzugefügt.
- Es wurde ein [TTS-Beispiel für das Serverszenario](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs) hinzugefügt.
- Es wurden [Schnellstarts für die Mehrgerätekonversation in C# und C++](quickstarts/multi-device-conversation.md) hinzugefügt.

**Weitere Änderungen**

- Die Größe der SDK-Kernbibliothek unter Android wurde optimiert.
- Das SDK ab Version 1.9.0 unterstützt sowohl `int`- als auch `string`-Typen im Feld für die Stimmensignaturversion für die Unterhaltungstranskription.

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0: Release von November 2019

**Neue Features**

- `FromHost()`-API hinzugefügt, um die Verwendung mit lokalen Containern und Sovereign Clouds zu vereinfachen
- Automatische Erkennung der Ausgangssprache für die Spracherkennung hinzugefügt (in Java und C++)
- `SourceLanguageConfig`-Objekt zur Angabe erwarteter Ausgangssprachen für die Spracherkennung hinzugefügt (in Java und C++)
- `KeywordRecognizer`-Unterstützung unter Windows (UWP), Android und iOS über die NuGet- und Unity-Pakete hinzugefügt
- Java-Remoteunterhaltungs-API für die Unterhaltungstranskription in asynchronen Batches hinzugefügt

**Wichtige Änderungen**

- Die Funktionen für die Unterhaltungstranskription wurden unter den Namespace `Microsoft.CognitiveServices.Speech.Transcription` verschoben.
- Ein Teil der Unterhaltungstranskriptionsmethoden wurde in die neue `Conversation`-Klasse verschoben.
- Die Unterstützung für 32-Bit-iOS (ARMv7 und x86) wurde eingestellt.

**Fehlerbehebungen**

- Ein Absturz wurde behoben, der auftrat, wenn die lokale `KeywordRecognizer`-Instanz ohne gültigen Abonnementschlüssel für den Speech-Dienst verwendet wurde.

**Beispiele**

- Xamarin-Beispiel für `KeywordRecognizer`
- Unity-Beispiel für `KeywordRecognizer`
- C++- und Java-Beispiele für die automatische Erkennung der Ausgangssprache

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0: Release von September 2019

**Neue Features**

- Unterstützung der Betaversion für Xamarin unter der universellen Windows-Plattform (UWP), Android und iOS wurde hinzugefügt
- iOS-Unterstützung für Unity wurde hinzugefügt
- Unterstützung von `Compressed`-Eingaben für ALaw, Mulaw, FLAC unter Android, iOS und Linux hinzugefügt
- `SendMessageAsync` in der Klasse `Connection` zum Senden einer Nachricht an einen Dienst hinzugefügt
- `SetMessageProperty` in der Klasse `Connection` zum Festlegen der Eigenschaft einer Nachricht hinzugefügt
- Die Sprachsynthese hat Bindungen für Java (JRE und Android), Python, Swift und Objective-C hinzugefügt.
- TTS hat die Unterstützung der Wiedergabe für macOS, iOS und Android hinzugefügt
- Es wurden Informationen zur „Wortgrenze“ für TTS hinzugefügt

**Fehlerbehebungen**

- IL2CPP-Buildproblem in Unity 2019 für Android wurde behoben
- Es wurde ein Problem behoben, bei dem falsch formatierte Header in der Eingabe von WAV-Dateien falsch verarbeitet wurden
- Es wurde ein Problem behoben, bei dem UUIDs in einigen Verbindungseigenschaften nicht eindeutig waren
- Es wurden einige Warnungen bezüglich Spezifizierer für die NULL-Zulässigkeit in den Swift-Bindungen behoben (möglicherweise sind kleine Codeänderungen erforderlich)
- Es wurde ein Fehler behoben, der dazu führte, dass WebSocket-Verbindungen unter Netzwerklast nicht ordnungsgemäß geschlossen wurden
- Problem unter Android behoben, das gelegentlich dazu führt, dass `DialogServiceConnector` doppelte Eindruck-IDs verwendet
- Es wurden Verbesserungen an der Stabilität von Verbindungen über Interaktionen mit Mehrfachdurchläufen und an der Berichterstellung bei Fehlern vorgenommen (über Ereignisse vom Typ `Canceled`), wenn sie mit `DialogServiceConnector` auftreten.
- `DialogServiceConnector`-Sitzungsstarts stellen jetzt ordnungsgemäß Ereignisse bereit, einschließlich des Aufrufs von `ListenOnceAsync()`, während `StartKeywordRecognitionAsync()` aktiv ist.
- Es wurde ein Absturzproblem behoben, das mit dem Empfangen von `DialogServiceConnector`-Aktivitäten verbunden war.

**Beispiele**

- Schnellstart für Xamarin
- Aktualisierter CPP-Schnellstart mit Linux ARM64-Informationen
- Aktualisierter Unity-Schnellstart mit iOS-Informationen

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0: Release von Juni 2019

**Beispiele**

- Schnellstartbeispiele für Sprachsynthese auf UWP und Unity
- Schnellstartbeispiel für Swift unter iOS
- Unity-Beispiele für Sprach- und Absichtserkennung sowie Übersetzung
- Schnellstartbeispiele für `DialogServiceConnector` aktualisiert

**Verbesserungen/Änderungen**

- Dialog „Namespace“:
  - `SpeechBotConnector` wurde in `DialogServiceConnector` umbenannt.
  - `BotConfig` wurde in `DialogServiceConfig` umbenannt.
  - `BotConfig::FromChannelSecret()` wurde `DialogServiceConfig::FromBotSecret()` neu zugeordnet.
  - Alle vorhandenen Direct Line Speech-Clients werden nach der Umbenennung weiterhin unterstützt.
- Aktualisierung des TTS-REST-Adapter zur Unterstützung von Proxys, dauerhafte Verbindung
- Verbesserung von Fehlermeldungen, wenn eine ungültige Region übergeben wird.
- Swift/Objective-C:
  - Verbesserte Fehlerberichterstellung: Methoden, die zu einem Fehler führen können, sind jetzt in zwei Versionen vorhanden: Eine, die ein `NSError`-Objekt für die Fehlerbehandlung bereitstellt, und eine, das eine Ausnahme auslöst. Das erste wird für Swift verfügbar gemacht. Diese Änderung erfordert Anpassungen an vorhandenem Swift-Code.
  - Verbesserte Behandlung von Ereignissen

**Fehlerbehebungen**

- Korrektur für TTS: Hierbei führte `SpeakTextAsync` die Rückgabe aus, ohne zu warten, bis das Audiorendering abgeschlossen war.
- Korrektur für das Marshalling von Zeichenfolgen in C#, um vollständige Sprachunterstützung zu ermöglichen.
- Korrektur für ein .NET Core-App-Problem beim Laden der Core-Bibliothek mit dem Zielframework net461 in Beispielen.
- Korrektur für gelegentlich Probleme beim Bereitstellen nativer Bibliotheken im Ausgabeordner in Beispielen.
- Korrektur für das zuverlässige Schließen von WebSockets.
- Korrektur für mögliche Abstürze beim Öffnen einer Verbindung bei hoher Auslastung unter Linux
- Korrektur für fehlende Metadaten im Frameworkbündel für macOS.
- Korrektur für Probleme mit `pip install --user` unter Windows.

## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

Dies ist ein Fehlerbehebungsrelease und betrifft nur das native/verwaltete SDK. Es betrifft nicht die JavaScript-Version des SDK.

**Fehlerbehebungen**

- Fehlerbehebung bei FromSubscription bei Verwendung mit Unterhaltungstranskription.
- Fehlerbehebung bei der Schlüsselworterkennung für Sprachassistenten.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0: Release von Mai 2019

**Neue Features**

- Die Schlüsselworterkennung (Keyword Spotting Functionality, KWS) ist für Windows und Linux verfügbar. Die KWS-Funktionalität kann u. U. mit jedem Mikrofontyp verwendet werden, offiziell wird KWS derzeit jedoch nur für die Mikrofonarrays in der Azure Kinect DK-Hardware oder im Speech Devices SDK unterstützt.
- Begriffshinweisfunktionalität ist über das SDK verfügbar. Weitere Informationen finden Sie [hier](./get-started-speech-to-text.md).
- Unterhaltungstranskriptionsfunktionalität ist über das SDK verfügbar. Klicken Sie [hier](./conversation-transcription.md).
- Unterstützung für Sprachassistenten über den Direct Line Speech-Kanal wurde hinzugefügt.

**Beispiele**

- Beispiele für neue Funktionen oder neue Dienste, die vom SDK unterstützt werden, wurden hinzugefügt.

**Verbesserungen/Änderungen**

- Verschiedene Erkennungseigenschaften wurden hinzugefügt, um das Dienstverhalten oder Dienstergebnisse anzupassen (z. B. Maskieren von Obszönitäten).
- Sie können die Erkennung jetzt über die Standardkonfigurationseigenschaften konfigurieren, auch wenn Sie den Erkenner `FromEndpoint` erstellt haben.
- Objective-C: Die Eigenschaft `OutputFormat` wurde zu `SPXSpeechConfiguration` hinzugefügt.
- Das SDK unterstützt jetzt Debian 9 als Linux-Distribution.

**Fehlerbehebungen**

- Es wurde ein Problem behoben, bei dem die Sprecherressource in der Sprachsynthese zu früh zerstört wurde.

## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

Dies ist ein Fehlerbehebungsrelease und betrifft nur das native/verwaltete SDK. Es betrifft nicht die JavaScript-Version des SDK.

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

Dieses Release gilt nur für JavaScript. Es wurden keine Features hinzugefügt. Die folgenden Fehler wurden behoben:

- Verhindern Sie das Laden von „https-proxy-agent“ durch Webpack.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0: Release von April 2019

**Neue Features**

- Das SDK unterstützt jetzt den Text-Sprach-Dienst als Betaversion. Dies wird unter Windows- und Linux-Desktops für C++ und C# unterstützt. Weitere Informationen finden Sie in der [Übersicht über die Sprachsynthese](text-to-speech.md#get-started).
- Das SDK unterstützt jetzt MP3- und Opus/OGG-Audiodateien als Streameingabedateien. Dieses Feature steht nur unter Linux mit C++ und C# zur Verfügung und befindet sich derzeit in der Betaversion (weitere Details finden Sie [hier](how-to-use-codec-compressed-audio-input-streams.md)).
- Das Speech SDK für Java, .NET Core, C++ und Objective-C unterstützt nun auch macOS. Die Objective-C-Unterstützung für macOS befindet sich derzeit in der Betaphase.
- iOS: Das Speech SDK für iOS (Objective-C) wird jetzt auch als ein CocoaPod veröffentlicht.
- JavaScript: Unterstützung von nicht standardisierten Mikrofonen als Eingabegeräte.
- JavaScript: Proxyunterstützung für Node.js.

**Beispiele**

- Beispiele für die Verwendung des Speech SDK mit C++ und Objective-C unter macOS wurden hinzugefügt.
- Beispiele zur Veranschaulichung der Verwendung des Text-zu-Sprache-Diensts wurden hinzugefügt.

**Verbesserungen/Änderungen**

- Python: Zusätzliche Eigenschaften der Erkennungsergebnisse werden jetzt über die `properties`-Eigenschaft verfügbar gemacht.
- Zur weiteren Unterstützung beim Entwickeln und Debuggen können Sie die Informationen aus SDK-Protokollierung und Diagnose in eine Protokolldatei umleiten (weitere Details finden Sie [hier](how-to-use-logging.md)).
- JavaScript: Verbesserte Prozessleistung bei Audiodaten.

**Fehlerbehebungen**

- Mac/iOS: Ein Fehler, der zu einer langen Wartezeit geführt hat, wenn keine Verbindung mit Speech Services hergestellt werden konnte, wurde behoben.
- Python: verbesserte Fehlerbehandlung für Argumente in Python-Rückrufen.
- JavaScript: Ein Fehler bei Statusmeldungen nach dem Ende der Spracheingabe mit RequestSession wurde behoben.

## <a name="speech-sdk-131-2019-february-refresh"></a>Sprach-SDK 1.3.1: Aktualisierung von Februar 2019

Dies ist ein Fehlerbehebungsrelease und betrifft nur das native/verwaltete SDK. Es betrifft nicht die JavaScript-Version des SDK.

**Fehlerbehebung**

- Korrigiert einen Speicherverlust bei der Verwendung von Mikrofoneingabe. Streambasierte oder Dateieingaben sind nicht betroffen.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0: Version von Februar 2019

**Neue Features**

- Das Speech SDK unterstützt die Auswahl des Eingangsmikrofons über die `AudioConfig`-Klasse. Dadurch können Sie Audiodaten über ein anderes als das Standardmikrofon an den Spracherkennungsdienst streamen. Weitere Informationen finden Sie in der Dokumentation, in der die [Auswahl eines Audioeingabegeräts](how-to-select-audio-input-devices.md) beschrieben wird. Für JavaScript ist diese Funktion noch nicht verfügbar.
- Das Speech SDK unterstützt jetzt Unity in einer Betaversion. Senden Sie uns Feedback über den Abschnitt „Issue“ im [GitHub-Beispielrepository](https://aka.ms/csspeech/samples). Dieses Release unterstützt Unity unter Windows x86 und x64 (Desktopanwendungen oder Anwendungen der universellen Windows-Plattform) und unter Android (ARM32/64, x86). Weitere Informationen finden Sie in unserem [Unity-Schnellstart](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=unity).
- Die Datei `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (im Lieferumfang von früheren Releases enthalten) ist nicht mehr erforderlich. Die Funktion ist jetzt in das Core-SDK integriert.

**Beispiele**

Die folgenden neuen Inhalte stehen in unserem [Beispielrepository](https://aka.ms/csspeech/samples) zur Verfügung:

- Weitere Beispiele für `AudioConfig.FromMicrophoneInput`
- Weitere Python-Beispiele für Absichtserkennung und Übersetzung.
- Weitere Beispiele für die Verwendung des Objekts `Connection` in iOS
- Weitere Java-Beispiele für die Übersetzung mit Audioausgabe.
- Neues Beispiel für die Verwendung der [REST-API zur Batchtranskription](batch-transcription.md).

**Verbesserungen/Änderungen**

- Python
  - Verbesserte Parameterüberprüfung und Fehlermeldungen in `SpeechConfig`
  - Unterstützung für das Objekt `Connection` hinzugefügt
  - Unterstützung für 32-Bit-Python (x86) unter Windows.
  - Das Speech SDK für Python befindet sich nicht mehr in der Betaversion.
- iOS
  - Das SDK wird jetzt für das iOS SDK, Version 12.1, erstellt.
  - Das SDK unterstützt jetzt die iOS-Versionen 9.2 und höher.
  - Verbesserte Referenzdokumentation und Korrektur mehrerer Eigenschaftsnamen.
- JavaScript
  - Unterstützung für das Objekt `Connection` hinzugefügt
  - Hinzugefügte Typdefinitionsdateien für JavaScript-Pakete
  - Anfangsunterstützung und Implementierung für Phrasenhinweise.
  - Rückgabe der Eigenschaftensammlung mit Dienst-JSON für die Erkennung.
- Windows-DLLs enthalten jetzt eine Versionsressource.
- Wenn Sie eine `FromEndpoint`-Erkennung erstellen, können Sie der Endpunkt-URL direkt Parameter hinzufügen. Mithilfe von `FromEndpoint` können Sie die Erkennung nicht über die Standardkonfigurationseigenschaften konfigurieren.

**Fehlerbehebungen**

- Leere Angaben für Proxybenutzername und Proxykennwort wurden nicht ordnungsgemäß behandelt. Wenn Sie in diesem Release den Proxybenutzernamen und das Proxykennwort auf eine leere Zeichenfolge festlegen, werden diese bei der Verbindungsherstellung mit dem Proxy nicht übermittelt.
- Vom SDK erstellte SessionId-Angaben wurden&nbsp;für einige Sprachen/Umgebungen nicht immer wirklich zufällig gewählt. Es wurde eine Initialisierung des Zufallsgenerators hinzugefügt, um dieses Problem zu beheben.
- Verbesserte Verarbeitung des Autorisierungstokens. Wenn Sie ein Autorisierungstoken verwenden möchten, geben Sie es in `SpeechConfig` an, und lassen Sie den Abonnementschlüssel leer. Erstellen Sie die Erkennung dann wie gewohnt.
- In einigen Fällen wurde das Objekt `Connection` nicht ordnungsgemäß freigegeben. Dieses Problem wurde behoben.
- Das JavaScript-Beispiel wurde korrigiert, um die Audioausgabe für die Übersetzungssynthese auch in Safari zu unterstützen.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Dieses Release gilt nur für JavaScript. Es wurden keine Features hinzugefügt. Die folgenden Fehler wurden behoben:

- Ende des Datenstroms wird bei turn.end und nicht bei speech.end ausgelöst.
- In Audiopump wurde der Fehler behoben, dass der nächste Sendevorgang nicht geplant wurde, wenn beim aktuellen Sendevorgang ein Fehler auftrat.
- Die kontinuierliche Erkennung mit Authentifizierungstoken wurde korrigiert.
- Programmfehlerbehebung für verschiedene Erkennungen/Endpunkte.
- Verbesserungen bei der Dokumentation.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0: Release von Dezember 2018

**Neue Features**

- Python
  - Die Betaversion der Python-Unterstützung (ab 3.5) ist mit diesem Release verfügbar. Weitere Informationen finden Sie hier (quickstart-python.md).
- JavaScript
  - Das Speech SDK für JavaScript wird jetzt als Open-Source-Code bereitgestellt. Der Quellcode steht auf [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js)zur Verfügung.
  - Node.js wird jetzt unterstützt. Weitere Informationen finden Sie [hier](./get-started-speech-to-text.md).
  - Die Längenbeschränkung für Audiositzungen wurde entfernt. Die Verbindungswiederherstellung erfolgt automatisch im Hintergrund.
- `Connection`-Objekt
  - Über `Recognizer` kann auf ein Objekt vom Typ `Connection` zugegriffen werden. Mit diesem Objekt können Sie die Dienstverbindung explizit initiieren und Verbindungsherstellungs- und Verbindungstrennungsereignisse abonnieren.
    (Für JavaScript und Python ist diese Funktion noch nicht verfügbar.)
- Unterstützung von Ubuntu 18.04
- Android
  - ProGuard-Unterstützung während der APK-Generierung aktiviert

**Verbesserungen**

- Verbesserungen bei der internen Threadverwendung (weniger Threads, Sperren, Mutexe)
- Verbesserte Fehlerberichterstellung/-informationen. In einigen Fällen wurden Fehlermeldungen nicht ordnungsgemäß verteilt.
- Entwicklungsabhängigkeiten in JavaScript wurden für die Verwendung aktueller Module aktualisiert.

**Fehlerbehebungen**

- Arbeitsspeicherverluste aufgrund eines Typenkonflikts in `RecognizeAsync` behoben
- In einigen Fällen sind Ausnahmen verloren gegangen.
- Behebung des Arbeitsspeicherverlusts in Übersetzungsereignisargumenten
- Sperrproblem bei der Verbindungswiederherstellung in langen Sitzungen behoben
- Problem behoben, dass dazu führen konnte, dass das Endergebnis für fehlerhafte Übersetzungen verpasst wird.
- C#: Wenn im Hauptthread nicht auf einen Vorgang vom Typ `async` gewartet wurde, konnte es vorkommen, dass die Erkennung vor Abschluss der asynchronen Aufgabe entfernt wurde.
- Java: Problem behoben, das zum Absturz des virtuellen Java-Computers geführt hat
- Objective-C: Enumerationszuordnung korrigiert. Anstelle von `RecognizingIntent` wurde „RecognizedIntent“ zurückgegeben.
- JavaScript: Standardausgabeformat in `SpeechConfig` auf „einfach“ festgelegt
- JavaScript: Beseitigung der Inkonsistenz zwischen Eigenschaften des Konfigurationsobjekts in JavaScript und anderen Sprachen

**Beispiele**

- Mehrere Beispiele aktualisiert und korrigiert (z.B. die Ausgabestimmen für die Übersetzung).
- Node.js-Beispiele zum [Beispielrepository](https://aka.ms/csspeech/samples) hinzugefügt

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Neue Features**

- Unterstützung für Android x86/x64.
- Proxyunterstützung: Im `SpeechConfig`-Objekt können Sie jetzt eine Funktion aufrufen, um die Proxyinformationen (Hostname, Port, Benutzername und Kennwort) festzulegen. Dieses Feature ist in iOS noch nicht verfügbar.
- Verbesserte Fehlercodes und Meldungen. Wenn eine Erkennung einen Fehler zurückgab, wurde dadurch bereits `Reason` (im abgebrochenen Ereignis) oder `CancellationDetails` (im Erkennungsergebnis) auf `Error` festgelegt. Das abgebrochene Ereignis enthält jetzt zwei zusätzliche Member: `ErrorCode` und `ErrorDetails`. Wenn der Server zusätzliche Fehlerinformationen mit dem Fehler zurückgibt, sind diese jetzt in den neuen Membern verfügbar.

**Verbesserungen**

- In der Konfiguration der Erkennung wurde eine zusätzliche Überprüfung hinzugefügt, und es wurde eine zusätzliche Fehlermeldung hinzugefügt.
- Die Verarbeitung von langen Pausen mitten in einer Audiodatei wurde verbessert.
- NuGet-Paket: Für .NET Framework-Projekte wird die Erstellung mit AnyCPU-Konfiguration verhindert.

**Fehlerbehebungen**

- In Erkennungen wurden verschiedene Ausnahmen behoben. Darüber hinaus werden Ausnahmen abgefangen und in Ereignisse vom Typ `Canceled` konvertiert.
- Ein Arbeitsspeicherverlust in der Eigenschaftenverwaltung wurde behoben.
- Es wurde ein Fehler behoben, bei dem eine Audioeingabedatei zum Absturz der Erkennung führen konnte.
- Es wurde ein Fehler behoben, bei dem nach dem Ereignis zum Beenden einer Sitzung weiter Ereignisse empfangen werden konnten.
- Einige Racebedingungen im Threading wurden korrigiert.
- Ein iOS-Kompatibilitätsproblem wurde behoben, das zu einem Absturz führen konnte.
- Verbesserungen bei der Stabilität für die Android-Mikrofonunterstützung.
- Es wurde ein Fehler behoben, bei dem eine Erkennung in JavaScript die Erkennungssprache ignorierte.
- Es wurde ein Fehler behoben, der (in einigen Fällen) das Festlegen von `EndpointId` in JavaScript verhinderte.
- Die Parameterreihenfolge in AddIntent in JavaScript wurde geändert, und es wurde eine fehlende JavaScript-Signatur für `AddIntent` hinzugefügt.

**Beispiele**

- Dem [Beispielrepository](https://aka.ms/csspeech/samples) wurden C++- und C#-Beispiele für die Verwendung von Pull- und Pushstreams hinzugefügt.

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

Verbesserte Zuverlässigkeit und Fehlerbehebungen:

- Ein potenziell schwerwiegender Fehler aufgrund einer Racebedingung bei der Löscherkennung wurde behoben.
- Ein potenziell schwerwiegender Fehler bei nicht festgelegten Eigenschaften wurde behoben.
- Zusätzliche Fehler- und Parameterüberprüfungen wurden hinzugefügt.
- Objective-C: Ein potenziell schwerwiegender Fehler durch Namensüberschreibungen in NSString wurde behoben.
- Objective-C: Sichtbarkeit der API wurde angepasst
- JavaScript: Korrektur in Bezug auf Ereignisse und deren Nutzlasten.
- Verbesserungen bei der Dokumentation.

Im [Beispielrepository](https://aka.ms/csspeech/samples) wurde ein neues Beispiel für JavaScript hinzugefügt.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: Release von September 2018

**Neue Features**

- Unterstützung für Objective-C unter iOS. Sehen Sie sich unseren [Objective-C-Schnellstart für iOS](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios/from-microphone) an.
- Unterstützung für JavaScript im Browser. Sehen Sich unseren [JavaScript-Schnellstart](./get-started-speech-to-text.md) an.

**Wichtige Änderungen**

- Mit diesem Release werden einige Breaking Changes eingeführt.
  Ausführliche Informationen finden Sie auf [dieser Seite](https://aka.ms/csspeech/breakingchanges_1_0_0).

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: Release von August 2018

**Neue Features**

- Mit dem Speech SDK erstellte UWP-Apps erfüllen nun die Anforderungen des Windows App Certification Kit (WACK).
  Sehen Sie sich den [UWP-Schnellstart](./get-started-speech-to-text.md?pivots=programming-language-chsarp&tabs=uwp) an.
- Unterstützung für .NET Standard 2.0 unter Linux (Ubuntu 16.04 x 64)
- Experimentell: Unterstützung für Java 8 unter Windows (64 Bit) und Linux (Ubuntu 16.04 x64).
  Sehen Sie sich den [Schnellstart zur Java Runtime Environment](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=jre) an.

**Funktionale Änderung**

- Es werden weitere Detailinformationen zu Verbindungsfehlern verfügbar gemacht.

**Wichtige Änderungen**

- In Java (Android) erfordert die `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate`-Funktion keinen Path-Parameter mehr. Der Pfad wird nun auf allen unterstützten Plattformen automatisch erkannt.
- Der get-Accessor der `EndpointUrl`-Eigenschaft in Java und C# wurde entfernt.

**Fehlerbehebungen**

- In Java werden die Ergebnisse der Audiosynthese in der Übersetzungserkennung jetzt implementiert.
- Ein Problem wurde behoben, das inaktive Threads und eine erhöhte Anzahl von offenen und nicht verwendeten Sockets verursachen konnte.
- Ein Problem wurde behoben, das dazu führen konnte, dass lange ausgeführte Erkennungen während der Übertragung beendet wurden.
- Eine Racebedingung beim Herunterfahren der Erkennung wurde behoben.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: Release von Juli 2018

**Neue Features**

- Unterstützung für Android-Plattform (API 23: Android 6.0 Marshmallow oder höher). Sehen Sie sich den [Android-Schnellstart](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=android) an.
- Unterstützung für .NET Standard 2.0 unter Windows. Sehen Sie sich den [.NET Core-Schnellstart](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) an.
- Experimentell: Unterstützung für UWP unter Windows (Version 1709 oder höher).
  - Sehen Sie sich den [UWP-Schnellstart](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) an.
  - Hinweis: Mit dem Speech SDK erstellte UWP-Apps erfüllen die Anforderungen des Windows App Certification Kit (WACK) noch nicht.
- Unterstützung einer lang andauernden Erkennung mit automatischer erneuter Verbindungsherstellung.

**Funktionale Änderungen**

- `StartContinuousRecognitionAsync()` unterstützt eine lang andauernde Erkennung.
- Das Erkennungsergebnis enthält mehr Felder. Versatz vom Audiobeginn und Dauer (beides in Takten) des erkannten Texts und weitere Werte, die den Erkennungsstatus darstellen, z.B. `InitialSilenceTimeout` und `InitialBabbleTimeout`.
- Unterstützung für AuthorizationToken zum Erstellen von Factoryinstanzen.

**Wichtige Änderungen**

- Erkennungsereignisse: Der `NoMatch`-Ereignistyp wurde mit dem `Error`-Ereignis zusammengeführt.
- SpeechOutputFormat in C# wurde in `OutputFormat` umbenannt, um mit C++ konsistent zu bleiben.
- Der Rückgabetyp einiger Methoden der `AudioInputStream`-Schnittstelle wurde geringfügig geändert:
  - In Java gibt die `read`-Methode jetzt `long` anstelle von `int` zurück.
  - In C# gibt die `Read`-Methode jetzt `uint` anstelle von `int` zurück.
  - In C++ geben die `Read`- und die `GetFormat`-Methoden jetzt `size_t` anstelle von `int` zurück.
- C++: Instanzen von Audioeingabestreams können jetzt nur als `shared_ptr` übergeben werden.

**Fehlerbehebungen**

- Korrektur falscher Rückgabewerte im Ergebnis, wenn bei `RecognizeAsync()` ein Timeout auftritt.
- Die Abhängigkeit von Media Foundation-Bibliotheken für Windows wurde entfernt. Das SDK verwendet jetzt die Core Audio-APIs.
- Korrektur der Dokumentation: Eine Seite [Regionen](regions.md) wurde hinzugefügt, um die unterstützten Regionen zu beschreiben.

**Bekanntes Problem**

- Das Speech SDK für Android meldet die Ergebnisse der Sprachsynthese für Übersetzungen nicht. Dieses Problem wird im nächsten Release behoben.

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

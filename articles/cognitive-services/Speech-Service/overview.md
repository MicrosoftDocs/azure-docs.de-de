---
title: Worum handelt es sich beim Speech-Dienst?
titleSuffix: Azure Cognitive Services
description: Der Speech-Dienst dient zur Vereinheitlichung von Spracherkennung, Sprachsynthese und Sprachübersetzung in einem einzelnen Azure-Abonnement. Über das Speech SDK, das Speech Devices SDK oder REST-APIs können Sie Ihren Anwendungen, Tools und Geräten Sprachfunktionen hinzufügen.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 11/23/2020
ms.author: trbye
ms.openlocfilehash: 3b20773de62f036b507f1612f2b38d613d54ba2f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485194"
---
# <a name="what-is-the-speech-service"></a>Worum handelt es sich beim Speech-Dienst?

Der Speech-Dienst dient zur Vereinheitlichung von Spracherkennung, Sprachsynthese und Sprachübersetzung in einem einzelnen Azure-Abonnement. Über die [Speech-Befehlszeilenschnittstelle](spx-overview.md), das [Speech SDK](./speech-sdk.md), das [Speech Devices SDK](./speech-devices-sdk-quickstart.md?pivots=platform-android), [Speech Studio](https://speech.microsoft.com/) oder [REST-APIs](#reference-docs) können Sie Ihre Anwendungen, Tools und Geräten komfortabel mit Sprachfunktionen ausstatten.

> [!IMPORTANT]
> Der Speech-Dienst hat die Bing-Spracheingabe-API sowie die Sprachübersetzung abgelöst. Eine Migrationsanleitung finden Sie im Abschnitt _Migration_.

Die folgenden Features sind Teil des Speech-Diensts. Verwenden Sie die Links in der Tabelle, um weitere Informationen zu gängigen Anwendungsfällen für das jeweilige Feature zu erhalten oder sich die API-Referenz anzusehen.

| Dienst | Funktion | BESCHREIBUNG | SDK | REST |
|---------|---------|-------------|-----|------|
| [Spracherkennung](speech-to-text.md) | Spracherkennung in Echtzeit | Bei der Spracherkennung werden Sie Audiodatenströme oder lokale Dateien in Echtzeit in Text umgewandelt oder übersetzt, der von Ihren Anwendungen, Tools oder Geräten genutzt oder angezeigt werden kann. In Kombination mit [Language Understanding (LUIS)](../luis/index.yml) können Sie Benutzerabsichten aus transkribierter Sprache ableiten und auf Sprachbefehle reagieren. | [Ja](./speech-sdk.md) | [Ja](#reference-docs) |
| | [Batch-Spracherkennung](batch-transcription.md) | Die Batch-Spracherkennung ermöglicht die asynchrone Spracherkennungstranskription großer Mengen von in Azure Blob Storage gespeicherten Sprachaudiodaten. Außer der Konvertierung von Sprachaudiodaten in Text sind mit der Batch-Spracherkennung auch die Diarisierung und Stimmungsanalyse möglich. | Nein | [Ja](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| | [Konversation mit mehreren Geräten](multi-device-conversation.md) | Verbinden Sie mehrere Geräte oder Clients in einer Konversation, um sprach- oder textbasierte Nachrichten mit einfacher Unterstützung von Transkription und Übersetzung zu senden.| Ja | Nein |
| | [Unterhaltungstranskription](./conversation-transcription.md) | Dieser Dienst ermöglicht Spracherkennung, Sprecheridentifikation und Diarisierung in Echtzeit. Er eignet sich optimal für das Transkribieren persönlicher Besprechungen, wobei zwischen Sprechern unterschieden werden kann. | Ja | Nein |
| | [Erstellen von benutzerdefinierten Sprachmodellen](#customize-your-speech-experience) | Wenn Sie die Spracherkennung für die Erkennung und Transkription in einer individuellen Umgebung verwenden, können Sie benutzerdefinierte Akustik-, Sprach- und Aussprachemodelle erstellen, um Umgebungsgeräusche zu kompensieren oder branchenspezifisches Vokabular zu berücksichtigen. | Nein | [Ja](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| [Text-zu-Sprache](text-to-speech.md) | Text-zu-Sprache | Die Sprachsynthese konvertiert Eingabetext mithilfe von [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md) in menschenähnliche synthetische Sprache. Sie haben die Wahl zwischen Standardstimmen und neuronalen Stimmen. (Weitere Informationen finden Sie unter [Sprach- und Regionsunterstützung für die API für den Spracherkennungsdienst](language-support.md).) | [Ja](./speech-sdk.md) | [Ja](#reference-docs) |
| | [Erstellen benutzerdefinierter Stimmen](#customize-your-speech-experience) | Erstellen Sie spezielle benutzerdefinierte Voicefonts für Ihre Marke oder Ihr Produkt. | Nein | [Ja](#reference-docs) |
| [Sprachübersetzung](speech-translation.md) | Sprachübersetzung | Die Sprachübersetzung ermöglicht Echtzeitübersetzungen in mehreren Sprachen für Ihre Anwendungen, Tools und Geräte. Verwenden Sie diesen Dienst für die Übersetzung von Sprache in Sprache und Sprache in Text. | [Ja](./speech-sdk.md) | Nein |
| [Sprachassistenten](voice-assistants.md) | Sprachassistenten | Sprachassistenten, die den Speech-Dienst verwenden, ermöglichen es Entwicklern, natürliche Konversationsschnittstellen für ihre Anwendungen und Umgebungen zu erstellen, die der menschlichen Sprache nahekommen. Der Dienst für Sprachassistenten ermöglicht eine schnelle und zuverlässige Interaktion zwischen einem Gerät und einer Assistentenimplementierung über den Direct Line Speech-Kanal des Bot-Frameworks oder über benutzerdefinierte Befehle (Vorschauversion) für die Aufgabenausführung. | [Ja](voice-assistants.md) | Nein |
| [Sprechererkennung](speaker-recognition-overview.md) | Sprecherüberprüfung und -identifikation | Der Dienst „Sprechererkennung“ bietet Algorithmen, mit denen Sprecher anhand ihrer individuellen Stimmmerkmale überprüft und identifiziert werden. Sprechererkennung wird verwendet, um herauszufinden, wer spricht. | Ja | [Ja](/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service-for-free"></a>Kostenloses Testen des Speech-Diensts

Für die folgenden Schritte benötigen Sie ein Microsoft-Konto und ein Azure-Konto. Wenn Sie kein Microsoft-Konto besitzen, können Sie sich kostenlos im [Microsoft-Kontoportal](https://account.microsoft.com/account) registrieren. Wählen Sie **Mit Microsoft anmelden** und dann **Microsoft-Konto erstellen** aus, wenn Sie zur Anmeldung aufgefordert werden. Führen Sie die Schritte zum Erstellen und Überprüfen Ihres neuen Microsoft-Kontos aus.

Wenn Sie ein Microsoft-Konto besitzen, navigieren Sie zur [Azure-Registrierungsseite](https://azure.microsoft.com/free/ai/), wählen Sie **Kostenlos starten** aus, und erstellen Sie ein neues Azure-Konto mithilfe Ihres Microsoft-Kontos. Hier finden Sie ein Video zum [Registrieren für ein kostenloses Azure-Konto](https://www.youtube.com/watch?v=GWT2R1C_uUU).

> [!NOTE]
> Wenn Sie sich für ein kostenloses Azure-Konto registrieren, beinhaltet dieses ein Guthaben von 200 USD, das Sie bis zu 30 Tage lang auf ein kostenpflichtiges Abonnement für den Speech-Dienst anwenden können. Wenn Ihr Guthaben verbraucht oder nach 30 Tagen abgelaufen ist, werden Ihre Azure-Dienste deaktiviert. Damit Sie die Azure-Dienste weiterhin verwenden können, ist ein Upgrade Ihres Kontos erforderlich. Weitere Informationen finden Sie unter [Upgrade Ihres kostenlosen Azure-Kontos](../../cost-management-billing/manage/upgrade-azure-subscription.md). 
>
> Der Speech-Dienst weist die beiden Dienstebenen „Free“ (f0) und „Abonnement“ (s0) auf, die mit unterschiedlichen Einschränkungen und Vorteilen verbunden sind. Wenn Sie die Dienstebene „Free“ des Speech-Dienst für kleine Datenmengen verwenden, können Sie dieses kostenlose Abonnement auch nach Ablauf Ihrer kostenlosen Testversion oder Ihres Dienstguthabens weiterhin nutzen. Weitere Informationen finden Sie unter [Cognitive Services-Preise: Speech-Dienst](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-azure-resource"></a>Erstellen der Azure-Ressource

Gehen Sie wie folgt vor, um Ihrem Azure-Konto eine Ressource des Sprachdiensts (kostenloser oder kostenpflichtiger Tarif) hinzuzufügen.

1. Melden Sie sich mit Ihrem Microsoft-Konto am [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie links oben im Portal die Option **Ressource erstellen** aus. Wird **Ressource erstellen** nicht angezeigt, finden Sie die Option jederzeit durch Auswählen des reduzierten Menüs in der linken oberen Bildschirmecke:

1. Geben Sie im Fenster **Neu** die Zeichenfolge „Speech“ in das Suchfeld ein, und drücken Sie die EINGABETASTE.

1. Wählen Sie in den Suchergebnissen **Sprache** aus.

   ![Suchergebnisse für „Speech“](media/index/speech-search.png)

1. Wählen Sie **Erstellen** aus, und führen Sie dann folgende Schritte aus:

   - Geben Sie der neuen Ressource einen eindeutigen Namen. Über den Namen können Sie zwischen mehreren Abonnements unterscheiden, die für denselben Dienst gelten.
   - Wählen Sie das Azure-Abonnement aus, dem die neue Ressource zugeordnet ist, um zu bestimmen, wie die Gebühren berechnet werden. Hier finden Sie die Einführung zum [Erstellen eines Azure-Abonnements](../../cost-management-billing/manage/create-subscription.md#create-a-subscription-in-the-azure-portal) im Azure-Portal.
   - Wählen Sie die [Region](regions.md) aus, in der die Ressource verwendet wird. Azure ist eine globale Cloudplattform, die in vielen Regionen weltweit allgemein verfügbar ist. Um die beste Leistung zu erzielen, wählen Sie eine Region aus, die Ihnen am nächsten liegt oder in der Ihre Anwendung ausgeführt werden kann. Die Verfügbarkeit des Speech-Diensts variiert je nach Region. Stellen Sie sicher, dass Sie die Ressource in einer unterstützten Region erstellen. Weitere Informationen finden Sie unter [Unterstützung für Regionen für Speech-Dienste](./regions.md#speech-to-text-text-to-speech-and-translation).
   - Wählen Sie einen kostenlosen (F0) oder einen kostenpflichtigen (S0) Tarif aus. Wählen Sie **Alle Preisinformationen anzeigen** aus, oder zeigen Sie [Preise für Spracherkennungsdienste](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) an, um vollständige Informationen zu Preisen und Verwendungskontingenten für die einzelnen Tarife zu erhalten. Grenzwerte zu Ressourcen finden Sie unter [Grenzwerte für Azure Cognitive Services](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cognitive-services-limits).
   - Erstellen Sie eine neue Ressourcengruppe für dieses Abonnement für den Sprachdienst, oder weisen Sie das Abonnement einer vorhandenen Ressourcengruppe zu. Anhand von Ressourcengruppen können Sie Ihre verschiedenen Azure-Abonnements organisieren.
   - Klicken Sie auf **Erstellen**. Dadurch gelangen Sie zur Bereitstellungsübersicht, in der Statusmeldungen zur Bereitstellung angezeigt werden.  
<!--
> [!NOTE]
> You can create an unlimited number of standard-tier subscriptions in one or multiple regions. However, you can create only one free-tier subscription. Model deployments on the free tier that remain unused for 7 days will be decommissioned automatically.
-->
Es dauert einen Moment, bis die neue Speech-Ressource bereitgestellt wird. 

### <a name="find-keys-and-region"></a>Ermitteln von Schlüsseln und Region

Führen Sie die folgenden Schritte aus, um die Schlüssel und die Region einer abgeschlossenen Bereitstellung zu ermitteln:

1. Melden Sie sich mit Ihrem Microsoft-Konto am [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie **Alle Ressourcen** und dann den Namen der Cognitive Services-Ressource aus.

3. Wählen Sie im Bereich auf der linken Seite unter **RESSOURCENVERWALTUNG** die Option **Schlüssel und Endpunkt** aus.

Jedem Abonnement sind zwei Schlüssel zugewiesen. Sie können jeden dieser Schlüssel in Ihrem Abonnement verwenden. Um einen Schlüssel zu kopieren und in Ihrem Code-Editor oder an einem anderen Speicherort einzufügen, wählen Sie die Schaltfläche „Kopieren“ neben dem Schlüssel aus, wechseln Sie das Fenster, und fügen Sie den Inhalt der Zwischenablage an der gewünschten Stelle ein

Kopieren Sie außerdem den Wert `LOCATION` für SDK-Aufrufe. Dabei handelt es sich um Ihre Regions-ID (z. B. `westus`, `westeurope`).

> [!IMPORTANT]
> Diese Abonnementschlüssel werden für den Zugriff auf Ihre Cognitive Service-API verwendet. Geben Sie Ihre Schlüssel nicht weiter. Speichern Sie diese sicher, z. B. mit Azure Key Vault. Es wird außerdem empfohlen, diese Schlüssel regelmäßig neu zu generieren. Für einen API-Aufruf ist nur ein Schlüssel erforderlich. Beim erneuten Generieren des ersten Schlüssels können Sie den zweiten Schlüssel für kontinuierlichen Zugriff auf den Dienst verwenden.

## <a name="complete-a-quickstart"></a>Abschließen einer Schnellstartanleitung

Wir stellen Schnellstartanleitungen für die gängigsten Programmiersprachen bereit. Diese enthalten Informationen zu grundlegenden Entwurfsmustern und sind jeweils so konzipiert, dass Sie in weniger als zehn Minuten über ausführbaren Code verfügen. Die folgende Liste enthält Schnellstartanleitungen zu jedem Feature:

* [Grundlegendes zur Spracherkennung](get-started-speech-to-text.md)
* [Schnellstartanleitung zur Sprachsynthese](get-started-text-to-speech.md)
* [Grundlegendes zur Sprachübersetzung](./get-started-speech-translation.md)
* [Schnellstart: Erkennen von Sprache, Absichten und Entitäten mit Language Understanding (LUIS)](quickstarts/intent-recognition.md)
* [Grundlagen zur Sprechererkennung](./get-started-speaker-recognition.md)

Nachdem Sie Gelegenheit hatten, erste Schritte mit dem Speech-Dienst auszuführen, erfahren Sie in unseren Tutorials mehr zu verschiedenen Szenarien.

- [Tutorial: Erkennen von Absichten anhand von gesprochener Sprache mit dem Speech SDK für C#](how-to-recognize-intents-from-speech-csharp.md)
- [Tutorial: Voice-enable your bot using the Speech SDK](tutorial-voice-enable-your-bot-speech-sdk.md) (Ermöglichen der Verwendung von Sprache in Ihrem Bot mit dem Speech SDK, C#)
- [Tutorial: Erstellen einer Flask-App zum Übersetzen von Text, Analysieren der Stimmung und Synthetisieren von Sprache, REST](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json%252c%2fen-us%2fazure%2fbread%2ftoc.json&toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json%252c%2fen-us%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json)

## <a name="get-sample-code"></a>Abrufen des Beispielcodes

Beispielcode für den Speech-Dienst finden Sie auf GitHub. In den Beispielen werden gängige Szenarios wie etwa das Lesen von Audiodaten aus einer Datei oder einem Stream, die kontinuierliche und einmalige Erkennung oder die Verwendung benutzerdefinierter Modelle behandelt. Über die folgenden Links gelangen Sie zu den SDK- und REST-Beispielen:

- [Beispiele für Spracherkennung, Sprachsynthese und Sprachübersetzung (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch transcription samples (REST) (Batchtranskriptionsbeispiele (REST))](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Beispiele für Text-zu-Sprache (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Beispiele für Sprachassistenten (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Anpassen der Speech-Funktionen

Der Speech-Dienst funktioniert bestens mit integrierten Modellen, können auf Wunsch aber auch speziell für Ihr Produkt oder Ihre Umgebung angepasst und optimiert werden. Die Anpassungsoptionen reichen von der Feinabstimmung des Akustikmodells bis hin zu individuellen Voicefonts für Ihre Marke.

Andere Produkte bieten Sprachmodelle, die für bestimmte Zwecke wie Gesundheits- oder Versicherungswesen optimiert sind, aber für alle gleichermaßen verfügbar sind. Durch die Anpassung in Azure Speech haben Sie einen *einzigartigen* Wettbewerbsvorteil, der für andere Benutzer oder Kunden nicht verfügbar ist. Anders ausgedrückt: Ihre Modelle sind privat und nur an Ihren Anwendungsfall angepasst.

| Spracherkennungsdienst | Plattform | BESCHREIBUNG |
| -------------- | -------- | ----------- |
| Spracherkennung | [Custom Speech Service](https://aka.ms/customspeech) | Passen Sie Spracherkennungsmodelle an Ihre Anforderungen und verfügbaren Daten an. Überwinden Sie Grenzen der Spracherkennung wie z. B. Sprachstil, Vokabular und Hintergrundgeräusche. |
| Sprachsynthese | [Custom Voice](https://aka.ms/customvoice) | Erstellen Sie mit Ihren verfügbaren Sprachdaten eine erkennbare, einzigartige Stimme für Ihre Sprachsynthese-Apps. Sie können die Sprachausgaben weiter optimieren, indem Sie eine Reihe von Sprachparametern anpassen. |

## <a name="deploy-on-premises-using-docker-containers"></a>Lokales Bereitstellen unter Verwendung von Docker-Containern

[Verwenden Sie Container für den Speech-Dienst](speech-container-howto.md), um API-Features lokal bereitzustellen. Mithilfe dieser Docker-Container können Sie den Dienst näher an Ihre Daten heranbringen, um Compliance- oder Sicherheitsanforderungen zu erfüllen oder anderen betrieblichen Anforderungen gerecht zu werden. Der Speech-Dienst bietet folgende Container:

* Standardspracherkennung
* Benutzerdefinierte Spracherkennung
* Standardsprachsynthese
* Text-zu-Sprache (neuronal)
* Benutzerdefinierte Sprachsynthese (Vorschauversion)
* Speech-Sprachenerkennung (Vorschauversion)

## <a name="reference-docs"></a>Referenz

- [Speech SDK](./speech-sdk.md)
- [Speech-Geräte-SDK](speech-devices-sdk.md)
- [REST-API: Spracherkennung](rest-speech-to-text.md)
- [REST-API: Sprachsynthese](rest-text-to-speech.md)
- [REST-API: Batchtranskription und Anpassung](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erste Schritte mit Spracherkennung](./get-started-speech-to-text.md)
> [Erste Schritte mit Sprachsynthese](get-started-text-to-speech.md)
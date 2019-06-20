---
title: 'Benutzerdefinierte virtuelle Voice-First-Assistenten (Vorschauversion): Speech-Dienste'
titleSuffix: Azure Cognitive Services
description: Eine Übersicht über die Funktionen, Fähigkeiten und Einschränkungen für benutzerdefinierte virtuelle Voice-First-Assistenten, die den Direct Line Speech-Kanal im Bot-Framework und das Cognitive Services Speech SDK (Software Development Kit) verwenden
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.custom: ''
ms.openlocfilehash: 1c5712fa8bbdb158992127f8f48d810a0a9b6f79
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65603481"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>Informationen zu virtuellen Voice-First-Assistenten (Vorschauversion)

Individuelle virtuelle Assistenten, die Azure Speech-Dienste verwenden, ermöglichen es Entwicklern, natürliche, menschenähnliche Konversationsschnittstellen für ihre Anwendungen und Umgebungen zu erstellen. Der Direct Line Speech-Kanal des Bot Framework erweitert diese Möglichkeiten, indem er einen koordinierten, orchestrierten Einstiegspunkt zu einem kompatiblen Bot bietet, der Voice-In/Voice-Out-Interaktionen mit geringer Latenz und hoher Zuverlässigkeit ermöglicht. Diese Bots können Language Understanding (LUIS) von Microsoft für die natürliche Sprachinteraktion nutzen. Direct Line Speech wird von Geräten mit dem Speech Software Development Kit (SDK) aufgerufen.

   ![Konzeptionelle Darstellung des Orchestrierungsdienst-Flows](media/voice-first-virtual-assistants/overview.png "Der Flow des Speech-Kanals")


Direct Line Speech und die damit verbundene Funktionalität für benutzerdefinierte virtuelle Voice-First-Assistenten sind eine ideale Ergänzung zur [Lösung für virtuelle Assistenten](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) und der Unternehmensvorlage. Direct Line Speech kann zwar mit jedem kompatiblen Bot verwendet werden, diese Ressourcen bieten jedoch eine wiederverwendbare Baseline für qualitativ hochwertige Konversationsumgebungen sowie gemeinsame unterstützende Fähigkeiten und Modelle für einen schnellen Einstieg.


## <a name="core-features"></a>Wichtige Funktionen

| Category (Kategorie) | Features |
|----------|----------|
|[Benutzerdefiniertes Aktivierungswort](speech-devices-sdk-create-kws.md) | Sie können es Benutzern ermöglichen, Konversationen mit Bots mit einem benutzerdefinierten Stichwort wie „Hey Contoso“ zu beginnen. Dazu wird eine benutzerdefinierte Aktivierungswort-Engine im Speech SDK verwendet, die mit einem benutzerdefinierten Aktivierungswort konfiguriert werden kann, [das Sie hier generieren können](speech-devices-sdk-create-kws.md). Der Direct Line Speech-Kanal beinhaltet eine dienstseitige Überprüfung des Aktivierungsworts, um eine höhere Genauigkeit der Aktivierung des Aktivierungsworts sicherzustellen, als wenn diese nur vom Gerät überprüft wird.
|[Spracherkennung](speech-to-text.md) | Der Direct Line Speech-Kanal beinhaltet die Echtzeit-Transkription von Audio in erkannten Text mithilfe der [Spracherkennung](speech-to-text.md) von Azure Speech-Diensten. Dieser Text ist während der Transkription sowohl für Ihren Bot als auch für Ihre Clientanwendung verfügbar.
|[Sprachsynthese](text-to-speech.md) | Textantworten von Ihrem Bot werden mit der [Sprachsynthese](text-to-speech.md) von Azure Speech-Diensten synthetisiert. Diese Synthese wird dann Ihrer Clientanwendung als Audiostream zur Verfügung gestellt. Microsoft bietet die Möglichkeit, Ihre eigene benutzerdefinierte, hochwertige neuronale TTS-Stimme zu erstellen, die Ihrer Marke eine Stimme verleiht. [Wenden Sie sich an uns](mailto:mstts@microsoft.com), um weitere Informationen zu erhalten.
|[Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | Als Kanal innerhalb des Bot Frameworks ermöglicht Direct Line Speech eine reibungslose und nahtlose Verbindung zwischen Ihrer Clientanwendung, einem kompatiblen Bot und den Funktionen von Azure Speech-Diensten. Weitere Informationen zur Konfiguration Ihres Bot für die Verwendung des Direct Line Speech-Kanals finden Sie auf der [entsprechenden Seite in der Bot Framework-Dokumentation](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="sample-code"></a>Beispielcode

Beispielcode für die Erstellung eines virtuellen Voice-First-Assistenten ist auf GitHub verfügbar. Diese Beispiele decken die Clientanwendung für die Verbindung mit Ihrem Bot in mehreren gängigen Programmiersprachen ab.

* [Beispiele für virtuelle Voice-First-Assistenten (SDK)](https://aka.ms/csspeech/samples)
* [Schnellstart: Virtuelle Voice-First-Assistenten (C#)](quickstart-virtual-assistant-csharp-uwp.md)
* [Schnellstart: Virtuelle Voice-First-Assistenten (Java)](quickstart-virtual-assistant-java-jre.md)

## <a name="customization"></a>Anpassung

Virtuelle Voice-First-Assistenten, die mithilfe von Azure Speech-Diensten erstellt wurden, können die gesamte Bandbreite der Anpassungsoptionen nutzen, die für die [Spracherkennung](speech-to-text.md), [Sprachsynthese](text-to-speech.md) und [Auswahl benutzerdefinierter Stichwörter](speech-devices-sdk-create-kws.md) verfügbar sind.

> [!NOTE]
> Die Anpassungsoptionen unterscheiden sich je nach Sprache/Gebietsschema. Weitere Informationen erhalten Sie unter [Unterstützte Sprachen](supported-languages.md).

## <a name="reference-docs"></a>Referenz

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Nächste Schritte

* [Beziehen eines kostenlosen Abonnementschlüssels für die Spracherkennungsdienste](get-started.md)
* [Abrufen des Speech SDK](speech-sdk.md)
* [Erstellen und Bereitstellen eines Basisbots](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Abrufen der Lösung für virtuelle Assistenten und der Unternehmensvorlage](https://github.com/Microsoft/AI)

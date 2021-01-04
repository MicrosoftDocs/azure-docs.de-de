---
title: Verbessern der Synthese mit Custom Voice – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Bei Custom Voice handelt es sich um eine Reihe von Onlinetools, mit denen Sie eine wiedererkennbare, einzigartige Stimme für Ihre Marke erstellen können. Für den Einstieg benötigen Sie lediglich einige Audiodateien und die dazugehörigen Transkriptionen. Nutzen Sie die unten angegebenen Links, um mit der Erstellung einer benutzerdefinierten Spracherkennung zu beginnen.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 6ae164b1300119481e495e647dd491beafdd5241
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025077"
---
# <a name="get-started-with-custom-voice"></a>Erste Schritte mit Custom Voice

Bei [Custom Voice](https://aka.ms/customvoice) handelt es sich um eine Reihe von Onlinetools, mit denen Sie eine wiedererkennbare, einzigartige Stimme für Ihre Marke erstellen können. Für den Einstieg benötigen Sie lediglich einige Audiodateien und die dazugehörigen Transkriptionen. Nutzen Sie die unten angegebenen Links, um mit dem Erstellen einer benutzerdefinierten Sprachsynthese zu beginnen.

## <a name="whats-in-custom-voice"></a>Was ist in Custom Voice enthalten?

Bevor Sie mit der Verwendung von Custom Voice beginnen können, benötigen Sie ein Azure-Konto und ein Abonnement für den Speech-Dienst. Nachdem Sie ein Konto erstellt haben, können Sie Ihre Daten vorbereiten, Ihre Modelle trainieren und testen, die Stimmqualität bewerten und schließlich Ihr benutzerdefiniertes Stimmmodell bereitstellen.

Im folgenden Diagramm sind die Schritte zum Erstellen eines benutzerdefinierten Stimmmodells über das [Custom Voice-Portal](https://aka.ms/customvoice) hervorgehoben. Weitere Informationen finden Sie unter den Links.

![Custom Voice-Architekturdiagramm](media/custom-voice/custom-voice-diagram.png)

1. [Abonnieren und Erstellen eines Projekts:](#set-up-your-azure-account) Erstellen Sie ein Azure-Konto und ein Abonnement für den Speech-Dienst. Durch dieses konsolidierte Abonnement erhalten Sie Zugriff auf die Spracherkennung, Sprachsynthese, Sprachübersetzung und das Custom Voice-Portal. Erstellen Sie anschließend unter Verwendung Ihres Abonnements für den Speech-Dienst Ihr erstes Custom Voice-Projekt.

2. [Hochladen von Daten:](how-to-custom-voice-create-voice.md#upload-your-datasets) Laden Sie Daten (Audio und Text) über das Custom Voice-Portal oder über die Custom Voice-API hoch. Über das Portal können Sie Aussprachebewertungen und Signal-Rausch-Verhältnisse untersuchen und bewerten. Weitere Informationen finden Sie unter [Prepare data to create a custom voice](how-to-custom-voice-prepare-data.md) (Vorbereiten von Daten zum Erstellen einer benutzerdefinierten Stimme).

3. [Trainieren Ihres Modells:](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) Erstellen Sie unter Verwendung Ihrer Daten ein benutzerdefiniertes Stimmmodell für die Sprachsynthese. Sie können ein Modell in verschiedenen Sprachen trainieren. Testen Sie Ihr trainiertes Modell. Wenn Sie mit dem Ergebnis zufrieden sind, können Sie das Modell bereitstellen.

4. [Bereitstellen Ihres Modells:](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) Erstellen Sie einen benutzerdefinierten Endpunkt für Ihr Stimmmodell für die Sprachsynthese, und verwenden sie ihn für die Sprachsynthese in Ihren Produkten, Tools und Anwendungen.

## <a name="custom-neural-voices"></a>Benutzerdefinierte neuronale Stimmen

Die neuronale Stimmenanpassungsfunktion befindet sich derzeit in der öffentlichen Vorschauversion und ist auf ausgewählte Kunden beschränkt. Füllen Sie dieses [Antragsformular](https://go.microsoft.com/fwlink/?linkid=2108737) aus, um loszulegen.

> [!NOTE]
> Im Rahmen der Verpflichtung von Microsoft für die Entwicklung einer verantwortungsvollen KI ist es unsere Absicht, die Rechte von Personen und der Gesellschaft zu schützen und transparente Interaktionen zwischen Mensch und Computer zu fördern. Aus diesem Grund ist die benutzerdefinierte neuronale Stimme nicht für alle Kunden verfügbar. Sie erhalten erst Zugriff auf die Technologie, nachdem Ihre Anwendungen überprüft wurden und Sie sich zur Einhaltung der ethischen Grundlagen verpflichtet haben. Erfahren Sie mehr über unseren [Anwendungskontrollprozess](./concepts-gating-overview.md).

## <a name="set-up-your-azure-account"></a>Einrichten Ihres Azure-Kontos

Sie benötigen ein Abonnement für den Speech-Dienst, um das Custom Speech-Portal zum Erstellen eines benutzerdefinierten Modells verwenden zu können. Befolgen Sie diese Anweisungen, um in Azure ein Abonnement für den Speech-Dienst zu erstellen. Sollten Sie über kein Azure-Konto verfügen, können Sie sich für ein neues Konto registrieren.  

Nachdem Sie ein Azure-Konto und ein Abonnement für den Speech-Dienst erstellt haben, müssen Sie sich beim Custom Voice-Portal anmelden und eine Verbindung mit Ihrem Abonnement herstellen.

1. Rufen Sie über das Azure-Portal Ihren Abonnementschlüssel für die Speech-Dienste ab.
2. Melden Sie sich beim [Custom Voice-Portal](https://aka.ms/custom-voice) an.
3. Wählen Sie Ihr Abonnement aus, und erstellen Sie ein Speech-Projekt.
4. Wenn Sie zu einem anderen Speech-Abonnement wechseln möchten, verwenden Sie das Zahnradsymbol auf der oberen Navigationsleiste.

> [!NOTE]
> Sie müssen über einen in Azure erstellten F0- oder S0-Schlüssel verfügen, um den Dienst nutzen zu können.

## <a name="how-to-create-a-project"></a>So erstellen Sie ein Projekt

Inhalte wie Daten, Modelle, Tests und Endpunkte sind im Custom Voice-Portal in **Projekten** organisiert. Jedes Projekt ist spezifisch für ein Land/eine Sprache und das Geschlecht der Stimme, die Sie erstellen möchten. So können Sie beispielsweise ein Projekt mit einer weiblichen Stimme für die Chatbots Ihres Callcenters erstellen, die US-amerikanisches Englisch (en-US) verwenden.

Wählen Sie zum Erstellen Ihres ersten Projekts die Registerkarte **Text-to-Speech/Custom Voice** (Sprachsynthese/Benutzerdefinierte Stimme) aus, und klicken Sie auf **Neues Projekt**. Folgen Sie den Anweisungen des Assistenten, um Ihr Projekt zu erstellen. Nachdem Sie ein Projekt erstellt haben, werden vier Registerkarten angezeigt: **Daten**, **Training**, **Test** und **Bereitstellung**. Verwenden Sie die Links unter [Nächste Schritte](#next-steps), um mehr über die Verwendung der einzelnen Registerkarten zu erfahren.

> [!IMPORTANT]
> Das [Custom Voice-Portal](https://aka.ms/custom-voice) wurde kürzlich aktualisiert! Wenn Sie vorherige Daten, Modelle, Tests und veröffentlichte Endpunkte im CRIS.ai-Portal oder mit APIs erstellt haben, müssen Sie im neuen Portal ein neues Projekt erstellen, um eine Verbindung mit diesen alten Entitäten herzustellen.

## <a name="next-steps"></a>Nächste Schritte

- [Get started with Custom Voice](how-to-custom-voice-prepare-data.md) (Erste Schritte mit Custom Voice)
- [Create a Custom Voice](how-to-custom-voice-create-voice.md) (Erstellen einer benutzerdefinierten Stimme)
- [Aufzeichnen Aufnehmen Ihrer Sprachbeispiele](record-custom-voice-samples.md)
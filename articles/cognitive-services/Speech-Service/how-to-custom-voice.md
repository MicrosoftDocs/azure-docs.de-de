---
title: Was ist Custom Voice? – Speech-Dienste
titlesuffix: Azure Cognitive Services
description: Bei Custom Voice handelt es sich um eine Reihe von Onlinetools, mit denen Sie eine wiedererkennbare, einzigartige Stimme für Ihre Marke erstellen können. Für den Einstieg benötigen Sie lediglich einige Audiodateien und die dazugehörigen Transkriptionen. Nutzen Sie die unten angegebenen Links, um mit der Erstellung einer benutzerdefinierten Spracherkennung zu beginnen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 016dcf32f2f846e43362f17bc9f4627113908352
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075150"
---
# <a name="get-started-with-custom-voice"></a>Erste Schritte mit Custom Voice

Bei Custom Voice handelt es sich um eine Reihe von Onlinetools, mit denen Sie eine wiedererkennbare, einzigartige Stimme für Ihre Marke erstellen können. Für den Einstieg benötigen Sie lediglich einige Audiodateien und die dazugehörigen Transkriptionen. Nutzen Sie die unten angegebenen Links, um mit dem Erstellen einer benutzerdefinierten Sprachsynthese zu beginnen.

## <a name="whats-in-custom-voice"></a>Was ist in Custom Voice enthalten?

Bevor Sie mit der Verwendung von Custom Voice beginnen können, benötigen Sie ein Azure-Konto und ein Abonnement für die Speech-Dienste. Nachdem Sie ein Konto erstellt haben, können Sie Ihre Daten vorbereiten, Ihre Modelle trainieren und testen, die Stimmqualität bewerten und schließlich Ihr benutzerdefiniertes Stimmmodell bereitstellen.

Im folgenden Diagramm sind die Schritte zum Erstellen eines benutzerdefinierten Stimmmodells über das Custom Voice-Portal hervorgehoben. Weitere Informationen finden Sie unter den Links.

![Custom Voice-Architekturdiagramm](media/custom-voice/custom-voice-diagram.png)

1.  [Abonnieren und Erstellen eines Projekts:](#set-up-your-azure-account) Erstellen Sie ein Azure-Konto und ein Abonnement für die Speech-Dienste. Durch dieses konsolidierte Abonnement erhalten Sie Zugriff auf die Spracherkennung, Sprachsynthese, Sprachübersetzung und das Custom Voice-Portal. Erstellen Sie anschließend unter Verwendung Ihres Abonnements für die Speech-Dienste Ihr erstes Custom Voice-Projekt.

2.  [Hochladen von Daten:](how-to-custom-voice-create-voice.md#upload-your-datasets) Laden Sie Daten (Audio und Text) über das Custom Voice-Portal oder über die Custom Voice-API hoch. Über das Portal können Sie Aussprachebewertungen und Signal-Rausch-Verhältnisse untersuchen und bewerten. Weitere Informationen finden Sie unter [Prepare data to create a custom voice](how-to-custom-voice-prepare-data.md) (Vorbereiten von Daten zum Erstellen einer benutzerdefinierten Stimme).

3.  [Trainieren Ihres Modells:](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) Erstellen Sie unter Verwendung Ihrer Daten ein benutzerdefiniertes Stimmmodell für die Sprachsynthese. Sie können ein Modell in verschiedenen Sprachen trainieren. Testen Sie Ihr trainiertes Modell. Wenn Sie mit dem Ergebnis zufrieden sind, können Sie das Modell bereitstellen.

4.  [Bereitstellen Ihres Modells:](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) Erstellen Sie einen benutzerdefinierten Endpunkt für Ihr Stimmmodell für die Sprachsynthese, und verwenden sie ihn für die Sprachsynthese in Ihren Produkten, Tools und Anwendungen.

## <a name="set-up-your-azure-account"></a>Einrichten Ihres Azure-Kontos

Sie benötigen ein Abonnement für die Speech-Dienste, um das Custom Speech-Portal zum Erstellen eines benutzerdefinierten Modells verwenden zu können. Gehen Sie wie folgt vor, um in Azure ein Abonnement für die Speech-Dienste zu erstellen. Sollten Sie über kein Azure-Konto verfügen, können Sie sich für ein neues Konto registrieren.  

Nachdem Sie ein Azure-Konto und ein Abonnement für die Speech-Dienste erstellt haben, müssen Sie sich beim Custom Voice-Portal anmelden und eine Verbindung mit Ihrem Abonnement herstellen.

1. Rufen Sie über das Azure-Portal Ihren Abonnementschlüssel für die Speech-Dienste ab.
2. Melden Sie sich beim [Custom Voice-Portal](https://aka.ms/custom-voice) an.
3. Wählen Sie Ihr Abonnement aus, und erstellen Sie ein Speech-Projekt.
4. Wenn Sie zu einem anderen Speech-Abonnement wechseln möchten, verwenden Sie das Zahnradsymbol auf der oberen Navigationsleiste.

> [!NOTE]
> Der Schlüssel der 30-tägigen kostenlosen Testversion wird vom Custom Voice-Dienst NICHT unterstützt. Sie müssen über einen in Azure erstellten F0- oder S0-Schlüssel verfügen, um den Dienst nutzen zu können.

## <a name="how-to-create-a-project"></a>So erstellen Sie ein Projekt

Inhalte wie Daten, Modelle, Tests und Endpunkte sind im Custom Voice-Portal in **Projekten** organisiert. Jedes Projekt ist spezifisch für ein Land/eine Sprache und das Geschlecht der Stimme, die Sie erstellen möchten. So können Sie beispielsweise ein Projekt mit einer weiblichen Stimme für die Chatbots Ihres Callcenters erstellen, die US-amerikanisches Englisch (en-US) verwenden.

Wählen Sie zum Erstellen Ihres ersten Projekts die Registerkarte **Text-to-Speech/Custom Voice** (Sprachsynthese/Benutzerdefinierte Stimme) aus, und klicken Sie auf **Neues Projekt**. Folgen Sie den Anweisungen des Assistenten, um Ihr Projekt zu erstellen. Nachdem Sie ein Projekt erstellt haben, werden vier Registerkarten angezeigt: **Daten**, **Training**, **Test** und **Bereitstellung**. Verwenden Sie die Links unter [Nächste Schritte](#next-steps), um mehr über die Verwendung der einzelnen Registerkarten zu erfahren.

## <a name="next-steps"></a>Nächste Schritte

- [Get started with Custom Voice](how-to-custom-voice.md) (Erste Schritte mit Custom Voice)
- [Create a Custom Voice](how-to-custom-voice-create-voice.md) (Erstellen einer benutzerdefinierten Stimme)
- [Aufzeichnen Aufnehmen Ihrer Sprachbeispiele](record-custom-voice-samples.md)

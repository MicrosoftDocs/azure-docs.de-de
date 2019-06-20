---
title: Migrieren vom Custom Speech Service zu Speech Services
titlesuffix: Azure Cognitive Services
description: Der Custom Speech Service ist nun Teil der Sprachdienste. Wechseln Sie zu den Sprachdiensten, um von den neuesten Updates zu Qualität und Features zu profitieren.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 8a2c149faa0ec9d135713a123a33d7c220522496
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60995637"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrieren vom Custom Speech Service zum Speech-Dienst

Verwenden Sie die Informationen in diesem Artikel, um Ihre Anwendungen vom Custom Speech Service zum Speech-Dienst zu migrieren.

Der Custom Speech Service ist nun Teil des Speech-Diensts. Wechseln Sie zu den Sprachdiensten, um von den neuesten Updates zu Qualität und Features zu profitieren.

## <a name="migration-for-new-customers"></a>Migration für Neukunden

Das Preismodell ist einfacher, da für den Speech-Dienst ein stundenbasiertes Preismodell verwendet wird.  

1. Erstellen Sie eine Azure-Ressource in jeder Region, in der Ihre Anwendung verfügbar ist. Der Name der Azure-Ressource lautet **Speech**. Sie können eine einzelne Azure-Ressource für die folgenden Dienste in der gleichen Region verwenden, anstatt separate Ressourcen zu erstellen:

    * Spracherkennung
    * Benutzerdefinierte Spracherkennung
    * Text-zu-Sprache
    * Sprachübersetzung

2. Laden Sie das [Spracherkennungs-SDK](speech-sdk.md) herunter.

3. Befolgen Sie die Schnellstarts und die SDK-Beispiele, um die richtigen APIs zu verwenden. Wenn Sie die REST-APIs verwenden, müssen Sie auch die richtigen Endpunkte und Ressourcenschlüssel verwenden.

4. Aktualisieren Sie die Clientanwendung, um die Sprachdienste und APIs zu verwenden.

## <a name="migration-for-existing-customers"></a>Migration für Bestandskunden

Migrieren Sie Ihre vorhandenen Ressourcenschlüssel zu den Sprachdiensten im Sprachdiensteportal. Führen Sie die folgenden Schritte aus:

> [!NOTE]
> Ressourcenschlüssel können nur innerhalb derselben Region migriert werden.

1. Melden Sie sich im Portal [cris.ai](https://cris.ai/Home/CustomSpeech) an, und wählen Sie das Abonnement im Menü rechts oben aus.

2. Wählen Sie **Migrate selected subscription** (Ausgewähltes Abonnement migrieren) aus.

3. Geben Sie den Abonnementschlüssel in das Textfeld ein, und wählen Sie **Migrieren** aus.

## <a name="next-steps"></a>Nächste Schritte

* [Testen Sie die Sprachdienste kostenlos](get-started.md).
* Lernen Sie die Konzepte der [Spracherkennung](./speech-to-text.md) kennen.

## <a name="see-also"></a>Weitere Informationen

* [Worum handelt es sich beim Speech-Dienst?](overview.md)
* [Dokumentation zu Sprachdiensten und zum Spracherkennungsdienst-SDK](speech-sdk.md#get-the-sdk)

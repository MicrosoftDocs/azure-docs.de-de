---
title: Kundenseitig verwaltete Schlüssel für Cognitive Services
titleSuffix: Cognitive Services
description: Hier erfahren Sie, wie Sie im Azure-Portal kundenseitig verwaltete Schlüssel mit Azure Key Vault konfigurieren. Mit von Kunden verwalteten Schlüsseln können Sie Zugriffssteuerungen erstellen, rotieren, deaktivieren und widerrufen.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: 1d161c82c087fd86a3774f0d121330260b1574e4
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366093"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>Konfigurieren von kundenseitig verwalteten Schlüsseln mit Azure Key Vault für Cognitive Services

Der Prozess zum Aktivieren von kundenseitig verwalteten Schlüsseln mit Azure Key Vault für Cognitive Services variiert je nach Produkt. Unter den folgenden Links finden Sie dienstspezifische Anweisungen:

## <a name="vision"></a>Bildanalyse

* [Custom Vision-Verschlüsselung für ruhende Daten](../Custom-Vision-Service/custom-vision-encryption-of-data-at-rest.md)
* [Verschlüsselung für ruhende Daten der Gesichtserkennungsdienste](../Face/face-encryption-of-data-at-rest.md)
* [Verschlüsselung für ruhende Daten der Formularerkennung](../form-recognizer/form-recognizer-encryption-of-data-at-rest.md)

## <a name="language"></a>Sprache

* [Verschlüsselung für ruhende Daten des Language Understanding-Diensts](../LUIS/luis-encryption-of-data-at-rest.md)
* [QnA Maker-Verschlüsselung für ruhende Daten](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Verschlüsselung für ruhende Daten des Übersetzers](../translator/translator-encryption-of-data-at-rest.md)

## <a name="decision"></a>Entscheidung

* [Content Moderator-Verschlüsselung für ruhende Daten](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [Verschlüsselung für ruhende Daten der Personalisierung](../personalizer/personalizer-encryption-of-data-at-rest.md)

## <a name="next-steps"></a>Nächste Schritte

* [What is Azure Key Vault? (Was ist Azure Key Vault?)](../../key-vault/general/overview.md)
* [Formular zum Anfordern von kundenseitig verwalteten Schlüsseln für Cognitive Services](https://aka.ms/cogsvc-cmk)
---
title: Content Moderator-Verschlüsselung für ruhende Daten
titleSuffix: Azure Cognitive Services
description: Content Moderator-Verschlüsselung für ruhende Daten.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 1401108a594e30790e842ec379724603f11d493f
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913703"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Content Moderator-Verschlüsselung für ruhende Daten

Content Moderator verschlüsselt automatisch Daten, wenn sie in der Cloud persistent gespeichert werden, und unterstützt Sie so bei der Einhaltung der Sicherheits- und Complianceziele Ihrer Organisation.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Vom Kunden verwaltete Schlüssel sind nur im Tarif E0 verfügbar. Wenn Sie die Möglichkeit haben möchten, von Kunden verwaltete Schlüssel zu verwenden, füllen Sie das [Formular zum Anfordern von kundenseitig verwalteten Schlüsseln für Content Moderator](https://aka.ms/cogsvc-cmk) aus, und reichen Sie es ein. Nach ca. 3–5 Werktagen erhalten Sie eine Rückmeldung zum Status Ihrer Anforderung. Je nach Bedarf können Sie in einer Warteschlange platziert und genehmigt werden, sobald Platz verfügbar ist. Nachdem Ihre Verwendung von CMK mit Content Moderator genehmigt wurde, müssen Sie eine neue Content Moderator-Ressource erstellen und als Tarif „E0“ auswählen. Nachdem die Content Moderator-Ressource mit dem Tarif „E0“ erstellt wurde, können Sie mit Azure Key Vault Ihre verwaltete Identität einrichten.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Aktivieren der Datenverschlüsselung für Ihr Content Moderator-Team

Informationen zum Aktivieren der Datenverschlüsselung für Ihr Content Moderator-Bewertungsteam finden Sie unter [Schnellstart: Testen von Content Moderator im Web](quick-start.md#create-a-review-team).  

> [!NOTE]
> Für den Content Moderator-Tarif E0 müssen Sie eine _Ressourcen-ID_.

## <a name="next-steps"></a>Nächste Schritte

* Eine vollständige Liste der Dienste, die CMK unterstützen, finden Sie unter [Kundenseitig verwaltete Schlüssel für Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md).
* [What is Azure Key Vault? (Was ist Azure Key Vault?)](../../key-vault/general/overview.md)
* [Formular zum Anfordern von kundenseitig verwalteten Schlüsseln für Cognitive Services](https://aka.ms/cogsvc-cmk)
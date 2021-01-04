---
title: Verschlüsselung für ruhende Daten der Formularerkennung
titleSuffix: Azure Cognitive Services
description: Microsoft bietet von Microsoft verwaltete Verschlüsselungsschlüssel an und ermöglicht Ihnen auch die Verwaltung Ihrer Cognitive Services-Abonnements mit Ihren eigenen Schlüsseln, den so genannten kundenseitig verwalteten Schlüsseln (Customer Managed Keys, CMK). In diesem Artikel erfahren Sie mehr über die Datenverschlüsselung im Ruhezustand für die Formularerkennung und wie Sie CMK aktivieren und verwalten können.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 7a8b331c1295ed19afa64e95318bfa14414e6d9f
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913057"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Verschlüsselung für ruhende Daten der Formularerkennung

Die Azure-Formularerkennung verschlüsselt Ihre Daten beim Speichern in der Cloud automatisch. Durch die Verschlüsselung der Formularerkennung werden Ihre Daten ausreichend geschützt, um den Sicherheits- und Complianceanforderungen Ihrer Organisation gerecht zu werden.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Vom Kunden verwaltete Schlüssel sind nur für Ressourcen erhältlich, die nach dem 11. Mai 2020 erstellt wurden. Um CMK mit der Formularerkennung zu verwenden, müssen Sie eine neue Formularerkennungsressource erstellen. Nachdem die Ressource erstellt wurde, können Sie mit Azure Key Vault Ihre verwaltete Identität einrichten.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Nächste Schritte

* [Formular zum Anfordern von kundenseitig verwalteten Schlüsseln für die Formularerkennung](https://aka.ms/cogsvc-cmk)
* [Weitere Informationen zu Azure Key Vault](../../key-vault/general/overview.md)
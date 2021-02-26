---
title: Custom Vision-Verschlüsselung für ruhende Daten
titleSuffix: Azure Cognitive Services
description: Microsoft bietet von Microsoft verwaltete Verschlüsselungsschlüssel an und ermöglicht Ihnen auch die Verwaltung Ihrer Cognitive Services-Abonnements mit Ihren eigenen Schlüsseln, den so genannten kundenseitig verwalteten Schlüsseln (Customer Managed Keys, CMK). In diesem Artikel erfahren Sie mehr über die Datenverschlüsselung im Ruhezustand für Custom Vision und sowie darüber, wie Sie CMK aktivieren und verwalten.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 6c65f28c040b15aaa2ec8f3425209351e4b60486
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524057"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Custom Vision-Verschlüsselung für ruhende Daten

Azure Custom Vision verschlüsselt Ihre Daten beim Speichern in der Cloud automatisch. Durch die Custom Vision-Verschlüsselung werden Ihre Daten ausreichend geschützt, um den Sicherheits- und Complianceanforderungen Ihrer Organisation gerecht zu werden.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Vom Kunden verwaltete Schlüssel sind nur für Ressourcen erhältlich, die nach dem 11. Mai 2020 erstellt wurden. Um CMK mit Custom Vision zu verwenden, müssen Sie eine neue Custom Vision-Ressource erstellen. Nachdem die Ressource erstellt wurde, können Sie mit Azure Key Vault Ihre verwaltete Identität einrichten.

## <a name="regional-availability"></a>Regionale Verfügbarkeit

Kundenseitig verwaltete Schlüssel sind zurzeit in den folgenden Regionen verfügbar:

* USA, Süden-Mitte
* USA, Westen 2
* East US
* US Government, Virginia

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Nächste Schritte

* Eine vollständige Liste der Dienste, die CMK unterstützen, finden Sie unter [Konfigurieren von kundenseitig verwalteten Schlüsseln mit Azure Key Vault über das Azure-Portal](../encryption/cognitive-services-encryption-keys-portal.md).
* [What is Azure Key Vault? (Was ist Azure Key Vault?)](../../key-vault/general/overview.md)
* [Formular zum Anfordern von kundenseitig verwalteten Schlüsseln für Cognitive Services](https://aka.ms/cogsvc-cmk)
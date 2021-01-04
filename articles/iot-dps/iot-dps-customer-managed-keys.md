---
title: Verschlüsselung ruhender Daten mit dem Azure Device Provisioning-Dienst über kundenseitig verwaltete Schlüssel | Microsoft-Dokumentation
description: Verschlüsselung ruhender Daten über kundenseitig verwaltete Schlüssel für den Device Provisioning-Dienst
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: d22a01bab81fc330484e7715a65c89a1cfd7802c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967175"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Verschlüsselung ruhender Daten über kundenseitig verwaltete Schlüssel für den Device Provisioning-Dienst

## <a name="overview"></a>Übersicht

Der Gerätebereitstellungsdienst (Device Provisioning Service, DPS) unterstützt die Verschlüsselung ruhender Daten mit kundenseitig verwalteten Schlüsseln (Customer-Managed Key, CMK), auch als „Bring Your Own Key“ (BYOK) bezeichnet. DPS bietet Verschlüsselung von ruhenden Daten und während der Übertragung, wenn diese in unseren Rechenzentren geschrieben werden, und entschlüsselt sie, wenn Sie darauf zugreifen. Standardmäßig verwendet DPS von Microsoft verwaltete Schlüssel, um die ruhenden Daten zu verschlüsseln. Mit CMK können Sie eine zusätzliche Verschlüsselungsebene zusätzlich zur Standardplattformverschlüsselung erhalten, indem Sie sich dafür entscheiden, ruhende Daten mit einem Schlüsselverschlüsselungsschlüssel zu verschlüsseln, der über [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) verwaltet wird. Dadurch erhalten Sie die Flexibilität, Schlüssel zu erstellen, zu rotieren, zu deaktivieren und zu widerrufen. Wenn CMK für Ihren DPS konfiguriert ist, bedeutet dies, dass doppelte Verschlüsselung mit zwei Schutzebenen aktiviert ist und Ihre Daten aktiv geschützt werden. 

Für diese Funktion muss ein neuer DPS erstellt werden. Um diese Funktion auszuprobieren, kontaktieren Sie uns über den [Microsoft-Support](https://azure.microsoft.com/support/create-ticket/). Geben Sie Ihren Firmennamen und Ihre Abonnement-ID an, wenn Sie sich an den Microsoft-Support wenden.


## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zum Device Provisioning-Dienst](./index.yml)

* [Weitere Informationen zu Azure Key Vault](../key-vault/general/overview.md)
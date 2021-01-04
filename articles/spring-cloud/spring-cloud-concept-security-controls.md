---
title: 'Konzepte: Sicherheitskontrollen für den Azure Spring Cloud-Dienst'
description: Verwenden Sie die in den Azure Spring Cloud-Dienst integrierten Sicherheitskontrollen.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 25636611795a18761a1fc7135efd4abba5af5fe3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501106"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Sicherheitskontrollen für den Azure Spring Cloud-Dienst

**Dieser Artikel gilt für:** ✔️ Java ✔️ C#

Sicherheitskontrollen sind in den Azure Spring Cloud-Dienst integriert.

Eine Sicherheitskontrolle stellt eine Qualitätseigenschaft oder ein Feature eines Azure-Diensts dar. Sie ermöglicht dem Dienst, Sicherheitsrisiken zu verhindern, zu erkennen und darauf zu reagieren.  Für jedes Steuerelement verwenden wir *Ja* oder *Nein*, um anzugeben, ob es derzeit für den Dienst eingerichtet ist.  Wir verwenden *N/V* für ein Steuerelement, das auf den Dienst nicht anwendbar ist. 

**Sicherheitskontrollen für den Datenschutz**

| Sicherheitskontrolle | Ja/Nein | Notizen | Dokumentation |
|:-------------|:-------|:-------------------------------|:----------------------|
| Serverseitige Verschlüsselung ruhender Daten: Von Microsoft verwaltete Schlüssel | Ja | Vom Benutzer hochgeladene Quellen und Artefakte, Konfigurationsservereinstellungen, Anwendungseinstellungen und Daten im beständigen Speicher werden in Azure Storage gespeichert, in dem ruhende Inhalte automatisch verschlüsselt werden.<br><br>Konfigurationsservercache, Runtimebinärdateien, die aus der hochgeladenen Quelle erstellt wurden, und Anwendungsprotokolle während der Anwendungslebensdauer werden auf verwalteten Azure-Datenträgern gespeichert, durch die ruhende Inhalte automatisch verschlüsselt werden.<br><br>Vom Benutzer aus der hochgeladenen Quelle erstellte Containerimages werden in der Azure Container Registry gespeichert, durch die ruhende Imageinhalte automatisch verschlüsselt werden. | [Azure Storage encryption for data at rest (Azure Storage-Verschlüsselung für ruhende Daten)](../storage/common/storage-service-encryption.md)<br><br>[Serverseitige Verschlüsselung von verwalteten Azure-Datenträgern](../virtual-machines/disk-encryption.md)<br><br>[Speichern von Containerimages in der Azure Container Registry](../container-registry/container-registry-storage.md) |
| Vorübergehende Verschlüsselung | Ja | Öffentliche Endpunkte der Benutzer-App verwenden standardmäßig HTTPS für eingehenden Datenverkehr. |  |
| Verschlüsselte API-Aufrufe | Ja | Verwaltungsaufrufe zum Konfigurieren des Azure Spring Cloud-Diensts werden mit Azure Resource Manager-Aufrufen über HTTPS gesendet. | [Azure Resource Manager](../azure-resource-manager/index.yml) |

**Sicherheitssteuerungen für den Netzwerkzugriff**

| Sicherheitskontrolle | Ja/Nein | Notizen | Dokumentation |
|:-------------|:-------|:-------------------------------|:----------------------|
| Diensttag | Ja | Verwenden Sie das **AzureSpringCloud**-Diensttag, um Zugriffssteuerungen für ausgehenden Netzwerkzugriff für [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md#security-rules) oder [Azure-Firewall](../firewall/service-tags.md) zu definieren, um Datenverkehr zu Azure Spring Cloud-Anwendungen zu ermöglichen.<br><br>*Hinweis:* Derzeit unterstützt nur die neue Azure Spring Cloud-Dienstinstanz, die nach dem 14.7.2020 erstellt wurde, das **AzureSpringCloud**-Diensttag. | [Diensttags](../virtual-network/service-tags-overview.md) |

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Bereitstellen Ihrer ersten Azure Spring Cloud-Anwendung](spring-cloud-quickstart.md)
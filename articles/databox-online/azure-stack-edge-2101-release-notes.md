---
title: Versionshinweise zu Azure Stack Edge Pro mit FPGA 2101 | Microsoft-Dokumentation
description: In diesem Artikel werden schwerwiegende ungelöste Probleme und Lösungen für das Release 2101 von Azure Stack Edge beschrieben.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/01/2021
ms.author: alkohli
ms.openlocfilehash: 7c1f78f405a67687168457448f62d46a39da165f
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99225123"
---
# <a name="azure-stack-edge-pro-with-fpga-2101-release-notes"></a>Versionshinweise zu Azure Stack Edge Pro mit FPGA 2101

In den folgenden Versionshinweisen werden schwerwiegende ungelöste und behobene Probleme für das Release 2101 von Azure Stack Edge Pro mit integriertem Field Programmable Gate Array (FPGA) beschrieben.

Die Anmerkungen zu dieser Version werden fortlaufend aktualisiert. Wenn schwerwiegende Probleme festgestellt werden, die eine Problemumgehung erfordern, werden sie hinzugefügt. Lesen Sie vor der Bereitstellung Ihres Azure Stack Edge-Geräts die Informationen in den Versionshinweisen sorgfältig durch.  

Dieses Release entspricht der folgenden Softwareversion:

- **Azure Stack Edge 2101 (1.6.1475.2528)** : KB 4599267

> [!NOTE]
> Das Update 2101 kann nur auf Geräte angewendet werden, auf denen allgemein verfügbare Versionen (oder höhere Versionen) der Software ausgeführt werden.

## <a name="whats-new"></a>Neues

Dieses Release enthält die folgenden Fehlerbehebungen:

- **Uploadproblem:** Dieses Release behebt ein Uploadproblem, bei dem fehlerbedingte Neustarts von Uploads zu einer langsameren Uploadabschlussrate führen können. Dieses Problem kann auftreten, wenn ein Dataset hochgeladen wird, das hauptsächlich aus Dateien besteht, die im Verhältnis zur verfügbaren Bandbreite sehr groß sind. Das gilt insbesondere bei aktivierter Bandbreiteneinschränkung. Diese Änderung sorgt dafür, dass dem Abschluss von Uploads genügend Zeit eingeräumt wird, bevor der Upload für eine bestimmte Datei neu gestartet wird.

Dieses Release enthält auch die folgenden Updates:

- Alle kumulativen Windows-Updates und .NET Framework-Updates, die bis einschließlich Oktober 2020 veröffentlicht wurden.
- Die BMC-Firmwareversion (Baseboard-Verwaltungscontroller) wird bei der Herstellerinstallation von 3.32.32.32 auf 3.36.36.36 aktualisiert, um die Inkompatibilität mit neueren Dell-Netzteilen zu beheben.
- Dieses Release unterstützt IoT Edge 1.0.9.3 auf Azure Stack Edge-Geräten.

## <a name="known-issues-in-this-release"></a>Bekannte Probleme in dieser Version

Für dieses Release werden keine neuen Probleme aufgeführt. Alle in den Versionshinweisen enthaltenen Probleme wurden aus früheren Releases übernommen. Um eine Liste der bekannten Probleme anzuzeigen, wechseln Sie zu [Bekannte Probleme in der Version für die allgemeine Verfügbarkeit](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Nächste Schritte

- [Vorbereiten der Bereitstellung von Azure Stack Edge](../databox-online/azure-stack-edge-deploy-prep.md)

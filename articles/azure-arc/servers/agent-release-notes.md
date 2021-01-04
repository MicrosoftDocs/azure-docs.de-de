---
title: Neuerungen im Agent für Azure Arc-fähige Server
description: Dieser Artikel enthält Versionshinweise für den Agent für Azure Arc-fähige Server. Für viele der zusammengefassten Issues werden Links zu weiteren Informationen bereitgestellt.
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 5322a92bfc67d9f80c2271a3ef9b5626455e9ad7
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445284"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Neuerungen im Agent für Azure Arc-fähige Server

Der Connected Machine-Agent für Azure Arc-fähige Server wird ständig verbessert. Damit Sie bezüglich der neuesten Entwicklungen auf dem neuesten Stand bleiben, bietet dieser Artikel Informationen zu Folgendem:

- Neueste Versionen
- Bekannte Probleme
- Behebung von Programmfehlern

## <a name="november-2020"></a>November 2020

### <a name="fixed"></a>Fest

Ein Problem wurde behoben, bei dem die Proxykonfiguration nach dem Upgrade auf RPM-basierte Verteilungen verloren gehen konnte.

## <a name="october-2020"></a>Oktober 2020

### <a name="fixed"></a>Fest

- Es wurde ein Proxyskript korrigiert, sodass der Speicherort einer alternativen GC-Daemon-Moduldatei verarbeitet werden kann.
- Änderungen an der Zuverlässigkeit des GuestConfig-Agent.
- Unterstützung des GuestConfig-Agent für die Region „US Gov Virginia“.
- Berichtsmeldungen der GuestConfig-Agent-Erweiterung sind bei Fehlern ausführlicherer.

## <a name="september-2020"></a>September 2020

Version: 1.0 (Allgemeine Verfügbarkeit)

### <a name="plan-for-change"></a>Plan für Änderung

- Unterstützung für Vorschau-Agents (alle Versionen, die älter als 1.0 sind) wird in einem zukünftigen Dienstupdate entfernt.
- Unterstützung für den Fallbackendpunkt `.azure-automation.net` wurde entfernt. Wenn Sie einen Proxy verwenden, müssen Sie den Endpunkt `*.his.arc.azure.com` zulassen.
- Wenn der Connected Machine-Agent auf einem virtuellen Computer installiert ist, der in Azure gehostet wird, können VM-Erweiterungen nicht von der Ressource „Arc-fähige Server“ installiert oder geändert werden. Dadurch soll vermieden werden, dass widersprüchliche Erweiterungsvorgänge von der Ressource **Microsoft.Compute** und **Microsoft.HybridCompute** des virtuellen Computers durchgeführt werden. Verwenden Sie die **Microsoft.Compute**-Ressource für den Computer für alle Erweiterungsvorgänge.
- Der Name des Gastkonfigurationsprozesses wurde aus *gcd* in *gcad* unter Linux und aus *gcservice* in *gcarcservice* unter Windows geändert.

### <a name="new-feature"></a>Neues Feature

- `azcmagent logs`-Option zum Erfassen von Informationen für den Support wurde hinzugefügt.
- `azcmagent license`-Option zum Anzeigen der EULA wurde hinzugefügt.
- `azcmagent show --json`-Option zum Ausgeben des Agent-Zustands in einem leicht zu analysierenden Format wurde hinzugefügt.
- Flag in `azcmagent show`-Ausgabe wurde hinzugefügt, um anzugeben, ob sich der Server auf einem in Azure gehosteten virtuellen Computer befindet.
- `azcmagent disconnect --force-local-only`-Option wurde hinzugefügt, um das Zurücksetzen des lokalen Agent-Zustands zuzulassen, wenn der Azure-Dienst nicht erreichbar ist.
- `azcmagent connect --cloud`-Option zur Unterstützung weiterer Clouds wurde hinzugefügt. In diesem Release wird nur Azure vom Dienst zum Zeitpunkt des Agent-Releases unterstützt.
- Der Agent wurde in von Azure unterstützte Sprachen lokalisiert.

### <a name="fixed"></a>Fest

- Verbesserungen der Verbindungsüberwachung.
- Problem wurde behoben, durch das die Proxyservereinstellungen beim Upgrade des Agents unter Linux verloren gingen.
- Probleme beim Versuch behoben, den Agent auf einem Server zu installieren, auf dem Windows Server 2012 R2 ausgeführt wird.
- Verbesserungen bei der Zuverlässigkeit der Erweiterung.

## <a name="august-2020"></a>August 2020

Version: 0.11

- Für dieses Release wurde zuvor Unterstützung für Ubuntu 20.04 angekündigt. Da einige Azure-VM-Erweiterungen Ubuntu 20.04 nicht unterstützen, wird Unterstützung für diese Version von Ubuntu entfernt.

- Verbesserungen bei der Zuverlässigkeit von Erweiterungsbereitstellungen.

### <a name="known-issues"></a>Bekannte Probleme

Wenn Sie eine ältere Version des Linux-Agents verwenden und diesen für die Verwendung eines Proxyservers konfiguriert haben, müssen Sie die Proxyservereinstellung nach dem Upgrade neu konfigurieren. Führen Sie hierzu `sudo azcmagent_proxy add http://proxyserver.local:83` aus.

## <a name="next-steps"></a>Nächste Schritte

Bevor Sie Server mit Arc-Unterstützung auf mehreren Hybridcomputern auswerten oder aktivieren, lesen Sie [Übersicht über den Connected Machine-Agent](agent-overview.md), um die Anforderungen, technischen Details zum Agent und Bereitstellungsmethoden zu verstehen.
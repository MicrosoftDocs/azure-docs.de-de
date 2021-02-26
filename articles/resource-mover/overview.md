---
title: Was ist Azure Resource Mover?
description: Hier finden Sie Informationen zu Azure Resource Mover.
author: rayne-wiselman
manager: evansma
ms.service: azure-migrate
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 06d6352f018238318c3bb4625ae86a2974f14569
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820162"
---
# <a name="what-is-azure-resource-mover"></a>Was ist Azure Resource Mover?

Dieser Artikel enthält eine Übersicht über den Azure Resource Mover-Dienst. Mit Azure Resource Mover können Sie Azure-Ressourcen zwischen Azure-Regionen verschieben.

Sie können Ressourcen aus folgenden Gründen in verschiedene Azure-Regionen verschieben:

- **Ausrichtung an einer neuen Region**: Verschieben von Ressourcen in eine neu eingeführte Azure-Region, die zuvor noch nicht verfügbar war.
- **Ausrichtung für Dienste/Features**: Verschieben von Ressourcen, um die Vorteile der Dienste oder Features zu nutzen, die in einer bestimmten Region verfügbar sind.
- **Reagieren auf Geschäftsentwicklungen**: Verschieben von Ressourcen in eine Region als Reaktion auf Geschäftsänderungen, z.B. Fusionen oder Akquisitionen.
- **Ausrichtung an einer nahegelegenen Region**: Verschieben von Ressourcen in eine Region, die für Ihr Unternehmen lokal ist.
- **Erfüllen von Datenanforderungen**: Verschieben von Ressourcen, um Anforderungen an Datenresidenz oder an Datenklassifizierung zu erfüllen
- **Reagieren auf Bereitstellungsanforderungen**: Verschieben von Ressourcen, die fehlerhaft bereitgestellt wurden, oder als Reaktion auf Kapazitätsanforderungen.
- **Reagieren auf Außerbetriebnahmen**: Verschieben von Ressourcen aufgrund der Außerbetriebnahme einer Region


## <a name="why-use-resource-mover"></a>Gründe für die Verwendung von Azure Resource Mover

Resource Mover bietet Folgendes:

- Eine einzelne Anlaufstelle für das regionsübergreifende Verschieben von Ressourcen.
- Kürzere Verschiebungszeit und verringerte Komplexität Alle erforderlichen Komponenten an einem einzelnen Ort
- Ein einfaches und konsistentes Verfahren zum Verschieben verschiedener Arten von Azure-Ressourcen
- Eine einfache Möglichkeit zum Erkennen von Abhängigkeiten zwischen den zu verschiebenden Ressourcen. Dies hilft Ihnen, zusammenhängende Ressourcen gemeinsam zu verschieben, damit nach der Verschiebung in der Zielregion alles wie erwartet funktioniert.
- Automatische Bereinigung der Ressourcen in der Quellregion, falls Sie sie nach dem Verschieben löschen möchten
- Tests. Sie können eine Verschiebung ausprobieren und sie dann verwerfen, wenn Sie keine vollständige Verschiebung durchführen möchten.

## <a name="move-across-regions"></a>Regionsübergreifendes Verschieben

Wählen Sie für die Migration in andere Regionen die Ressourcen aus, die Sie verschieben möchten. Azure Resource Mover überprüft diese Ressourcen und löst jegliche bestehenden Abhängigkeiten von anderen Ressourcen auf. Wenn Abhängigkeiten vorliegen, stehen Ihnen mehrere Optionen zur Verfügung:
- Sie können die abhängigen Ressourcen zur Zielregion verschieben.
- Sie können die abhängigen Ressourcen nicht verschieben, aber stattdessen gleichwertige Ressourcen in der Zielregion verwenden.

Nachdem alle Abhängigkeiten aufgelöst wurden, führt Azure Resource Mover Sie durch einen einfachen Migrationsprozess.

1. Sie starten eine anfängliche Verschiebung.
2. Nach der anfänglichen Verschiebung können Sie entscheiden, ob Sie die Verschiebung abschließen oder verwerfen.
3. Nach Abschluss der Verschiebung können Sie entscheiden, ob Sie die Ressourcen am Ursprungsstandort löschen möchten.

Sie können Ressourcen innerhalb des Azure Resource Mover-Hubs oder innerhalb einer Ressourcengruppe verschieben. [Weitere Informationen](select-move-tool.md)

## <a name="what-resources-can-i-move-across-regions"></a>Welche Ressourcen kann ich mithilfe von Azure Resource Mover in andere Regionen verschieben?

Mit Resource Mover können Sie derzeit die folgenden Ressourcen regionsübergreifend verschieben:

- Azure-VMs und zugehörige Datenträger
- Verschlüsselte virtuelle Azure-Computer und zugehörige Datenträger. Dazu zählen virtuelle Computer mit aktivierter Azure-Datenträgerverschlüsselung und virtuelle Azure-Computer mit der serverseitigen Standardverschlüsselung (sowohl mit plattformseitig verwalteten Schlüsseln als auch kundenseitig verwalteten Schlüsseln).
- NICs
- Verfügbarkeitsgruppen 
- Virtuelle Azure-Netzwerke 
- Öffentliche IP-Adressen
- Netzwerksicherheitsgruppen (NSGs)
- Interne und öffentliche Load Balancer 
- Azure SQL-Datenbanken und Pools für elastische Datenbanken


## <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie mehr](about-move-process.md) über Resource Mover-Komponenten und den Verschiebungsvorgang.

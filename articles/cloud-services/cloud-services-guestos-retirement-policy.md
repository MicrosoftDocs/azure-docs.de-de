---
title: Unterstützungs- und Deaktivierungsrichtlinien für Azure-Gastbetriebssysteme | Microsoft-Dokumentation
description: Bietet Informationen zu den Elementen, die Microsoft hinsichtlich des von Clouddiensten verwendeten Azure-Gastbetriebssystems unterstützt.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: ce66d44c0ddb84ed8c2908d02b8062195d6b461d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61215838"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Unterstützungs- und Deaktivierungsrichtlinie für Azure-Gastbetriebssysteme
Die Informationen auf dieser Seite beziehen sich auf das Azure-Gastbetriebssystem ([Gast-BS](cloud-services-guestos-update-matrix.md)) für Worker- und Webrollen für Clouddienste (PaaS). Sie gelten nicht für virtuelle Computer (IaaS).

Microsoft verfügt über eine veröffentlichte [Unterstützungsrichtlinie für das Gastbetriebssystem](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq). Auf der vorliegenden Seite wird beschrieben, wie diese Richtlinie implementiert wird.

Die Richtlinie lautet:

1. Microsoft unterstützt **mindestens die beiden neuesten Gastbetriebssystemfamilien**. Wenn eine Familie deaktiviert wird, haben die Kunden ab dem offiziellen Deaktivierungsdatum 12 Monate Zeit, um auf eine neuere unterstützte Gastbetriebssystemfamilie zu aktualisieren.
2. Microsoft unterstützt **mindestens die beiden neuesten Versionen der unterstützten Gastbetriebssystemfamilien**.
3. Microsoft unterstützt **mindestens die beiden neuesten Versionen des Azure SDK**. Wenn eine SDK-Version deaktiviert wird, haben die Kunden ab dem offiziellen Deaktivierungsdatum 12 Monate Zeit, um auf eine neuere Version zu aktualisieren.

Es werden möglicherweise mehr als zwei Familien oder Versionen gleichzeitig unterstützt. Offizielle Informationen zur Unterstützung von Gastbetriebssystemen finden Sie auf der Seite [Azure-Gastbetriebssystemreleases und SDK-Kompatibilitätsmatrix](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-version-is-retired"></a>Wann wird eine Gast-BS-Version deaktiviert?
Neue **Versionen** von Gastbetriebssystemen werden etwa jeden Monat eingeführt und enthalten die neuesten MSRC-Updates. Aufgrund dieser regelmäßigen monatlichen Updates wird eine Gastbetriebssystemversion normalerweise etwa 60 Tage nach ihrer Veröffentlichung deaktiviert. Durch diese Aktivität sind mindestens zwei Versionen jeder Gastbetriebssystemfamilie zur Verwendung verfügbar.

### <a name="process-during-a-guest-os-family-retirement"></a>Verfahren während der Deaktivierung einer Gastbetriebssystemfamilie
Nachdem die Deaktivierung angekündigt wurde, steht den Kunden ein Übergangszeitraum von 12 Monaten zur Verfügung, bevor die ältere Familie offiziell aus dem Dienst entfernt wird. Diese Übergangszeit kann nach dem Ermessen von Microsoft verlängert werden. Updates werden auf der Seite [Azure-Gastbetriebssystemreleases und SDK-Kompatibilitätsmatrix](cloud-services-guestos-update-matrix.md)veröffentlicht.

Sechs (6) Monate nach Beginn des Übergangszeitraums beginnt ein abgestufter Deaktivierungsprozess. Während dieses Zeit geschieht Folgendes:

1. Microsoft benachrichtigt die Kunden über die Deaktivierung.
2. Die neuere Version des Azure SDK unterstützt die deaktivierte Gastbetriebssystemfamilie nicht.
3. Neue und erneute Bereitstellungen von Clouddiensten sind in der deaktivierten Familie nicht zulässig.

Microsoft führt bis zum letzten Tag des Übergangszeitraums, dem so genannten "Ablaufdatum", weiterhin neue Gastbetriebsversionen einschließlich neuer MSRC-Updates ein. Am Ablaufdatum wird die Unterstützung für alle noch ausgeführten Clouddienste im Rahmen der Azure-SLA aufgehoben. Nach diesem Datum kann Microsoft nach eigenem Ermessen ein Upgrade für diese Dienste erzwingen oder diese Dienste löschen oder beenden.

### <a name="process-during-a-guest-os-version-retirement"></a>Verfahren während der Deaktivierung einer Gastbetriebssystemversion
Wenn Kunden ihr Gastbetriebssystem so eingerichtet haben, dass es automatisch aktualisiert wird, müssen sie sich keine Gedanken um Gastbetriebssystemversionen machen. Sie werden immer die neueste Version des Gastbetriebssystems verwenden.

Gastbetriebssystemversionen werden jeden Monat veröffentlicht. Aufgrund der Häufigkeit der regulären Releases hat jede Version eine feste Lebensdauer.

60 Tage nach Beginn der Lebensdauer wird eine Version *deaktiviert*. „Deaktiviert“ bedeutet, dass die Version aus dem Portal entfernt wurde. Die Version kann nicht mehr über die CSCFG-Konfigurationsdatei festgelegt werden. Vorhandene Bereitstellungen werden weiterhin ausgeführt. Neue Bereitstellungen sowie Code- und Konfigurationsupdates für vorhandene Bereitstellungen sind jedoch nicht zulässig.

Einige Zeit nach der „Deaktivierung“ wird die Gastbetriebssystemversion „ablaufen“, und alle Installationen, die noch diese abgelaufene Version ausführen, werden Sicherheitsrisiken ausgesetzt. Generell erfolgt der Ablauf in Batches, sodass die Zeitspanne zwischen Deaktivierung und tatsächlichem Ablauf variieren kann.

Kunden, die ihre Dienste für die manuelle Aktualisierung des Gastbetriebssystems konfigurieren, sollten sicherstellen, dass ihre Dienste unter einem unterstützten Gastbetriebssystem ausgeführt werden. Wenn ein Dienst für die automatische Aktualisierung des Gastbetriebssystems konfiguriert ist, stellt die zugrunde liegende Plattform die Compliance sicher und führt ein Upgrade auf das neueste Gastbetriebssystem aus.

Diese Zeiträume können nach Ermessen von Microsoft verlängert werden, um den Kunden den Übergang zu erleichtern. Updates werden auf der Seite [Azure-Gastbetriebssystemreleases und SDK-Kompatibilitätsmatrix](cloud-services-guestos-update-matrix.md)veröffentlicht.

### <a name="notifications-during-retirement"></a>Benachrichtigungen während der Deaktivierung
* **Deaktivierung der Familie** <br>Microsoft verwendet Blogbeiträge sowie Benachrichtigungen im Portal, um die Kunden zu informieren. Bei Kunden, die eine deaktivierte Gastbetriebssystemfamilie weiterhin verwenden, werden zugewiesene Dienstadministratoren auf direktem Weg benachrichtigt (E-Mail, Mitteilung im Portal, Telefonanruf). Alle Änderungen werden auf der Seite [Azure-Gastbetriebssystemreleases und SDK-Kompatibilitätsmatrix](cloud-services-guestos-update-matrix.md) veröffentlicht.
* **Deaktivierung der Version** <br>Alle Änderungen und die dazugehörigen Termine werden auf der Seite [Azure-Gastbetriebssystemversionen und SDK-Kompatibilitätsmatrix](cloud-services-guestos-update-matrix.md) veröffentlicht – einschließlich der Termine für Veröffentlichung, Deaktivierung und Ablauf. Dienstadministratoren erhalten E-Mails, wenn sie über Bereitstellungen verfügen, die unter einer deaktivierten Gastbetriebssystemversion oder -familie ausgeführt werden. Diese E-Mails können zu verschiedenen Zeitpunkten gesendet werden. Im Allgemeinen werden sie mindestens einen Monat vor der Deaktivierung gesendet, dies ist jedoch keine offizielle SLA.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen
**Wie kann ich die Auswirkungen einer Migration minimieren?**

Es empfiehlt sich, dass Sie die neueste Gastbetriebssystemfamilie verwenden, um Ihre Clouddienste zu konzipieren.

1. Starten Sie die Planung der Migration zu einer neueren Familie frühzeitig.
2. Richten Sie temporäre Testbereitstellungen ein, um die Ausführung Ihres Clouddiensts unter der neuen Gastbetriebssystemfamilie zu testen.
3. Legen Sie die Gastbetriebssystemversion auf **Automatisch** fest („osVersion=*“ in der [CSCFG](cloud-services-model-and-package.md#cscfg) -Datei), sodass die Migration zu neuen Gastbetriebssystemversionen automatisch erfolgt.

**Was geschieht, wenn meine Webanwendung eine tiefer greifende tiefere Integration in das Betriebssystem erfordert?**

Wenn die Architektur Ihrer Webanwendung von zugrunde liegenden Features des Betriebssystems abhängt, verwenden Sie von der Plattform unterstützte Funktionen wie z.B. [Starttasks](cloud-services-startup-tasks.md) oder andere Mechanismen zur Erweiterbarkeit. Alternativ dazu können Sie auch [Azure Virtual Machines](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – Infrastructure-as-a-Service) verwenden, wo die Verwaltung des zugrunde liegenden Betriebssystems in Ihrem Verantwortungsbereich liegt.

## <a name="next-steps"></a>Nächste Schritte
Überprüfen Sie die neuesten [Gastbetriebssystemreleases](cloud-services-guestos-update-matrix.md).

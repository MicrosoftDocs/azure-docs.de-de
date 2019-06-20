---
title: Zuordnungen zwischen Azure Automation und einem Log Analytics-Arbeitsbereich
description: In diesem Artikel werden die Zuordnungen beschrieben, die zwischen einem Automation-Konto und einem Log Analytics-Arbeitsbereich zulässig sind, um die Lösung zu unterstützen.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8afe8fed8912dd365071f1c4c5560c9f5578dcd8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66133101"
---
# <a name="workspace-mappings"></a>Arbeitsbereichzuordnungen

Beim Aktivieren von Lösungen, z. B. Updateverwaltung, Änderungsnachverfolgung und Bestand oder der Lösung „VMs außerhalb der Geschäftszeiten starten/beenden“, werden in Bezug auf das Verknüpfen eines Log Analytics-Arbeitsbereichs mit einem Automation-Konto nur bestimmte Regionen unterstützt. Diese Zuordnung gilt nur für das Automation-Konto und den Log Analytics-Arbeitsbereich. Die Ressourcen, die Daten an Ihr Automation-Konto oder den Log Analytics-Arbeitsbereich melden, können sich in anderen Regionen befinden.

## <a name="supported-mappings"></a>Unterstützte Zuordnungen

Die folgende Tabelle zeigt die unterstützten Zuordnungen:

|**Log Analytics-Arbeitsbereichsregion**|**Azure Automation-Region**|
|---|---|
|**USA**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Kanada**||
|CanadaCentral|CanadaCentral|
|**Asien-Pazifik**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**Europa**||
|UKSouth|UKSouth|
|Europa, Westen|Europa, Westen|
|**US Gov**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> EastUS-Zuordnung für Log Analytics-Arbeitsbereiche zu Automation-Konten ist keine exakte Region-zu-Region-Zuordnung, aber es handelt sich um die richtige Zuordnung.

<sup>2</sup> Aufgrund von Kapazitätseinschränkungen ist die Region nicht verfügbar, wenn neue Ressourcen erstellt werden. Dies umfasst auch Automation-Konten und Log Analytics-Arbeitsbereiche. Allerdings sollten bereits in der Region vorhandene verknüpfte Ressourcen weiterhin funktionieren.

## <a name="unlink-workspace"></a>Aufheben der Verknüpfung mit dem Arbeitsbereich

Wenn Sie Ihr Automation-Konto nicht länger in einen Log Analytics-Arbeitsbereich integriert sein soll, können Sie die Verknüpfung direkt im Azure-Portal aufheben. Vor dem Fortfahren müssen Sie zuerst die Lösungen für Updateverwaltung, Änderungsnachverfolgung und Bestand bzw. die Lösung „VMs außerhalb der Geschäftszeiten starten/beenden“ entfernen, falls Sie sie nutzen. Wenn Sie das Entfernen nicht durchführen, wird der Prozess nicht fortgesetzt. Lesen Sie den Artikel für die jeweilige Lösung, die Sie importiert haben, um die Schritte zu deren Entfernung zu verstehen.

Nach dem Entfernen dieser Lösungen können Sie die folgenden Schritte ausführen, um die Verknüpfung Ihres Automation-Kontos aufzuheben.

> [!NOTE]
> Einige Lösungen – einschließlich früherer Versionen der Azure SQL-Überwachungslösung – haben möglicherweise Automatisierungsressourcen erstellt und müssen möglicherweise vor dem Aufheben der Verknüpfung des Arbeitsbereichs entfernt werden.

1. Öffnen Sie im Azure-Portal Ihr Automation-Konto, und wählen Sie links auf der Seite „Automation-Konto“ im Abschnitt **Zugehörige Ressourcen** die Option **Verknüpfter Arbeitsbereich** aus.

2. Klicken Sie auf der Seite „Verknüpfung des Arbeitsbereichs aufheben“ auf **Verknüpfung des Arbeitsbereichs aufheben**. Sie werden gefragt, ob Sie fortfahren möchten.

3. Während Azure Automation versucht, die Verknüpfung des Kontos mit Ihrem Log Analytics-Arbeitsbereich aufzuheben, können Sie den Fortschritt unter **Benachrichtigungen** im Menü nachverfolgen.

Wenn Sie die Lösung „Updateverwaltung“ verwendet haben, können Sie optional die folgenden Elemente entfernen, die nach dem Entfernen der Lösung nicht mehr benötigt werden.

* Zeitpläne für Updates: Diese verfügen über Namen, die den Updatebereitstellungen entsprechen, die Sie erstellt haben.

* Für die Lösung erstellte Hybrid Worker-Gruppen: Jede wird ähnlich wie „machine1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8“ benannt.

Wenn Sie die Lösung „Starten und Beenden von VMs außerhalb der Kernzeit“ verwendet haben, können Sie optional die folgenden Elemente entfernen, die nach dem Entfernen der Lösung nicht mehr benötigt werden.

* Starten und beenden Sie Zeitpläne für VM-Runbooks.
* Starten und beenden Sie VM-Runbooks.
* Variables

Alternativ können Sie Ihren Arbeitsbereich auch von Ihrem Automation-Konto in Ihrem Log Analytics-Arbeitsbereich trennen. Wählen Sie in Ihrem Arbeitsbereich unter **Verwandte Ressourcen** die Option **Automation-Konto**. Klicken Sie auf der Seite „Automation-Konto“ auf **Verknüpfung zu diesem Konto aufheben**.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich, wie Sie das Onboarding für die folgenden Lösungen durchführen:

Updateverwaltung, Änderungsnachverfolgung und Bestand:

* Über einen [virtuellen Computer](../automation-onboard-solutions-from-vm.md)
* Über Ihr [Automation-Konto](../automation-onboard-solutions-from-automation-account.md)
* Über das [Durchsuchen mehrerer Computer](../automation-onboard-solutions-from-browse.md)
* Über ein [Runbook](../automation-onboard-solutions.md)

Starten/Beenden von VMs außerhalb der Kernzeit

* [Bereitstellen der Lösung „VMs außerhalb der Geschäftszeiten starten/beenden“](../automation-solution-vm-management.md)
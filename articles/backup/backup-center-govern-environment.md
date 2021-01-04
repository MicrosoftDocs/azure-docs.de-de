---
title: Steuern Ihres Sicherungsbestands mit Backup Center
description: Erfahren Sie, wie Sie Ihre Azure-Umgebung so steuern, dass sichergestellt ist, dass sämtliche Ihrer Ressourcen aus Sicherungssicht zu Backup Center konform sind.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: ebb4d72c3a3377072e185251bd642762f6876c03
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173798"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>Steuern Ihres Sicherungsbestands mit Backup Center

Backup Center unterstützt Sie dabei, Ihre Azure-Umgebung so zu steuern, dass sichergestellt ist, dass sämtliche Ihrer Ressourcen aus Sicherungssicht zu Backup Center konform sind. Nachstehend sind einige der Steuerungsfunktionen von Backup Center aufgeführt:

* Anzeigen und Zuweisen von Azure-Richtlinien für Sicherungen

* Anzeigen der Compliance Ihrer Ressourcen für alle integrierten Azure-Richtlinien für die Sicherung

* Anzeigen aller Datenquellen, die nicht für eine Sicherung konfiguriert wurden

## <a name="supported-scenarios"></a>Unterstützte Szenarien

* Eine ausführliche Liste der unterstützten und nicht unterstützten Szenarien finden Sie in der [Supportmatrix](backup-center-support-matrix.md).

## <a name="azure-policies-for-backup"></a>Azure-Richtlinien für Sicherung

Um alle [Azure-Richtlinien](../governance/policy/overview.md) anzuzeigen, die für Sicherungen verfügbar sind, wählen Sie das Menüelement **Azure-Richtlinien für Sicherung** aus. Danach werden alle integrierten und benutzerdefinierten [Azure-Richtliniendefinitionen für Sicherung](policy-reference.md) angezeigt, die Sie Ihren Abonnements und Ressourcengruppen zuweisen können.

Nachdem Sie eine der Definitionen ausgewählt haben, können Sie [die Richtlinie einem Bereich zuweisen](../governance/policy/tutorials/create-and-manage.md#assign-a-policy).

![Azure Policy-Definitionen auswählen](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="backup-compliance"></a>Sicherungskonformität

Wenn Sie auf das Menüelement „Backup Compliance“ (Sicherungskonformität) klicken, können Sie die [Konformität](../governance/policy/how-to/get-compliance-data.md) Ihrer Ressourcen entsprechend den verschiedenen integrierten Richtlinien anzeigen, die Sie Ihrer Azure-Umgebung zugewiesen haben. Sie können den Prozentsatz der Ressourcen, die mit allen Richtlinien konform sind, sowie die Richtlinien mit mindestens einer nicht konformen Ressource anzeigen.

![Anzeigen der Sicherungskonformität](./media/backup-center-govern-environment/azure-policy-compliance.png)

## <a name="protectable-datasources"></a>Schützbare Datenquellen

Wenn Sie das Menüelement **Schützbare Datenquellen** auswählen, können Sie alle Datenquellen anzeigen, die noch nicht für die Sicherung konfiguriert wurden. Sie können die Liste nach Datenquellenabonnement, -ressourcengruppe, -speicherort, -typ und Tags filtern. Sobald Sie eine Datenquelle ermittelt haben, die gesichert werden muss, können Sie mit der rechten Maustaste auf das entsprechende Rasterelement klicken und **Sicherung** auswählen, um die Sicherung für die Ressource zu konfigurieren.

![Menüelement „Schützbare Datenquellen“](./media/backup-center-govern-environment/protectable-datasources.png)

## <a name="next-steps"></a>Nächste Schritte

* [Überwachen und Betreiben von Sicherungen](backup-center-monitor-operate.md)
* [Ausführen von Aktionen mithilfe des Backup Centers](backup-center-actions.md)
* [Gewinnen von Einblicken in Ihre Sicherungen](backup-center-obtain-insights.md)
---
title: 'Azure Active Directory Domain Services: Angehaltene Domänen | Microsoft-Dokumentation'
description: Anhalten und Löschen verwalteter Domänen
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: a4e43518ce70c040b8243034c52e4b215548a47c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245407"
---
# <a name="suspended-domains"></a>Angehaltene Domänen
Wenn Azure Active Directory Domain Services (Azure AD DS) Anforderungen für eine verwaltete Domäne für einen längeren Zeitraum-nicht verarbeiten kann, wird die verwaltete Domäne in den Zustand „Angehalten“ versetzt. In diesem Artikel wird erläutert, weshalb verwaltete Domänen angehalten werden und wie Sie eine angehaltene Domäne bereinigen.


## <a name="states-your-managed-domain-can-be-in"></a>Mögliche Zustände für Ihre verwaltete Domäne

![Zeitachse für angehaltene Domänen](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

In der Grafik oben werden die möglichen Zustände einer von Azure AD DS verwalteten Domäne dargestellt.

### <a name="running-state"></a>Zustand „Wird ausgeführt“
Eine verwaltete Domäne, die ordnungsgemäß konfiguriert ist und regulär arbeitet, befindet sich im Zustand **Wird ausgeführt**.

**Was ist zu erwarten?**
* Microsoft kann die Integrität Ihrer verwalteten Domäne regelmäßig überwachen.
* Domänencontroller für Ihre verwaltete Domäne werden regelmäßig gepatcht und aktualisiert.
* Änderungen von Azure Active Directory werden regelmäßig mit Ihrer verwalteten Domäne synchronisiert.
* Für Ihre verwaltete Domäne werden regelmäßige Sicherungen durchgeführt.


### <a name="needs-attention-state"></a>Zustand „Eingreifen erforderlich“
Eine verwaltete Domäne befindet sich im Zustand **Eingreifen erforderlich**, wenn mindestens ein Problem erfordert, dass ein Administrator Maßnahmen ergreift. Auf der Integritätsseite Ihrer verwalteten Domäne wird mindestens Warnung zu diesem Zustand aufgelistet.

Wenn Sie beispielsweise eine restriktive Netzwerksicherheitsgruppe für Ihr virtuelles Netzwerk konfiguriert haben, ist Microsoft möglicherweise nicht in der Lage, Ihre verwaltete Domäne zu aktualisieren und zu überwachen. Diese ungültige Konfiguration löst eine Warnung aus, die Ihre verwaltete Domäne in den Zustand „Eingreifen erforderlich“ versetzt.

Jede Warnung verfügt über bestimmte Lösungsschritte. Einige Warnungen sind vorübergehend und werden vom Dienst automatisch aufgelöst. Sie können einige andere Warnungen mithilfe der Anweisungen aus den entsprechenden Lösungsschritten für diese Warnung auflösen. Bei einigen kritischen Warnungen müssen Sie sich an den Microsoft-Support wenden, damit diese aufgelöst werden.

Weitere Informationen finden Sie unter [Azure AD Domain Services – Problembehandlung von Warnungen](troubleshoot-alerts.md).

**Was ist zu erwarten?**

In einigen Fällen (z.B. bei einer ungültigen Netzwerkkonfiguration) sind die Domänencontroller für Ihre verwaltete Domäne möglicherweise nicht erreichbar. Wenn sich Ihre Domäne im Zustand „Eingreifen erforderlich“ befindet, kann Microsoft nicht garantieren, dass die Domäne regelmäßig überwacht, gepatcht, aktualisiert oder gesichert wird.

* Ihre verwaltete Domäne befindet sich in einem fehlerhaften Zustand und die fortlaufende Systemüberwachung wird möglicherweise beendet, bis die Warnung aufgelöst wurde.
* Domänencontroller für Ihre verwaltete Domäne können nicht gepatcht und aktualisiert werden.
* Änderungen von Azure Active Directory werden möglicherweise nicht mit Ihrer verwalteten Domäne synchronisiert.
* Nach Möglichkeit werden Sicherungen für Ihre verwalte Domäne durchgeführt.
* Wenn Sie Warnungen auflösen, die Ihre verwaltete Domäne beeinträchtigen, können Sie die Domäne möglicherweise wieder in den Zustand „Wird ausgeführt“ versetzen.
* Kritische Warnungen werden für Konfigurationsprobleme ausgelöst, wenn Microsoft nicht auf Ihre Domänencontroller zugreifen kann. Wenn solche Warnungen nicht innerhalb von 15 Tagen aufgelöst werden, wird Ihre verwaltete Domäne in den Zustand „Angehalten“ versetzt.


### <a name="the-suspended-state"></a>Zustand „Angehalten“
Eine verwaltete Domäne wird aus den folgenden Gründen in den Zustand **Angehalten** versetzt:

* Mindestens eine kritische Warnung wurde nicht innerhalb von 15 Tagen aufgelöst. Kritische Warnungen können durch eine Fehlkonfiguration verursacht werden, die den Zugriff auf von Azure AD DS benötigte Ressourcen blockiert.
    * Beispiel: Die Warnung [AADDS104: Netzwerkfehler](alert-nsg.md) wurde in der verwalteten Domäne mehr als 15 Tage lang nicht aufgelöst.
* Ein Abrechnungsproblem in Bezug auf Ihr Azure-Abonnement liegt vor, oder das Azure-Abonnement ist abgelaufen.

Verwaltete Domänen werden angehalten, wenn Microsoft nicht in der Lage ist, die Domäne fortlaufend zu verwalten, zu überwachen, zu patchen oder zu sichern.

**Was ist zu erwarten?**
* Die Bereitstellung der Domänencontroller für die verwaltete Domäne wird aufgehoben, und die Domänencontroller sind nicht mehr erreichbar im virtuellen Netzwerk.
* Der Secure LDAP-Zugriff auf Ihre verwaltete Domäne über das Internet (sofern aktiviert) funktioniert nicht mehr.
* Sie stellen Fehler beim Authentifizieren in der verwalteten Domäne, bei der Anmeldung bei in die Domäne eingebundenen virtuellen Computern oder beim Herstellen einer Verbindung über LDAP/LDAPS fest.
* Sicherungen für Ihre verwaltete Domäne werden nicht mehr erstellt.
* Die Synchronisierung mit Azure AD wird beendet.

Nachdem Sie die Warnung aufgelöst haben, wechselt Ihre verwaltete Domäne in den Zustand „Angehalten“. In diesem Fall müssen Sie sich an den Support wenden.
Der Support kann Ihre verwaltete Domäne möglicherweise wiederherstellen, allerdings nur dann, wenn eine weniger als 30 Tage alte Sicherung vorhanden ist.

Die verwaltete Domäne bleibt nur 15 Tage lang im Zustand „Angehalten“. Zum Wiederherstellen Ihrer verwalteten Domäne empfiehlt Microsoft, dass Sie kritische Warnungen sofort auflösen.


### <a name="deleted-state"></a>Zustand „Gelöscht“
Eine verwaltete Domäne, die 15 Tage im Zustand „Angehalten“ verbleibt, wird **gelöscht**.

**Was ist zu erwarten?**
* Alle Ressourcen und Sicherungen für die verwaltete Domäne werden gelöscht.
* Sie können die verwaltete Domäne nicht wiederherstellen und müssen eine neue verwaltete Domäne erstellen, um Azure AD DS zu verwenden.
* Nach dem Löschen wird Ihnen die verwaltete Domäne nicht mehr in Rechnung gestellt.


## <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Wie erkennen Sie, dass Ihre verwaltete Domäne angehalten wurde?
Sie sehen im Azure-Portal auf der Seite mit Informationen zur Integrität der Azure AD Domain Services eine [Warnung](troubleshoot-alerts.md), die angibt, dass die Domäne angehalten wurde. Der Zustand der Domäne wird ebenfalls als „Angehalten“ angezeigt.


## <a name="restore-a-suspended-domain"></a>Wiederherstellen einer angehaltenen Domäne
Um eine Domäne im Zustand „Angehalten“ wiederherzustellen, führen Sie die folgenden Schritte aus:

1. Wechseln Sie im Azure-Portal zur Seite der [Azure Active Directory Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices).
2. Wählen Sie die verwaltete Domäne aus.
3. Wählen Sie im linken Bereich **Integrität** aus.
4. Wählen Sie die Warnung aus. Die Warnungs-ID lautet je nach Grund für das Anhalten entweder AADDS503 oder AADDS504.
5. Klicken Sie in der Warnung auf den Lösungslink. Führen Sie die erforderlichen Schritte aus, um die Warnung aufzulösen.

Ihre verwaltete Domäne kann nur auf das Datum der letzten Sicherung wiederhergestellt werden. Das Datum der letzten Sicherung wird auf der Seite „Integrität“ Ihrer verwalteten Domäne angezeigt. Alle nach der letzten Sicherung aufgetretenen Änderungen werden nicht wiederhergestellt. Sicherungen für eine verwaltete Domäne werden bis zu 30 Tage lang gespeichert. Sicherungen, die älter als 30 Tage sind, werden gelöscht.


## <a name="next-steps"></a>Nächste Schritte
- [Azure AD Domain Services – Problembehandlung von Warnungen](troubleshoot-alerts.md)
- [Mehr über die Azure Active Directory Domain Services erfahren](overview.md)
- [Wenden Sie sich an das Produktteam](contact-us.md)

## <a name="contact-us"></a>Kontakt
Wenden Sie sich an das Produktteam der Azure Active Directory Domain Services, um [Feedback zu geben oder Support zu erhalten](contact-us.md).

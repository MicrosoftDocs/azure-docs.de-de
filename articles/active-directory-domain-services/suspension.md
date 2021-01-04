---
title: Angehaltene Domänen in Azure AD Domain Services | Microsoft-Dokumentation
description: Erfahren Sie etwas über die verschiedenen Integritätszustände bei einer von Azure AD DS verwalteten Domäne und wie eine angehaltene Domäne wiederhergestellt wird.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: b0b48e7ad494386052e6d94c32d7215b3f4f0202
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618823"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Grundlegendes zu den Integritätszuständen und dem Auflösen von angehaltenen Domänen in Azure Active Directory Domain Services

Wenn Azure Active Directory Domain Services (Azure AD DS) Anforderungen für eine verwaltete Domäne für einen längeren Zeitraum-nicht verarbeiten kann, wird die verwaltete Domäne in den Zustand „Angehalten“ versetzt. Bleibt eine verwaltete Domäne im Zustand „Angehalten“, wird sie automatisch gelöscht. Lösen Sie Warnungen so schnell wie möglich auf, um Ihre von Azure AD DS verwaltete Domäne fehlerfrei zu halten und ein Anhalten zu vermeiden.

In diesem Artikel wird erläutert, warum verwaltete Domänen angehalten werden und wie eine angehaltene Domäne wiederhergestellt wird.

## <a name="overview-of-managed-domain-states"></a>Übersicht über die Zustände einer verwalteten Domäne

Während des Lebenszyklus einer verwalteten Domäne gibt es unterschiedliche Zustände zur Angabe der Integrität. Wenn die verwaltete Domäne ein Problem meldet, lösen Sie die zugrunde liegende Ursache schnell auf, um zu verhindern, dass sich der Zustand verschlechtert.

![Der Verlauf von Zuständen einer verwalteten Domäne, bis sie angehalten wird](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Eine verwaltete Domäne kann sich in einem der folgenden Zustände befinden:

* [Wird ausgeführt](#running-state)
* [Eingreifen erforderlich](#needs-attention-state)
* [Angehalten](#suspended-state)
* [Gelöscht](#deleted-state)

## <a name="running-state"></a>Zustand „Wird ausgeführt“

Eine verwaltete Domäne, die ordnungsgemäß konfiguriert ist und ohne Probleme ausgeführt wird, befindet sich im Status *Wird ausgeführt*. Dies ist der gewünschte Zustand für eine verwaltete Domäne.

### <a name="what-to-expect"></a>Ausblick

* Die Azure-Plattform kann die Integrität der verwalteten Domäne regelmäßig überwachen.
* Domänencontroller für die verwaltete Domäne werden regelmäßig gepatcht und aktualisiert.
* Änderungen von Azure Active Directory werden mit der verwalteten Domäne regelmäßig synchronisiert.
* Für die verwaltete Domäne werden regelmäßige Sicherungen durchgeführt.

## <a name="needs-attention-state"></a>Zustand „Eingreifen erforderlich“

Eine verwaltete Domäne mit einem oder mehreren Problemen, die behoben werden müssen, befindet sich im Zustand *Eingreifen erforderlich*. Auf der Seite „Integrität“ für die verwaltete Domäne werden die Warnungen aufgelistet, und es wird angegeben, wo ein Problem aufgetreten ist.

Einige Warnungen sind vorübergehend und werden von der Azure-Plattform automatisch aufgelöst. Bei anderen Warnungen können Sie das Problem beheben, indem Sie die bereitgestellten Schritte zur Auflösung befolgen. Bei einer kritischen Warnung [öffnen Sie eine Azure-Supportanfrage][azure-support], um zusätzliche Hilfe bei der Problembehandlung zu erhalten.

Ein Beispiel für eine Warnung ist, wenn es eine eingeschränkte Netzwerksicherheitsgruppe gibt. In dieser Konfiguration kann die Azure-Plattform die verwaltete Domäne möglicherweise nicht aktualisieren und überwachen. Eine Warnung wird generiert, und der Zustand ändert sich in *Eingreifen erforderlich*.

Weitere Informationen finden Sie unter [Problembehandlung bei Warnungen für eine verwaltete Domäne][resolve-alerts].

### <a name="what-to-expect"></a>Ausblick

Wenn sich eine verwaltete Domäne im Zustand *Eingreifen erforderlich* befindet, kann die Azure-Plattform Daten möglicherweise nicht regelmäßig überwachen, patchen, aktualisieren oder sichern. In einigen Fällen, z. B. bei einer ungültigen Netzwerkkonfiguration, sind die Domänencontroller für die verwaltete Domäne vielleicht nicht erreichbar.

* Die verwaltete Domäne befindet sich in einem fehlerhaften Zustand, und die fortlaufende Systemüberwachung wird möglicherweise so lange beendet, bis die Warnung aufgelöst wurde.
* Domänencontroller für die verwaltete Domäne können nicht gepatcht oder aktualisiert werden.
* Änderungen von Azure Active Directory werden möglicherweise nicht mit der verwalteten Domäne synchronisiert.
* Sicherungen für die verwaltete Domäne können möglicherweise nicht durchgeführt werden.
* Wenn Sie nicht kritische Warnungen auflösen, die sich auf die verwaltete Domäne auswirken, sollte die Integrität in den Zustand *Wird ausgeführt* zurückkehren.
* Kritische Warnungen werden bei Konfigurationsproblemen ausgelöst, wenn die Azure-Plattform die Domänencontroller nicht erreichen kann. Sollten diese kritischen Warnungen nicht innerhalb von 15 Tagen aufgelöst werden, wechselt die verwaltete Domäne in den Zustand *Angehalten*.

## <a name="suspended-state"></a>Zustand „Angehalten“

Eine verwaltete Domäne wechselt aus einem der folgenden Gründe in den Zustand **Angehalten**:

* Mindestens eine kritische Warnung wurde nicht innerhalb von 15 Tagen aufgelöst.
    * Kritische Warnungen können durch eine Fehlkonfiguration verursacht werden, die den Zugriff auf von Azure AD DS benötigte Ressourcen blockiert. Beispiel: Die Warnung [AADDS104: Netzwerkfehler][alert-nsg] wurde in der verwalteten Domäne mehr als 15 Tage lang nicht aufgelöst.
* Es liegt ein Abrechnungsproblem beim Azure-Abonnement vor, oder das Azure-Abonnement ist abgelaufen.

Verwaltete Domänen werden angehalten, wenn die Azure-Plattform die Domäne nicht verwalten, überwachen, patchen oder sichern kann. Eine verwaltete Domäne bleibt 15 Tage lang im Zustand *Angehalten*. Wenn Sie weiterhin auf die verwaltete Domäne zugreifen möchten, lösen Sie kritische Warnungen sofort auf.

### <a name="what-to-expect"></a>Ausblick

Das folgende Verhalten tritt auf, wenn sich eine verwaltete Domäne im Zustand *Angehalten* befindet:

* Die Bereitstellung von Domänencontrollern für die verwaltete Domäne wird aufgehoben, und die Domänencontroller sind im virtuellen Netzwerk nicht mehr erreichbar.
* Der Secure LDAP-Zugriff auf die verwaltete Domäne über das Internet (sofern aktiviert) funktioniert nicht mehr.
* Es treten Fehler beim Authentifizieren in der verwalteten Domäne, bei der Anmeldung an in die Domäne eingebundenen VMs oder beim Herstellen einer Verbindung über LDAP/LDAPS auf.
* Sicherungen für die verwaltete Domäne werden nicht mehr erstellt.
* Die Synchronisierung mit Azure AD wird beendet.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Wie erkennen Sie, dass Ihre verwaltete Domäne angehalten wurde?

Sie sehen im Azure-Portal auf der Seite mit Informationen zur Integrität der Azure AD DS eine [Warnung][resolve-alerts], die mitteilt, dass die Domäne angehalten wurde. Der Zustand der Domäne wird ebenfalls als *Angehalten* angezeigt.

### <a name="restore-a-suspended-domain"></a>Wiederherstellen einer angehaltenen Domäne

Führen Sie die folgenden Schritte aus, um die Integrität einer verwalteten Domäne wiederherzustellen, die sich im Zustand *Angehalten* befindet:

1. Suchen Sie im Azure-Portal nach dem Eintrag **Domain Services**, und wählen Sie ihn aus.
1. Wählen Sie in der Liste Ihre verwaltete Domäne (z. B. *aaddscontoso.com*) aus, und wählen Sie dann **Integrität** aus.
1. Wählen Sie die Warnung aus,die je nach dem Grund für das Anhalten z.B. *AADDS503* oder *AADDS504* lautet.
1. Wählen Sie den Auflösungslink in der Warnung, und führen Sie die Schritte zu deren Auflösung aus.

Eine verwaltete Domäne kann nur auf das Datum der letzten Sicherung wiederhergestellt werden. Das Datum Ihrer letzten Sicherung wird auf der Seite **Integrität** der verwalteten Domäne angezeigt. Alle nach der letzten Sicherung aufgetretenen Änderungen werden nicht wiederhergestellt. Sicherungen für eine verwaltete Domäne werden bis zu 30 Tage lang gespeichert. Sicherungen, die älter als 30 Tage sind, werden gelöscht.

Nachdem Sie Warnungen aufgelöst haben, wenn sich die verwaltete Domäne im Status *Angehalten* befindet, [öffnen Sie eine Azure-Supportanfrage][azure-support], um in einen fehlerfreien Status zurückzukehren. Wenn eine Sicherung weniger als 30 Tage besteht, kann der Azure-Support die verwaltete Domäne wiederherstellen.

## <a name="deleted-state"></a>Zustand „Gelöscht“

Wenn sich eine verwaltete Domäne 15 Tage lang im Zustand *Angehalten* befindet, wird sie gelöscht. Dieser Prozess ist nicht wiederherstellbar.

### <a name="what-to-expect"></a>Ausblick

Wenn eine verwaltete Domäne in den Status *Gelöscht* wechselt, ergibt sich das folgende Verhalten:

* Alle Ressourcen und Sicherungen für die verwaltete Domäne werden gelöscht.
* Die verwaltete Domäne kann nicht wiederhergestellt werden. Sie müssen eine verwaltete Ersatzdomäne erstellen, um Azure AD DS wiederzuverwenden.
* Nach dem Löschen wird Ihnen die verwaltete Domäne nicht mehr in Rechnung gestellt.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich darüber, wie Sie [Warnungen für Ihre verwaltete Domäne auflösen][resolve-alerts] können, um Ihre verwaltete Domäne fehlerfrei zu halten und das Risiko zu minimieren, dass sie angehalten wird.

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md

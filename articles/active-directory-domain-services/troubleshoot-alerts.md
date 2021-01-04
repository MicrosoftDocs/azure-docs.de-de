---
title: Häufige Warnungen und deren Auflösung in Azure AD Domain Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie häufige Warnungen auflösen, die im Rahmen des Integritätsstatus für Azure Active Directory Domain Services generiert werden.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 80011b2ae2a70ce4c540ac31897cc8be4a6580c3
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618619"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>Bekannte Probleme: Häufige Warnungen und deren Auflösung in Azure AD Domain Services

Als zentraler Bestandteil von Identifizierung und Authentifizierung von Anwendungen treten bei Azure Active Directory Domain Services (Azure AD DS) manchmal Probleme auf. Wenn das der Fall ist, können Sie mithilfe einiger häufiger Warnungen und den entsprechenden Schritten zur Problembehandlung diese Fehler beheben und für einen reibungslosen Betrieb sorgen. Sie können auch jederzeit [eine Azure-Supportanfrage öffnen][azure-support], um weitere Hilfe bei der Problembehandlung zu erhalten.

Dieser Artikel enthält Informationen zur Problembehandlung für häufige Warnungen in Azure AD DS.

## <a name="aadds100-missing-directory"></a>AADDS100: Fehlendes Verzeichnis

### <a name="alert-message"></a>Warnmeldung

*Das Ihrer verwalteten Domäne zugeordnete Azure AD-Verzeichnis wurde möglicherweise gelöscht. Die verwaltete Domäne befindet sich nicht mehr in einer unterstützten Konfiguration. Microsoft kann Ihre verwaltete Domäne nicht überwachen, verwalten, patchen und synchronisieren.*

### <a name="resolution"></a>Lösung

Dieser Fehler wird normalerweise verursacht, wenn ein Azure-Abonnement in ein neues Azure AD-Verzeichnis verschoben und das alte Azure AD-Verzeichnis, das Azure AD DS zugeordnet ist, gelöscht wird.

Dieser Fehler ist nicht behebbar. Um das Problem zu lösen, [löschen Sie Ihre vorhandene verwaltete Domäne](delete-aadds.md), und erstellen Sie sie in Ihrem neuen Verzeichnis neu. Wenn Probleme beim Löschen der verwalteten Domäne auftreten, [öffnen Sie eine Azure-Supportanfrage][azure-support], um zusätzliche Hilfe bei der Problembehandlung zu erhalten.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C wird in diesem Verzeichnis ausgeführt

### <a name="alert-message"></a>Warnmeldung

*Azure AD Domain Services kann in einem Azure AD B2C-Verzeichnis nicht aktiviert werden.*

### <a name="resolution"></a>Lösung

Azure AD DS wird automatisch mit einem Azure AD-Verzeichnis synchronisiert. Wenn das Azure AD-Verzeichnis für B2C konfiguriert ist, kann Azure AD DS nicht bereitgestellt und synchronisiert werden.

Damit Azure AD DS verwendet werden kann, müssen Sie die verwaltete Domäne in einem Verzeichnis neu erstellen, das kein Azure AD B2C-Verzeichnis ist. Gehen Sie dazu wie folgt vor:

1. [Löschen Sie die verwaltete Domäne](delete-aadds.md) aus Ihrem vorhandenen Azure AD-Verzeichnis.
1. Erstellen Sie ein neues Azure AD-Verzeichnis, das kein Azure AD B2C-Verzeichnis ist.
1. [Erstellen Sie eine verwaltete Ersatzdomäne](tutorial-create-instance.md).

Die Integrität der verwalteten Domäne wird innerhalb von zwei Stunden automatisch aktualisiert, und die Warnung wird entfernt.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Adresse befindet sich in einem öffentlichen IP-Adressbereich

### <a name="alert-message"></a>Warnmeldung

*Der IP-Adressbereich für das virtuelle Netzwerk, in dem Sie Azure AD Domain Services aktiviert haben, liegt in einem öffentlichen IP-Bereich. Azure AD Domain Services müssen in einem virtuellen Netzwerk mit einem privaten IP-Adressbereich aktiviert werden. Diese Konfiguration wirkt sich darauf aus, wie Microsoft Ihre verwaltete Domäne überwachen, verwalten, patchen und synchronisieren kann.*

### <a name="resolution"></a>Lösung

Stellen Sie zunächst sicher, dass Sie mit [privaten IP v4-Adressräumen](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces) vertraut sind.

Innerhalb eines virtuellen Netzwerks können virtuelle Computer Anforderungen an Azure-Ressourcen richten, die sich im gleichen IP-Adressbereich befinden, der für das Subnetz konfiguriert wurde. Wenn Sie einen öffentlichen IP-Adressbereich für ein Subnetz konfigurieren, erreichen Anforderungen, die innerhalb eines virtuellen Netzwerks weitergeleitet werden, möglicherweise nicht die beabsichtigten Webressourcen. Diese Konfiguration kann zu unvorhersehbaren Fehlern bei Azure AD DS führen.

> [!NOTE]
> Wenn Sie der Besitzer des in Ihrem virtuellen Netzwerk konfigurierten IP-Adressbereichs sind, kann diese Warnung ignoriert werden. Allerdings kann Azure AD Domain Services die [Vereinbarung zum Servicelevel (SLA)](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) mit dieser Konfiguration nicht erfüllen, da sie zu unvorhersehbaren Fehlern führen kann.

Zum Auflösen dieser Warnung müssen Sie Ihre vorhandene verwaltete Domäne löschen und die Domäne in einem virtuellen Netzwerk mit einem privaten IP-Adressbereich neu erstellen. Dieser Vorgang geht mit einer Dienstunterbrechung einher, weil die verwaltete Domäne nicht verfügbar ist und alle von Ihnen erstellten benutzerdefinierten Ressourcen wie Organisationseinheiten oder Dienstkonten verloren gehen.

1. [Löschen Sie die verwaltete Domäne](delete-aadds.md) aus Ihrem Verzeichnis.
1. Um den IP-Adressbereich des virtuellen Netzwerks zu aktualisieren, suchen Sie im Azure-Portal nach dem Eintrag *Virtuelles Netzwerk*, und wählen Sie ihn aus. Wählen Sie das virtuelle Netzwerk für Azure AD DS aus, für das fälschlicherweise ein öffentlicher IP-Adressbereich festgelegt wurde.
1. Wählen Sie unter *Einstellungen* die Option **Adressraum** aus.
1. Aktualisieren Sie den Adressbereich, indem Sie den vorhandenen Adressbereich auswählen und bearbeiten oder einen zusätzlichen Adressbereich hinzufügen. Achten Sie dabei darauf, dass sich der neue IP-Adressbereich in einem privaten IP-Bereich befindet. **Speichern** Sie abschließend die Änderungen.
1. Wählen Sie im linken Navigationsbereich den Eintrag **Subnetze** aus.
1. Wählen Sie das Subnetz aus, das Sie bearbeiten möchten, oder erstellen Sie ein zusätzliches Subnetz.
1. Aktualisieren Sie einen privaten IP-Adressbereich, oder geben Sie einen Bereich an. **Speichern** Sie anschließend Ihre Änderungen.
1. [Erstellen Sie eine verwaltete Ersatzdomäne](tutorial-create-instance.md). Stellen Sie sicher, dass Sie ein aktualisiertes Subnetz eines virtuellen Netzwerks mit einem privaten IP-Adressbereich auswählen.

Die Integrität der verwalteten Domäne wird innerhalb von zwei Stunden automatisch aktualisiert, und die Warnung wird entfernt.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Ihr Azure-Abonnement wurde nicht gefunden

### <a name="alert-message"></a>Warnmeldung

*Ihr Azure-Abonnement, das Ihrer verwalteten Domäne zugeordnet ist, wurde gelöscht.  Azure AD Domain Services erfordert ein aktives Abonnement, um weiterhin ordnungsgemäß zu funktionieren.*

### <a name="resolution"></a>Lösung

Azure AD DS erfordert ein aktives Abonnement und kann nicht in ein anderes Abonnement verschoben werden. Wenn das Azure-Abonnement, dem die verwaltete Domäne zugeordnet war, gelöscht wird, müssen Sie ein Azure-Abonnement und eine verwaltete Domäne neu erstellen.

1. [Erstellen Sie ein Azure-Abonnement](../cost-management-billing/manage/create-subscription.md).
1. [Löschen Sie die verwaltete Domäne](delete-aadds.md) aus Ihrem vorhandenen Azure AD-Verzeichnis.
1. [Erstellen Sie eine verwaltete Ersatzdomäne](tutorial-create-instance.md).

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Das Azure-Abonnement wurde deaktiviert

### <a name="alert-message"></a>Warnmeldung

*Ihr Azure-Abonnement, das Ihrer verwalteten Domäne zugeordnet ist, ist inaktiv.  Azure AD Domain Services erfordert ein aktives Abonnement, um weiterhin ordnungsgemäß zu funktionieren.*

### <a name="resolution"></a>Lösung

Azure AD DS erfordert ein aktives Abonnement. Wenn das Azure-Abonnement, dem die verwaltete Domäne zugeordnet war, nicht aktiv ist, müssen Sie es erneuern, um das Abonnement zu reaktivieren.

1. [Erneuern Sie Ihr Azure-Abonnement.](../cost-management-billing/manage/subscription-disabled.md)
2. Nachdem das Abonnement erneuert wurde, können Sie mithilfe einer Azure AD DS-Benachrichtigung die verwaltete Domäne erneut aktivieren.

Nach der erneuten Aktivierung der verwalteten Domäne wird die Integrität der verwalteten Domäne innerhalb von zwei Stunden automatisch aktualisiert, und die Warnung wird entfernt.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Verzeichnisse durch Abonnement verschoben

### <a name="alert-message"></a>Warnmeldung

*Das von Azure AD Domain Services verwendete Abonnement wurde in ein anderes Verzeichnis verschoben. Für eine ordnungsgemäße Funktionsweise von Azure AD Domain Services muss ein aktives Abonnement im gleichen Verzeichnis vorhanden sein.*

### <a name="resolution"></a>Lösung

Azure AD DS erfordert ein aktives Abonnement und kann nicht in ein anderes Abonnement verschoben werden. Wenn das Azure-Abonnement, dem die verwaltete Domäne zugeordnet war, verschoben wurde, verschieben Sie das Abonnement wieder in das vorherige Verzeichnis, oder [löschen Sie Ihre verwaltete Domäne](delete-aadds.md) aus dem vorhandenen Verzeichnis, und [erstellen Sie eine verwaltete Ersatzdomäne im ausgewählten Abonnement](tutorial-create-instance.md).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: Keine Ressourcen für Ihre verwalteten Domäne gefunden

### <a name="alert-message"></a>Warnmeldung

*Eine Ressource, die für Ihre verwaltete Domäne verwendet wird, wurde gelöscht. Diese Ressource ist erforderlich, damit Azure AD Domain Services ordnungsgemäß funktioniert.*

### <a name="resolution"></a>Lösung

Azure AD DS erstellt zusätzliche Ressourcen wie öffentliche IP-Adressen, virtuelle Netzwerkschnittstellen und einen Load Balancer, um ordnungsgemäß funktionieren zu können. Wenn eine dieser Ressourcen gelöscht wird, befindet sich die verwaltete Domäne in einem nicht unterstützten Zustand und kann nicht mehr verwaltet werden kann. Weitere Informationen zu diesen Ressourcen finden Sie unter [Von Azure AD DS verwendete Netzwerkressourcen](network-considerations.md#network-resources-used-by-azure-ad-ds).

Diese Warnung wird generiert, wenn eine dieser erforderlichen Ressourcen gelöscht wird. Wenn die Ressource vor weniger als vier Stunden gelöscht wurde, besteht die Möglichkeit, dass die Azure-Plattform die gelöschte Ressource automatisch neu erstellen kann. In den folgenden Schritten wird beschrieben, wie Sie den Integritätsstatus und den Zeitstempel für das Löschen von Ressourcen überprüfen:

1. Suchen Sie im Azure-Portal nach dem Eintrag **Domain Services**, und wählen Sie ihn aus. Wählen Sie Ihre verwaltete Domäne (z. B. *aaddscontoso.com*) aus.
1. Wählen Sie im linken Navigationsbereich den Eintrag **Integrität** aus.
1. Wählen Sie auf der Seite „Integrität“ die Warnung mit der ID *AADDS109* aus.
1. Die Warnung enthält einen Zeitstempel für das erste Auftreten. Wenn dieser Zeitstempel vor weniger als 4 Stunden erstellt wurde, kann die Azure-Plattform die Ressource möglicherweise automatisch neu erstellen und die Warnung selbst auflösen.

    Wenn die Warnung mehr als 4 Stunden alt ist, kann die verwaltete Domäne nicht wiederhergestellt werden. [Löschen Sie die verwaltete Domäne](delete-aadds.md), und [erstellen Sie dann eine verwaltete Ersatzdomäne](tutorial-create-instance.md).

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: Das Ihrer verwalteten Domäne zugeordnete Subnetz ist voll

### <a name="alert-message"></a>Warnmeldung

*Das für die Bereitstellung von Azure AD Domain Services ausgewählte Subnetz ist voll und hat keinen Platz für den zusätzlichen Domänencontroller, der erstellt werden muss.*

### <a name="resolution"></a>Lösung

Das Subnetz des virtuellen Netzwerks für Azure AD DS benötigt genügend IP-Adressen für die automatisch erstellten Ressourcen. Dieser IP-Adressraum beinhaltet auch die Notwendigkeit zum Erstellen von Ersatzressourcen bei einem Wartungsereignis. Um das Risiko zu minimieren, dass nicht genügend IP-Adressen verfügbar sind, sollten Sie im selben Subnetz des virtuellen Netzwerks neben der verwalteten Domäne keine weiteren Ressourcen (z. B. eigene virtuelle Computer) bereitstellen.

Dieser Fehler ist nicht behebbar. Zum Auflösen der Warnung müssen Sie [Ihre vorhandene verwaltete Domäne löschen](delete-aadds.md) und neu erstellen. Wenn Probleme beim Löschen der verwalteten Domäne auftreten, [öffnen Sie eine Azure-Supportanfrage][azure-support], um zusätzliche Hilfe bei der Problembehandlung zu erhalten.

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Dienstprinzipal nicht autorisiert

### <a name="alert-message"></a>Warnmeldung

*Ein Dienstprinzipal, den Azure AD Domain Services für Ihre Domäne verwendet, ist für das Verwalten von Ressourcen im Azure-Abonnement nicht autorisiert. Der Dienstprinzipal muss die entsprechenden Berechtigungen für Ihre verwaltete Domäne erhalten.*

### <a name="resolution"></a>Lösung

Einige automatisch generierte Dienstprinzipale werden zum Verwalten und Erstellen von Ressourcen für eine verwaltete Domäne verwendet. Wenn die Zugriffsberechtigungen für einen dieser Dienstprinzipale geändert werden, können Ressourcen von der Domäne nicht ordnungsgemäß verwaltet werden. In den folgenden Schritten lernen Sie die Zugriffsberechtigungen für Dienstprinzipale kennen und erfahren, wie diese gewährt werden:

1. Lesen Sie hierzu auch [Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC und dem Azure-Portal](../role-based-access-control/role-assignments-portal.md).
2. Überprüfen Sie den Zugriff des Dienstprinzipals mit der ID *abba844e-bc0e-44b0-947a-dc74e5d09022*, und gewähren Sie den Zugriff, der zu einem früheren Zeitpunkt verweigert wurde.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Nicht genügend IP-Adressen in der verwalteten Domäne

### <a name="alert-message"></a>Warnmeldung

*Das Subnetz des virtuellen Netzwerks in dieser Domäne verfügt möglicherweise nicht über genügend IP-Adressen. Azure AD Domain Services benötigt mindestens zwei verfügbare IP-Adressen innerhalb des Subnetzes, in dem es aktiviert ist. Es wird empfohlen, mindestens 3 bis 5 freie IP-Adressen innerhalb des Subnetzes bereitzuhalten. Dies kann aufgetreten sein, wenn andere virtuelle Computer innerhalb des Subnetzes bereitgestellt wurden und damit die Anzahl der verfügbaren IP-Adressen ausgeschöpft wurde oder wenn die Anzahl der verfügbaren IP-Adressen im Subnetz beschränkt ist.*

### <a name="resolution"></a>Lösung

Das Subnetz des virtuellen Netzwerks für Azure AD DS benötigt genügend IP-Adressen für die automatisch erstellten Ressourcen. Dieser IP-Adressraum beinhaltet auch die Notwendigkeit zum Erstellen von Ersatzressourcen bei einem Wartungsereignis. Um das Risiko zu minimieren, dass nicht genügend IP-Adressen verfügbar sind, sollten Sie im selben Subnetz des virtuellen Netzwerks neben der verwalteten Domäne keine weiteren Ressourcen (z. B. eigene virtuelle Computer) bereitstellen.

Zum Auflösen dieser Warnung müssen Sie Ihre vorhandene verwaltete Domäne löschen und die Domäne in einem virtuellen Netzwerk mit einem ausreichend großen IP-Adressbereich neu erstellen. Dieser Vorgang geht mit einer Dienstunterbrechung einher, weil die verwaltete Domäne nicht verfügbar ist und alle von Ihnen erstellten benutzerdefinierten Ressourcen wie Organisationseinheiten oder Dienstkonten verloren gehen.

1. [Löschen Sie die verwaltete Domäne](delete-aadds.md) aus Ihrem Verzeichnis.
1. Um den IP-Adressbereich des virtuellen Netzwerks zu aktualisieren, suchen Sie im Azure-Portal nach dem Eintrag *Virtuelles Netzwerk*, und wählen Sie ihn aus. Wählen Sie das virtuelle Netzwerk für die verwaltete Domäne aus, für die ein zu kleiner IP-Adressbereich festgelegt ist.
1. Wählen Sie unter *Einstellungen* die Option **Adressraum** aus.
1. Aktualisieren Sie den Adressbereich, indem Sie den vorhandenen Adressbereich auswählen und bearbeiten oder einen zusätzlichen Adressbereich hinzufügen. Stellen Sie sicher, dass der neue IP-Adressbereich für den Subnetzbereich der verwalteten Domäne groß genug ist. **Speichern** Sie abschließend die Änderungen.
1. Wählen Sie im linken Navigationsbereich den Eintrag **Subnetze** aus.
1. Wählen Sie das Subnetz aus, das Sie bearbeiten möchten, oder erstellen Sie ein zusätzliches Subnetz.
1. Geben Sie einen genügend großen IP-Adressbereich an, oder aktualisieren Sie den vorhandenen. **Speichern** Sie anschließend Ihre Änderungen.
1. [Erstellen Sie eine verwaltete Ersatzdomäne](tutorial-create-instance.md). Stellen Sie sicher, dass Sie ein aktualisiertes Subnetz eines virtuellen Netzwerks mit einem genügend großen IP-Adressbereich auswählen.

Die Integrität der verwalteten Domäne wird innerhalb von zwei Stunden automatisch aktualisiert, und die Warnung wird entfernt.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Ressourcen nicht wiederherstellbar

### <a name="alert-message"></a>Warnmeldung

*Für die von Azure AD Domain Services verwendeten Ressourcen wurde ein unerwarteter Zustand erkannt, und sie können nicht wiederhergestellt werden.*

### <a name="resolution"></a>Lösung

Dieser Fehler ist nicht behebbar. Zum Auflösen der Warnung müssen Sie [Ihre vorhandene verwaltete Domäne löschen](delete-aadds.md) und neu erstellen. Wenn Probleme beim Löschen der verwalteten Domäne auftreten, [öffnen Sie eine Azure-Supportanfrage][azure-support], um zusätzliche Hilfe bei der Problembehandlung zu erhalten.

## <a name="aadds114-subnet-invalid"></a>AADDS114: Subnetz ungültig

### <a name="alert-message"></a>Warnmeldung

*Das für die Bereitstellung von Azure AD Domain Services ausgewählte Subnetz ist ungültig und kann nicht verwendet werden.*

### <a name="resolution"></a>Lösung

Dieser Fehler ist nicht behebbar. Zum Auflösen der Warnung müssen Sie [Ihre vorhandene verwaltete Domäne löschen](delete-aadds.md) und neu erstellen. Wenn Probleme beim Löschen der verwalteten Domäne auftreten, [öffnen Sie eine Azure-Supportanfrage][azure-support], um zusätzliche Hilfe bei der Problembehandlung zu erhalten.

## <a name="aadds115-resources-are-locked"></a>AADDS115: Ressourcen gesperrt

### <a name="alert-message"></a>Warnmeldung

*Mindestens eine der Netzwerkressourcen, die von der verwalteten Domäne verwendet werden, kann nicht betrieben werden, da der Zielbereich gesperrt wurde.*

### <a name="resolution"></a>Lösung

Ressourcensperren können auf Azure-Ressourcen angewandt werden, um Änderungs- oder Löschvorgänge zu verhindern. Da Azure AD DS ein verwalteter Dienst ist, muss die Azure-Plattform Konfigurationsänderungen vornehmen können. Wenn eine Ressourcensperre auf einige Azure AD DS-Komponenten angewendet wird, kann die Azure-Plattform keine Verwaltungsaufgaben ausführen.

Führen Sie die folgenden Schritte aus, um die Azure AD DS-Komponenten auf Ressourcensperren zu überprüfen und diese zu entfernen:

1. Überprüfen Sie für jede Netzwerkkomponente der verwalteten Domäne in der Ressourcengruppe (z. B. virtuelles Netzwerk, Netzwerkschnittstelle oder öffentliche IP-Adresse) die Vorgangsprotokolle im Azure-Portal. Diese Vorgangsprotokolle sollten darauf hinweisen, warum ein Vorgang fehlschlägt und wo eine Ressourcensperre angewendet wird.
1. Wählen Sie die Ressource aus, auf die eine Sperre angewendet wird. Wählen Sie dann unter **Sperren** die Sperre(n) aus, und entfernen Sie diese.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Ressourcen nicht verwendbar

### <a name="alert-message"></a>Warnmeldung

*Mindestens eine der Netzwerkressourcen, die von der verwalteten Domäne verwendet werden, kann aufgrund von Richtlinienbeschränkungen nicht betrieben werden.*

### <a name="resolution"></a>Lösung

Richtlinien werden auf Azure-Ressourcen und -Ressourcengruppen angewandt, um zu steuern, welche Konfigurationsaktionen zulässig sind. Da Azure AD DS ein verwalteter Dienst ist, muss die Azure-Plattform Konfigurationsänderungen vornehmen können. Wenn eine Richtlinie auf einige Azure AD DS-Komponenten angewendet wird, kann die Azure-Plattform möglicherweise keine Verwaltungsaufgaben mehr ausführen.

Führen Sie die folgenden Schritte aus, um die Azure AD DS-Komponenten auf angewendete Richtlinien zu überprüfen und entsprechend zu aktualisieren:

1. Überprüfen Sie für jede Netzwerkkomponente der verwalteten Domäne in der Ressourcengruppe (z. B. virtuelles Netzwerk, Netzwerkadapter oder öffentliche IP-Adresse) die Vorgangsprotokolle im Azure-Portal. Diese Vorgangsprotokolle sollten darauf hinweisen, warum ein Vorgang fehlschlägt und wo eine restriktive Richtlinie angewendet wird.
1. Wählen Sie die Ressource aus, auf die eine Richtlinie angewendet wird, und wählen Sie dann unter **Richtlinien** die Richtlinie aus, und bearbeiten Sie diese, damit sie weniger restriktiv ist.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Synchronisierung wurde nicht in kurzer Zeit abgeschlossen

### <a name="alert-message"></a>Warnmeldung

*Die verwaltete Domäne wurde zuletzt am [Datum] mit Azure AD synchronisiert. Benutzer können sich möglicherweise nicht bei der verwalteten Domäne anmelden, oder Gruppenmitgliedschaften sind möglicherweise nicht mit Azure AD synchronisiert.*

### <a name="resolution"></a>Lösung

[Überprüfen Sie die Integrität von Azure AD DS](check-health.md) auf Warnungen, die auf Probleme in der Konfiguration der verwalteten Domäne hinweisen könnten. Probleme mit der Netzwerkkonfiguration können die Synchronisierung von Azure AD verhindern. Wenn Sie Warnungen auflösen können, die auf ein Konfigurationsproblem hinweisen, warten Sie zwei Stunden, und überprüfen Sie dann, ob die Synchronisierung erfolgreich abgeschlossen wurde.

Die folgenden häufigen Gründe können dazu führen, dass die Synchronisierung in einer verwalteten Domäne beendet wird:

* Die erforderliche Netzwerkkonnektivität ist blockiert. Weitere Informationen zum Überprüfen des virtuellen Azure-Netzwerks auf Probleme und zum Ermitteln der erforderlichen Schritte finden Sie unter [Bekannte Probleme: Netzwerkkonfigurationswarnungen in Azure Active Directory Domain Services](alert-nsg.md) und [Überlegungen zum Entwurf virtueller Netzwerke und Konfigurationsoptionen für Azure Active Directory Domain Services](network-considerations.md).
*  Die Kennwortsynchronisierung wurde nicht eingerichtet oder nicht erfolgreich abgeschlossen, als die verwaltete Domäne bereitgestellt wurde. Sie können die Kennwortsynchronisierung für [Reine Cloudbenutzer](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) oder [Hybridbenutzer aus der lokalen Umgebung](tutorial-configure-password-hash-sync.md) einrichten.

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Eine Sicherung wurde eine Weile nicht durchgeführt

### <a name="alert-message"></a>Warnmeldung

*Die verwaltete Domäne wurde zuletzt am [Datum] gesichert.*

### <a name="resolution"></a>Lösung

[Überprüfen Sie die Integrität von Azure AD DS](check-health.md) auf Warnungen, die auf Probleme bei der Konfiguration der verwalteten Domäne hinweisen. Probleme mit der Netzwerkkonfiguration können verhindern, dass die Azure-Plattform Sicherungen erfolgreich durchführt. Wenn Sie Warnungen auflösen können, die auf ein Konfigurationsproblem hinweisen, warten Sie zwei Stunden, und überprüfen Sie dann, ob die Synchronisierung erfolgreich abgeschlossen wurde.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Anhalten aufgrund eines deaktivierten Abonnements

### <a name="alert-message"></a>Warnmeldung

*Die verwaltete Domäne wird angehalten, da das mit der Domäne verknüpfte Azure-Abonnement nicht aktiv ist.*

### <a name="resolution"></a>Lösung

> [!WARNING]
> Wenn eine verwaltete Domäne über einen längeren Zeitraum angehalten wird, besteht die Gefahr, dass sie gelöscht wird. Beheben Sie so schnell wie möglich den Grund für den angehaltenen Zustand. Weitere Informationen finden Sie unter [Grundlegendes zu angehaltenen Zuständen bei Azure AD DS](suspension.md).

Azure AD DS erfordert ein aktives Abonnement. Wenn das Azure-Abonnement, dem die verwaltete Domäne zugeordnet war, nicht aktiv ist, müssen Sie es erneuern, um das Abonnement zu reaktivieren.

1. [Erneuern Sie Ihr Azure-Abonnement.](../cost-management-billing/manage/subscription-disabled.md)
2. Nachdem das Abonnement erneuert wurde, können Sie mithilfe einer Azure AD DS-Benachrichtigung die verwaltete Domäne erneut aktivieren.

Nach der erneuten Aktivierung der verwalteten Domäne wird die Integrität der verwalteten Domäne innerhalb von zwei Stunden automatisch aktualisiert, und die Warnung wird entfernt.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Anhalten aufgrund einer ungültigen Konfiguration

### <a name="alert-message"></a>Warnmeldung

*Die verwaltete Domäne wird aufgrund einer ungültigen Konfiguration angehalten. Der Dienst konnte die Domänencontroller für Ihre verwaltete Domäne für einen längeren Zeitraum nicht verwalten, kein Patch für sie ausführen oder sie nicht aktualisieren.*

### <a name="resolution"></a>Lösung

> [!WARNING]
> Wenn eine verwaltete Domäne über einen längeren Zeitraum angehalten wird, besteht die Gefahr, dass sie gelöscht wird. Beheben Sie so schnell wie möglich den Grund für den angehaltenen Zustand. Weitere Informationen finden Sie unter [Grundlegendes zu angehaltenen Zuständen bei Azure AD DS](suspension.md).

[Überprüfen Sie die Integrität von Azure AD DS](check-health.md) auf Warnungen, die auf Probleme bei der Konfiguration der verwalteten Domäne hinweisen. Wenn Sie Warnungen auflösen können, die auf ein Konfigurationsproblem hinweisen, warten Sie zwei Stunden, und überprüfen Sie dann, ob die Synchronisierung abgeschlossen wurde. [Öffnen Sie eine Azure-Supportanfrage][azure-support], wenn Sie bereit sind, die verwaltete Domäne erneut zu aktivieren.

## <a name="next-steps"></a>Nächste Schritte

Falls weiterhin Probleme auftreten, [öffnen Sie eine Azure-Supportanfrage][azure-support], um weitere Hilfe bei der Problembehandlung zu erhalten.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
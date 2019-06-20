---
title: 'Azure Active Directory Domain Services: Behandlung von Warnungen | Microsoft-Dokumentation'
description: Problembehandlung von Warnungen für die Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: ed0ff8cde39f2660c2149e43399d937184620773
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245405"
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services – Problembehandlung von Warnungen
Dieser Artikel enthält Leitfäden für die Problembehandlung aller Warnungen, denen Sie in Ihrer verwalteten Domäne begegnen können.


Verwenden Sie die Problembehandlungsschritte für die ID oder Meldung in der Warnung.

| **Warnungs-ID** | **Warnmeldung** | **Lösung** |
| --- | --- | :--- |
| AADDS001 | *Für die verwaltete Domäne ist Secure LDAP über Internet aktiviert. Der Zugriff auf Port 636 ist jedoch nicht über eine Netzwerksicherheitsgruppe gesperrt. Dies kann Brute-Force-Kennwortangriffe auf Benutzerkonten in der verwalteten Domäne möglich machen.* | [Falsche sichere LDAP-Konfiguration](alert-ldaps.md) |
| AADDS100 | *Das Ihrer verwalteten Domäne zugeordnete Azure AD-Verzeichnis wurde möglicherweise gelöscht. Die verwaltete Domäne befindet sich nicht mehr in einer unterstützten Konfiguration. Microsoft kann Ihre verwaltete Domäne nicht überwachen, verwalten, patchen und synchronisieren.* | [Fehlendes Verzeichnis](#aadds100-missing-directory) |
| AADDS101 | *Azure AD Domain Services kann in einem Azure AD B2C-Verzeichnis nicht aktiviert werden.* | [Azure AD B2C wird in diesem Verzeichnis ausgeführt](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Ein für den ordnungsgemäßen Betrieb von Azure AD Domain Services erforderlicher Dienstprinzipal wurde aus Ihrem Azure AD-Verzeichnis gelöscht. Diese Konfiguration wirkt sich darauf aus, wie Microsoft Ihre verwaltete Domäne überwachen, verwalten, patchen und synchronisieren kann.* | [Fehlender Dienstprinzipal](alert-service-principal.md) |
| AADDS103 | *Der IP-Adressbereich für das virtuelle Netzwerk, in dem Sie Azure AD Domain Services aktiviert haben, liegt in einem öffentlichen IP-Bereich. Azure AD Domain Services müssen in einem virtuellen Netzwerk mit einem privaten IP-Adressbereich aktiviert werden. Diese Konfiguration wirkt sich darauf aus, wie Microsoft Ihre verwaltete Domäne überwachen, verwalten, patchen und synchronisieren kann.* | [Adresse befindet sich in einem öffentlichen IP-Adressbereich](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *Microsoft kann nicht auf die Domänencontroller für diese verwaltete Domäne zugreifen. Dies kann geschehen, wenn eine für Ihr virtuelles Netzwerk konfigurierte Netzwerksicherheitsgruppe (NSG) den Zugriff auf ihre verwaltete Domäne blockiert. Eine weitere mögliche Ursache besteht in einer benutzerdefinierten Route, die eingehenden Datenverkehr aus dem Internet blockiert.* | [Netzwerkfehler](alert-nsg.md) |
| AADDS105 | *Der Dienstprinzipal mit der Anwendungs-ID „d87dcbc6-a371-462e-88e3-28ad15ec4e64“ wurde gelöscht und dann neu erstellt. Die Wiederherstellung hinterlässt inkonsistente Berechtigungen für Azure AD Domain Services-Ressourcen, die für Ihre verwaltete Domäne benötigt werden. Die Synchronisierung von Kennwörtern in Ihrer verwalteten Domäne kann beeinträchtigt werden.* | [Die Kennwortsynchronisierungsanwendung ist veraltet.](alert-service-principal.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS106 | *Ihr Azure-Abonnement, das Ihrer verwalteten Domäne zugeordnet ist, wurde gelöscht.  Azure AD Domain Services erfordert ein aktives Abonnement, um weiterhin ordnungsgemäß zu funktionieren.* | [Azure-Abonnement nicht gefunden](#aadds106-your-azure-subscription-is-not-found) |
| AADDS107 | *Ihr Azure-Abonnement, das Ihrer verwalteten Domäne zugeordnet ist, ist inaktiv.  Azure AD Domain Services erfordert ein aktives Abonnement, um weiterhin ordnungsgemäß zu funktionieren.* | [Azure-Abonnement deaktiviert](#aadds107-your-azure-subscription-is-disabled) |
| AADDS108 | *Das von Azure AD Domain Services verwendete Abonnement wurde in ein anderes Verzeichnis verschoben. Für eine ordnungsgemäße Funktionsweise von Azure AD Domain Services muss ein aktives Abonnement im gleichen Verzeichnis vorhanden sein.* | [Verzeichnisse durch Abonnement verschoben](#aadds108-subscription-moved-directories) |
| AADDS109 | *Eine Ressource, die für Ihre verwaltete Domäne verwendet wird, wurde gelöscht. Diese Ressource ist erforderlich, damit Azure AD Domain Services ordnungsgemäß funktioniert.* | [Eine Ressource wurde gelöscht.](#aadds109-resources-for-your-managed-domain-cannot-be-found) |
| AADDS110 | *Das für die Bereitstellung von Azure AD Domain Services ausgewählte Subnetz ist voll und hat keinen Platz für den zusätzlichen Domänencontroller, der erstellt werden muss.* | [Subnetz ist voll](#aadds110-the-subnet-associated-with-your-managed-domain-is-full) |
| AADDS111 | *Ein Dienstprinzipal, den Azure AD Domain Services für Ihre Domäne verwendet, ist für das Verwalten von Ressourcen im Azure-Abonnement nicht autorisiert. Der Dienstprinzipal muss die entsprechenden Berechtigungen für Ihre verwaltete Domäne erhalten.* | [Dienstprinzipal nicht autorisiert](#aadds111-service-principal-unauthorized) |
| AADDS112 | *Das Subnetz des virtuellen Netzwerks in dieser Domäne verfügt möglicherweise nicht über genügend IP-Adressen. Azure AD Domain Services benötigt mindestens zwei verfügbare IP-Adressen innerhalb des Subnetzes, in dem es aktiviert ist. Es wird empfohlen, mindestens 3 bis 5 freie IP-Adressen innerhalb des Subnetzes bereitzuhalten. Dies kann aufgetreten sein, wenn andere virtuelle Computer innerhalb des Subnetzes bereitgestellt wurden und damit die Anzahl der verfügbaren IP-Adressen ausgeschöpft wurde oder wenn die Anzahl der verfügbaren IP-Adressen im Subnetz beschränkt ist.* | [Nicht genügend IP-Adressen](#aadds112-not-enough-ip-address-in-the-managed-domain) |
| AADDS113 | *Für die von Azure AD Domain Services verwendeten Ressourcen wurde ein unerwarteter Zustand erkannt, und sie können nicht wiederhergestellt werden.* | [Ressourcen nicht wiederherstellbar](#aadds113-resources-are-unrecoverable) |
| AADDS114 | *Das für die Bereitstellung von Azure AD Domain Services ausgewählte Subnetz ist ungültig und kann nicht verwendet werden.* | [Subnetz ungültig](#aadds114-subnet-invalid) |
| AADDS115 | *Mindestens eine der Netzwerkressourcen, die von der verwalteten Domäne verwendet werden, kann nicht betrieben werden, da der Zielbereich gesperrt wurde.* | [Ressourcen gesperrt](#aadds115-resources-are-locked) |
| AADDS116 | *Mindestens eine der Netzwerkressourcen, die von der verwalteten Domäne verwendet werden, kann aufgrund von Richtlinienbeschränkungen nicht betrieben werden.* | [Ressourcen nicht verwendbar](#aadds116-resources-are-unusable) |
| AADDS500 | *Die verwaltete Domäne wurde zuletzt am [Datum] mit Azure AD synchronisiert. Benutzer können sich möglicherweise nicht bei der verwalteten Domäne anmelden, oder Gruppenmitgliedschaften sind möglicherweise nicht mit Azure AD synchronisiert.* | [Die Synchronisierung wurde eine Weile nicht durchgeführt.](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *Die verwaltete Domäne wurde zuletzt am [Datum] gesichert.* | [Eine Sicherung wurde eine Weile nicht durchgeführt.](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *Das Secure LDAP-Zertifikat für die verwaltete Domäne läuft am [Datum] ab.* | [Ablauf des sicheren LDAP-Zertifikats](alert-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *Die verwaltete Domäne wird angehalten, da das mit der Domäne verknüpfte Azure-Abonnement nicht aktiv ist.* | [Anhalten aufgrund eines deaktivierten Abonnements](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *Die verwaltete Domäne wird aufgrund einer ungültigen Konfiguration angehalten. Der Dienst konnte die Domänencontroller für Ihre verwaltete Domäne für einen längeren Zeitraum nicht verwalten, kein Patch für sie ausführen oder sie nicht aktualisieren.* | [Anhalten aufgrund einer ungültigen Konfiguration](#aadds504-suspension-due-to-an-invalid-configuration) |



## <a name="aadds100-missing-directory"></a>AADDS100: Fehlendes Verzeichnis
**Warnmeldung:**

*Das Ihrer verwalteten Domäne zugeordnete Azure AD-Verzeichnis wurde möglicherweise gelöscht. Die verwaltete Domäne befindet sich nicht mehr in einer unterstützten Konfiguration. Microsoft kann Ihre verwaltete Domäne nicht überwachen, verwalten, patchen und synchronisieren.*

**Lösung:**

Dieser Fehler wird normalerweise durch das nicht ordnungsgemäße Verschieben Ihres Azure-Abonnements in ein neues Azure AD-Verzeichnis und das anschließende Löschen des alten Azure AD-Verzeichnisses verursacht, das immer noch Azure AD Domain Services zugeordnet ist.

Dieser Fehler ist nicht behebbar. Um das Problem zu lösen, müssen Sie [Ihre vorhandene verwaltete Domäne löschen](delete-aadds.md) und sie in Ihrem neuen Verzeichnis neu erstellen. Wenn beim Löschen Probleme auftreten, bitten Sie das Produktteam für Azure Active Directory Domain Services um [Unterstützung](contact-us.md).

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C wird in diesem Verzeichnis ausgeführt
**Warnmeldung:**

*Azure AD Domain Services kann in einem Azure AD B2C-Verzeichnis nicht aktiviert werden.*

**Lösung:**

>[!NOTE]
>Um Azure AD Domain Services weiterhin zu verwenden, müssen Sie Ihre Instanz von Azure AD Domain Services in einem anderen als einem Azure AD B2C-Verzeichnis neu erstellen.

Führen Sie die folgenden Schritte aus, um Ihren Dienst wiederherzustellen:

1. [Löschen Sie Ihre verwaltete Domäne](delete-aadds.md) aus Ihrem vorhandenen Azure AD-Verzeichnis.
2. Erstellen Sie ein neues Verzeichnis, das kein Azure AD B2C-Verzeichnis ist.
3. Folgen Sie den Anweisungen im Leitfaden [Erste Schritte](create-instance.md), um eine verwaltete Domäne neu zu erstellen.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Adresse befindet sich in einem öffentlichen IP-Adressbereich

**Warnmeldung:**

*Der IP-Adressbereich für das virtuelle Netzwerk, in dem Sie Azure AD Domain Services aktiviert haben, liegt in einem öffentlichen IP-Bereich. Azure AD Domain Services müssen in einem virtuellen Netzwerk mit einem privaten IP-Adressbereich aktiviert werden. Diese Konfiguration wirkt sich darauf aus, wie Microsoft Ihre verwaltete Domäne überwachen, verwalten, patchen und synchronisieren kann.*

**Lösung:**

> [!NOTE]
> Zum Beheben dieses Problems müssen Sie Ihre vorhandene verwaltete Domäne löschen und sie in einem virtuellen Netzwerk mit einem privaten IP-Adressbereich neu erstellen. Dieser Vorgang geht mit einer Dienstunterbrechung einher.

Lesen Sie den Abschnitt **privater IP-v4-Adressbereich** in [diesem Artikel](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces), bevor Sie beginnen.

Innerhalb des virtuellen Netzwerks können Computer Anforderungen für Azure-Ressourcen ausführen, die sich im gleichen IP-Adressbereich wie die für das Subnetz konfigurierten befinden. Da das virtuelle Netzwerk jedoch für diesen Bereich konfiguriert ist, werden diese Anforderungen innerhalb des virtuellen Netzwerks weitergeleitet und erreichen nicht die vorgesehenen Webressourcen. Diese Konfiguration kann zu unvorhersehbaren Fehlern mit Azure AD Domain Services führen.

**Wenn Sie der Besitzer des in Ihrem virtuellen Netzwerk konfigurierten IP-Adressbereichs sind, kann diese Warnung ignoriert werden. Allerdings kann Azure AD Domain Services die [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] mit dieser Konfiguration nicht einhalten, da sie zu unvorhersehbaren Fehlern führen kann.**


1. [Löschen Sie Ihre verwaltete Domäne](delete-aadds.md) aus Ihrem Verzeichnis.
2. Korrigieren Sie den IP-Adressbereich für das Subnetz
   1. Navigieren Sie zur [Seite „Virtuelle Netzwerke“ im Azure-Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
   2. Wählen Sie das virtuelle Netzwerk aus, das Sie für Azure AD Domain Services verwenden möchten.
   3. Klicken Sie unter „Einstellungen“ auf **Adressbereich**.
   4. Aktualisieren Sie den Adressbereich, indem Sie auf den vorhandenen Adressbereich klicken und ihn bearbeiten oder einen zusätzlichen Adressbereich hinzufügen. Achten Sie dabei darauf, dass sich der neue Adressbereich in einem privaten IP-Bereich befindet. Speichern Sie die Änderungen.
   5. Klicken Sie in linken Navigationsbereich auf **Subnetze**.
   6. Klicken Sie in der Tabelle auf das Subnetz, das Sie bearbeiten möchten.
   7. Aktualisieren Sie den Adressbereich, und speichern Sie Ihre Änderungen.
3. Befolgen Sie die Anweisungen im Handbuch [Erste Schritte mit Azure Active Directory Domain Services](create-instance.md), um Ihre verwaltete Domäne neu zu erstellen. Achten Sie darauf, ein virtuelles Netzwerk mit einem privaten IP-Adressbereich auszuwählen.
4. Um den Beitritt Ihrer virtuellen Computer zu Ihrer neuen Domäne zu realisieren, befolgen Sie die Anweisungen in [diesem Handbuch](join-windows-vm.md).
8. Um sicherzustellen, dass die Warnung aufgelöst wurde, überprüfen Sie den Status Ihrer Domäne nach zwei Stunden noch einmal.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Ihr Azure-Abonnement wurde nicht gefunden

**Warnmeldung:**

*Ihr Azure-Abonnement, das Ihrer verwalteten Domäne zugeordnet ist, wurde gelöscht.  Azure AD Domain Services erfordert ein aktives Abonnement, um weiterhin ordnungsgemäß zu funktionieren.*

**Lösung:**

Azure AD Domain Services benötigt ein Abonnement und kann nicht in ein anderes Abonnement verschoben werden. Da das Azure-Abonnement, dem Ihre verwaltete Domäne zugeordnet war, gelöscht wurde, müssen Sie ein Azure-Abonnement und Azure AD Domain Services neu erstellen.

1. Erstellt ein Azure-Abonnement.
2. [Löschen Sie Ihre verwaltete Domäne](delete-aadds.md) aus Ihrem vorhandenen Azure AD-Verzeichnis.
3. Folgen Sie den Anweisungen im Leitfaden [Erste Schritte](create-instance.md), um eine verwaltete Domäne neu zu erstellen.

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Das Azure-Abonnement wurde deaktiviert

**Warnmeldung:**

*Ihr Azure-Abonnement, das Ihrer verwalteten Domäne zugeordnet ist, ist inaktiv.  Azure AD Domain Services erfordert ein aktives Abonnement, um weiterhin ordnungsgemäß zu funktionieren.*

**Lösung:**


1. [Erneuern Sie Ihr Azure-Abonnement.](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)
2. Nachdem das Abonnement erneuert wurde, erhält Azure AD Domain Services eine Benachrichtigung von Azure, um Ihre verwaltete Domäne erneut zu aktivieren.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Verzeichnisse durch Abonnement verschoben

**Warnmeldung:**

*Das von Azure AD Domain Services verwendete Abonnement wurde in ein anderes Verzeichnis verschoben. Für eine ordnungsgemäße Funktionsweise von Azure AD Domain Services muss ein aktives Abonnement im gleichen Verzeichnis vorhanden sein.*

**Lösung:**

Sie können das mit Azure AD Domain Services verknüpfte Abonnement wieder in das vorherige Verzeichnis verschieben. Alternativ dazu müssen Sie [Ihre verwaltete Domäne aus dem vorhandenen Verzeichnis löschen](delete-aadds.md) und im ausgewählten Verzeichnis neu erstellen (entweder mit einem neuen Abonnement oder indem Sie das Verzeichnis ändern, in dem sich Ihre Azure AD Domain Services-Instanz befindet).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: Keine Ressourcen für Ihre verwalteten Domäne gefunden

**Warnmeldung:**

*Eine Ressource, die für Ihre verwaltete Domäne verwendet wird, wurde gelöscht. Diese Ressource ist erforderlich, damit Azure AD Domain Services ordnungsgemäß funktioniert.*

**Lösung:**

Azure AD Domain Services erstellt für eine ordnungsgemäße Funktion bestimmte Ressourcen während der Bereitstellung, einschließlich öffentlicher IP-Adressen, Netzwerkkarten und Load Balancer. Wenn eine der genannten Ressourcen gelöscht wird, führt dies dazu, dass Ihre verwaltete Domäne in einen nicht unterstützten Zustand übergeht und damit nicht mehr verwaltet werden kann. Diese Warnung tritt auf, wenn eine Person, die Azure AD Domain Services-Ressourcen bearbeiten kann, eine erforderliche Ressource löscht. Die folgenden Schritte beschreiben, wie Sie Ihre verwaltete Domäne wiederherstellen.

1. Navigieren Sie zur Statusseite von Azure AD Domain Services.
   1.    Navigieren Sie im Azure-Portal zur Seite [Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices).
   2.    Klicken Sie im linken Navigationsbereich auf **Integrität**.
2. Überprüfen Sie, ob die Warnung weniger als 4 Stunden alt ist.
   1.    Klicken Sie auf der Seite „Integrität“ auf die Warnung mit der ID **AADDS109**.
   2.    Die Warnung hat einen Zeitstempel für das erste Auftreten. Wenn dieser Zeitstempel weniger als 4 Stunden alt ist, besteht die Möglichkeit, dass Azure AD Domain Services die gelöschte Ressource wiederherstellen kann.
3. Wenn die Warnung mehr als 4 Stunden alt ist, kann die verwaltete Domäne nicht wiederhergestellt werden. Sie müssen Azure AD Domain Services löschen und neu erstellen.


## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: Das Ihrer verwalteten Domäne zugeordnete Subnetz ist voll

**Warnmeldung:**

*Das für die Bereitstellung von Azure AD Domain Services ausgewählte Subnetz ist voll und hat keinen Platz für den zusätzlichen Domänencontroller, der erstellt werden muss.*

**Lösung:**

Dieser Fehler ist nicht behebbar. Um das Problem zu lösen, müssen Sie [Ihre vorhandene verwaltete Domäne löschen](delete-aadds.md) und [sie neu erstellen](create-instance.md).

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Dienstprinzipal nicht autorisiert

**Warnmeldung:**

*Ein Dienstprinzipal, den Azure AD Domain Services für Ihre Domäne verwendet, ist für das Verwalten von Ressourcen im Azure-Abonnement nicht autorisiert. Der Dienstprinzipal muss die entsprechenden Berechtigungen für Ihre verwaltete Domäne erhalten.*

**Lösung:**

Unsere Dienstprinzipale benötigen Zugriff, um Ressourcen in Ihrer verwalteten Domäne zu erstellen und zu verwalten. Jemand hat dem Dienstprinzipal den Zugriff verweigert, daher kann dieser die Ressourcen nicht verwalten. Führen Sie die folgenden Schritte aus, um Ihrem Dienstprinzipal Zugriff zu gewähren.

1. Weitere Informationen finden Sie unter [Verwalten des Zugriffs mithilfe der RBAC und des Azure-Portals](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
2. Überprüfen Sie den Zugriff des Dienstprinzipals mit der ID ```abba844e-bc0e-44b0-947a-dc74e5d09022```, und gewähren Sie den Zugriff, der zu einem früheren Zeitpunkt verweigert wurde.


## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Nicht genügend IP-Adressen in der verwalteten Domäne

**Warnmeldung:**

*Das Subnetz des virtuellen Netzwerks in dieser Domäne verfügt möglicherweise nicht über genügend IP-Adressen. Azure AD Domain Services benötigt mindestens zwei verfügbare IP-Adressen innerhalb des Subnetzes, in dem es aktiviert ist. Es wird empfohlen, mindestens 3 bis 5 freie IP-Adressen innerhalb des Subnetzes bereitzuhalten. Dies kann aufgetreten sein, wenn andere virtuelle Computer innerhalb des Subnetzes bereitgestellt wurden und damit die Anzahl der verfügbaren IP-Adressen ausgeschöpft wurde oder wenn die Anzahl der verfügbaren IP-Adressen im Subnetz beschränkt ist.*

**Lösung:**

1. Löschen Sie Ihre verwaltete Domäne aus Ihrem Mandanten.
2. Korrigieren Sie den IP-Adressbereich für das Subnetz
   1. Navigieren Sie zur [Seite „Virtuelle Netzwerke“ im Azure-Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
   2. Wählen Sie das virtuelle Netzwerk aus, das Sie für Azure AD Domain Services verwenden möchten.
   3. Klicken Sie unter „Einstellungen“ auf **Adressbereich**.
   4. Aktualisieren Sie den Adressbereich, indem Sie auf den vorhandenen Adressbereich klicken und ihn bearbeiten oder einen zusätzlichen Adressbereich hinzufügen. Speichern Sie die Änderungen.
   5. Klicken Sie in linken Navigationsbereich auf **Subnetze**.
   6. Klicken Sie in der Tabelle auf das Subnetz, das Sie bearbeiten möchten.
   7. Aktualisieren Sie den Adressbereich, und speichern Sie Ihre Änderungen.
3. Befolgen Sie die Anweisungen im Handbuch [Erste Schritte mit Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started), um Ihre verwaltete Domäne neu zu erstellen. Achten Sie darauf, ein virtuelles Netzwerk mit einem privaten IP-Adressbereich auszuwählen.
4. Um den Beitritt Ihrer virtuellen Computer zu Ihrer neuen Domäne zu realisieren, befolgen Sie die Anweisungen in [diesem Handbuch](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).
5. Überprüfen Sie in zwei Stunden die Integrität Ihrer Domäne, um sicherzustellen, dass Sie die Schritte ordnungsgemäß abgeschlossen haben.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Ressourcen nicht wiederherstellbar

**Warnmeldung:**

*Für die von Azure AD Domain Services verwendeten Ressourcen wurde ein unerwarteter Zustand erkannt, und sie können nicht wiederhergestellt werden.*

**Lösung:**

Dieser Fehler ist nicht behebbar. Um das Problem zu lösen, müssen Sie [Ihre vorhandene verwaltete Domäne löschen](delete-aadds.md) und [sie neu erstellen](create-instance.md).

## <a name="aadds114-subnet-invalid"></a>AADDS114: Subnetz ungültig

**Warnmeldung:**

*Das für die Bereitstellung von Azure AD Domain Services ausgewählte Subnetz ist ungültig und kann nicht verwendet werden.*

**Lösung:**

Dieser Fehler ist nicht behebbar. Um das Problem zu lösen, müssen Sie [Ihre vorhandene verwaltete Domäne löschen](delete-aadds.md) und [sie neu erstellen](create-instance.md).

## <a name="aadds115-resources-are-locked"></a>AADDS115: Ressourcen gesperrt

**Warnmeldung:**

*Mindestens eine der Netzwerkressourcen, die von der verwalteten Domäne verwendet werden, kann nicht betrieben werden, da der Zielbereich gesperrt wurde.*

**Lösung:**

1.  Überprüfen Sie die Vorgangsprotokolle von Resource Manager auf den Netzwerkressourcen (diese sollten Informationen zu der Sperre enthalten, die eine Änderung verhindert).
2.  Entfernen Sie die Sperren für die Ressourcen, sodass der Dienstprinzipal von Azure AD Domain Services sie wieder verwenden kann.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Ressourcen nicht verwendbar

**Warnmeldung:**

*Mindestens eine der Netzwerkressourcen, die von der verwalteten Domäne verwendet werden, kann aufgrund von Richtlinienbeschränkungen nicht betrieben werden.*

**Lösung:**

1.  Überprüfen Sie die Vorgangsprotokolle von Resource Manager in den Netzwerkressourcen für Ihre verwaltete Domäne.
2.  Heben Sie einige Einschränkungen in der Richtlinie für die Ressourcen auf, sodass der Dienstprinzipal von AAD DS sie verwenden kann.



## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Synchronisierung wurde nicht in kurzer Zeit abgeschlossen

**Warnmeldung:**

*Die verwaltete Domäne wurde zuletzt am [Datum] mit Azure AD synchronisiert. Benutzer können sich möglicherweise nicht bei der verwalteten Domäne anmelden, oder Gruppenmitgliedschaften sind möglicherweise nicht mit Azure AD synchronisiert.*

**Lösung:**

[Überprüfen Sie die Integrität Ihrer Domäne](check-health.md) auf Warnungen, die auf Probleme in Ihrer Konfiguration der verwalteten Domäne hinweisen könnten. In einigen Fällen können Probleme mit der Konfiguration die Fähigkeit von Microsoft zum Synchronisieren Ihrer verwalteten Domäne blockieren. Wenn Sie Warnungen auflösen können, warten Sie zwei Stunden, und überprüfen Sie dann, ob die Synchronisierung abgeschlossen ist.

Einige häufige Gründe, warum die Synchronisierung in verwalteten Domänen unterbrochen wird:
- Die Netzwerkverbindung ist für die verwaltete Domäne blockiert. Weitere Informationen zum Überprüfen des Netzwerks auf Probleme finden Sie unter [Problembehandlung bei Netzwerksicherheitsgruppen](alert-nsg.md) und [Netzwerkanforderungen für Azure AD Domain Services](network-considerations.md).
-  Die Kennwortsynchronisierung wurde nicht eingerichtet oder nicht abgeschlossen. Informationen zum Einrichten der Kennwortsynchronisierung finden Sie in [diesem Artikel](active-directory-ds-getting-started-password-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Eine Sicherung wurde eine Weile nicht durchgeführt

**Warnmeldung:**

*Die verwaltete Domäne wurde zuletzt am [Datum] gesichert.*

**Lösung:**

[Überprüfen Sie die Integrität Ihrer Domäne](check-health.md) auf Warnungen, die auf Probleme in Ihrer Konfiguration der verwalteten Domäne hinweisen könnten. In einigen Fällen können Probleme mit der Konfiguration verhindern, dass Microsoft Ihre verwaltete Domäne sichern kann. Wenn Sie Warnungen auflösen können, warten Sie zwei Stunden, und überprüfen Sie dann, ob die Sicherung abgeschlossen ist.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Anhalten aufgrund eines deaktivierten Abonnements

**Warnmeldung:**

*Die verwaltete Domäne wird angehalten, da das mit der Domäne verknüpfte Azure-Abonnement nicht aktiv ist.*

**Lösung:**

> [!WARNING]
> Wenn die verwaltete Domäne über einen längeren Zeitraum angehalten wird, besteht die Gefahr, dass sie gelöscht wird. Es empfiehlt sich, dies möglichst schnell zu beheben. Weitere Informationen finden Sie in [diesem Artikel](suspension.md).

Zum Wiederherstellen Ihres Diensts [erneuern Sie Ihr Azure-Abonnement](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable), das Ihrer verwalteten Domäne zugeordnet ist.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Anhalten aufgrund einer ungültigen Konfiguration

**Warnmeldung:**

*Die verwaltete Domäne wird aufgrund einer ungültigen Konfiguration angehalten. Der Dienst konnte die Domänencontroller für Ihre verwaltete Domäne für einen längeren Zeitraum nicht verwalten, kein Patch für sie ausführen oder sie nicht aktualisieren.*

**Lösung:**

> [!WARNING]
> Wenn die verwaltete Domäne über einen längeren Zeitraum angehalten wird, besteht die Gefahr, dass sie gelöscht wird. Es empfiehlt sich, dies möglichst schnell zu beheben. Weitere Informationen finden Sie in [diesem Artikel](suspension.md).

[Überprüfen Sie die Integrität Ihrer Domäne](check-health.md) auf Warnungen, die auf Probleme in Ihrer Konfiguration der verwalteten Domäne hinweisen könnten. Wenn Sie einige von diesen Warnungen auflösen können, tun Sie dies. Wenden Sie sich danach an den Support, um Ihr Abonnement erneut zu aktivieren.


## <a name="contact-us"></a>Kontakt
Wenden Sie sich an das Produktteam der Azure Active Directory Domain Services, um [Feedback zu geben oder Support zu erhalten](contact-us.md).

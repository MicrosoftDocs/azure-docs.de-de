---
title: Problembehandlung von RBAC für Azure-Ressourcen | Microsoft-Dokumentation
description: Beheben von Problemen mit der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) für Azure-Ressourcen.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 5dda2eafe86d037faab6284c2af0d8026c194d11
ms.sourcegitcommit: d73c46af1465c7fd879b5a97ddc45c38ec3f5c0d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65921146"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Problembehandlung von RBAC für Azure-Ressourcen

In diesem Artikel werden häufig gestellte Fragen über die rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) für Azure-Ressourcen beantwortet. Sie erfahren also, was Sie erwarten können, wenn Sie die Rollen im Azure-Portal verwenden und wie Sie Zugriffsprobleme lösen können.

## <a name="problems-with-rbac-role-assignments"></a>Probleme mit RBAC-Rollenzuweisungen

- Wenn Sie keine Rollenzuweisung im Azure-Portal für die **Zugriffssteuerung (IAM)** hinzufügen können, weil die Option **Hinzufügen** > **Rollenzuweisung hinzufügen** deaktiviert ist, oder weil der Berechtigungsfehler „The client with object id does not have authorization to perform action“ (Der Client mit der Objekt-ID ist zum Ausführen der Aktion nicht autorisiert) ausgegeben wird, überprüfen Sie, ob Sie aktuell mit einem Benutzer angemeldet sind, dem eine Rolle mit der Berechtigung `Microsoft.Authorization/roleAssignments/write`, wie z.B. [Besitzer](built-in-roles.md#owner) oder [Benutzerzugriffsadministrator](built-in-roles.md#user-access-administrator), in dem Bereich zugewiesen ist, dem Sie versuchen die Rolle zuzuweisen.
- Wenn beim Zuweisen einer Rolle die Fehlermeldung „No more role assignments can be created (code: RoleAssignmentLimitExceeded)“ (Es können keine weiteren Rollenzuweisungen erstellt werden (Code: RoleAssignmentLimitExceeded)) auftritt, weisen Sie Rollen stattdessen Gruppen zu, um die Anzahl von Rollenzuweisungen zu verringern. Azure unterstützt pro Abonnement bis zu **2.000** Rollenzuweisungen.

## <a name="problems-with-custom-roles"></a>Probleme mit benutzerdefinierten Rollen

- Wenn Sie wissen möchten, wie Sie eine benutzerdefinierte Rolle erstellen können, sehen Sie sich die entsprechenden Tutorials für die Verwendung von [Azure PowerShell](tutorial-custom-role-powershell.md) bzw. [Azure CLI](tutorial-custom-role-cli.md) an.
- Wenn Sie eine vorhandene benutzerdefinierte Rolle nicht aktualisieren können, stellen Sie sicher, dass Sie mit einem Benutzer angemeldet sind, dem eine Rolle mit der Berechtigung `Microsoft.Authorization/roleDefinition/write`, wie z.B. [Besitzer](built-in-roles.md#owner) oder [Benutzerzugriffsadministrator](built-in-roles.md#user-access-administrator), zugewiesen ist.
- Wenn Sie eine benutzerdefinierte Rolle nicht löschen können und die Fehlermeldung „There are existing role assignments referencing role (code: RoleDefinitionHasAssignments)“ (Es sind Rollenzuweisungen vorhanden, die auf die Rolle verweisen (Code: RoleDefinitionHasAssignments)) angezeigt wird, wird die benutzerdefinierte Rolle noch von Rollenzuweisungen verwendet. Entfernen Sie die entsprechenden Rollenzuweisungen, und wiederholen Sie anschließend den Löschvorgang für die benutzerdefinierte Rolle.
- Wird beim Erstellen einer neuen benutzerdefinierten Rolle die Fehlermeldung „Role definition limit exceeded. No more role definitions can be created (code: RoleDefinitionLimitExceeded)“ (Limit für Rollendefinition überschritten. Es können keine weiteren Rollendefinitionen erstellt werden (Code: RoleDefinitionLimitExceeded)) angezeigt, löschen Sie alle nicht verwendeten benutzerdefinierten Rollen. Azure unterstützt bis zu **5.000** benutzerdefinierte Rollen in einem Mandanten. (Für spezielle Clouds, z. B. Azure Government, Azure Deutschland und Azure China 21Vianet, beträgt das Limit 2.000 benutzerdefinierte Rollen.)
- Wenn beim Aktualisieren einer benutzerdefinierten Rolle ein Fehler auftritt, ähnlich „The client has permission to perform action 'Microsoft.Authorization/roleDefinitions/write' on scope '/subscriptions/{subscriptionid}', however the linked subscription was not found“ (Der Client ist berechtigt die Aktion 'Microsoft.Authorization/roleDefinitions/write' im Bereich '/subscriptions/{subscriptionid}' auszuführen, das verknüpfte Abonnement wurde jedoch nicht gefunden), überprüfen Sie, ob mindestens ein [zuweisbarer Bereich](role-definitions.md#assignablescopes) im Mandanten gelöscht wurde. Wurde der Bereich gelöscht, erstellen Sie ein Supportticket, da hierfür derzeit keine Self-Service-Lösung zur Verfügung steht.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Wiederherstellen von RBAC beim übergreifenden Verschieben von Abonnements auf Mandanten

- Schritte zum Übertragen eines Abonnements auf einen anderen Azure AD-Mandanten finden Sie unter [Übertragen des Besitzes eines Azure-Abonnements auf ein anderes Konto](../billing/billing-subscription-transfer.md).
- Wenn Sie ein Abonnement auf einen anderen Azure AD-Mandanten übertragen, werden alle Rollenzuweisungen dauerhaft aus dem Azure AD-Quellmandanten gelöscht und nicht zum Azure AD-Zielmandanten migriert. Sie müssen Ihre Rollenzuweisungen auf dem Zielmandanten neu erstellen. Sie müssen verwaltete Identitäten für Azure-Ressourcen außerdem manuell neu erstellen. Weitere Informationen finden Sie unter [Häufig gestellte Fragen und bekannte Probleme mit verwalteten Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/known-issues.md).
- Globale Azure AD-Administratoren ohne Zugriff auf ein Abonnement können, nachdem dieses zwischen Mandanten verschoben wurde, mithilfe der **Zugriffsverwaltung für Azure-Ressourcen** vorübergehend ihren [Zugriff erhöhen](elevate-access-global-admin.md), um Zugriff auf das Abonnement zu erhalten.

## <a name="issues-with-service-admins-or-co-admins"></a>Probleme mit Dienstadministratoren oder Co-Administratoren

- Wenn Sie Probleme mit Dienstadministrator oder Co-Admins haben, finden Sie weitere Informationen unter [Hinzufügen oder Ändern von Azure-Abonnementadministratoren](../billing/billing-add-change-azure-subscription-administrator.md) und [Administratorrollen für klassische Abonnements, Azure RBAC-Rollen und Azure AD-Administratorrollen](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Zugriff verweigert oder Berechtigungsfehler

- Wenn beim Erstellen einer Ressource der Berechtigungsfehler „The client with object id does not have authorization to perform action over scope (code: AuthorizationFailed)“ (Der Client mit der Objekt-ID hat keine Berechtigung zum Ausführen der Aktion über Bereich (Code: AuthorizationFailed)) auftritt, vergewissern Sie sich, dass Sie mit einem Benutzer angemeldet sind, dem eine Rolle zugewiesen ist, die im ausgewählten Bereich über Schreibberechtigungen für die Ressource verfügt. Zum Verwalten virtueller Computer in einer Ressourcengruppe sollte Ihnen zum Beispiel die Rolle [Mitwirkender für virtuelle Computer](built-in-roles.md#virtual-machine-contributor) für die Ressourcengruppe (oder den übergeordneten Bereich) zugewiesen sein. Eine Liste mit den Berechtigungen für die integrierten Rollen finden Sie unter [Integrierte Rollen für die rollenbasierte Zugriffssteuerung in Azure](built-in-roles.md).
- Wenn beim Erstellen oder Aktualisieren eines Supporttickets der Berechtigungsfehler „You don't have permission to create a support request“ (Sie sind zum Erstellen einer Supportanfrage nicht berechtigt) auftritt, vergewissern Sie sich, dass Sie mit einem Benutzer angemeldet sind, dem eine Rolle zugewiesen ist, die über die Berechtigung `Microsoft.Support/supportTickets/write` verfügt, wie z.B. [Mitwirkender für Supportanfragen](built-in-roles.md#support-request-contributor).

## <a name="rbac-changes-are-not-being-detected"></a>Keine Erkennung von RBAC-Änderungen

Azure Resource Manager speichert Konfigurationen und Daten manchmal zwischen, um die Leistung zu verbessern. Beim Erstellen oder Löschen von Rollenzuweisungen kann es bis zu 30 Minuten dauern, bis Änderungen wirksam werden. Wenn Sie das Azure-Portal, Azure PowerShell oder die Azure CLI verwenden, können Sie eine Aktualisierung der Rollenzuweisungsänderungen erzwingen, indem Sie sich abmelden und wieder anmelden. Wenn Sie Rollenzuweisungsänderungen mit REST-API-Aufrufen vornehmen, können Sie eine Aktualisierung erzwingen, indem Sie das Zugriffstoken aktualisieren.

## <a name="web-app-features-that-require-write-access"></a>Web-App-Features, für die Schreibzugriff erforderlich ist

Wenn Sie einem Benutzer schreibgeschützten Zugriff für eine einzelne Web-App gewähren, sind einige Features deaktiviert, von denen Sie das unter Umständen nicht erwartet haben. Die folgenden Verwaltungsfunktionen erfordern **Schreibzugriff** auf eine Web-App (entweder als Mitwirkender oder Besitzer) und stehen nicht zur Verfügung, wenn der Benutzer nur über Lesezugriff für die Web-App verfügt.

* Befehle (z.B. starten, anhalten usw.)
* Änderung von Einstellungen wie allgemeine Konfigurationen, Skalierungseinstellungen, Sicherungseinstellungen und Überwachungseinstellungen
* Zugriff auf Anmeldedaten für die Veröffentlichung oder andere geheime Schlüssel wie App- und Verbindungseinstellungen
* Streamingprotokolle
* Konfiguration von Diagnoseprotokollen
* Konsole (Eingabeaufforderung)
* Aktive und kürzlich vorgenommene Bereitstellungen (für fortlaufende Bereitstellungen lokaler Gits)
* Geschätzte Ausgaben
* Webtests
* Virtuelles Netzwerk (für Leser nur sichtbar, wenn ein virtuelles Netzwerk zuvor von einem Benutzer mit Schreibzugriff konfiguriert wurde)

Wenn Sie auf keine dieser Kacheln zugreifen können, fragen Sie den Administrator nach Zugriff als Mitwirkender auf die Web-App.

## <a name="web-app-resources-that-require-write-access"></a>Web-App-Ressourcen, für die Schreibzugriff erforderlich ist

Web-Apps können aufgrund verschiedener miteinander verknüpfter Ressourcen kompliziert sein. Im Folgenden ist eine typische Ressourcengruppe mit einigen Websites dargestellt:

![Web-App-Ressourcengruppe](./media/troubleshooting/website-resource-model.png)

Wenn Sie daher lediglich auf die Web-App Zugriff gewähren, sind viele Funktionen des Blatts „Website“ im Azure-Portal deaktiviert.

Die folgenden Elemente erfordern **Schreibzugriff** auf den **App Service-Plan** für Ihre Website:  

* Anzeigen des Tarifs für die Web-App (Free oder Standard)  
* Skalierungskonfiguration (Anzahl der Instanzen, Größe des virtuellen Computers, Einstellungen für automatische Skalierung)  
* Kontingente (Speicher, Bandbreite, CPU)  

Die folgenden Elemente erfordern **Schreibzugriff** auf die gesamte **Ressourcengruppe**, die Ihre Website umfasst:  

* SSL-Zertifikate und -Bindungen (SSL-Zertifikate können von Websites derselben Ressourcengruppe und desselben geografischen Standorts gemeinsam genutzt werden)  
* Warnregeln  
* Einstellungen für automatische Skalierung  
* Application Insights-Komponenten  
* Webtests  

## <a name="virtual-machine-features-that-require-write-access"></a>Features virtueller Computer, für die Schreibzugriff erforderlich ist

Ähnlich wie bei Web-Apps erfordern einige Funktionen auf dem Blatt „Virtueller Computer“ Schreibzugriff auf den virtuellen Computer oder auf andere Ressourcen in der Ressourcengruppe.

Virtuelle Computer stehen in Verbindung mit Domänennamen, virtuellen Netzwerken, Speicherkonten und Warnungsregeln.

Die folgenden Elemente erfordern **Schreibzugriff** auf den **virtuellen Computer**:

* Endpunkte  
* IP-Adressen  
* Datenträger  
* Erweiterungen  

Die folgenden Elemente erfordern **Schreibzugriff** auf den **virtuellen Computer** und die **Ressourcengruppe** (zusammen mit dem Domänennamen), in der sich der virtuelle Computer befindet:  

* Verfügbarkeitsgruppe  
* Satz mit Lastenausgleich  
* Warnregeln  

Wenn Sie auf keine dieser Kacheln zugreifen können, fragen Sie den Administrator nach Zugriff als Mitwirkender auf diese Ressourcengruppe.

## <a name="azure-functions-and-write-access"></a>Azure Functions und Schreibzugriff

Einige Funktionen von [Azure Functions](../azure-functions/functions-overview.md) erfordern Schreibzugriff. Wenn einem Benutzer beispielsweise die Leserolle zugewiesen ist, können Sie nicht die Funktionen in einer Funktions-App anzeigen. Im Portal wird **(Kein Zugriff)** angezeigt.

![Funktions-Apps ohne Zugriff](./media/troubleshooting/functionapps-noaccess.png)

Ein Leser kann auf die Registerkarte **Plattformfeatures** und dann auf **Alle Einstellungen** klicken, um einige Einstellungen im Zusammenhang mit einer Funktions-App (ähnlich wie bei einer Web-App) anzuzeigen, kann diese Einstellungen jedoch nicht ändern.

## <a name="next-steps"></a>Nächste Schritte
* [Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC und dem Azure-Portal](role-assignments-portal.md)
* [Anzeigen von Aktivitätsprotokollen für RBAC-Änderungen an Azure-Ressourcen](change-history-report.md)


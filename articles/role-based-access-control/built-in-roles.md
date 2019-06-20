---
title: Integrierte Rollen für die rollenbasierte Zugriffssteuerung in Azure | Microsoft-Dokumentation
description: In diesem Artikel werden die integrierten Rollen der rollenbasierten Zugriffssteuerung (RBAC) und Azure-Ressourcen beschrieben. Listet „Actions“, „NotActions“, „DataActions“ und „NotDataActions“ auf.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 427c4615fcbb036ffff56a8fc592f258fb98845e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755110"
---
# <a name="built-in-roles-for-azure-resources"></a>Integrierte Rollen für die rollenbasierte Zugriffssteuerung in Azure

Die [rollenbasierte Zugriffssteuerung (RBAC)](overview.md) verfügt über mehrere integrierte Rollendefinitionen für Azure-Ressourcen, die Sie Benutzern, Gruppen, Dienstprinzipalen und verwalteten Identitäten zuweisen können. Durch Rollenzuweisungen wird die Art und Weise gesteuert, wie Sie auf Azure-Ressourcen zugreifen. Sollten die integrierten Rollen den individuellen Ansprüchen Ihrer Organisation nicht genügen, können Sie Ihre eigenen [benutzerdefinierten Rollen für Azure-Ressourcen](custom-roles.md) erstellen.

Dieser Artikel enthält eine Liste der integrierten Rollen für Azure-Ressourcen, die ständig erweitert wird. Verwenden Sie zum Abrufen der aktuellen Rollen [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) oder [az role definition list](/cli/azure/role/definition#az-role-definition-list). Eine Liste mit Administratorrollen für Azure Active Directory finden Sie unter [Berechtigungen der Administratorrolle in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="built-in-role-descriptions"></a>Beschreibungen der integrierten Rollen

Die folgende Tabelle enthält eine kurze Beschreibung aller integrierten Rollen. Klicken Sie auf den Rollennamen, um die Liste der `Actions`, `NotActions`, `DataActions` und `NotDataActions` für jede Rolle anzuzeigen. Informationen zur Bedeutung dieser Aktionen und deren Anwendung auf die Verwaltung und Datenebenen finden Sie unter [Grundlegendes zu Rollendefinitionen für Azure-Ressourcen](role-definitions.md).


| Integrierte Rolle | BESCHREIBUNG |
| --- | --- |
| [Besitzer](#owner) | Ermöglicht Ihnen das Verwalten aller Komponenten einschließlich des Zugriffs auf Ressourcen. |
| [Mitwirkender](#contributor) | Ermöglicht Ihnen das Verwalten aller Komponenten außer des Zugriffs auf Ressourcen. |
| [Leser](#reader) | Sie können alles anzeigen, aber keine Änderungen vornehmen. |
| [AcrDelete](#acrdelete) | ACR-Löschvorgang |
| [AcrImageSigner](#acrimagesigner) | ACR-Imagesignaturgeber |
| [AcrPull](#acrpull) | ACR-Pullvorgang |
| [AcrPush](#acrpush) | ACR-Pushvorgang |
| [AcrQuarantineReader](#acrquarantinereader) | ACR-Quarantänedatenleser |
| [AcrQuarantineWriter](#acrquarantinewriter) | ACR-Quarantänedatenschreiber |
| [Mitwirkender des API-Verwaltungsdienstes](#api-management-service-contributor) | Kann Dienst und APIs verwalten. |
| [Operatorrolle des API Management-Diensts](#api-management-service-operator-role) | Kann den Dienst, aber nicht die APIs verwalten. |
| [Leserrolle des API Management-Diensts](#api-management-service-reader-role) | Schreibgeschützter Zugriff auf Dienst und APIs |
| [Mitwirkender der Application Insights-Komponente](#application-insights-component-contributor) | Kann Application Insights-Komponenten verwalten |
| [Application Insights-Momentaufnahmedebugger](#application-insights-snapshot-debugger) | Gibt dem Benutzer die Berechtigung zum Anzeigen und Herunterladen von Debugmomentaufnahmen, die mit dem Application Insights-Momentaufnahmedebugger erfasst wurden. Beachten Sie, dass diese Berechtigungen in der Rolle [Besitzer](#owner) oder [Mitwirkender](#contributor) nicht enthalten sind. |
| [Automation-Auftragsoperator](#automation-job-operator) | Hiermit werden Aufträge mithilfe von Automation-Runbooks erstellt und verwaltet. |
| [Operator für Automation](#automation-operator) | Automatisierungsoperatoren können Aufträge starten, beenden, anhalten und fortsetzen. |
| [Automation-Runbookoperator](#automation-runbook-operator) | Runbookeigenschaften lesen: Ermöglicht das Erstellen von Runbookaufträgen. |
| [Avere-Mitwirkender](#avere-contributor) | Kann einen Avere vFXT-Cluster erstellen und verwalten. |
| [Avere-Bediener](#avere-operator) | Wird vom Avere vFXT-Cluster zum Verwalten des Clusters verwendet |
| [Administratorrolle für Azure Kubernetes Service-Cluster](#azure-kubernetes-service-cluster-admin-role) | Listet die Aktion für Anmeldeinformationen des Clusteradministrators auf. |
| [Benutzerrolle für Azure Kubernetes Service-Cluster](#azure-kubernetes-service-cluster-user-role) | Listet die Aktion für Anmeldeinformationen des Clusterbenutzer auf. |
| [Azure Maps-Datenleser (Vorschauversion)](#azure-maps-data-reader-preview) | Gewährt Lesezugriff auf kartenbezogene Daten von einem Azure Maps-Konto. |
| [Besitzer der Azure Stack-Registrierung](#azure-stack-registration-owner) | Ermöglicht Ihnen die Verwaltung von Azure Stack-Registrierungen. |
| [Mitwirkender für Sicherungen](#backup-contributor) | Ermöglicht Ihnen das Verwalten des Sicherungsdiensts, jedoch nicht das Erstellen von Tresoren und das Erteilen des Zugriffs an andere Benutzer. |
| [Sicherungsoperator](#backup-operator) | Ermöglicht Ihnen das Verwalten von Sicherungsdiensten, jedoch nicht das Entfernen der Sicherung, die Tresorerstellung und das Erteilen von Zugriff an andere Benutzer. |
| [Sicherungsleser](#backup-reader) | Kann Sicherungsdienste anzeigen, aber keine Änderungen vornehmen. |
| [Abrechnungsleser](#billing-reader) | Hiermit wird Lesezugriff auf Abrechnungsdaten ermöglicht. |
| [Mitwirkender von BizTalk](#biztalk-contributor) | Ermöglicht Ihnen das Verwalten von BizTalk-Diensten, nicht aber den Zugriff darauf. |
| [Zugriff auf Blockchainmitgliedsknoten (Vorschauversion)](#blockchain-member-node-access-preview) | Ermöglicht den Zugriff auf Blockchainmitgliedsknoten. |
| [Mitwirkender für den CDN-Endpunkt](#cdn-endpoint-contributor) | Kann CDN-Endpunkte verwalten, aber anderen Benutzern keinen Zugriff erteilen. |
| [CDN-Endpunktleser](#cdn-endpoint-reader) | Kann CDN-Endpunkte anzeigen, aber keine Änderungen vornehmen. |
| [Mitwirkender für das CDN-Profil](#cdn-profile-contributor) | Kann CDN-Profile und deren Endpunkte verwalten, aber anderen Benutzern keinen Zugriff erteilen. |
| [CDN-Profilleser](#cdn-profile-reader) | Kann CDN-Profile und deren Endpunkte anzeigen, aber keine Änderungen vornehmen. |
| [Mitwirkender von klassischem Netzwerk](#classic-network-contributor) | Ermöglicht Ihnen das Verwalten von klassischen Netzwerken, nicht aber den Zugriff darauf. |
| [Mitwirkender von klassischem Speicherkonto](#classic-storage-account-contributor) | Ermöglicht Ihnen das Verwalten klassischer Speicherkonten, nicht aber den Zugriff darauf. |
| [Klassische Dienstrolle „Speicherkonto-Schlüsseloperator“](#classic-storage-account-key-operator-service-role) | Klassische Speicherkonto-Schlüsseloperatoren dürfen Schlüssel für klassische Speicherkonten auflisten und neu generieren. |
| [Mitwirkender von klassischen virtuellen Computern](#classic-virtual-machine-contributor) | Ermöglicht Ihnen das Verwalten klassischer virtueller Computer, aber weder den Zugriff darauf noch auf die mit ihnen verbundenen virtuellen Netzwerke oder Speicherkonten. |
| [Mitwirkender für Cognitive Services](#cognitive-services-contributor) | Ermöglicht Ihnen das Erstellen, Lesen, Aktualisieren, Löschen und Verwalten von Cognitive Services-Schlüsseln. |
| [Cognitive Services-Datenleser (Vorschau)](#cognitive-services-data-reader-preview) | Ermöglicht das Lesen von Cognitive Services-Daten. |
| [Cognitive Services-Benutzer](#cognitive-services-user) | Ermöglicht Ihnen das Lesen und Auflisten von Cognitive Services-Schlüsseln. |
| [Cosmos DB-Rolle „Kontoleser“](#cosmos-db-account-reader-role) | Kann Azure Cosmos DB-Kontodaten lesen. Informationen zum Verwalten von Azure Cosmos DB-Konten finden Sie unter [Mitwirkender von DocumentDB-Konto](#documentdb-account-contributor). |
| [Cosmos DB-Operator](#cosmos-db-operator) | Ermöglicht das Verwalten von Azure Cosmos DB-Konten, aber nicht das Zugreifen auf deren Daten. Verhindert den Zugriff auf Kontoschlüssel und Verbindungszeichenfolgen. |
| [CosmosBackupOperator](#cosmosbackupoperator) | Kann eine Wiederherstellungsanforderung für eine Cosmos DB-Datenbank oder einen Container für ein Konto übermitteln. |
| [Mitwirkender für Cost Management](#cost-management-contributor) | Ermöglicht Ihnen das Anzeigen der Kosten und das Verwalten der Kostenkonfiguration (z. B. Budgets, Exporte). |
| [Cost Management-Leser](#cost-management-reader) | Ermöglicht Ihnen das Anzeigen der Kostendaten und -konfiguration (z. B. Budgets, Exporte). |
| [Data Box-Mitwirkender](#data-box-contributor) | Ermöglicht Ihnen das Verwalten aller Komponenten unter dem Data Box-Dienst, mit Ausnahme der Gewährung des Zugriffs für andere Benutzer. |
| [Data Box-Leser](#data-box-reader) | Ermöglicht Ihnen das Verwalten des Data Box-Diensts, mit Ausnahme der Erstellung von Aufträgen oder der Bearbeitung von Auftragsdetails und der Gewährung des Zugriffs für andere Benutzer. |
| [Mitwirkender von Data Factory](#data-factory-contributor) | Erstellen und verwalten Sie Data Factorys sowie die darin enthaltenen untergeordneten Ressourcen. |
| [Data Lake Analytics-Entwickler](#data-lake-analytics-developer) | Ermöglicht Ihnen das Übermitteln, Überwachen und Verwalten Ihrer eigenen Aufträge, aber nicht das Erstellen oder Löschen von Data Lake Analytics-Konten. |
| [Datenpurger](#data-purger) | Kann Analysedaten endgültig löschen. |
| [DevTest Labs-Benutzer](#devtest-labs-user) | Ermöglicht Ihnen das Verbinden, Starten, Neustarten und Herunterfahren Ihrer virtuellen Computer in Ihren Azure DevTest Labs. |
| [DNS Zone Contributor](#dns-zone-contributor) | Ermöglicht Ihnen die Verwaltung von DNS-Zonen und Ressourceneintragssätzen in Azure DNS, aber nicht zu steuern, wer darauf Zugriff hat. |
| [Mitwirkender von DocumentDB-Konto](#documentdb-account-contributor) | Kann Azure Cosmos DB-Konten verwalten. Azure Cosmos DB wurde früher als DocumentDB bezeichnet. |
| [Event Hubs-Datenbesitzer](#event-hubs-data-owner) | Ermöglicht den uneingeschränkten Zugriff auf die Azure Event Hubs-Ressourcen. | 
| [EventGrid EventSubscription-Mitwirkender](#eventgrid-eventsubscription-contributor) | Ermöglicht die Verwaltung von EventGrid-Ereignisabonnementvorgängen. |
| [EventGrid EventSubscription-Leser](#eventgrid-eventsubscription-reader) | Ermöglicht das Lesen von EventGrid-Ereignisabonnements. |
| [HDInsight-Clusteroperator](#hdinsight-cluster-operator) | Ermöglicht Ihnen das Lesen und Ändern von HDInsight-Clusterkonfigurationen. |
| [Mitwirkender für die HDInsight-Domänendienste](#hdinsight-domain-services-contributor) | Ermöglicht Ihnen, Vorgänge im Zusammenhang mit Domänendiensten, die für das HDInsight Enterprise-Sicherheitspaket erforderlich sind, zu lesen, zu erstellen, zu ändern und zu löschen. |
| [Mitwirkender von Intelligent Systems-Konto](#intelligent-systems-account-contributor) | Ermöglicht Ihnen das Verwalten von Intelligent Systems-Konten, nicht aber den Zugriff darauf. |
| [Key Vault-Mitwirkender](#key-vault-contributor) | Ermöglicht Ihnen die Verwaltung von Schlüsseltresoren, aber nicht den Zugriff darauf. |
| [Lab-Ersteller](#lab-creator) | Ermöglicht Ihnen das Erstellen, Verwalten und Löschen verwalteter Labs unter Ihren Azure Lab-Konten. |
| [Log Analytics-Mitwirkender](#log-analytics-contributor) | Ein Log Analytics-Mitwirkender kann alle Überwachungsdaten lesen und Überwachungseinstellungen bearbeiten. Das Bearbeiten von Überwachungseinstellungen schließt folgende Aufgaben ein: Hinzufügen der VM-Erweiterung zu VMs, Lesen von Speicherkontoschlüsseln zum Konfigurieren von Protokollsammlungen aus Azure Storage, Erstellen und Konfigurieren von Automation-Konten, Hinzufügen von Lösungen, Konfigurieren der Azure-Diagnose für alle Azure-Ressourcen. |
| [Log Analytics-Leser](#log-analytics-reader) | Ein Log Analytics-Leser kann alle Überwachungsdaten anzeigen und durchsuchen sowie Überwachungseinstellungen anzeigen. Hierzu zählt auch die Anzeige der Konfiguration von Azure-Diagnosen für alle Azure-Ressourcen. |
| [Logik-App-Mitwirkender](#logic-app-contributor) | Ermöglicht Ihnen das Verwalten von Logik-Apps, aber nicht den Zugriff darauf. |
| [Logik-App-Operator](#logic-app-operator) | Ermöglicht Ihnen das Lesen, Aktivieren und Deaktivieren von Logik-Apps. |
| [Rolle „Bediener für verwaltete Anwendung“](#managed-application-operator-role) | Ermöglicht Ihnen das Lesen und Durchführen von Aktionen für Ressourcen der verwalteten Anwendung. |
| [Leser für verwaltete Anwendungen](#managed-applications-reader) | Ermöglicht Ihnen, Ressourcen in einer verwalteten App zu lesen und JIT-Zugriff anzufordern. |
| [Mitwirkender für verwaltete Identität](#managed-identity-contributor) | Dem Benutzer zugewiesene Identität erstellen, lesen, aktualisieren und löschen. |
| [Operator für verwaltete Identität](#managed-identity-operator) | Dem Benutzer zugewiesene Identität lesen und zuweisen. |
| [Verwaltungsgruppenmitwirkender](#management-group-contributor) | Rolle „Verwaltungsgruppenmitwirkender“ |
| [Verwaltungsgruppenleser](#management-group-reader) | Rolle „Verwaltungsgruppenleser“ |
| [Überwachungsmitwirkender](#monitoring-contributor) | Kann sämtliche Überwachungsdaten lesen und Überwachungseinstellungen bearbeiten. Siehe auch [Erste Schritte mit Rollen, Berechtigungen und Sicherheit in Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Herausgeber von Überwachungsmetriken](#monitoring-metrics-publisher) | Ermöglicht die Veröffentlichung von Metriken für Azure-Ressourcen. |
| [Überwachungsleser](#monitoring-reader) | Kann alle Überwachungsdaten (Metriken, Protokolle usw.) lesen. Siehe auch [Erste Schritte mit Rollen, Berechtigungen und Sicherheit in Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Mitwirkender von virtuellem Netzwerk](#network-contributor) | Ermöglicht Ihnen das Verwalten von Netzwerken, nicht aber den Zugriff darauf. |
| [Mitwirkender von New Relic APM-Konto](#new-relic-apm-account-contributor) | Ermöglicht Ihnen das Verwalten von New Relic Application Performance Management-Konten und -Anwendungen, nicht aber den Zugriff auf diese. |
| [Lese- und Datenzugriff](#reader-and-data-access) | Ermöglicht Ihnen das Anzeigen sämtlicher Aspekte, jedoch nicht das Löschen oder Erstellen eines Speicherkontos oder einer darin enthaltenen Ressource. Sie können auch Lese-/Schreibzugriff auf alle Daten in einem Speicherkonto durch Zugriff auf Speicherkontoschlüssel gewähren. |
| [Mitwirkender von Redis-Cache](#redis-cache-contributor) | Ermöglicht Ihnen das Verwalten von Redis Caches, nicht aber den Zugriff darauf. |
| [Mitwirkender an Ressourcenrichtlinien (Vorschau)](#resource-policy-contributor-preview) | (Vorschau) Über EA abgeglichene Benutzer mit Rechten zum Erstellen/Ändern der Ressourcenrichtlinie, zum Erstellen eines Supporttickets und zum Lesen von Ressourcen/der Hierarchie. |
| [Mitwirkender von Zeitplanungsauftragssammlung](#scheduler-job-collections-contributor) | Ermöglicht Ihnen das Verwalten von Scheduler-Auftragssammlungen, nicht aber den Zugriff darauf. |
| [Mitwirkender von Suchdienst](#search-service-contributor) | Ermöglicht Ihnen das Verwalten von Search-Diensten, nicht aber den Zugriff darauf. |
| [Sicherheitsadministrator](#security-admin) | Nur in Security Center: Kann Sicherheitsrichtlinien und -zustände anzeigen, Sicherheitsrichtlinien bearbeiten sowie Warnungen und Empfehlungen anzeigen und verwerfen. |
| [Sicherheits-Manager (Legacy)](#security-manager-legacy) | Dies ist eine Legacyrolle. Verwenden Sie stattdessen „Sicherheitsadministrator“. |
| [Sicherheitsleseberechtigter](#security-reader) | Nur in Security Center: Kann Empfehlungen und Warnungen sowie Sicherheitsrichtlinien und -zustände anzeigen, aber keine Änderungen vornehmen. |
| [Service Bus-Datenbesitzer](#service-bus-data-owner) | Ermöglicht den uneingeschränkten Zugriff auf die Azure Service Bus-Ressourcen. |
| [Site Recovery-Mitwirkender](#site-recovery-contributor) | Ermöglicht Ihnen die Verwaltung des Site Recovery-Diensts mit Ausnahme der Tresorerstellung und der Rollenzuweisung. |
| [Site Recovery-Operator](#site-recovery-operator) | Ermöglicht Ihnen ein Failover und ein Failback, aber nicht das Durchführen weiterer Site Recovery-Verwaltungsvorgänge. |
| [Site Recovery-Leser](#site-recovery-reader) | Ermöglicht Ihnen die Anzeige des Site Recovery-Status, aber nicht die Durchführung weiterer Verwaltungsvorgänge. |
| [Spatial Anchors-Kontomitwirkender](#spatial-anchors-account-contributor) | Ermöglicht Ihnen das Verwalten von Raumankern in Ihrem Konto, nicht jedoch das Löschen von Ankern. |
| [Spatial Anchors-Kontobesitzer](#spatial-anchors-account-owner) | Ermöglicht Ihnen das Verwalten von Raumankern in Ihrem Konto, einschließlich der Löschung von Ankern. |
| [Spatial Anchors-Kontoleser](#spatial-anchors-account-reader) | Ermöglicht Ihnen das Ermitteln und Lesen von Eigenschaften für Raumanker in Ihrem Dokument. |
| [Mitwirkender von SQL DB](#sql-db-contributor) | Ermöglicht Ihnen das Verwalten von SQL-Datenbanken, nicht aber den Zugriff darauf. Darüber hinaus können Sie deren sicherheitsbezogenen Richtlinien oder übergeordneten SQL-Server nicht verwalten. |
| [Verwaltete SQL-Instanz: Mitwirkender](#sql-managed-instance-contributor) | Ermöglicht Ihnen das Verwalten verwalteter SQL-Instanzen und der erforderlichen Netzwerkkonfiguration, jedoch nicht das Erteilen des Zugriffs an andere. |
| [SQL-Sicherheits-Manager](#sql-security-manager) | Ermöglicht Ihnen das Verwalten von sicherheitsbezogenen Richtlinien von SQL-Server und Datenbanken, jedoch nicht den Zugriff darauf. |
| [Mitwirkender von SQL Server](#sql-server-contributor) | Ermöglicht Ihnen, SQL-Server und -Datenbanken zu verwalten, gewährt Ihnen jedoch keinen Zugriff darauf und auch nicht auf deren sicherheitsbezogenen Richtlinien. |
| [Mitwirkender von Speicherkonto](#storage-account-contributor) | Ermöglicht Ihnen das Verwalten von Speicherkonten, nicht aber den Zugriff darauf. |
| [Dienstrolle „Speicherkonto-Schlüsseloperator“](#storage-account-key-operator-service-role) | Speicherkonto-Schlüsseloperatoren dürfen Schlüssel für Speicherkonten auflisten und neu generieren. |
| [Mitwirkender an Speicherblobdaten](#storage-blob-data-contributor) | Ermöglicht den Lese-, Schreib- und Löschzugriff auf Azure Storage-Blobcontainer und -Daten. |
| [Besitzer von Speicherblobdaten](#storage-blob-data-owner) | Ermöglicht Vollzugriff auf Azure Storage-Blobcontainer und -Daten, einschließlich POSIX-Zugriffssteuerung. |
| [Leser von Speicherblobdaten](#storage-blob-data-reader) | Ermöglicht den Lesezugriff auf Azure Storage-Blobcontainer und -Daten. |
| [Mitwirkender an Storage-Warteschlangendaten](#storage-queue-data-contributor) | Ermöglicht den Lese-, Schreib- und Löschzugriff auf Azure Storage-Warteschlangen und -Warteschlangennachrichten. |
| [Verarbeiter von Speicherwarteschlangen-Datennachrichten](#storage-queue-data-message-processor) | Ermöglicht Anzeige-, Empfangs- und Löschzugriff auf Azure Storage-Warteschlangen. |
| [Absender der Speicherwarteschlangen-Datennachricht](#storage-queue-data-message-sender) | Ermöglicht das Senden von Azure Storage-Warteschlangennachrichten. |
| [Storage-Warteschlangendatenleser](#storage-queue-data-reader) | Ermöglicht den Lesezugriff auf Azure Storage-Warteschlangen und -Warteschlangennachrichten. |
| [Mitwirkender für Supportanfragen](#support-request-contributor) | Ermöglicht Ihnen die Erstellung und Verwaltung von Supportanfragen. |
| [Traffic Manager-Mitwirkender](#traffic-manager-contributor) | Ermöglicht Ihnen die Verwaltung von Traffic Manager-Profilen, aber nicht die Steuerung des Zugriffs darauf. |
| [Benutzerzugriffsadministrator](#user-access-administrator) | Ermöglicht Ihnen die Verwaltung von Benutzerzugriffen auf Azure-Ressourcen. |
| [VM-Administratoranmeldung](#virtual-machine-administrator-login) | Anzeigen von virtuellen Computern im Portal und Anmelden als Administrator |
| [Mitwirkender von virtuellen Computern](#virtual-machine-contributor) | Ermöglicht Ihnen das Verwalten virtueller Computer, aber weder den Zugriff darauf, noch auf deren verbundenen virtuellen Netzwerke oder Speicherkonten. |
| [VM-Benutzeranmeldung](#virtual-machine-user-login) | Anzeigen von virtuellen Computern im Portal und Anmelden als regulärer Benutzer. |
| [Mitwirkender von Webplan](#web-plan-contributor) | Ermöglicht Ihnen das Verwalten der Webpläne für Websites, nicht aber den Zugriff darauf. |
| [Mitwirkender von Website](#website-contributor) | Ermöglicht Ihnen das Verwalten von Websites (nicht der Webpläne), nicht aber den Zugriff darauf. |


## <a name="owner"></a>Owner (Besitzer)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten aller Komponenten einschließlich des Zugriffs auf Ressourcen. |
> | **Id** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Aktionen** |  |
> | * | Erstellen und Verwalten von Ressourcen aller Typen |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="contributor"></a>Mitwirkender
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten aller Komponenten außer des Zugriffs auf Ressourcen. |
> | **Id** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Aktionen** |  |
> | * | Erstellen und Verwalten von Ressourcen aller Typen |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | Löschen von Rollen und Rollenzuweisungen |
> | Microsoft.Authorization/*/Write | Erstellen von Rollen und Rollenzuweisungen |
> | Microsoft.Authorization/elevateAccess/Action | Gewährt dem Aufrufer Zugriff vom Typ „Benutzerzugriffsadministrator“ auf der Mandantenebene. |
> | Microsoft.Blueprint/blueprintAssignments/write | Erstellt oder aktualisiert alle Blaupausenartefakte. |
> | Microsoft.Blueprint/blueprintAssignments/delete | Löscht alle Blaupausenartefakte. |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="reader"></a>Leser
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Sie können alles anzeigen, aber keine Änderungen vornehmen. |
> | **Id** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Aktionen** |  |
> | */Lesen | Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="acrdelete"></a>AcrDelete
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | ACR-Löschvorgang |
> | **Id** | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | **Aktionen** |  |
> | Microsoft.ContainerRegistry/registries/artifacts/delete | Löschen von Artefakten aus einer Containerregistrierung. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | ACR-Imagesignaturgeber |
> | **Id** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Aktionen** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | Pushen/Pullen von Inhaltsvertrauen-Metadaten für eine Containerregistrierung |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="acrpull"></a>AcrPull
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | ACR-Pullvorgang |
> | **Id** | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | **Aktionen** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Pullen oder Abrufen von Images aus einer Containerregistrierung |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="acrpush"></a>AcrPush
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | ACR-Pushvorgang |
> | **Id** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **Aktionen** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Pullen oder Abrufen von Images aus einer Containerregistrierung |
> | Microsoft.ContainerRegistry/registries/push/write | Pushen oder Schreiben von Images in eine Containerregistrierung |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | ACR-Quarantänedatenleser |
> | **Id** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Aktionen** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | Pullen oder Abrufen von Images in Quarantäne aus einer Containerregistrierung |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | ACR-Quarantänedatenschreiber |
> | **Id** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Aktionen** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | Pullen oder Abrufen von Images in Quarantäne aus einer Containerregistrierung |
> | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Schreiben/Ändern des Quarantänezustands von unter Quarantäne gestellten Images |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="api-management-service-contributor"></a>Mitwirkender des API-Verwaltungsdienstes
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Kann Dienst und APIs verwalten. |
> | **Id** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Aktionen** |  |
> | Microsoft.ApiManagement/service/* | Erstellen und Verwalten des API Management-Diensts |
> | Microsoft.Authorization/*/read | Lesen von Autorisierungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="api-management-service-operator-role"></a>Operatorrolle des API Management-Diensts
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Kann den Dienst, aber nicht die APIs verwalten. |
> | **Id** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Aktionen** |  |
> | Microsoft.ApiManagement/service/*/read | Dient zum Lesen von API Management-Dienstinstanzen. |
> | Microsoft.ApiManagement/service/backup/action | Dient zum Sichern des API Management-Diensts im angegebenen Container in einem vom Benutzer bereitgestellten Speicherkonto. |
> | Microsoft.ApiManagement/service/delete | Dient zum Löschen einer API Management-Dienstinstanz. |
> | Microsoft.ApiManagement/service/managedeployments/action | Dient zum Ändern der SKU/Einheiten sowie zum Hinzufügen/Entfernen regionaler Bereitstellungen des API Management-Diensts. |
> | Microsoft.ApiManagement/service/read | Dient zum Lesen der Metadaten für eine API Management-Dienstinstanz. |
> | Microsoft.ApiManagement/service/restore/action | Dient zum Wiederherstellen des API Management-Diensts aus dem angegebenen Container in einem vom Benutzer bereitgestellten Speicherkonto. |
> | Microsoft.ApiManagement/service/updatecertificate/action | Dient zum Hochladen eines SSL-Zertifikats für einen API Management-Dienst. |
> | Microsoft.ApiManagement/service/updatehostname/action | Dient zum Einrichten, Aktualisieren oder Entfernen benutzerdefinierter Domänennamen für einen API Management-Dienst. |
> | Microsoft.ApiManagement/service/write | Dient zum Erstellen einer neuen Instanz des API Management-Diensts. |
> | Microsoft.Authorization/*/read | Lesen von Autorisierungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Abrufen von Benutzern zugeordneten Schlüsseln |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="api-management-service-reader-role"></a>Leserrolle des API Management-Diensts
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Schreibgeschützter Zugriff auf Dienst und APIs |
> | **Id** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Aktionen** |  |
> | Microsoft.ApiManagement/service/*/read | Dient zum Lesen von API Management-Dienstinstanzen. |
> | Microsoft.ApiManagement/service/read | Dient zum Lesen der Metadaten für eine API Management-Dienstinstanz. |
> | Microsoft.Authorization/*/read | Lesen von Autorisierungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Abrufen von Benutzern zugeordneten Schlüsseln |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="application-insights-component-contributor"></a>Mitwirkender der Application Insights-Komponente
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Kann Application Insights-Komponenten verwalten |
> | **Id** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
> | Microsoft.Insights/components/* | Erstellen und Verwalten von Insights-Komponenten |
> | Microsoft.Insights/webtests/* | Erstellen und Verwalten von Webtests |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="application-insights-snapshot-debugger"></a>Application Insights-Momentaufnahmedebugger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Gibt dem Benutzer die Berechtigung zum Anzeigen und Herunterladen von Debugmomentaufnahmen, die mit dem Application Insights-Momentaufnahmedebugger erfasst wurden. Beachten Sie, dass diese Berechtigungen in der Rolle [Besitzer](#owner) oder [Mitwirkender](#contributor) nicht enthalten sind. |
> | **Id** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="automation-job-operator"></a>Automation-Auftragsoperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Hiermit werden Aufträge mithilfe von Automation-Runbooks erstellt und verwaltet. |
> | **Id** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Liest Hybrid Runbook Worker-Ressourcen. |
> | Microsoft.Automation/automationAccounts/jobs/read | Ruft einen Azure Automation-Auftrag ab. |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Setzt einen Azure Automation-Auftrag fort. |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Beendet einen Azure Automation-Auftrag. |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Ruft einen Azure Automation-Auftragsdatenstrom ab. |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Hält einen Azure Automation-Auftrag an. |
> | Microsoft.Automation/automationAccounts/jobs/write | Erstellt einen Azure Automation-Auftrag. |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Ruft die Ausgabe eines Auftrags ab. |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="automation-operator"></a>Operator für Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Automatisierungsoperatoren können Aufträge starten, beenden, anhalten und fortsetzen. |
> | **Id** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Liest Hybrid Runbook Worker-Ressourcen. |
> | Microsoft.Automation/automationAccounts/jobs/read | Ruft einen Azure Automation-Auftrag ab. |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Setzt einen Azure Automation-Auftrag fort. |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Beendet einen Azure Automation-Auftrag. |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Ruft einen Azure Automation-Auftragsdatenstrom ab. |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Hält einen Azure Automation-Auftrag an. |
> | Microsoft.Automation/automationAccounts/jobs/write | Erstellt einen Azure Automation-Auftrag. |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Ruft einen Azure Automation-Auftragszeitplan ab. |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Erstellt einen Azure Automation-Auftragszeitplan. |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Ruft den Arbeitsbereich ab, der mit dem Automation-Konto verknüpft ist. |
> | Microsoft.Automation/automationAccounts/read | Ruft ein Azure Automation-Konto ab. |
> | Microsoft.Automation/automationAccounts/runbooks/read | Ruft ein Azure Automation-Runbook ab. |
> | Microsoft.Automation/automationAccounts/schedules/read | Ruft ein Azure Automation-Zeitplanasset ab. |
> | Microsoft.Automation/automationAccounts/schedules/write | Erstellt oder aktualisiert ein Azure Automation-Zeitplanasset. |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Ruft die Ausgabe eines Auftrags ab. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="automation-runbook-operator"></a>Automation-Runbookoperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Runbookeigenschaften lesen: Ermöglicht das Erstellen von Runbookaufträgen. |
> | **Id** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Automation/automationAccounts/runbooks/read | Ruft ein Azure Automation-Runbook ab. |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="avere-contributor"></a>Avere-Mitwirkender
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Kann einen Avere vFXT-Cluster erstellen und verwalten. |
> | **Id** | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Compute/*/read |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft.Compute/disks/* |  |
> | Microsoft.Network/*/read |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | Dient zum Abrufen der Definition des virtuellen Netzwerks. |
> | Microsoft.Network/virtualNetworks/subnets/read | Ruft eine Subnetzdefinition für virtuelle Netzwerke ab. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Verknüpft ein virtuelles Netzwerk. Nicht warnbar. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Verknüpft Ressourcen wie etwa ein Speicherkonto oder eine SQL-Datenbank mit einem Subnetz. Nicht warnbar. |
> | Microsoft.Network/networkSecurityGroups/join/action | Verknüpft eine Netzwerksicherheitsgruppe. Nicht warnbar. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Storage/*/read |  |
> | Microsoft.Storage/storageAccounts/* |  |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | Microsoft.Resources/subscriptions/resourceGroups/resources/read | Ruft die Ressourcen für die Ressourcengruppe ab. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Gibt das Ergebnis beim Löschen eines Blobs zurück. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Gibt ein Blob oder eine Liste von Blobs zurück. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Gibt das Ergebnis beim Schreiben eines Blobs zurück. |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="avere-operator"></a>Avere-Bediener
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Wird vom Avere vFXT-Cluster zum Verwalten des Clusters verwendet |
> | **Id** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | **Aktionen** |  |
> | Microsoft.Compute/virtualMachines/read | Dient zum Abrufen der Eigenschaften eines virtuellen Computers. |
> | Microsoft.Network/networkInterfaces/read | Ruft eine Netzwerkschnittstellendefinition ab.  |
> | Microsoft.Network/networkInterfaces/write | Erstellt eine Netzwerkschnittstelle oder aktualisiert eine vorhandene Netzwerkschnittstelle.  |
> | Microsoft.Network/virtualNetworks/read | Dient zum Abrufen der Definition des virtuellen Netzwerks. |
> | Microsoft.Network/virtualNetworks/subnets/read | Ruft eine Subnetzdefinition für virtuelle Netzwerke ab. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Verknüpft ein virtuelles Netzwerk. Nicht warnbar. |
> | Microsoft.Network/networkSecurityGroups/join/action | Verknüpft eine Netzwerksicherheitsgruppe. Nicht warnbar. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Gibt das Ergebnis beim Löschen eines Containers zurück. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Hiermit wird eine Liste von Containern zurückgegeben. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Gibt das Ergebnis des PUT-Vorgangs für den Blobcontainer zurück. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Gibt das Ergebnis beim Löschen eines Blobs zurück. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Gibt ein Blob oder eine Liste von Blobs zurück. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Gibt das Ergebnis beim Schreiben eines Blobs zurück. |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Administratorrolle für Azure Kubernetes Service-Cluster
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Listet die Aktion für Anmeldeinformationen des Clusteradministrators auf. |
> | **Id** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Aktionen** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Listet die clusterAdmin-Anmeldeinformationen eines verwalteten Clusters auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Benutzerrolle für Azure Kubernetes Service-Cluster
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Listet die Aktion für Anmeldeinformationen des Clusterbenutzer auf. |
> | **Id** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Aktionen** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Listet die clusterUser-Anmeldeinformationen eines verwalteten Clusters auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="azure-maps-data-reader-preview"></a>Azure Maps-Datenleser (Vorschauversion)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Gewährt Lesezugriff auf kartenbezogene Daten von einem Azure Maps-Konto. |
> | **Id** | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | **Aktionen** |  |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.Maps/accounts/data/read | Gewährt Datenlesezugriff auf ein Maps-Konto. |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="azure-stack-registration-owner"></a>Besitzer der Azure Stack-Registrierung
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen die Verwaltung von Azure Stack-Registrierungen. |
> | **Id** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Aktionen** |  |
> | Microsoft.AzureStack/registrations/products/listDetails/action | Ruft erweiterte Details für ein Azure Stack-Marketplace-Produkt ab. |
> | Microsoft.AzureStack/registrations/products/read | Ruft die Eigenschaften eines Azure Stack-Marketplace-Produkts ab. |
> | Microsoft.AzureStack/registrations/read | Ruft die Eigenschaften einer Azure Stack-Registrierung ab. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="backup-contributor"></a>Mitwirkender für Sicherungen
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten des Sicherungsdiensts, jedoch nicht das Erstellen von Tresoren und das Erteilen des Zugriffs an andere Benutzer. |
> | **Id** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Network/virtualNetworks/read | Dient zum Abrufen der Definition des virtuellen Netzwerks. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Verwalten der Ergebnisse eines Vorgangs in der Sicherungsverwaltung |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Erstellen und Verwalten von Sicherungscontainern in Sicherungsfabrics des Recovery Services-Tresors |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Aktualisiert die Containerliste. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Erstellen und Verwalten von Sicherungsaufträgen |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Dient zum Exportieren von Aufträgen. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Erstellen und Verwalten von Metadaten in Zusammenhang mit der Sicherungsverwaltung |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Erstellen und Verwalten der Ergebnisse von Sicherungsverwaltungsvorgängen |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Erstellen und Verwalten von Sicherungsrichtlinien |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Erstellen und Verwalten von Elementen, die gesichert werden können |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Erstellen und Verwalten von gesicherten Elementen |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Erstellen und Verwalten von Containern mit Sicherungselementen |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Gibt Zusammenfassungen für geschützte Elemente und geschützte Server für einen Recovery Services-Tresor zurück. |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Erstellen und Verwalten von Zertifikaten in Zusammenhang mit Sicherungen in einem Recovery Services-Tresor |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Erstellen und Verwalten erweiterter Informationen in Zusammenhang mit einem Tresor |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Ruft die Warnungen für den Recovery Services-Tresor ab. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Der Vorgang „Tresor abrufen“ ruft ein Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Erstellen und Verwalten von registrierten Identitäten |
> | Microsoft.RecoveryServices/Vaults/usages/* | Erstellen und Verwalten der Nutzung des Recovery Services-Tresors |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Storage/storageAccounts/read | Gibt die Liste mit Speicherkonten zurück oder ruft die Eigenschaften für das angegebene Speicherkonto ab. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Überprüft Vorgang für geschütztes Element. |
> | Microsoft.RecoveryServices/Vaults/write | Der Vorgang „Tresor erstellen“ erstellt eine Azure-Ressource vom Typ „Tresor“. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Gibt den Status eines Sicherungsvorgangs für Recovery Services-Tresore zurück. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Gibt alle Sicherungsverwaltungsserver zurück, die beim Tresor registriert sind. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Ruft alle schützbaren Container ab. |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Überprüft den Sicherungsstatus für Recovery Services-Tresore. |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Überprüft Features. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Löst die Warnung auf. |
> | Microsoft.RecoveryServices/operations/read | Der Vorgang gibt die Liste der Vorgänge für einen Ressourcenanbieter zurück. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Ruft den Vorgangsstatus eines angegebenen Vorgangs ab. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Listet den gesamten beabsichtigten Sicherungsschutz auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="backup-operator"></a>Sicherungsoperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten von Sicherungsdiensten, jedoch nicht das Entfernen der Sicherung, die Tresorerstellung und das Erteilen von Zugriff an andere Benutzer. |
> | **Id** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Network/virtualNetworks/read | Dient zum Abrufen der Definition des virtuellen Netzwerks. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Gibt den Status des Vorgangs zurück. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Ruft das Ergebnis eines Vorgangs ab, der für den Schutzcontainer ausgeführt wurde. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Führt eine Sicherung für geschützte Elemente aus. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Ruft das Ergebnis eines Vorgangs ab, der für geschützte Elemente ausgeführt wurde. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Gibt den Status eines Vorgangs zurück, der für geschützte Elemente ausgeführt wurde. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Gibt Objektdetails des geschützten Elements zurück. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Dient zum Bereitstellen der sofortigen Elementwiederherstellung für geschützte Elemente. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Dient zum Abrufen von Wiederherstellungspunkten für geschützte Elemente. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Dient zum Wiederherstellen von Wiederherstellungspunkten für geschützte Elemente. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Dient zum Widerrufen der sofortigen Elementwiederherstellung für geschützte Elemente. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Dient zum Erstellen eines geschützten Elements für die Sicherung. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Gibt alle registrierten Container zurück. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Aktualisiert die Containerliste. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Erstellen und Verwalten von Sicherungsaufträgen |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Dient zum Exportieren von Aufträgen. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Erstellen und Verwalten der Ergebnisse von Sicherungsverwaltungsvorgängen |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Dient zum Abrufen der Ergebnisse von Richtlinienvorgängen. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Gibt alle Schutzrichtlinien zurück. |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Erstellen und Verwalten von Elementen, die gesichert werden können |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Gibt die Liste mit allen geschützten Elementen zurück. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Gibt alle zum Abonnement gehörenden Container zurück. |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Gibt Zusammenfassungen für geschützte Elemente und geschützte Server für einen Recovery Services-Tresor zurück. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Der Vorgang „Ressourcenzertifikat aktualisieren“ aktualisiert das Zertifikat für die Ressourcen-/Tresoranmeldeinformationen. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Der Vorgang „Ausführliche Informationen abrufen“ ruft die ausführlichen Informationen zu einem Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Der Vorgang „Ausführliche Informationen abrufen“ ruft die ausführlichen Informationen zu einem Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Ruft die Warnungen für den Recovery Services-Tresor ab. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Der Vorgang „Tresor abrufen“ ruft ein Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Mit dem Vorgang „Vorgangsergebnisse abrufen“ können der Vorgangsstatus und das Ergebnis für den asynchron übermittelten Vorgang abgerufen werden. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Der Vorgang „Container abrufen“ kann zum Abrufen der für eine Ressource registrierten Container verwendet werden. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Der Vorgang „Dienstcontainer registrieren“ kann zum Registrieren eines Containers beim Wiederherstellungsdienst verwendet werden. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Gibt Nutzungsdetails für einen Recovery Services-Tresor zurück. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Storage/storageAccounts/read | Gibt die Liste mit Speicherkonten zurück oder ruft die Eigenschaften für das angegebene Speicherkonto ab. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Überprüft Vorgang für geschütztes Element. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Gibt den Status eines Sicherungsvorgangs für Recovery Services-Tresore zurück. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Dient zum Abrufen des Status von Richtlinienvorgängen. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Erstellt einen registrierten Container. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Führt die Abfrage für Workloads innerhalb eines Containers durch. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Gibt alle Sicherungsverwaltungsserver zurück, die beim Tresor registriert sind. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Erstellt einen beabsichtigten Sicherungsschutz. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Ruft einen beabsichtigten Sicherungsschutz ab. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Ruft alle schützbaren Container ab. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Ruft alle Elemente in einem Container ab. |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Überprüft den Sicherungsstatus für Recovery Services-Tresore. |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Überprüft Features. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Löst die Warnung auf. |
> | Microsoft.RecoveryServices/operations/read | Der Vorgang gibt die Liste der Vorgänge für einen Ressourcenanbieter zurück. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Ruft den Vorgangsstatus eines angegebenen Vorgangs ab. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Listet den gesamten beabsichtigten Sicherungsschutz auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="backup-reader"></a>Sicherungsleser
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Kann Sicherungsdienste anzeigen, aber keine Änderungen vornehmen. |
> | **Id** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | „GetAllocatedStamp“ ist ein interner Vorgang des Diensts. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Gibt den Status des Vorgangs zurück. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Ruft das Ergebnis eines Vorgangs ab, der für den Schutzcontainer ausgeführt wurde. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Ruft das Ergebnis eines Vorgangs ab, der für geschützte Elemente ausgeführt wurde. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Gibt den Status eines Vorgangs zurück, der für geschützte Elemente ausgeführt wurde. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Gibt Objektdetails des geschützten Elements zurück. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Dient zum Abrufen von Wiederherstellungspunkten für geschützte Elemente. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Gibt alle registrierten Container zurück. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Gibt das Ergebnis von Auftragsvorgängen zurück. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Gibt alle Auftragsobjekte zurück. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Dient zum Exportieren von Aufträgen. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Gibt das Ergebnis eines Sicherungsvorgangs für Recovery Services-Tresore zurück. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Dient zum Abrufen der Ergebnisse von Richtlinienvorgängen. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Gibt alle Schutzrichtlinien zurück. |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Gibt die Liste mit allen geschützten Elementen zurück. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Gibt alle zum Abonnement gehörenden Container zurück. |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Gibt Zusammenfassungen für geschützte Elemente und geschützte Server für einen Recovery Services-Tresor zurück. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Der Vorgang „Ausführliche Informationen abrufen“ ruft die ausführlichen Informationen zu einem Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Ruft die Warnungen für den Recovery Services-Tresor ab. |
> | Microsoft.RecoveryServices/Vaults/read | Der Vorgang „Tresor abrufen“ ruft ein Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Mit dem Vorgang „Vorgangsergebnisse abrufen“ können der Vorgangsstatus und das Ergebnis für den asynchron übermittelten Vorgang abgerufen werden. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Der Vorgang „Container abrufen“ kann zum Abrufen der für eine Ressource registrierten Container verwendet werden. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Gibt die Speicherkonfiguration für Recovery Services-Tresore zurück. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Gibt die Konfiguration für Recovery Services-Tresore zurück. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Gibt den Status eines Sicherungsvorgangs für Recovery Services-Tresore zurück. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Dient zum Abrufen des Status von Richtlinienvorgängen. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Gibt alle Sicherungsverwaltungsserver zurück, die beim Tresor registriert sind. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Ruft einen beabsichtigten Sicherungsschutz ab. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Ruft alle Elemente in einem Container ab. |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Überprüft den Sicherungsstatus für Recovery Services-Tresore. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Löst die Warnung auf. |
> | Microsoft.RecoveryServices/operations/read | Der Vorgang gibt die Liste der Vorgänge für einen Ressourcenanbieter zurück. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Ruft den Vorgangsstatus eines angegebenen Vorgangs ab. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Listet den gesamten beabsichtigten Sicherungsschutz auf. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Gibt Nutzungsdetails für einen Recovery Services-Tresor zurück. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="billing-reader"></a>Abrechnungsleser
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Hiermit wird Lesezugriff auf Abrechnungsdaten ermöglicht. |
> | **Id** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Billing/*/read | Lesen von Abrechnungsinformationen |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | Listet die Verwaltungsgruppen für den authentifizierten Benutzer auf. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="biztalk-contributor"></a>Mitwirkender von BizTalk
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten von BizTalk-Diensten, nicht aber den Zugriff darauf. |
> | **Id** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.BizTalkServices/BizTalk/* | Erstellen und Verwalten von BizTalk-Diensten |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="blockchain-member-node-access-preview"></a>Zugriff auf Blockchainmitgliedsknoten (Vorschauversion)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht den Zugriff auf Blockchainmitgliedsknoten. |
> | **Id** | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **Aktionen** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Ruft die vorhandenen Transaktionsknoten eines Blockchainmitglieds ab oder listet sie auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Stellt eine Verbindung mit dem Transaktionsknoten eines Blockchainmitglieds her. |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="cdn-endpoint-contributor"></a>Mitwirkender für den CDN-Endpunkt
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Kann CDN-Endpunkte verwalten, aber anderen Benutzern keinen Zugriff erteilen. |
> | **Id** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="cdn-endpoint-reader"></a>CDN-Endpunktleser
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Kann CDN-Endpunkte anzeigen, aber keine Änderungen vornehmen. |
> | **Id** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="cdn-profile-contributor"></a>Mitwirkender für das CDN-Profil
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Kann CDN-Profile und deren Endpunkte verwalten, aber anderen Benutzern keinen Zugriff erteilen. |
> | **Id** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="cdn-profile-reader"></a>CDN-Profilleser
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Kann CDN-Profile und deren Endpunkte anzeigen, aber keine Änderungen vornehmen. |
> | **Id** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="classic-network-contributor"></a>Mitwirkender von klassischem Netzwerk
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten von klassischen Netzwerken, nicht aber den Zugriff darauf. |
> | **Id** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Autorisierungen |
> | Microsoft.ClassicNetwork/* | Erstellen und Verwalten von klassischen Netzwerken |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="classic-storage-account-contributor"></a>Mitwirkender von klassischem Speicherkonto
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten klassischer Speicherkonten, nicht aber den Zugriff darauf. |
> | **Id** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Autorisierungen |
> | Microsoft.ClassicStorage/storageAccounts/* | Erstellen und Verwalten von Speicherkonten |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="classic-storage-account-key-operator-service-role"></a>Klassische Dienstrolle „Speicherkonto-Schlüsseloperator“
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Klassische Speicherkonto-Schlüsseloperatoren dürfen Schlüssel für klassische Speicherkonten auflisten und neu generieren. |
> | **Id** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Aktionen** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Listet die Zugriffsschlüssel für die Speicherkonten auf. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Generiert die vorhandenen Zugriffsschlüssel für das Speicherkonto neu. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="classic-virtual-machine-contributor"></a>Mitwirkender von klassischen virtuellen Computern
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten klassischer virtueller Computer, aber weder den Zugriff darauf noch auf die mit ihnen verbundenen virtuellen Netzwerke oder Speicherkonten. |
> | **Id** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Autorisierungen |
> | Microsoft.ClassicCompute/domainNames/* | Erstellen und Verwalten von klassischen Compute-Domänennamen |
> | Microsoft.ClassicCompute/virtualMachines/* | Erstellen und Verwalten von virtuellen Computern |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Dient zum Verknüpfen einer reservierten IP-Adresse. |
> | Microsoft.ClassicNetwork/reservedIps/read | Ruft die reservierten IP-Adressen ab. |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Führt zum Beitritt zum virtuellen Netzwerk. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Dient zum Abrufen des virtuellen Netzwerks. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Gibt den Speicherkontodatenträger zurück. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Gibt das Speicherkontoimage zurück. (Veraltet. Verwenden Sie „Microsoft.ClassicStorage/storageAccounts/vmImages“) |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listet die Zugriffsschlüssel für die Speicherkonten auf. |
> | Microsoft.ClassicStorage/storageAccounts/read | Dient zum Zurückgeben des Speicherkontos mit dem angegebenen Konto. |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="cognitive-services-contributor"></a>Mitwirkender für Cognitive Services
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Erstellen, Lesen, Aktualisieren, Löschen und Verwalten von Cognitive Services-Schlüsseln. |
> | **Id** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Ruft die Features eines Abonnements ab. |
> | Microsoft.Features/providers/features/read | Ruft das Feature eines Abonnements in einem angegebenen Ressourcenanbieter ab. |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Insights/diagnosticSettings/* | Erstellt, aktualisiert oder liest die Diagnoseeinstellung für den Analysis-Server. |
> | Microsoft.Insights/logDefinitions/read | Dient zum Lesen von Protokolldefinitionen. |
> | Microsoft.Insights/metricdefinitions/read | Dient zum Lesen von Metrikdefinitionen. |
> | Microsoft.Insights/metrics/read | Dient zum Lesen von Metriken. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/deployments/operations/read | Ruft Bereitstellungsvorgänge ab oder listet sie auf. |
> | Microsoft.Resources/subscriptions/operationresults/read | Dient zum Abrufen der Ergebnisse des Abonnementvorgangs. |
> | Microsoft.Resources/subscriptions/read | Ruft die Abonnementliste ab. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="cognitive-services-data-reader-preview"></a>Cognitive Services-Datenleser (Vorschau)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht das Lesen von Cognitive Services-Daten. |
> | **Id** | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | **Aktionen** |  |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="cognitive-services-user"></a>Cognitive Services-Benutzer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Lesen und Auflisten von Cognitive Services-Schlüsseln. |
> | **Id** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Aktionen** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Dient zum Auflisten von Schlüsseln. |
> | Microsoft.Insights/alertRules/read | Liest eine klassische Metrikwarnung. |
> | Microsoft.Insights/diagnosticSettings/read | Liest eine Diagnoseeinstellung für eine Ressource. |
> | Microsoft.Insights/logDefinitions/read | Dient zum Lesen von Protokolldefinitionen. |
> | Microsoft.Insights/metricdefinitions/read | Dient zum Lesen von Metrikdefinitionen. |
> | Microsoft.Insights/metrics/read | Dient zum Lesen von Metriken. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/operations/read | Ruft Bereitstellungsvorgänge ab oder listet sie auf. |
> | Microsoft.Resources/subscriptions/operationresults/read | Dient zum Abrufen der Ergebnisse des Abonnementvorgangs. |
> | Microsoft.Resources/subscriptions/read | Ruft die Abonnementliste ab. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="cosmos-db-account-reader-role"></a>Cosmos DB-Rolle „Kontoleser“
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Kann Azure Cosmos DB-Kontodaten lesen. Informationen zum Verwalten von Azure Cosmos DB-Konten finden Sie unter [Mitwirkender von DocumentDB-Konto](#documentdb-account-contributor). |
> | **Id** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen, kann die jedem Benutzer erteilten Berechtigungen lesen |
> | Microsoft.DocumentDB/*/read | Lesen einer beliebigen Sammlung |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Liest die schreibgeschützten Schlüssel für Datenbankkonten. |
> | Microsoft.Insights/MetricDefinitions/read | Dient zum Lesen von Metrikdefinitionen. |
> | Microsoft.Insights/Metrics/read | Dient zum Lesen von Metriken. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="cosmos-db-operator"></a>Cosmos DB-Operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht das Verwalten von Azure Cosmos DB-Konten, aber nicht das Zugreifen auf deren Daten. Verhindert den Zugriff auf Kontoschlüssel und Verbindungszeichenfolgen. |
> | **Id** | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | **Aktionen** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | Microsoft.DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="cosmosbackupoperator"></a>CosmosBackupOperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Kann eine Wiederherstellungsanforderung für eine Cosmos DB-Datenbank oder einen Container für ein Konto übermitteln. |
> | **Id** | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | **Aktionen** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | Sendet eine Anforderung zum Konfigurieren der Sicherung. |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Sendet eine Wiederherstellungsanforderung. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="cost-management-contributor"></a>Mitwirkender für Cost Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Anzeigen der Kosten und das Verwalten der Kostenkonfiguration (z. B. Budgets, Exporte). |
> | **Id** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **Aktionen** |  |
> | Microsoft.Consumption/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read | Listet die verfügbaren Abrechnungszeiträume auf. |
> | Microsoft.Resources/subscriptions/read | Ruft die Abonnementliste ab. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | Microsoft.Advisor/configurations/read | Konfigurationen abrufen |
> | Microsoft.Advisor/recommendations/read | Liest Empfehlungen. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="cost-management-reader"></a>Cost Management-Leser
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Anzeigen der Kostendaten und -konfiguration (z. B. Budgets, Exporte). |
> | **Id** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **Aktionen** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Billing/billingPeriods/read | Listet die verfügbaren Abrechnungszeiträume auf. |
> | Microsoft.Resources/subscriptions/read | Ruft die Abonnementliste ab. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | Microsoft.Advisor/configurations/read | Konfigurationen abrufen |
> | Microsoft.Advisor/recommendations/read | Liest Empfehlungen. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="data-box-contributor"></a>Data Box-Mitwirkender
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten aller Komponenten unter dem Data Box-Dienst, mit Ausnahme der Gewährung des Zugriffs für andere Benutzer. |
> | **Id** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | Microsoft.Databox/* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="data-box-reader"></a>Data Box-Leser
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten des Data Box-Diensts, mit Ausnahme der Erstellung von Aufträgen oder der Bearbeitung von Auftragsdetails und der Gewährung des Zugriffs für andere Benutzer. |
> | **Id** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | Hiermit werden die unverschlüsselten Anmeldeinformationen für den Auftrag aufgelistet. |
> | Microsoft.Databox/locations/availableSkus/action | Mit dieser Methode wird die Liste der verfügbaren SKUs zurückgegeben. |
> | Microsoft.Databox/locations/validateAddress/action | Hiermit wird die Lieferadresse überprüft, und es werden – sofern vorhanden – alternative Adressen bereitgestellt. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="data-factory-contributor"></a>Mitwirkender von Data Factory
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Erstellen und verwalten Sie Data Factorys sowie die darin enthaltenen untergeordneten Ressourcen. |
> | **Id** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.DataFactory/dataFactories/* | Erstellt und verwaltet Data Factorys und darin enthaltene untergeordnete Ressourcen. |
> | Microsoft.DataFactory/factories/* | Erstellt und verwaltet Data Factorys und darin enthaltene untergeordnete Ressourcen. |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="data-lake-analytics-developer"></a>Data Lake Analytics-Entwickler
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Übermitteln, Überwachen und Verwalten Ihrer eigenen Aufträge, aber nicht das Erstellen oder Löschen von Data Lake Analytics-Konten. |
> | **Id** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Löscht ein DataLakeAnalytics-Konto. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Erteilt Berechtigungen zum Abbrechen von Aufträgen, die von anderen Benutzern übermittelt wurden. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Erstellt oder aktualisiert ein DataLakeAnalytics-Konto. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Erstellt oder aktualisiert ein verknüpftes DataLakeStore-Konto eines DataLakeAnalytics-Kontos. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Hebt die Verknüpfung eines DataLakeStore-Kontos mit einem DataLakeAnalytics-Konto auf. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Erstellt oder aktualisiert ein verknüpftes Speicherkonto eines DataLakeAnalytics-Kontos. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Hebt die Verknüpfung eines Speicherkontos mit einem DataLakeAnalytics-Konto auf. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Dient zum Erstellen oder Aktualisieren einer Firewallregel. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Dient zum Löschen einer Firewallregel. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Erstellt oder aktualisiert eine Computerichtlinie. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Löscht eine Computerichtlinie. |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="data-purger"></a>Datenpurger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Kann Analysedaten endgültig löschen. |
> | **Id** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Aktionen** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Daten werden aus Application Insights gelöscht |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | Löscht die angegebenen Daten aus dem Arbeitsbereich. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="devtest-labs-user"></a>DevTest Labs-Benutzer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verbinden, Starten, Neustarten und Herunterfahren Ihrer virtuellen Computer in Ihren Azure DevTest Labs. |
> | **Id** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Compute/availabilitySets/read | Dient zum Abrufen der Eigenschaften einer Verfügbarkeitsgruppe. |
> | Microsoft.Compute/virtualMachines/*/read | Lesen der Eigenschaften eines virtuellen Computers (VM-Größen, Laufzeitstatus, VM-Erweiterungen usw.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Schaltet den virtuellen Computer aus und gibt die Computeressourcen frei. |
> | Microsoft.Compute/virtualMachines/read | Dient zum Abrufen der Eigenschaften eines virtuellen Computers. |
> | Microsoft.Compute/virtualMachines/restart/action | Startet den virtuellen Computer neu. |
> | Microsoft.Compute/virtualMachines/start/action | Startet den virtuellen Computer. |
> | Microsoft.DevTestLab/*/read | Lesen der Eigenschaften eines Labs |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Dient zum Anfordern eines beliebigen anforderbaren virtuellen Computers im Lab. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Dient zum Erstellen von virtuellen Computern in einem Lab. |
> | Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action | Stellt sicher, dass der aktuelle Benutzer über ein gültiges Profil im Lab verfügt. |
> | Microsoft.DevTestLab/labs/formulas/delete | Dient zum Löschen von Formeln. |
> | Microsoft.DevTestLab/labs/formulas/read | Dient zum Lesen von Formeln. |
> | Microsoft.DevTestLab/labs/formulas/write | Dient zum Hinzufügen oder Ändern von Formeln. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Wertet Labrichtlinien aus. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Dient dazu, einen vorhandenen virtuellen Computer in Besitz zu nehmen. |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action | Listet die anwendbaren Zeitpläne zum Starten/Beenden auf, sofern vorhanden. |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | Ruft eine Zeichenfolge ab, die den Inhalt der RDP-Datei für den virtuellen Computer darstellt. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Verknüpft einen Back-End-Adresspool für den Lastenausgleich. Nicht warnbar. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Verknüpft eine NAT-Regel für eingehenden Datenverkehr für den Lastenausgleich. Nicht warnbar. |
> | Microsoft.Network/networkInterfaces/*/read | Lesen der Eigenschaften einer Netzwerkschnittstelle (z.B. alle Load Balancer, zu denen die Netzwerkschnittstelle gehört) |
> | Microsoft.Network/networkInterfaces/join/action | Verknüpft einen virtuellen Computer mit einer Netzwerkschnittstelle. Nicht warnbar. |
> | Microsoft.Network/networkInterfaces/read | Ruft eine Netzwerkschnittstellendefinition ab.  |
> | Microsoft.Network/networkInterfaces/write | Erstellt eine Netzwerkschnittstelle oder aktualisiert eine vorhandene Netzwerkschnittstelle.  |
> | Microsoft.Network/publicIPAddresses/*/read | Lesen der Eigenschaften einer öffentlichen IP-Adresse |
> | Microsoft.Network/publicIPAddresses/join/action | Verknüpft eine öffentliche IP-Adresse. Nicht warnbar. |
> | Microsoft.Network/publicIPAddresses/read | Ruft eine Definition für eine öffentliche IP-Adresse ab. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Verknüpft ein virtuelles Netzwerk. Nicht warnbar. |
> | Microsoft.Resources/deployments/operations/read | Ruft Bereitstellungsvorgänge ab oder listet sie auf. |
> | Microsoft.Resources/deployments/read | Ruft Bereitstellungen ab oder listet sie auf. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Gibt die Zugriffsschlüssel für das angegebene Speicherkonto zurück. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Listet die verfügbaren Größen auf, auf die der virtuelle Computer aktualisiert werden kann. |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="dns-zone-contributor"></a>DNS Zone Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen die Verwaltung von DNS-Zonen und Ressourceneintragssätzen in Azure DNS, aber nicht zu steuern, wer darauf Zugriff hat. |
> | **Id** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
> | Microsoft.Network/dnsZones/* | Erstellen und Verwalten von DNS-Zonen und -Einträgen |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="documentdb-account-contributor"></a>Mitwirkender von DocumentDB-Konto
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Kann Azure Cosmos DB-Konten verwalten. Azure Cosmos DB wurde früher als DocumentDB bezeichnet. |
> | **Id** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.DocumentDb/databaseAccounts/* | Kann Azure Cosmos DB-Konten erstellen und verwalten |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="event-hubs-data-owner"></a>Event Hubs-Datenbesitzer

> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht den uneingeschränkten Zugriff auf die Azure Event Hubs-Ressourcen. |
> | **Id** | f526a384-b230-433a-b45c-95f59c4a2dec |
> | **Aktionen** |  |
> | Microsoft.EventHubs/* | Ermöglicht den uneingeschränkten Verwaltungszugriff auf den Event Hubs-Namespace. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.EventHubs/* | Ermöglicht den uneingeschränkten Datenzugriff auf den Event Hubs-Namespace. |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription-Mitwirkender
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht die Verwaltung von EventGrid-Ereignisabonnementvorgängen. |
> | **Id** | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Listet globale Ereignisabonnements nach Thematyp auf. |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Listet regionale Ereignisabonnements auf. |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Listet regionale Ereignisabonnements nach Thematyp auf. |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription-Leser
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht das Lesen von EventGrid-Ereignisabonnements. |
> | **Id** | 2414bbcf-6497-4faf-8c65-045460748405 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.EventGrid/eventSubscriptions/read | Liest eventSubscription. |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Listet globale Ereignisabonnements nach Thematyp auf. |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Listet regionale Ereignisabonnements auf. |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Listet regionale Ereignisabonnements nach Thematyp auf. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="hdinsight-cluster-operator"></a>HDInsight-Clusteroperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Lesen und Ändern von HDInsight-Clusterkonfigurationen. |
> | **Id** | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | **Aktionen** |  |
> | Microsoft.HDInsight/*/read |  |
> | Microsoft.HDInsight/clusters/getGatewaySettings/action | Ruft Gatewayeinstellungen für HDInsight-Cluster ab. |
> | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Aktualisiert Gatewayeinstellungen für HDInsight-Cluster. |
> | Microsoft.HDInsight/clusters/configurations/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Resources/deployments/operations/read | Ruft Bereitstellungsvorgänge ab oder listet sie auf. |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="hdinsight-domain-services-contributor"></a>Mitwirkender für die HDInsight-Domänendienste
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen, Vorgänge im Zusammenhang mit Domänendiensten, die für das HDInsight Enterprise-Sicherheitspaket erforderlich sind, zu lesen, zu erstellen, zu ändern und zu löschen. |
> | **Id** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **Aktionen** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="intelligent-systems-account-contributor"></a>Mitwirkender von Intelligent Systems-Konto
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten von Intelligent Systems-Konten, nicht aber den Zugriff darauf. |
> | **Id** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
> | Microsoft.IntelligentSystems/accounts/* | Erstellen und Verwalten von Intelligent Systems-Konten |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="key-vault-contributor"></a>Key Vault-Mitwirkender
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen die Verwaltung von Schlüsseltresoren, aber nicht den Zugriff darauf. |
> | **Id** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Dient zum endgültigen Löschen eines vorläufig gelöschten Schlüsseltresors. |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="lab-creator"></a>Lab-Ersteller
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Erstellen, Verwalten und Löschen verwalteter Labs unter Ihren Azure Lab-Konten. |
> | **Id** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Hiermit wird ein Lab in einem Lab-Konto erstellt. |
> | Microsoft.LabServices/labAccounts/sizes/GetRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Hiermit werden Informationen zur regionalen Verfügbarkeit jeder Größenkategorie abgerufen, die in einem Labkonto konfiguriert sind. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="log-analytics-contributor"></a>Log Analytics-Mitwirkender
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ein Log Analytics-Mitwirkender kann alle Überwachungsdaten lesen und Überwachungseinstellungen bearbeiten. Das Bearbeiten von Überwachungseinstellungen schließt folgende Aufgaben ein: Hinzufügen der VM-Erweiterung zu VMs, Lesen von Speicherkontoschlüsseln zum Konfigurieren von Protokollsammlungen aus Azure Storage, Erstellen und Konfigurieren von Automation-Konten, Hinzufügen von Lösungen, Konfigurieren der Azure-Diagnose für alle Azure-Ressourcen. |
> | **Id** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Aktionen** |  |
> | */Lesen | Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listet die Zugriffsschlüssel für die Speicherkonten auf. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Insights/diagnosticSettings/* | Erstellt, aktualisiert oder liest die Diagnoseeinstellung für den Analysis-Server. |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Gibt die Zugriffsschlüssel für das angegebene Speicherkonto zurück. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="log-analytics-reader"></a>Log Analytics-Leser
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ein Log Analytics-Leser kann alle Überwachungsdaten anzeigen und durchsuchen sowie Überwachungseinstellungen anzeigen. Hierzu zählt auch die Anzeige der Konfiguration von Azure-Diagnosen für alle Azure-Ressourcen. |
> | **Id** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Aktionen** |  |
> | */Lesen | Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Führt eine Suche mit der neuen Engine aus. |
> | Microsoft.OperationalInsights/workspaces/search/action | Führt eine Suchabfrage aus. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Ruft die gemeinsam verwendeten Schlüssel für den Arbeitsbereich ab. Diese Schlüssel werden verwendet, um Microsoft Operational Insights-Agents mit dem Arbeitsbereich zu verbinden. |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="logic-app-contributor"></a>Mitwirkender für Logik-Apps
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten von Logik-Apps, aber nicht den Zugriff darauf. |
> | **Id** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listet die Zugriffsschlüssel für die Speicherkonten auf. |
> | Microsoft.ClassicStorage/storageAccounts/read | Dient zum Zurückgeben des Speicherkontos mit dem angegebenen Konto. |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Insights/diagnosticSettings/* | Erstellt, aktualisiert oder liest die Diagnoseeinstellung für den Analysis-Server. |
> | Microsoft.Insights/logdefinitions/* | Diese Berechtigung ist für Benutzer notwendig, die über das Portal auf Aktivitätsprotokolle zugreifen müssen. Auflisten der Protokollkategorien im Aktivitätsprotokoll. |
> | Microsoft.Insights/metricDefinitions/* | Lesen von Metrikdefinitionen (Liste der verfügbaren Metriktypen für eine Ressource). |
> | Microsoft.Logic/* | Verwaltet Logic Apps-Ressourcen. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/operationresults/read | Dient zum Abrufen der Ergebnisse des Abonnementvorgangs. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Gibt die Zugriffsschlüssel für das angegebene Speicherkonto zurück. |
> | Microsoft.Storage/storageAccounts/read | Gibt die Liste mit Speicherkonten zurück oder ruft die Eigenschaften für das angegebene Speicherkonto ab. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | Microsoft.Web/connectionGateways/* | Erstellt und verwaltet ein Verbindungsgateway. |
> | Microsoft.Web/connections/* | Erstellt und verwaltet eine Verbindung. |
> | Microsoft.Web/customApis/* | Erstellt und verwaltet eine benutzerdefinierte API. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Dient zum Abrufen der Eigenschaften für einen App Service-Plan. |
> | Microsoft.Web/sites/functions/listSecrets/action | Dient zum Auflisten der Geheimnisse für Web-App-Funktionen. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="logic-app-operator"></a>Logik-App-Operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Lesen, Aktivieren und Deaktivieren von Logik-Apps. |
> | **Id** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Insights/alertRules/*/read | Lesen von Insights-Warnungsregeln |
> | Microsoft.Insights/diagnosticSettings/*/read | Ruft die Diagnoseeinstellungen für Logic Apps ab. |
> | Microsoft.Insights/metricDefinitions/*/read | Ruft die verfügbaren Metriken für Logic Apps ab. |
> | Microsoft.Logic/*/read | Liest Logic Apps-Ressourcen. |
> | Microsoft.Logic/workflows/disable/action | Deaktiviert den Workflow. |
> | Microsoft.Logic/workflows/enable/action | Aktiviert den Workflow. |
> | Microsoft.Logic/workflows/validate/action | Überprüft den Workflow. |
> | Microsoft.Resources/deployments/operations/read | Ruft Bereitstellungsvorgänge ab oder listet sie auf. |
> | Microsoft.Resources/subscriptions/operationresults/read | Dient zum Abrufen der Ergebnisse des Abonnementvorgangs. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | Microsoft.Web/connectionGateways/*/read | Liest Verbindungsgateways. |
> | Microsoft.Web/connections/*/read | Liest Verbindungen. |
> | Microsoft.Web/customApis/*/read | Liest benutzerdefinierte API. |
> | Microsoft.Web/serverFarms/read | Dient zum Abrufen der Eigenschaften für einen App Service-Plan. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="managed-application-operator-role"></a>Rolle „Bediener für verwaltete Anwendung“
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Lesen und Durchführen von Aktionen für Ressourcen der verwalteten Anwendung. |
> | **Id** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Aktionen** |  |
> | */Lesen | Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
> | Microsoft.Solutions/applications/read | Hiermit wird eine Liste mit Anwendungen abgerufen. |
> | Microsoft.Solutions/*/action |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="managed-applications-reader"></a>Leser für verwaltete Anwendungen
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen, Ressourcen in einer verwalteten App zu lesen und JIT-Zugriff anzufordern. |
> | **Id** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Aktionen** |  |
> | */Lesen | Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="managed-identity-contributor"></a>Mitwirkender für verwaltete Identität
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Dem Benutzer zugewiesene Identität erstellen, lesen, aktualisieren und löschen. |
> | **Id** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Aktionen** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="managed-identity-operator"></a>Operator für verwaltete Identität
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Dem Benutzer zugewiesene Identität lesen und zuweisen. |
> | **Id** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Aktionen** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="management-group-contributor"></a>Verwaltungsgruppenmitwirkender
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Rolle „Verwaltungsgruppenmitwirkender“ |
> | **Id** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Aktionen** |  |
> | Microsoft.Management/managementGroups/delete | Löscht eine Verwaltungsgruppe. |
> | Microsoft.Management/managementGroups/read | Listet die Verwaltungsgruppen für den authentifizierten Benutzer auf. |
> | Microsoft.Management/managementGroups/subscriptions/delete | Hebt die Zuordnung des Abonnements mit der Verwaltungsgruppe auf. |
> | Microsoft.Management/managementGroups/subscriptions/write | Ordnet ein vorhandenes Abonnement der Verwaltungsgruppe zu. |
> | Microsoft.Management/managementGroups/write | Erstellt oder aktualisiert eine Verwaltungsgruppe. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="management-group-reader"></a>Verwaltungsgruppenleser
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Rolle „Verwaltungsgruppenleser“ |
> | **Id** | ac63b705-F282-497d-ac71-919bf39d939d |
> | **Aktionen** |  |
> | Microsoft.Management/managementGroups/read | Listet die Verwaltungsgruppen für den authentifizierten Benutzer auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="monitoring-contributor"></a>Überwachungsmitwirkender
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Kann sämtliche Überwachungsdaten lesen und Überwachungseinstellungen bearbeiten. Siehe auch [Erste Schritte mit Rollen, Berechtigungen und Sicherheit in Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Aktionen** |  |
> | */Lesen | Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Lesen/Schreiben/Löschen von Warnungsregeln. |
> | Microsoft.Insights/components/* | Kann Application Insights-Komponenten lesen/schreiben/delegieren. |
> | Microsoft.Insights/DiagnosticSettings/* | Lesen/Schreiben/Löschen von Diagnoseeinstellungen. |
> | Microsoft.Insights/eventtypes/* | Auflisten von Aktivitätsprotokollereignissen (Verwaltungsereignissen) in einem Abonnement. Diese Berechtigung gilt sowohl für den programmgesteuerten als auch für den Portalzugriff auf das Aktivitätsprotokoll. |
> | Microsoft.Insights/LogDefinitions/* | Diese Berechtigung ist für Benutzer notwendig, die über das Portal auf Aktivitätsprotokolle zugreifen müssen. Auflisten der Protokollkategorien im Aktivitätsprotokoll. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Lesen von Metrikdefinitionen (Liste der verfügbaren Metriktypen für eine Ressource). |
> | Microsoft.Insights/Metrics/* | Lesen von Metriken für eine Ressource. |
> | Microsoft.Insights/Register/Action | Dient zum Registrieren des Microsoft Insights-Anbieters. |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Lesen/Schreiben/Löschen von Application Insights-Webtests. |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Lesen/Schreiben/Löschen von Log Analytics-Lösungspaketen. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Lesen/Schreiben/Löschen von gespeicherten Log Analytics-Suchvorgängen. |
> | Microsoft.OperationalInsights/workspaces/search/action | Führt eine Suchabfrage aus. |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Ruft die gemeinsam verwendeten Schlüssel für den Arbeitsbereich ab. Diese Schlüssel werden verwendet, um Microsoft Operational Insights-Agents mit dem Arbeitsbereich zu verbinden. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Lesen/Schreiben/Löschen von Log Analytics-Speicherdetailinformationen. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | Microsoft.WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="monitoring-metrics-publisher"></a>Herausgeber von Überwachungsmetriken
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht die Veröffentlichung von Metriken für Azure-Ressourcen. |
> | **Id** | 3913510d-42f4-4e42-8a64-420c390055eb |
> | **Aktionen** |  |
> | Microsoft.Insights/Register/Action | Dient zum Registrieren des Microsoft Insights-Anbieters. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.Insights/Metrics/Write | Hiermit werden Metriken geschrieben. |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="monitoring-reader"></a>Überwachungsleser
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Kann alle Überwachungsdaten (Metriken, Protokolle usw.) lesen. Siehe auch [Erste Schritte mit Rollen, Berechtigungen und Sicherheit in Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Aktionen** |  |
> | */Lesen | Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
> | Microsoft.OperationalInsights/workspaces/search/action | Führt eine Suchabfrage aus. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="network-contributor"></a>Mitwirkender von virtuellem Netzwerk
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten von Netzwerken, nicht aber den Zugriff darauf. |
> | **Id** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
> | Microsoft.Network/* | Erstellen und Verwalten von Netzwerken |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="new-relic-apm-account-contributor"></a>Mitwirkender von New Relic APM-Konto
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten von New Relic Application Performance Management-Konten und -Anwendungen, nicht aber den Zugriff auf diese. |
> | **Id** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="reader-and-data-access"></a>Lese- und Datenzugriff
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Anzeigen sämtlicher Aspekte, jedoch nicht das Löschen oder Erstellen eines Speicherkontos oder einer darin enthaltenen Ressource. Sie können auch Lese-/Schreibzugriff auf alle Daten in einem Speicherkonto durch Zugriff auf Speicherkontoschlüssel gewähren. |
> | **Id** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Aktionen** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Gibt die Zugriffsschlüssel für das angegebene Speicherkonto zurück. |
> | Microsoft.Storage/storageAccounts/ListAccountSas/action | Gibt das Konto-SAS-Token für das angegebene Speicherkonto zurück. |
> | Microsoft.Storage/storageAccounts/read | Gibt die Liste mit Speicherkonten zurück oder ruft die Eigenschaften für das angegebene Speicherkonto ab. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="redis-cache-contributor"></a>Mitwirkender von Redis-Cache
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten von Redis Caches, nicht aber den Zugriff darauf. |
> | **Id** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Cache/redis/* | Erstellen und Verwalten von Redis-Caches |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="resource-policy-contributor-preview"></a>Mitwirkender an Ressourcenrichtlinien (Vorschau)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | (Vorschau) Über EA abgeglichene Benutzer mit Rechten zum Erstellen/Ändern der Ressourcenrichtlinie, zum Erstellen eines Supporttickets und zum Lesen von Ressourcen/der Hierarchie. |
> | **Id** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Aktionen** |  |
> | */Lesen | Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
> | Microsoft.Authorization/policyassignments/* | Erstellen und Verwalten von Richtlinienzuweisungen |
> | Microsoft.Authorization/policydefinitions/* | Erstellen und Verwalten von Richtliniendefinitionen |
> | Microsoft.Authorization/policysetdefinitions/* | Erstellen und Verwalten von Richtliniensätzen |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="scheduler-job-collections-contributor"></a>Mitwirkender von Zeitplanungsauftragssammlung
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten von Scheduler-Auftragssammlungen, nicht aber den Zugriff darauf. |
> | **Id** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Scheduler/jobcollections/* | Erstellen und Verwalten von Auftragssammlungen |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="search-service-contributor"></a>Mitwirkender von Suchdienst
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten von Search-Diensten, nicht aber den Zugriff darauf. |
> | **Id** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Search/searchServices/* | Erstellen und Verwalten von Suchdiensten |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="security-admin"></a>Sicherheitsadministrator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Nur in Security Center: Kann Sicherheitsrichtlinien und -zustände anzeigen, Sicherheitsrichtlinien bearbeiten sowie Warnungen und Empfehlungen anzeigen und verwerfen. |
> | **Id** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Authorization/policyAssignments/* | Erstellen und Verwalten von Richtlinienzuweisungen |
> | Microsoft.Authorization/policyDefinitions/* | Erstellen und Verwalten von Richtliniendefinitionen |
> | Microsoft.Authorization/policySetDefinitions/* | Erstellen und Verwalten von Richtliniensätzen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
> | Microsoft.Management/managementGroups/read | Listet die Verwaltungsgruppen für den authentifizierten Benutzer auf. |
> | Microsoft.operationalInsights/workspaces/*/read | Anzeigen von Log Analytics-Daten |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Security/* |  |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="security-manager-legacy"></a>Sicherheits-Manager (Legacy)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Dies ist eine Legacyrolle. Verwenden Sie stattdessen „Sicherheitsadministrator“. |
> | **Id** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.ClassicCompute/*/read | Lesen von Konfigurationsinformationen zu klassischen virtuellen Computern |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Schreiben der Konfiguration für klassische virtuelle Computer |
> | Microsoft.ClassicNetwork/*/read | Lesen von Konfigurationsinformationen zu klassischem Netzwerk |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Security/* | Erstellen und Verwalten von Sicherheitskomponenten und -richtlinien |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="security-reader"></a>Sicherheitsleseberechtigter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Nur in Security Center: Kann Empfehlungen und Warnungen sowie Sicherheitsrichtlinien und -zustände anzeigen, aber keine Änderungen vornehmen. |
> | **Id** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
> | Microsoft.operationalInsights/workspaces/*/read | Anzeigen von Log Analytics-Daten |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Security/*/read | Lesen von Sicherheitskomponenten und -richtlinien |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | Microsoft.Management/managementGroups/read | Listet die Verwaltungsgruppen für den authentifizierten Benutzer auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="service-bus-data-owner"></a>Service Bus-Datenbesitzer

> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht den uneingeschränkten Zugriff auf die Azure Service Bus-Ressourcen. |
> | **Id** | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | **Aktionen** |  |
> | Microsoft.ServiceBus/* | Ermöglicht den uneingeschränkten Verwaltungszugriff auf den Service Bus-Namespace. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/* | Ermöglicht den uneingeschränkten Datenzugriff auf den Service Bus-Namespace. |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="site-recovery-contributor"></a>Site Recovery-Mitwirkender
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen die Verwaltung des Site Recovery-Diensts mit Ausnahme der Tresorerstellung und der Rollenzuweisung. |
> | **Id** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
> | Microsoft.Network/virtualNetworks/read | Dient zum Abrufen der Definition des virtuellen Netzwerks. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | „GetAllocatedStamp“ ist ein interner Vorgang des Diensts. |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | „AllocateStamp“ ist ein interner Vorgang des Diensts. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Der Vorgang „Ressourcenzertifikat aktualisieren“ aktualisiert das Zertifikat für die Ressourcen-/Tresoranmeldeinformationen. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Erstellen und Verwalten erweiterter Informationen in Zusammenhang mit einem Tresor |
> | Microsoft.RecoveryServices/Vaults/read | Der Vorgang „Tresor abrufen“ ruft ein Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Erstellen und Verwalten von registrierten Identitäten |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Erstellen oder Aktualisieren von Einstellungen für Replikationswarnungen |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Dient zum Lesen beliebiger Ereignisse |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Erstellen und Verwalten von Replikationsfabrics |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Erstellen und Verwalten von Replikationsaufträgen |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Erstellen und Verwalten von Replikationsrichtlinien |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Erstellen und Verwalten von Wiederherstellungsplänen |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Erstellen und Verwalten der Speicherkonfiguration des Recovery Services-Tresors |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Gibt Nutzungsdetails für einen Recovery Services-Tresor zurück. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Der Vorgang „Tresortoken“ kann zum Abrufen des Tresortokens für Back-End-Vorgänge auf Tresorebene verwendet werden. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Lesen von Warnungen für den Recovery Services-Tresor |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Storage/storageAccounts/read | Gibt die Liste mit Speicherkonten zurück oder ruft die Eigenschaften für das angegebene Speicherkonto ab. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="site-recovery-operator"></a>Site Recovery-Operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen ein Failover und ein Failback, aber nicht das Durchführen weiterer Site Recovery-Verwaltungsvorgänge. |
> | **Id** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
> | Microsoft.Network/virtualNetworks/read | Dient zum Abrufen der Definition des virtuellen Netzwerks. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | „GetAllocatedStamp“ ist ein interner Vorgang des Diensts. |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | „AllocateStamp“ ist ein interner Vorgang des Diensts. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Der Vorgang „Ausführliche Informationen abrufen“ ruft die ausführlichen Informationen zu einem Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | Microsoft.RecoveryServices/Vaults/read | Der Vorgang „Tresor abrufen“ ruft ein Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Mit dem Vorgang „Vorgangsergebnisse abrufen“ können der Vorgangsstatus und das Ergebnis für den asynchron übermittelten Vorgang abgerufen werden. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Der Vorgang „Container abrufen“ kann zum Abrufen der für eine Ressource registrierten Container verwendet werden. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Dient zum Lesen beliebiger Warnungseinstellungen |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Dient zum Lesen beliebiger Ereignisse |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Prüft die Konsistenz des Fabrics. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Dient zum Lesen beliebiger Fabrics |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Dient zum Neuzuordnen des Gateways. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Verlängert das Zertifikat für Fabrics. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Dient zum Lesen beliebiger Netzwerke |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Dient zum Lesen beliebiger Netzwerkzuordnungen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Dient zum Lesen beliebiger Schutzcontainer |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Dient zum Lesen beliebiger schützbarer Elemente |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Dient zum Anwenden des Wiederherstellungspunkts. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Failovercommit |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Geplantes Failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Dient zum Lesen beliebiger geschützter Elemente |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Dient zum Lesen beliebiger Wiederherstellungspunkte für die Replikation |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Dient zum Reparieren der Replikation. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Dient zum erneuten Schützen geschützter Elemente. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Testfailover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Testfailoverbereinigung |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Dient zum Aktualisieren von Mobility Service. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Dient zum Lesen beliebiger Schutzcontainerzuordnungen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Dient zum Lesen beliebiger Recovery Services-Anbieter |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Dient zum Aktualisieren des Anbieters. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Dient zum Lesen beliebiger Speicherklassifizierungen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Dient zum Lesen beliebiger Speicherklassifizierungszuordnungen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Dienst zum Lesen beliebiger vCenter |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Erstellen und Verwalten von Replikationsaufträgen |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Dient zum Lesen beliebiger Richtlinien |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Wiederherstellungsplan für Failovercommit |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Wiederherstellungsplan für geplantes Failover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Dient zum Lesen beliebiger Wiederherstellungspläne |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Wiederherstellungsplan für erneutes Schützen |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Wiederherstellungsplan für Testfailover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Wiederherstellungsplan für Testfailoverbereinigung |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Wiederherstellungsplan für Failover |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Lesen von Warnungen für den Recovery Services-Tresor |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Gibt Nutzungsdetails für einen Recovery Services-Tresor zurück. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Der Vorgang „Tresortoken“ kann zum Abrufen des Tresortokens für Back-End-Vorgänge auf Tresorebene verwendet werden. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Storage/storageAccounts/read | Gibt die Liste mit Speicherkonten zurück oder ruft die Eigenschaften für das angegebene Speicherkonto ab. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="site-recovery-reader"></a>Site Recovery-Leser
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen die Anzeige des Site Recovery-Status, aber nicht die Durchführung weiterer Verwaltungsvorgänge. |
> | **Id** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | „GetAllocatedStamp“ ist ein interner Vorgang des Diensts. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Der Vorgang „Ausführliche Informationen abrufen“ ruft die ausführlichen Informationen zu einem Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Ruft die Warnungen für den Recovery Services-Tresor ab. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | Der Vorgang „Tresor abrufen“ ruft ein Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Mit dem Vorgang „Vorgangsergebnisse abrufen“ können der Vorgangsstatus und das Ergebnis für den asynchron übermittelten Vorgang abgerufen werden. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Der Vorgang „Container abrufen“ kann zum Abrufen der für eine Ressource registrierten Container verwendet werden. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Dient zum Lesen beliebiger Warnungseinstellungen |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Dient zum Lesen beliebiger Ereignisse |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Dient zum Lesen beliebiger Fabrics |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Dient zum Lesen beliebiger Netzwerke |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Dient zum Lesen beliebiger Netzwerkzuordnungen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Dient zum Lesen beliebiger Schutzcontainer |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Dient zum Lesen beliebiger schützbarer Elemente |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Dient zum Lesen beliebiger geschützter Elemente |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Dient zum Lesen beliebiger Wiederherstellungspunkte für die Replikation |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Dient zum Lesen beliebiger Schutzcontainerzuordnungen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Dient zum Lesen beliebiger Recovery Services-Anbieter |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Dient zum Lesen beliebiger Speicherklassifizierungen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Dient zum Lesen beliebiger Speicherklassifizierungszuordnungen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Dienst zum Lesen beliebiger vCenter |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Dient zum Lesen beliebiger Aufträge |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Dient zum Lesen beliebiger Richtlinien |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Dient zum Lesen beliebiger Wiederherstellungspläne |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Gibt Nutzungsdetails für einen Recovery Services-Tresor zurück. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Der Vorgang „Tresortoken“ kann zum Abrufen des Tresortokens für Back-End-Vorgänge auf Tresorebene verwendet werden. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="spatial-anchors-account-contributor"></a>Spatial Anchors-Kontomitwirkender
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten von Raumankern in Ihrem Konto, nicht jedoch das Löschen von Ankern. |
> | **Id** | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | **Aktionen** |  |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Erstellen von Raumankern |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Ermitteln von Raumankern in räumlicher Nähe |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Abrufen der Eigenschaften von Raumankern |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Suchen von Raumankern |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Übermitteln von Diagnosedaten, um die Qualität des Azure Spatial Anchors-Diensts zu verbessern |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Aktualisieren der Eigenschaften von Raumankern |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="spatial-anchors-account-owner"></a>Spatial Anchors-Kontobesitzer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten von Raumankern in Ihrem Konto, einschließlich der Löschung von Ankern. |
> | **Id** | 70bbe301-9835-447d-afdd-19eb3167307c |
> | **Aktionen** |  |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Hiermit werden Raumanker erstellt. |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Löschen von Raumankern |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Ermitteln von Raumankern in räumlicher Nähe |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Abrufen der Eigenschaften von Raumankern |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Suchen von Raumankern |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Übermitteln von Diagnosedaten, um die Qualität des Azure Spatial Anchors-Diensts zu verbessern |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Aktualisieren der Eigenschaften von Raumankern |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="spatial-anchors-account-reader"></a>Spatial Anchors-Kontoleser
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Ermitteln und Lesen von Eigenschaften für Raumanker in Ihrem Dokument. |
> | **Id** | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Aktionen** |  |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Ermitteln von Raumankern in räumlicher Nähe |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Abrufen der Eigenschaften von Raumankern |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Suchen von Raumankern |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Übermitteln von Diagnosedaten, um die Qualität des Azure Spatial Anchors-Diensts zu verbessern |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="sql-db-contributor"></a>Mitwirkender von SQL DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten von SQL-Datenbanken, nicht aber den Zugriff darauf. Darüber hinaus können Sie deren sicherheitsbezogenen Richtlinien oder übergeordneten SQL-Server nicht verwalten. |
> | **Id** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Erstellen und Verwalten von SQL-Datenbanken |
> | Microsoft.Sql/servers/read | Gibt die Liste der Server zurück oder ruft die Eigenschaften für den angegebenen Server ab. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | Microsoft.Insights/metrics/read | Dient zum Lesen von Metriken. |
> | Microsoft.Insights/metricDefinitions/read | Dient zum Lesen von Metrikdefinitionen. |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Überwachungsrichtlinien bearbeiten |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Überwachungseinstellungen bearbeiten |
> | Microsoft.Sql/servers/databases/auditRecords/read | Dient zum Abrufen der Datensätze für die Datenbankblobüberwachung. |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Verbindungsrichtlinien bearbeiten |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Datenmaskierungsrichtlinien bearbeiten |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Richtlinien für Sicherheitswarnungen bearbeiten |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Sicherheitsmetriken bearbeiten |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="sql-managed-instance-contributor"></a>Verwaltete SQL-Instanz: Mitwirkender
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten verwalteter SQL-Instanzen und der erforderlichen Netzwerkkonfiguration, jedoch nicht das Erteilen des Zugriffs an andere. |
> | **Id** | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | **Aktionen** |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Network/networkSecurityGroups/* |  |
> | Microsoft.Network/routeTables/* |  |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/managedInstances/* |  |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | Microsoft.Network/virtualNetworks/subnets/* |  |
> | Microsoft.Network/virtualNetworks/* |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Insights/metrics/read | Dient zum Lesen von Metriken. |
> | Microsoft.Insights/metricDefinitions/read | Dient zum Lesen von Metrikdefinitionen. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="sql-security-manager"></a>SQL-Sicherheits-Manager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten von sicherheitsbezogenen Richtlinien von SQL-Server und Datenbanken, jedoch nicht den Zugriff darauf. |
> | **Id** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen der Microsoft-Autorisierung |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Verknüpft Ressourcen wie etwa ein Speicherkonto oder eine SQL-Datenbank mit einem Subnetz. Nicht warnbar. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Erstellen und Verwalten von SQL Server-Überwachungsrichtlinien |
> | Microsoft.Sql/servers/auditingSettings/* | Erstellen und Verwalten von SQL Server-Überwachungseinstellungen |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | Dient zum Abrufen von Details zur Richtlinie für die erweiterte Serverblobüberwachung, die für einen bestimmten Server konfiguriert ist. |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Erstellen und Verwalten von Überwachungsrichtlinien von SQL Server-Datenbanken |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Erstellen und Verwalten von Überwachungseinstellungen von SQL Server-Datenbanken |
> | Microsoft.Sql/servers/databases/auditRecords/read | Lesen von Überwachungsdatensätzen |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Erstellen und Verwalten von Verbindungsrichtlinien von SQL Server-Datenbanken |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Erstellen und Verwalten von Datenmaskierungsrichtlinien von SQL Server-Datenbanken |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Dient zum Abrufen von Details zur erweiterten Blobüberwachungsrichtlinie, die für eine bestimmte Datenbank konfiguriert ist. |
> | Microsoft.Sql/servers/databases/read | Gibt die Liste der Datenbanken zurück oder ruft die Eigenschaften für die angegebene Datenbank ab. |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/read | Abrufen eines Datenbankschema. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Abrufen einer Datenbankspalte. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Abrufen einer Datentabelle. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Erstellen und Verwalten von Richtlinien für Sicherheitswarnungen von SQL Server-Datenbanken |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Erstellen und Verwalten von Sicherheitsmetriken von SQL Server-Datenbanken |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Gibt die Liste der Server zurück oder ruft die Eigenschaften für den angegebenen Server ab. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Erstellen und Verwalten von Richtlinien für Sicherheitswarnungen von SQL Server |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="sql-server-contributor"></a>Mitwirkender von SQL Server
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen, SQL-Server und -Datenbanken zu verwalten, gewährt Ihnen jedoch keinen Zugriff darauf und auch nicht auf deren sicherheitsbezogenen Richtlinien. |
> | **Id** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Erstellen und Verwalten von SQL-Servern |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | Microsoft.Insights/metrics/read | Dient zum Lesen von Metriken. |
> | Microsoft.Insights/metricDefinitions/read | Dient zum Lesen von Metrikdefinitionen. |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | SQL Server-Überwachungsrichtlinien bearbeiten |
> | Microsoft.Sql/servers/auditingSettings/* | SQL Server-Überwachungseinstellungen bearbeiten |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Überwachungsrichtlinien von SQL Server-Datenbanken bearbeiten |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Überwachungseinstellungen von SQL Server-Datenbanken bearbeiten |
> | Microsoft.Sql/servers/databases/auditRecords/read | Lesen von Überwachungsdatensätzen |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Verbindungsrichtlinien von SQL Server-Datenbanken bearbeiten |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Datenmaskierungsrichtlinien von SQL Server-Datenbanken bearbeiten |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Richtlinien für Sicherheitswarnungen von SQL Server-Datenbanken bearbeiten |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Sicherheitsmetriken von SQL Server-Datenbanken bearbeiten |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Richtlinien für Sicherheitswarnungen von SQL Server bearbeiten |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="storage-account-contributor"></a>Mitwirkender von Speicherkonto
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Erlaubt die Verwaltung von Speicherkonten. Bietet keinen Zugriff auf Daten im Speicherkonto. |
> | **Id** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen aller Autorisierungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Insights/diagnosticSettings/* | Verwalten der Diagnoseeinstellungen |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Verknüpft Ressourcen wie etwa ein Speicherkonto oder eine SQL-Datenbank mit einem Subnetz. Nicht warnbar. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Storage/storageAccounts/* | Erstellen und Verwalten von Speicherkonten |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="storage-account-key-operator-service-role"></a>Dienstrolle „Speicherkonto-Schlüsseloperator“
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht das Auflisten und erneute Generieren von Zugriffsschlüsseln für Speicherkonten. |
> | **Id** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Aktionen** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Zurückgeben der Zugriffsschlüssel für das angegebene Speicherkonto. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Neugenerieren der Zugriffsschlüssel für das angegebene Speicherkonto. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="storage-blob-data-contributor"></a>Mitwirkender an Speicherblobdaten
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Lesen, Schreiben und Löschen von Azure Storage-Containern und -Blobs. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Aktionen** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Löschen eines Containers. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Zurückgeben eines Containers oder einer Liste von Containern. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Ändern der Metadaten oder Eigenschaften eines Containers. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Löschen eines Blobs |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Zurückgeben eines Blob oder einer Liste von Blobs. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Schreiben in ein Blob. |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="storage-blob-data-owner"></a>Besitzer von Speicherblobdaten
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Bietet Vollzugriff auf Azure Storage-Blobcontainer und -daten, einschließlich POSIX-Zugriffssteuerung. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **Aktionen** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Vollzugriffsberechtigungen für Container. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Vollzugriffsberechtigungen für Blobs. |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="storage-blob-data-reader"></a>Leser von Speicherblobdaten
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Lesen und Auflisten von Azure Storage-Containern und -Blobs. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Aktionen** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Zurückgeben eines Containers oder einer Liste von Containern. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Zurückgeben eines Blob oder einer Liste von Blobs. |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="storage-queue-data-contributor"></a>Mitwirkender an Storage-Warteschlangendaten
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Lesen, Schreiben und Löschen von Azure Storage-Warteschlangen und -Warteschlangennachrichten. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Aktionen** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Löschen einer Warteschlange. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Zurückgeben einer Warteschlange oder Liste mit Warteschlangen. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Ändern der Metadaten oder Eigenschaften einer Warteschlange. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Löschen einer oder mehrerer Nachrichten aus einer Warteschlange. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Einsehen oder Abrufen einer oder mehrerer Nachrichten aus einer Warteschlange. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Hinzufügen von Nachrichten zu einer Warteschlange. |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="storage-queue-data-message-processor"></a>Verarbeiter von Speicherwarteschlangen-Datennachrichten
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Einsehen, Abrufen und Löschen von Nachrichten aus Azure Storage-Warteschlangen. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | **Aktionen** |  |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Einsehen einer Nachricht. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Abrufen und Löschen einer Nachricht. |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="storage-queue-data-message-sender"></a>Absender der Speicherwarteschlangen-Datennachricht
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Hinzufügen von Nachrichten zu einer Azure Storage-Warteschlange. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | **Aktionen** |  |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Hinzufügen von Nachrichten zu einer Warteschlange. |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="storage-queue-data-reader"></a>Storage-Warteschlangendatenleser
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Lesen und Auflisten von Azure Storage-Warteschlangen und -Warteschlangennachrichten. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Aktionen** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Gibt eine Warteschlange oder eine Liste von Warteschlangen zurück. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Einsehen oder Abrufen einer oder mehrerer Nachrichten aus einer Warteschlange. |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="support-request-contributor"></a>Mitwirkender für Supportanfragen
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen die Erstellung und Verwaltung von Supportanfragen. |
> | **Id** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Autorisierungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="traffic-manager-contributor"></a>Traffic Manager-Mitwirkender
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen die Verwaltung von Traffic Manager-Profilen, aber nicht die Steuerung des Zugriffs darauf. |
> | **Id** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="user-access-administrator"></a>Benutzerzugriffsadministrator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen die Verwaltung von Benutzerzugriffen auf Azure-Ressourcen. |
> | **Id** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Aktionen** |  |
> | */Lesen | Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
> | Microsoft.Authorization/* | Verwalten der Autorisierung |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="virtual-machine-administrator-login"></a>VM-Administratoranmeldung
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Anzeigen von virtuellen Computern im Portal und Anmelden als Administrator |
> | **Id** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Aktionen** |  |
> | Microsoft.Network/publicIPAddresses/read | Ruft eine Definition für eine öffentliche IP-Adresse ab. |
> | Microsoft.Network/virtualNetworks/read | Dient zum Abrufen der Definition des virtuellen Netzwerks. |
> | Microsoft.Network/loadBalancers/read | Ruft eine Lastenausgleichsdefinition ab. |
> | Microsoft.Network/networkInterfaces/read | Ruft eine Netzwerkschnittstellendefinition ab.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Hiermit melden Sie sich bei einem virtuellen Computer als normaler Benutzer an. |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Hiermit melden Sie sich bei einem virtuellen Computer mit Windows-Administrator- oder Linux-Root-Benutzerrechten an. |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="virtual-machine-contributor"></a>Mitwirkender von virtuellen Computern
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten virtueller Computer, aber weder den Zugriff darauf, noch auf deren verbundenen virtuellen Netzwerke oder Speicherkonten. |
> | **Id** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Autorisierungen |
> | Microsoft.Compute/availabilitySets/* | Erstellen und Verwalten von Compute-Verfügbarkeitsgruppen |
> | Microsoft.Compute/locations/* | Erstellen und Verwalten von Compute-Speicherorten |
> | Microsoft.Compute/virtualMachines/* | Erstellen und Verwalten von virtuellen Computern |
> | Microsoft.Compute/virtualMachineScaleSets | Erstellen und Verwalten von Skalierungsgruppen für virtuelle Computer |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Verknüpft einen Back-End-Adresspool für ein Application Gateway. Nicht warnbar. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Verknüpft einen Back-End-Adresspool für den Lastenausgleich. Nicht warnbar. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Verknüpft einen NAT-Pool für eingehenden Datenverkehr für den Lastenausgleich. Nicht warnbar. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Verknüpft eine NAT-Regel für eingehenden Datenverkehr für den Lastenausgleich. Nicht warnbar. |
> | Microsoft.Network/loadBalancers/probes/join/action | Ermöglicht die Verwendung von Prüfpunkten eines Lastenausgleichs. Beispielsweise kann mit dieser Berechtigung die healthProbe-Eigenschaft einer VM-Skalierungsgruppe auf den Prüfpunkt verweisen. Nicht warnbar. |
> | Microsoft.Network/loadBalancers/read | Ruft eine Lastenausgleichsdefinition ab. |
> | Microsoft.Network/locations/* | Erstellen und Verwalten von Netzwerkspeicherorten |
> | Microsoft.Network/networkInterfaces/* | Erstellen und Verwalten von Netzwerkschnittstellen |
> | Microsoft.Network/networkSecurityGroups/join/action | Verknüpft eine Netzwerksicherheitsgruppe. Nicht warnbar. |
> | Microsoft.Network/networkSecurityGroups/read | Ruft eine Netzwerksicherheitsgruppen-Definition ab. |
> | Microsoft.Network/publicIPAddresses/join/action | Verknüpft eine öffentliche IP-Adresse. Nicht warnbar. |
> | Microsoft.Network/publicIPAddresses/read | Ruft eine Definition für eine öffentliche IP-Adresse ab. |
> | Microsoft.Network/virtualNetworks/read | Dient zum Abrufen der Definition des virtuellen Netzwerks. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Verknüpft ein virtuelles Netzwerk. Nicht warnbar. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Erstellt einen beabsichtigten Sicherungsschutz. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Gibt Objektdetails des geschützten Elements zurück. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Dient zum Erstellen eines geschützten Elements für die Sicherung. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Gibt alle Schutzrichtlinien zurück. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Erstellt Schutzrichtlinien. |
> | Microsoft.RecoveryServices/Vaults/read | Der Vorgang „Tresor abrufen“ ruft ein Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Gibt Nutzungsdetails für einen Recovery Services-Tresor zurück. |
> | Microsoft.RecoveryServices/Vaults/write | Der Vorgang „Tresor erstellen“ erstellt eine Azure-Ressource vom Typ „Tresor“. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Gibt die Zugriffsschlüssel für das angegebene Speicherkonto zurück. |
> | Microsoft.Storage/storageAccounts/read | Gibt die Liste mit Speicherkonten zurück oder ruft die Eigenschaften für das angegebene Speicherkonto ab. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="virtual-machine-user-login"></a>VM-Benutzeranmeldung
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Anzeigen von virtuellen Computern im Portal und Anmelden als regulärer Benutzer. |
> | **Id** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Aktionen** |  |
> | Microsoft.Network/publicIPAddresses/read | Ruft eine Definition für eine öffentliche IP-Adresse ab. |
> | Microsoft.Network/virtualNetworks/read | Dient zum Abrufen der Definition des virtuellen Netzwerks. |
> | Microsoft.Network/loadBalancers/read | Ruft eine Lastenausgleichsdefinition ab. |
> | Microsoft.Network/networkInterfaces/read | Ruft eine Netzwerkschnittstellendefinition ab.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Hiermit melden Sie sich bei einem virtuellen Computer als normaler Benutzer an. |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="web-plan-contributor"></a>Mitwirkender von Webplan
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten der Webpläne für Websites, nicht aber den Zugriff darauf. |
> | **Id** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Autorisierungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | Microsoft.Web/serverFarms/* | Erstellen und Verwalten von Serverfarmen |
> | Microsoft.Web/hostingEnvironments/Join/Action | Beitreten zu einer App Service-Umgebung |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="website-contributor"></a>Mitwirkender von Website
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschreibung** | Ermöglicht Ihnen das Verwalten von Websites (nicht der Webpläne), nicht aber den Zugriff darauf. |
> | **Id** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Aktionen** |  |
> | Microsoft.Authorization/*/read | Lesen von Autorisierungen |
> | Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
> | Microsoft.Insights/components/* | Erstellen und Verwalten von Insights-Komponenten |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
> | Microsoft.Web/certificates/* | Erstellen und Verwalten von Websitezertifikaten |
> | Microsoft.Web/listSitesAssignedToHostName/read | Dient zum Abrufen der Namen von Websites, die dem Hostnamen zugewiesen sind. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Dient zum Abrufen der Eigenschaften für einen App Service-Plan. |
> | Microsoft.Web/sites/* | Erstellen und Verwalten von Websites (die Erstellung von Websites erfordert auch Schreibberechtigungen für den zugehörigen App Service-Plan) |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

## <a name="next-steps"></a>Nächste Schritte

- [Benutzerdefinierte Rollen für Azure-Ressourcen](custom-roles.md)
- [Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC und dem Azure-Portal](role-assignments-portal.md)
- [Berechtigungen in Azure Security Center](../security-center/security-center-permissions.md)

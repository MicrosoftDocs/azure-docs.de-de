---
title: Übersicht über die Sicherheit in Azure Data Lake Storage Gen1 | Microsoft Docs
description: Erfahren Sie mehr über die Sicherheitsfunktionen von Azure Data Lake Storage Gen1, einschließlich Authentifizierung, Autorisierung, Netzwerkisolation, Datenschutz und Überwachung.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: twooley
ms.openlocfilehash: 240018381a3139a6378141d78514e43ae469de5d
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "92146298"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Sicherheit in Azure Data Lake Storage Gen1

Viele Unternehmen nutzen Big-Data-Analysen zur Gewinnung von Unternehmenseinblicken, um fundierte Entscheidungen treffen zu können. Ein Unternehmen verfügt unter Umständen über eine komplexe und regulierte Umgebung mit einer wachsenden Zahl von unterschiedlichen Benutzern. Unternehmen müssen sicherstellen, dass wichtige Geschäftsdaten sicherer gespeichert werden und dass einzelnen Benutzern die richtige Zugriffsebene gewährt wird. Azure Data Lake Storage Gen1 ist so konzipiert, dass diese Sicherheitsanforderungen besser erfüllt werden können. In diesem Artikel werden die Sicherheitsfunktionen von Data Lake Storage Gen1 beschrieben, z.B. die folgenden Aspekte:

* Authentifizierung
* Authorization
* Netzwerkisolation
* Schutz von Daten
* Überwachung

## <a name="authentication-and-identity-management"></a>Authentifizierung und Identitätsverwaltung

Die Authentifizierung ist der Prozess, bei dem die Identität des Benutzers überprüft wird, wenn der Benutzer mit Data Lake Storage Gen1 oder einem anderen Dienst interagiert, für den eine Verbindung mit Data Lake Storage Gen1 hergestellt wird. Für die Identitätsverwaltung und Authentifizierung nutzt Data Lake Storage Gen1 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Hierbei handelt es sich um eine umfassende Cloudlösung für die Identitäts- und Zugriffsverwaltung, mit der die Verwaltung von Benutzern und Gruppen vereinfacht wird.

Jedes Azure-Abonnement kann mit einer Instanz von Azure Active Directory verknüpft werden. Nur Benutzer- und Dienstidentitäten, die in Ihrem Azure Active Directory-Dienst definiert sind, können auf Ihr Data Lake Storage Gen1-Konto zugreifen. Hierfür werden das Azure-Portal, Befehlszeilentools oder Clientanwendungen verwendet, die von Ihrer Organisation mit dem Azure Data Lake Storage Gen1 SDK erstellt werden. Die Nutzung von Azure Active Directory als zentrales Verfahren für die Zugriffssteuerung hat die folgenden wichtigen Vorteile:

* Vereinfachte Verwaltung des Identitätslebenszyklus: Die Identität eines Benutzers oder Diensts (Dienstprinzipalidentität) kann schnell erstellt und widerrufen werden, indem das Konto im Verzeichnis einfach gelöscht oder deaktiviert wird.
* Multi-Factor Authentication: Per [Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) wird eine zusätzliche Sicherheitsebene für die Benutzeranmeldung und für Transaktionen geschaffen.
* Authentifizierung von jedem Client über ein geöffnetes Standardprotokoll, z.B. OAuth oder OpenID.
* Verbund mit Unternehmensverzeichnisdiensten und Cloudidentitätsanbietern.

## <a name="authorization-and-access-control"></a>Autorisierung und Zugriffssteuerung

Nachdem ein Benutzer über Azure Active Directory authentifiziert wurde, damit der Benutzer auf Data Lake Storage Gen1 zugreifen kann, werden von der Autorisierung die Zugriffsberechtigungen für Data Lake Storage Gen1 gesteuert. Data Lake Storage Gen1 trennt die Autorisierung von konto- und datenbezogenen Aktivitäten auf folgende Weise:

* [Rollenbasierte Zugriffssteuerung von Azure (Role-Based Access Control, RBAC)](../role-based-access-control/overview.md) zur Kontoverwaltung
* POSIX-ACL für den Zugriff auf die Daten im Store

### <a name="azure-rbac-for-account-management"></a>Azure RBAC für die Kontoverwaltung

Für Data Lake Storage Gen1 werden standardmäßig vier grundlegende Rollen definiert. Diese Rollen ermöglichen unterschiedliche Vorgänge für ein Data Lake Storage Gen1-Konto über das Azure-Portal, PowerShell-Cmdlets und REST-APIs. Mit den Rollen „Besitzer“ und „Mitwirkender“ können verschiedene Verwaltungsfunktionen für das Konto durchgeführt werden. Sie können die Rolle „Leser“ Benutzern zuweisen, die nur Kontoverwaltungsdaten anzeigen.

![Azure-Rollen](./media/data-lake-store-security-overview/rbac-roles.png "Azure-Rollen")

Beachten Sie Folgendes: Obwohl Rollen für die Kontoverwaltung zugewiesen werden, wirken sich einige Rollen auf den Datenzugriff aus. Sie müssen ACLs verwenden, um den Zugriff auf Vorgänge zu steuern, die ein Benutzer im Dateisystem durchführen kann. Die folgende Tabelle enthält eine Zusammenfassung der Verwaltungsrechte und Datenzugriffsrechte für die Standardrollen.

| Rollen | Verwaltungsrechte | Datenzugriffsrechte | Erklärung |
| --- | --- | --- | --- |
| Keine Rolle zugewiesen |Keine |Per ACL gesteuert |Der Benutzer kann nicht das Azure-Portal oder die Azure PowerShell-Cmdlets verwenden, um in Data Lake Storage Gen1 zu navigieren. Der Benutzer kann nur die Befehlszeilentools verwenden. |
| Besitzer |All |All |Die Rolle „Besitzer“ ist ein Superuser. Mit dieser Rolle können alle Bereiche verwaltet werden, und es besteht Vollzugriff auf die Daten. |
| Leser |Schreibgeschützt |Per ACL gesteuert |Mit der Rolle „Leser“ können alle Daten zur Kontoverwaltung angezeigt werden, z.B. welcher Benutzer welcher Rolle zugewiesen ist. Änderungen können mit der Rolle „Leser“ aber nicht vorgenommen werden. |
| Mitwirkender |Alle Rechte mit Ausnahme de Hinzufügens und Entfernens von Rollen |Per ACL gesteuert |Mit der Rolle „Mitwirkender“ können einige Aspekte eines Kontos verwaltet werden, z.B. Bereitstellungen und die Erstellung und Verwaltung von Warnungen. Das Hinzufügen oder Entfernen von Rollen ist mit der Rolle „Mitwirkender“ nicht möglich. |
| Benutzerzugriffsadministrator |Rollen hinzufügen und entfernen |Per ACL gesteuert |Mit der Rolle „Benutzerzugriffsadministrator“ können Sie den Benutzerzugriff auf Konten verwalten. |

Eine Anleitung finden Sie unter [Zuweisen von Benutzern oder Sicherheitsgruppen zu Data Lake Storage Gen1-Konten](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Verwenden von ACLs für Vorgänge in Dateisystemen

Data Lake Storage Gen1 ist ein hierarchisches Dateisystem, wie beispielsweise Hadoop Distributed File System (HDFS), und unterstützt [POSIX-ACLs](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Es steuert die Berechtigungen Lesen, Schreiben und Ausführen (read (r), write (w) und execute (x)) für Ressourcen für die Rolle „Besitzer“, für die Gruppe „Besitzer“ und für andere Benutzer und Gruppen. In Data Lake Storage Gen1 können ACLs für Stammordner, Unterordner und einzelne Dateien aktiviert werden. Weitere Informationen zur Funktionsweise von ACLs im Kontext von Data Lake Storage Gen1 finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen1](data-lake-store-access-control.md).

Es wird empfohlen, mit [Sicherheitsgruppen](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)ACLs für mehrere Benutzer zu definieren. Fügen Sie Benutzer einer Sicherheitsgruppe hinzu, und weisen Sie dieser Sicherheitsgruppe dann die ACLs für eine Datei oder einen Ordner zu. Dies ist hilfreich, wenn Sie zugewiesene Berechtigungen angeben möchten, weil bei zugewiesenen Berechtigungen eine Beschränkung auf maximal 28 Einträge besteht. Weitere Informationen dazu, wie Sie in Data Lake Storage Gen1 gespeicherte Daten mit Azure Active Directory-Sicherheitsgruppen besser schützen können, finden Sie unter [Zuweisen von Benutzern oder Sicherheitsgruppen als Zugriffssteuerungslisten zum Data Lake Storage Gen1-Dateisystem](data-lake-store-secure-data.md#filepermissions).

![Auflisten der Zugriffsberechtigungen](./media/data-lake-store-security-overview/adl.acl.2.png "Auflisten der Zugriffsberechtigungen")

## <a name="network-isolation"></a>Netzwerkisolation

Verwenden Sie Data Lake Storage Gen1, damit Sie den Zugriff auf Ihren Datenspeicher auf Netzwerkebene besser steuern können. Sie können Firewalls einrichten und einen IP-Adressbereich für Ihre vertrauenswürdigen Clients definieren. Mit einem IP-Adressbereich können nur Clients, die über eine IP-Adresse innerhalb des definierten Bereichs verfügen, eine Verbindung mit Data Lake Storage Gen1 herstellen.

![Firewalleinstellungen und IP-Zugriff](./media/data-lake-store-security-overview/firewall-ip-access.png "Firewalleinstellungen und IP-Zugriff")

Azure Virtual Networks (VNet) unterstützen Diensttags für Data Lake Gen1. Ein Diensttag steht für eine Gruppe von IP-Adresspräfixen eines bestimmten Azure-Diensts. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern. Weitere Informationen finden Sie unter [Azure-Diensttags – Übersicht](../virtual-network/service-tags-overview.md).

## <a name="data-protection"></a>Schutz von Daten

Data Lake Storage Gen1 schützt Ihre Daten während des gesamten Lebenszyklus. Für Daten im Übergang verwendet Data Lake Storage Gen1 das branchenübliche TLS-Protokoll 2.1 (Transport Layer Security), um Daten über das Netzwerk zu schützen.

![Verschlüsselung in Data Lake Storage Gen1](./media/data-lake-store-security-overview/adls-encryption.png "Verschlüsselung in Data Lake Storage Gen1")

Data Lake Storage Gen1 bietet auch eine Verschlüsselung für Daten, die im Konto gespeichert sind. Sie können auswählen, ob Ihre Daten verschlüsselt werden sollen oder nicht. Wenn Sie sich für die Verschlüsselung entscheiden, werden in Data Lake Storage Gen1 gespeicherte Daten vor dem Speichern auf persistenten Medien verschlüsselt. In einem solchen Fall verschlüsselt Data Lake Storage Gen1 automatisch Daten vor der persistenten Speicherung und entschlüsselt Daten vor dem Abruf, sodass der Vorgang für den Client, der auf die Daten zugreift, vollständig transparent ist. Auf der Clientseite ist zum Verschlüsseln/Entschlüsseln von Daten keine Änderung des Codes erforderlich.

Für die Schlüsselverwaltung bietet Data Lake Storage Gen1 zwei Modi zur Verwaltung Ihrer Hauptverschlüsselungsschlüssel (Master Encryption Keys, MEKs), die zum Entschlüsseln von in Data Lake Storage Gen1 gespeicherten Daten erforderlich sind. Sie können die MEKs entweder von Data Lake Storage Gen1 für sich verwalten lassen, oder Sie verwenden das Azure Key Vault-Konto und bleiben im Besitz der MEKs. Beim Erstellen eines Data Lake Storage Gen1-Kontos geben Sie die Art der Schlüsselverwaltung an. Weitere Informationen zum Bereitstellen einer verschlüsselungsbezogenen Konfiguration finden Sie unter [Erste Schritte mit Azure Data Lake Storage Gen1 im Azure-Portal](data-lake-store-get-started-portal.md).

## <a name="activity-and-diagnostic-logs"></a>Aktivitäts- und Diagnoseprotokolle

Sie können Aktivitäts- oder Diagnoseprotokolle verwenden. Dies richtet sich jeweils danach, ob Sie Protokolle für kontoverwaltungsbezogene Aktivitäten oder datenbezogene Aktivitäten benötigen.

* Für kontoverwaltungsbezogene Aktivitäten werden Azure Resource Manager-APIs verwendet. Sie tauchen im Azure-Portal in Form von Aktivitätsprotokollen auf.
* Für datenbezogene Aktivitäten werden die WebHDFS-REST-APIs verwendet. Sie tauchen im Azure-Portal in Form von Diagnoseprotokollen auf.

### <a name="activity-log"></a>Aktivitätsprotokoll

Zur Einhaltung von Vorschriften benötigt eine Organisation unter Umständen geeignete Überwachungspfade von Kontoverwaltungsaktivitäten, falls bestimmte Vorfälle genauer untersucht werden müssen. Data Lake Storage Gen1 verfügt über integrierte Überwachungsfunktionen, und alle Aktivitäten der Kontoverwaltung werden protokolliert.

Zeigen Sie für Überwachungspfade der Kontoverwaltung die Spalten an, die Sie protokollieren möchten, und wählen Sie sie aus. Sie können die Aktivitätsprotokolle auch in Azure Storage exportieren.

![Aktivitätsprotokoll](./media/data-lake-store-security-overview/activity-logs.png "Aktivitätsprotokoll")

Weitere Informationen zur Verwendung von Aktivitätsprotokollen finden Sie unter [Anzeigen von Aktivitätsprotokollen, um Aktionen an Ressourcen zu überwachen](../azure-resource-manager/management/view-activity-logs.md).

### <a name="diagnostics-logs"></a>Diagnoseprotokolle

Sie können die Überwachung des Datenzugriffs und die Diagnoseprotokollierung im Azure-Portal aktivieren und die Protokolle an ein Azure Blob Storage-Konto, einen Event Hub oder Azure Monitor-Protokolle senden.

![Diagnoseprotokolle](./media/data-lake-store-security-overview/diagnostic-logs.png "Diagnoseprotokolle")

Weitere Informationen zum Arbeiten mit Diagnoseprotokollen in Data Lake Storage Gen1 finden Sie unter [Zugreifen auf Diagnoseprotokolle für Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Zusammenfassung

Unternehmenskunden wünschen sich eine Cloudplattform für die Datenanalyse, die sicher und einfach zu verwenden ist. Data Lake Storage Gen1 wurde für die bessere Erfüllung dieser Anforderungen über Identitätsverwaltung und Authentifizierung mithilfe der Azure Active Directory-Integration, ACL-basierter Autorisierung, Netzwerkisolation, Verschlüsselung von Daten während der Übertragung und im ruhenden Zustand sowie Überwachung konzipiert.

Wenn Sie neue Features für Data Lake Storage Gen1 wünschen, können Sie uns Ihr Feedback im [UserVoice-Forum zu Data Lake Storage Gen1](https://feedback.azure.com/forums/327234-data-lake)senden.

## <a name="see-also"></a>Weitere Informationen

* [Übersicht über Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Erste Schritte mit Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Schützen von Daten in Data Lake Storage Gen1](data-lake-store-secure-data.md)
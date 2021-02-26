---
title: Delegierung und Rollen in der Berechtigungsverwaltung – Azure AD
description: Erfahren Sie, wie Sie die Zugriffskontrolle von IT-Administratoren an Abteilungsleiter und Projektmanager delegieren, damit diese den Zugriff selbst verwalten können.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8e6c13600cb5940351d31b54af403584cc68a5f
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515647"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Delegierung und Rollen in der Azure AD-Berechtigungsverwaltung

Standardmäßig können globale Administratoren und Benutzeradministratoren alle Aspekte der Azure AD-Berechtigungsverwaltung erstellen und verwalten. Die Benutzer in diesen Rollen kennen jedoch möglicherweise nicht alle Situationen, in denen Zugriffspakete erforderlich sind. In der Regel handelt es sich um Benutzer in den entsprechenden Abteilungen, Teams oder Projekten, die wissen, mit wem sie zusammenarbeiten, welche Ressourcen sie verwenden und wie lange. Anstatt Nicht-Administratoren uneingeschränkte Berechtigungen zu gewähren, können Sie Benutzern die Mindestberechtigungen erteilen, die sie für die Ausführung ihrer Arbeit benötigen, und vermeiden, widersprüchliche oder unangemessene Zugriffsrechte zu erstellen.

Dieses Video bietet eine Übersicht darüber, wie die Zugriffskontrolle von IT-Administratoren an Benutzer delegiert wird, die keine Administratoren sind.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Delegierungsbeispiel

Um zu verstehen, wie Sie die Zugriffskontrolle in der Berechtigungsverwaltung delegieren können, verwenden wir zur Veranschaulichung ein Beispiel. Nehmen wir an, dass Ihre Organisation über die folgenden Administratoren und Abteilungsleiter verfügt.

![Delegieren von IT-Administratoren an Abteilungsleiter](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

Als IT-Administratorin hat Hana Kontakte in jeder Abteilung (Mamta in der Marketingabteilung, Mark in der Finanzabteilung und Joe in der Rechtsabteilung), die für die Ressourcen und unternehmenskritischen Inhalte der jeweiligen Abteilung verantwortlich sind.

Mit der Berechtigungsverwaltung können Sie die Zugriffskontrolle an diese Nicht-Administratoren delegieren, weil es sich dabei um die Personen handelt, die wissen, welche Benutzer wie lange und auf welche Ressourcen Zugriff benötigen. Dadurch wird sichergestellt, dass die richtigen Personen den Zugriff für die jeweiligen Abteilungen verwalten.

Nachfolgend wird eine Möglichkeit aufgezeigt, wie Hana die Zugriffskontrolle an die Marketing-, Finanz- und Rechtsabteilung delegieren könnte.

1. Hana erstellt eine neue Azure AD-Sicherheitsgruppe und fügt Mamta, Mark und Joe als Mitglieder der Gruppe hinzu.

1. Hana fügt diese Gruppe der Rolle „Katalogersteller“ hinzu.

    Mamta, Mark und Joe können jetzt Kataloge für ihre Abteilungen erstellen, Ressourcen hinzufügen, die ihre Abteilungen benötigen, und weitere Delegierungen innerhalb des Katalogs durchführen.

    Beachten Sie, dass Mamta, Mark und Joe die Kataloge der jeweils anderen nicht anzeigen können.

1. Mamta erstellt einen Katalog **Marketing**, bei dem es sich um einen Container von Ressourcen handelt.

1. Mamta fügt diesem Katalog die Ressourcen hinzu, die ihre Marketingabteilung besitzt.

1. Mamta kann weitere Mitarbeiter ihrer Abteilung als Katalogbesitzer für diesen Katalog hinzufügen. Dadurch können die Aufgaben der Katalogverwaltung gemeinsam wahrgenommen werden.

1. Mamta kann die Erstellung und Verwaltung von Zugriffspaketen im Katalog „Marketing“ an Projektmanager in der Marketingabteilung weiter delegieren. Hierzu weist sie den entsprechenden Personen die Rolle „Zugriffspaket-Manager“ zu. Ein Zugriffspaket-Manager kann Zugriffspakete erstellen und verwalten. 

Im folgenden Diagramm sind Kataloge mit Ressourcen für die Marketing-, Finanz- und Rechtsabteilung dargestellt. Mit diesen Katalogen können Projektmanager Zugriffspakete für ihre Teams oder Projekte erstellen.

![Delegierungsbeispiel für die Berechtigungsverwaltung](./media/entitlement-management-delegate/elm-delegate.png)

Nach der Delegierung verfügt die Marketingabteilung möglicherweise über ähnliche Rollen wie in der folgenden Tabelle.

| Benutzer | Aufgabengebiet | Azure AD-Rolle | Berechtigungsverwaltungsrolle |
| --- | --- | --- | --- |
| Hana | IT-Administrator | Globaler Administrator oder Benutzeradministrator |  |
| Mamta | Marketing Managerin | Benutzer | Katalogersteller und Katalogbesitzer |
| Bernd | Marketingleiter | Benutzer | Katalogbesitzer |
| Jessica | Projektmanagerin Marketing | Benutzer | Zugriffspaketmanager |

## <a name="entitlement-management-roles"></a>Berechtigungsverwaltungsrollen

Die Berechtigungsverwaltung hat die folgenden Rollen, die für die Berechtigungsverwaltung spezifisch sind.

| Berechtigungsverwaltungsrolle | BESCHREIBUNG |
| --- | --- |
| Katalogersteller | Erstellen und Verwalten von Katalogen. In der Regel ein IT-Administrator, der kein globaler Administrator ist, oder ein Ressourcenbesitzer für eine Sammlung von Ressourcen. Die Person, die einen Katalog erstellt, ist automatisch der erste Besitzer des Katalogs und kann zusätzliche Katalogbesitzer hinzufügen. Ein Katalogersteller kann keine Kataloge verwalten, die ihm nicht gehören, und einem Katalog keine Ressourcen hinzufügen, die er nicht besitzt. Wenn der Katalogersteller einen anderen Katalog verwalten oder Ressourcen hinzufügen muss, die er nicht besitzt, kann er sich als Mitbesitzer des betreffenden Katalogs oder der betreffenden Ressource festlegen lassen. |
| Katalogbesitzer | Bearbeiten und Verwalten von vorhandenen Katalogen. In der Regel ein IT-Administrator oder Ressourcenbesitzer bzw. ein Benutzer, den der Katalogbesitzer bestimmt hat. |
| Zugriffspaketmanager | Bearbeiten und Verwalten aller in einem Katalog vorhandenen Zugriffspakete. |
| Zugriffspaketzuweisungsmanager | Bearbeiten und Verwalten aller vorhandenen Zugriffspaketzuweisungen. |

Darüber hinaus haben auch eine designierte genehmigende Person und ein Antragsteller eines Zugriffspakets Rechte, obwohl es sich nicht um Rollen handelt.

| Right | BESCHREIBUNG |
| --- | --- |
| Genehmigende Person | Autorisiert durch eine Richtlinie zur Genehmigung oder Ablehnung von Anforderungen zum Zugriff auf Pakete, obwohl sie die Zugriffspaketdefinitionen nicht ändern können. |
| Anforderer | Autorisiert durch eine Richtlinie eines Zugriffspakets zum Anfordern dieses Zugriffspakets. |

In der folgenden Tabelle sind die Aufgaben aufgeführt, die von den Berechtigungsverwaltungsrollen ausgeführt werden können.

| Aufgabe | Admin | Katalogersteller | Katalogbesitzer | Zugriffspaketmanager | Zugriffspaketzuweisungsmanager |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Delegieren an einen Katalogersteller](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |  |
| [Hinzufügen einer verbundenen Organisation](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |  |
| [Erstellen eines neuen Katalogs](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |  |
| [Hinzufügen einer Ressource zu einem Katalog](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Hinzufügen eines Katalogbesitzers](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Bearbeiten eines Katalogs](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Löschen eines Katalogs](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Delegieren an einen Zugriffspaket-Manager](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Entfernen eines Zugriffspaket-Managers](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Erstellen eines neuen Zugriffspakets in einem Katalog](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Ändern von Ressourcenrollen für ein Zugriffspaket](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Erstellen und Bearbeiten von Richtlinien](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Direktes Zuweisen eines Benutzers zu einem Zugriffspaket](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Direktes Entfernen eines Benutzers aus einem Zugriffspaket](entitlement-management-access-package-assignments.md#remove-an-assignment) | :heavy_check_mark:  |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Anzeigen, wem ein Zugriffspaket zugewiesen ist](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Anzeigen der Anforderungen eines Zugriffspakets](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [Anzeigen der Fehler bei der Übermittlung einer Anforderung](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Erneutes Verarbeiten einer Anforderung](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [Abbrechen einer ausstehenden Anforderung](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Ausblenden eines Zugriffspakets](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Löschen eines Zugriffspakets](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Erforderliche Rollen, um einem Katalog Ressourcen hinzuzufügen

Ein globaler Administrator kann jede Gruppe (in der Cloud erstellte Sicherheitsgruppen oder in der Cloud erstellte Microsoft 365-Gruppen), Anwendung oder SharePoint Online-Website zu einem Katalog hinzufügen oder daraus entfernen. Ein Benutzeradministrator kann eine beliebige Gruppe oder Anwendung in einem Katalog hinzufügen oder entfernen (ausgenommen hiervon sind Gruppen, die so konfiguriert sind, dass sie einer Verzeichnisrolle zugewiesen werden können). Beachten Sie, dass ein Benutzeradministrator Zugriffspakete in einem Katalog mit Gruppen verwalten kann, die so konfiguriert sind, dass sie einer Verzeichnisrolle zugewiesen werden können.

Damit ein Benutzer, der kein globaler Administrator oder Benutzeradministrator ist, Gruppen, Anwendungen oder SharePoint-Online-Websites einem Katalog hinzufügen kann, muss dieser Benutzer über die erforderliche Azure AD-Verzeichnisrolle *und* die Berechtigungsverwaltungsrolle für Katalogbesitzer verfügen. Die folgende Tabelle enthält die Rollenkombinationen, die erforderlich sind, um einem Katalog Ressourcen hinzuzufügen. Um Ressourcen aus einem Katalog zu entfernen, müssen Sie über die gleichen Rollen verfügen.

| Azure AD-Verzeichnisrolle | Berechtigungsverwaltungsrolle | Kann Sicherheitsgruppe hinzufügen | Kann Microsoft 365-Gruppe hinzufügen | Kann App hinzufügen | Kann SharePoint Online-Website hinzufügen |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Globaler Administrator](../roles/permissions-reference.md) | – |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Benutzeradministrator](../roles/permissions-reference.md) | – |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune-Administrator](../roles/permissions-reference.md) | Katalogbesitzer | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange-Administrator](../roles/permissions-reference.md) | Katalogbesitzer |  | :heavy_check_mark: |  |  |
| [Teams-Dienstadministrator](../roles/permissions-reference.md) | Katalogbesitzer |  | :heavy_check_mark: |  |  |
| [SharePoint-Administrator](../roles/permissions-reference.md) | Katalogbesitzer |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Anwendungsadministrator](../roles/permissions-reference.md) | Katalogbesitzer |  |  | :heavy_check_mark: |  |
| [Cloudanwendungsadministrator](../roles/permissions-reference.md) | Katalogbesitzer |  |  | :heavy_check_mark: |  |
| Benutzer | Katalogbesitzer | Nur, wenn Gruppenbesitzer | Nur, wenn Gruppenbesitzer | Nur, wenn App-Besitzer |  |

Wenn Sie die Rolle mit den geringsten Rechten für eine Aufgabe ermitteln möchten, können Sie auch [Administratorrollen nach Administratoraufgabe in Azure Active Directory](../roles/delegate-by-task.md#entitlement-management) zurate ziehen.

## <a name="next-steps"></a>Nächste Schritte

- [Delegieren der Zugriffskontrolle an Katalogersteller](entitlement-management-delegate-catalog.md)
- [Erstellen und Verwalten eines Katalogs von Ressourcen](entitlement-management-catalog-create.md)

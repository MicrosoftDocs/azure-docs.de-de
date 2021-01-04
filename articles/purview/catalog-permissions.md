---
title: Katalogberechtigungen (Vorschauversion)
description: Dieser Artikel enthält eine Übersicht über die Konfiguration der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) in Azure Purview.
author: yarong
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: e87c9efc910d08307d40d42e58f8272a01902a41
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550460"
---
# <a name="role-based-access-control-in-azure-purviews-data-plane"></a>Rollenbasierte Zugriffssteuerung auf der Datenebene von Azure Purview

In diesem Artikel wird beschrieben, wie die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) auf der [Datenebene](../azure-resource-manager/management/control-plane-and-data-plane.md#data-plane) von Azure Purview implementiert wird.

> [!IMPORTANT]
> Der Prinzipal, der ein Purview-Konto erstellt hat, erhält automatisch alle Datenebenenberechtigungen. Dies gilt unabhängig davon, welche Datenebenenrollen für den Prinzipal ggf. bereits vorhanden sind. Damit andere Benutzer Schritte in Azure Purview ausführen können, müssen sie mindestens über eine der vordefinierten Datenebenenrollen verfügen.

## <a name="azure-purviews-pre-defined-data-plane-roles"></a>Vordefinierte Datenebenenrollen von Azure Purview

In Azure Purview wird eine Gruppe mit vordefinierten Datenebenenrollen definiert, mit denen gesteuert werden kann, worauf Benutzer in Azure Purview zugreifen können. Die folgenden Rollen sind vorhanden:

* **Rolle „Datenleseberechtigter für Purview“** : Verfügt über Zugriff auf das Purview-Portal und kann den gesamten Inhalt in Azure Purview lesen, mit Ausnahme von Scanbindungen.
* **Rolle „Azure Purview-Datenkurator“** : Verfügt über Zugriff auf das Purview-Portal und kann den gesamten Inhalt in Azure Purview lesen (mit Ausnahme von Scanbindungen), Informationen zu Ressourcen sowie Klassifizierungsdefinitionen und Glossarbegriffe bearbeiten und Klassifizierungen und Glossarbegriffe auf Ressourcen anwenden.
* **Rolle „Datenquellenadministrator für Purview“** : Verfügt nicht über Zugriff auf das Purview-Portal (der Benutzer muss auch über die Rolle „Datenleser“ oder „Datenkurator“ verfügen) und kann alle Aspekte der Datenüberprüfung in Azure Purview verwalten, verfügt aber weder über Schreib- noch über Lesezugriff auf den Inhalt in Azure Purview, der über das Überprüfen hinausgeht.

## <a name="understanding-how-to-use-azure-purviews-data-plane-roles"></a>Grundlegendes zur Verwendung der Datenebenenrollen von Azure Purview

Bei der Erstellung eines Azure Purview-Kontos wird der Ersteller so behandelt, als ob er sowohl über die Rolle „Datenkurator für Purview“ als auch die Rolle „Datenquellenadministrator für Purview“ verfügt. Im Rollenspeicher ist der Kontoersteller diesen Rollen jedoch nicht zugewiesen. Azure Purview erkennt, dass der Prinzipal der Ersteller des Kontos ist, und stellt diese Funktionen auf der Grundlage ihrer Identität zur Verfügung.

Alle anderen Benutzer können das Azure Purview-Konto nur verwenden, wenn sie mindestens einer dieser Rollen zugewiesen werden. Dies bedeutet Folgendes: Nach der Erstellung eines Azure Purview-Kontos kann ausschließlich der Ersteller auf das Konto zugreifen und dessen APIs verwenden, bis die Benutzer mindestens einer der vorab definierten Rollen zugewiesen wurden.

Beachten Sie, dass die Rolle „Datenquellenadministrator für Purview“ zwei unterstützte Szenarien aufweist. Das erste Szenario ist für Benutzer bestimmt, die bereits Datenleseberechtigter oder Datenkurator für Purview sind und auch die Berechtigung zum Erstellen von Überprüfungen benötigen. Diese Benutzer müssen über zwei Rollen verfügen, nämlich mindestens über eine der beiden Rollen „Datenleseberechtigter für Purview“ und „Datenkurator für Purview“ sowie zusätzlich über die Rolle „Datenquellenadministrator für Purview“.

Das andere Szenario für „Datenquellenadministrator für Purview“ ist für programmgesteuerte Prozesse bestimmt, z. B. Dienstprinzipale, für die das Einrichten und Überwachen von Überprüfungen möglich sein soll, aber kein Zugriff auf die Daten des Katalogs.

Dieses Szenario kann implementiert werden, indem dem Dienstprinzipal die Rolle „Datenquellenadministrator für Purview“ zugewiesen wird, ohne dass er über eine der beiden anderen Rollen verfügt. Der Prinzipal hat keinen Zugriff auf das Purview-Portal. Dies stellt aber kein Problem dar, weil es sich um einen programmgesteuerten Prinzipal handelt, der nur über APIs kommuniziert.

## <a name="putting-users-into-roles"></a>Zuweisen von Rollen für Benutzer

Als Erstes müssen nach der Erstellung eines Azure Purview-Kontos Benutzern die Rollen zugewiesen werden.

Die Rollenzuweisung wird über die [rollenbasierte Zugriffssteuerung (RBAC) von Azure](../role-based-access-control/overview.md) verwaltet.

Rollen für Benutzer können nur über zwei integrierte Rollen der Steuerungsebene in Azure zugewiesen werden: entweder „Besitzer“ oder „Benutzerzugriffsadministrator“. Damit Benutzer über diese Rollen für Azure Purview verfügen, müssen sie sich also entweder an einen Besitzer oder Benutzerzugriffsadministrator wenden oder selbst eine dieser Rollen innehaben.

### <a name="an-example-of-assigning-someone-to-a-role"></a>Beispiel für die Zuweisung eines Benutzers zu einer Rolle

1. Navigieren Sie zu https://portal.azure.com und dann zu Ihrem Azure Purview-Konto.
1. Klicken Sie links auf „Zugriffssteuerung (IAM)“.
1. Befolgen Sie dann [diese allgemeine Anleitung](../role-based-access-control/quickstart-assign-role-user-portal.md#create-a-resource-group).

## <a name="role-definitions-and-actions"></a>Rollendefinitionen und -aktionen

Eine Rolle ist als eine Sammlung mit Aktionen definiert. Weitere Informationen zum Definieren von Rollen finden Sie [hier](../role-based-access-control/role-definitions.md). Die Rollendefinitionen für die Azure Purview-Rollen finden Sie [hier](../role-based-access-control/built-in-roles.md).

## <a name="getting-added-to-a-data-plane-role-in-an-azure-purview-account"></a>Erreichen der Hinzufügung zu einer Datenebenenrolle in einem Azure Purview-Konto

Wenn Sie Zugriff auf ein Azure Purview-Konto erhalten möchten, damit Sie das Studio-Feature nutzen oder die zugehörigen APIs aufrufen können, müssen Sie einer Datenebenenrolle von Azure Purview hinzugefügt werden. Dies kann nur von Personen durchgeführt werden, die ein Besitzer oder Benutzerzugriffsadministrator des Azure Purview-Kontos sind. Für die meisten Benutzer ist der nächste Schritt dann die Kontaktaufnahme mit einem lokalen Administrator, der die richtigen Personen für die Erteilung des Zugriffs nennen kann.

Benutzer, die Zugriff auf das [Azure-Portal](https://portal.azure.com) des Unternehmens haben, können nach dem gewünschten Azure Purview-Konto für den Beitritt suchen, auf die zugehörige Registerkarte „Zugriffssteuerung (IAM)“ klicken und die Besitzer bzw. Benutzerzugriffsadministrator ermitteln. Beachten Sie hierbei aber Folgendes: In einigen Fällen werden unter Umständen Azure Active Directory-Gruppen oder Dienstprinzipale als Besitzer oder Benutzerzugriffsadministrator verwendet, mit denen eine direkte Kontaktaufnahme dann ggf. nicht möglich ist. Stattdessen müssen Sie sich an einen Administrator wenden.

## <a name="who-should-be-assigned-to-what-role"></a>Wem sollte welche Rolle zugewiesen werden?

|Benutzerszenario|Geeignete Rollen|
|-------------|-----------------|
|Ich muss Ressourcen nur finden können und möchte keine Bearbeitungen durchführen|Rolle „Datenleseberechtigter für Purview“|
|Ich muss Informationen zu Ressourcen bearbeiten, entsprechende Klassifizierungen einfügen, Glossareinträge dafür zuordnen usw.|Rolle „Datenkurator für Purview“|
|Ich muss das Glossar bearbeiten oder neue Klassifizierungsdefinitionen einrichten|Rolle „Datenkurator für Purview“|
|Der Dienstprinzipal meiner Anwendung muss Daten per Pushvorgang an Azure Purview übertragen|Rolle „Datenkurator für Purview“|
|Ich muss Überprüfungen über das Studio-Feature von Purview einrichten|Rolle „Datenquellenadministrator für Purview“ sowie mindestens die Rolle „Datenleseberechtigter für Purview“ oder „Datenkurator für Purview“|
|Ich muss einen Dienstprinzipal oder eine andere programmgesteuerte Identität aktivieren, um Überprüfungen in Azure Purview einzurichten und zu überwachen, ohne dass die programmgesteuerte Identität Zugriff auf die Informationen des Katalogs hat |Rolle „Datenquellenadministrator für Purview“|
|Ich muss Benutzern in Azure Purview Rollen zuweisen | „Besitzer“ oder „Benutzerzugriffsadministrator“ |

Fahren Sie mit dem nächsten Artikel fort, um zu erfahren, wie Sie einen Sicherheitsprinzipal einer Rolle hinzufügen.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal](create-catalog-portal.md)

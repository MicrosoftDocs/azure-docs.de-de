---
title: Verwalten des Zugriffs auf Azure-Ressourcen für externe Benutzer mit RBAC | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Zugriff auf Azure-Ressourcen für organisationsexterne Benutzer mithilfe der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) verwalten.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: d919453816436366c00dde506210a2ed38cc69b7
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952208"
---
# <a name="manage-access-to-azure-resources-for-external-users-using-rbac"></a>Verwalten des Zugriffs auf Azure-Ressourcen für externe Benutzer mit RBAC

Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) ermöglicht eine bessere Sicherheitsverwaltung für große Organisationen sowie für kleine und mittelständische Unternehmen, die mit externen Projektmitarbeitern, Lieferanten oder Freiberuflern arbeiten und für diese Zugriff auf bestimmte Ressourcen in der Umgebung benötigen, nicht aber auf die gesamte Infrastruktur oder abrechnungsrelevante Bereiche. Die rollenbasierte Zugriffssteuerung bietet Flexibilität: Sie können ein Azure-Abonnement besitzen, das vom Administratorkonto (Dienstadministratorrolle auf Abonnementebene) verwaltet wird, und mehrere Benutzer dazu einladen, im gleichen Abonnement zu arbeiten, ohne dass diese über Administratorrechte für das Konto verfügen.

> [!NOTE]
> Office 365-Abonnements oder Azure Active Directory-Lizenzen (beispielsweise: Zugriff auf Azure Active Directory), die über das Microsoft 365 Admin Center bereitgestellt wurden, sind für die Verwendung von RBAC nicht geeignet.

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Zuweisen von RBAC-Rollen für den Abonnementbereich

In folgenden beiden Szenarien wird die rollenbasierte Zugriffssteuerung im Allgemeinen genutzt (es gibt allerdings noch weitere Szenarien):

* Organisationsexterne Benutzer (die nicht zum Azure Active Directory-Mandanten des Administratorbenutzers gehören) werden eingeladen, bestimmte Ressourcen oder das gesamte Abonnement zu verwalten.
* Sie arbeiten mit Benutzern innerhalb der Organisation (diese gehören zum Azure Active Directory-Mandanten des Benutzers), die aber verschiedenen Teams oder Gruppen angehören und differenzierten Zugriff auf das gesamte Abonnement oder auf bestimmte Ressourcengruppen oder Ressourcenbereiche in der Umgebung benötigen.

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Gewähren des Zugriffs auf Abonnementebene für einen Benutzer außerhalb von Azure Active Directory

RBAC-Rollen können nur von **Besitzern** des Abonnements zugeteilt werden. Daher muss der Administrator als ein Benutzer angemeldet sein, dem diese Rolle vorab zugewiesen wurde, oder der das Azure-Abonnement erstellt hat.

Nachdem Sie sich als Administrator beim Azure-Portal angemeldet haben, wählen Sie „Abonnements“ und dann das gewünschte Abonnement aus.
![Blatt „Abonnement“ im Azure-Portal](./media/role-assignments-external-users/0.png) Wenn der Administratorbenutzer das Azure-Abonnement erworben hat, wird der Benutzer standardmäßig als **Kontoadministrator** angezeigt. Dies ist die Abonnementrolle. Weitere Informationen zu den Azure-Abonnementrollen finden Sie unter [Hinzufügen oder Ändern von Azure-Abonnementadministratoren](../billing/billing-add-change-azure-subscription-administrator.md).

In diesem Beispiel ist der Benutzer „alflanigan@outlook.com“ der **Besitzer** des Abonnements „Free Trial“ im AAD-Mandanten „Default tenant Azure“. Da dieser Benutzer das Azure-Abonnement mit dem ursprünglichen Microsoft-Konto „Outlook“ (Microsoft-Konto = Outlook, Live usw.) erstellt hat, lautet der Standarddomänenname für alle weiteren Benutzer, die diesem Mandanten hinzugefügt werden, **\@alflaniganuoutlook.onmicrosoft.com**. Die Syntax der neuen Domäne wird gebildet, indem der Benutzername und der Domänenname des Benutzers, der den Mandanten erstellt hat, zusammengesetzt werden und die Erweiterung **„.onmicrosoft.com“** angefügt wird.
Darüber hinaus können Benutzer sich mit einem benutzerdefinierten Domänennamen beim Mandanten anmelden, nachdem dieser Name dem neuen Mandanten hinzugefügt und in diesem überprüft wurde. Weitere Informationen zum Überprüfen eines benutzerdefinierten Domänennamens in einem Azure Active Directory-Mandanten finden Sie unter [Hinzufügen eines benutzerdefinierten Domänennamens zu Ihrem Verzeichnis](../active-directory/fundamentals/add-custom-domain.md).

In diesem Beispiel enthält das Verzeichnis „Default tenant Azure“ nur Benutzer mit dem Domänennamen „\@alflanigan.onmicrosoft.com“.

Nach dem Auswählen des Abonnements muss der Administratorbenutzer auf **Zugriffssteuerung (IAM)** und dann auf **Neue Rolle hinzufügen** klicken.

![Zugriffssteuerung (IAM) im Azure-Portal](./media/role-assignments-external-users/1.png)

![Hinzufügen eines neuen Benutzers im Feature „Zugriffssteuerung (IAM)“ im Azure-Portal](./media/role-assignments-external-users/2.png)

Der nächste Schritt besteht darin, die zuzuweisende RBAC-Rolle sowie den Benutzer auszuwählen, dem die Rolle zugewiesen werden soll. Im Dropdownmenü **Rolle** werden dem Administratorbenutzer nur die integrierten RBAC-Rollen angezeigt, die in Azure verfügbar sind. Ausführlichere Erläuterungen der einzelnen Rollen und der Bereiche, die zugewiesen werden können, finden Sie unter [Integrierte Rollen für Azure-Ressourcen](built-in-roles.md).

Der Administratorbenutzer muss dann die E-Mail-Adresse des externen Benutzers hinzufügen. Das erwartete Verhalten: Der Benutzer wird im vorhandenen Mandanten nicht angezeigt. Nachdem der externe Benutzer eingeladen wurde, wird er unter **Abonnements > Zugriffssteuerung (IAM)** mit allen aktuellen Benutzern angezeigt, denen derzeit für den Abonnementbereich eine RBAC-Rolle zugewiesen ist.

![Hinzufügen von Berechtigungen zur neuen RBAC-Rolle](./media/role-assignments-external-users/3.png)

![Liste der RBAC-Rollen auf Abonnementebene](./media/role-assignments-external-users/4.png)

Der Benutzer „chessercarlton@gmail.com“ wurde eingeladen, **Besitzer** des Abonnements „Free Trial“ zu werden. Nach dem Senden der Einladung erhält der externe Benutzer eine E-Mail-Bestätigung mit einem Aktivierungslink.
![E-Mail-Einladung für eine RBAC-Rolle](./media/role-assignments-external-users/5.png)

Da der neue Benutzer ein organisationsexterner Benutzer ist, sind im Verzeichnis „Default tenant Azure“ keine Attribute für ihn vorhanden. Diese werden erstellt, nachdem der externe Benutzer der Aufnahme in das Verzeichnis zugestimmt hat, das dem Abonnement zugeordnet ist, für das dem Benutzer eine Rolle zugewiesen wurde.

![E-Mail-Einladungsnachricht für eine RBAC-Rolle](./media/role-assignments-external-users/6.png)

Der externe Benutzer wird ab jetzt im Azure Active Directory-Mandanten als externer Benutzer angezeigt, auch im Azure-Portal.

![Blatt „Benutzer“ im Azure-Portal für Azure Active Directory](./media/role-assignments-external-users/7.png)

In der **Benutzeransicht** können die externen Benutzer anhand des unterschiedlichen Symboltyps im Azure-Portal erkannt werden.

Wenn jedoch einem externen Benutzer im Bereich **Abonnement** Zugriff als **Besitzer** oder **Mitwirkender** erteilt wird, darf der Benutzer nicht auf das Verzeichnis des Administratorbenutzers zugreifen, sofern der **globale Administrator** dies nicht gestattet. In den Benutzereigenschaften kann der **Benutzertyp** ermittelt werden, der über zwei allgemeine Parameter verfügt: **Mitglied** und **Gast**. Ein Mitglied ist ein Benutzer, der im Verzeichnis registriert ist, ein Gast dagegen ist ein Benutzer, der von einer externen Quelle in das Verzeichnis eingeladen wurde. Weitere Informationen finden Sie unter [Wie fügen Azure Active Directory-Administratoren B2B-Zusammenarbeitsbenutzer hinzu?](../active-directory/active-directory-b2b-admin-add-users.md).

> [!NOTE]
> Stellen Sie nach der Eingabe der Anmeldeinformationen im Portal sicher, dass der externe Benutzer das richtige Verzeichnis für die Anmeldung auswählt. Ein Benutzer kann über Zugriff auf mehrere Verzeichnisse verfügen und eins der Verzeichnisse auswählen, indem er in der oberen rechten Ecke des Azure-Portals auf den Benutzernamen klickt und dann aus der Dropdownliste das geeignete Verzeichnis auswählt.

Als Gast in einem Verzeichnis kann ein externer Benutzer alle Ressourcen für das Azure-Abonnement verwalten, aber nicht auf das Verzeichnis zugreifen.

![Zugriffsbeschränkung auf Azure Active Directory im Azure-Portal](./media/role-assignments-external-users/9.png)

Azure Active Directory und Azure-Abonnements weisen keine Überordnungs-/Unterordnungsbeziehungen auf, wie sie für andere Azure-Ressourcen (z.B. virtuelle Computer, virtuelle Netzwerke, Web-Apps, Speicher usw.) mit einem Azure-Abonnement gelten. Alle letztgenannten Ressourcen werden in einem Azure-Abonnement erstellt, verwaltet und abgerechnet. Ein Azure-Abonnement wird zum Verwalten des Zugriffs auf ein Azure-Verzeichnis verwendet. Weitere Informationen finden Sie unter [Beziehung zwischen einem Azure-Abonnement und Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Von allen integrierten RBAC-Rollen bieten **Besitzer** und **Mitwirkender** vollständigen Verwaltungszugriff auf alle Ressourcen in der Umgebung. Der Unterschied besteht darin, dass ein Mitwirkender keine RBAC-Rollen erstellen und löschen kann. Die anderen integrierten Rollen, wie z.B. **Mitwirkender für virtuelle Computer** bieten vollständigen Verwaltungszugriff nur auf die mit Namen angegebenen Ressourcen, unabhängig von der **Ressourcengruppe**, in der sie erstellt werden.

Durch Zuweisen der integrierten RBAC-Rolle **Mitwirkender für virtuelle Computer** auf Abonnementebene gilt für den Benutzer, dem die Rolle zugewiesen wurde, Folgendes:

* Anzeigen aller virtuellen Computer, unabhängig vom Bereitstellungsdatum und der Ressourcengruppe, zu der sie gehören
* Vollständiger Verwaltungszugriff auf die virtuellen Computer im Abonnement
* Keine Anzeige anderer Ressourcentypen im Abonnement
* Kein Anwenden von Änderungen im Hinblick auf die Abrechnung

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Zuweisen einer integrierten RBAC-Rolle zu einem externen Benutzer

In einem anderen Szenario in diesem Test wird der externe Benutzer „alflanigan@gmail.com“ als **Mitwirkender für virtuelle Computer** hinzugefügt.

![Integrierte Rolle: Mitwirkender von virtuellen Computern](./media/role-assignments-external-users/11.png)

Normalerweise kann dieser externe Benutzer mit dieser integrierten Rolle nur virtuelle Computer und die zugehörigen Resource Manager-Ressourcen anzeigen und verwalten, die für die Bereitstellung erforderlich sind. Auslegungsbedingt bieten diese eingeschränkten Rollen nur Zugriff auf ihre im Azure-Portal erstellten entsprechenden Ressourcen.

![Übersicht über die Rolle „Mitwirkender für virtuelle Computer“ im Azure-Portal](./media/role-assignments-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Gewähren des Zugriffs auf Abonnementebene für einen Benutzer im gleichen Verzeichnis

Der Ablauf entspricht dem Prozess des Hinzufügens eines externen Benutzers – sowohl aus Sicht des Administrators, der die RBAC-Rolle gewährt, als auch aus Sicht des Benutzers, dem Zugriff auf die Rolle gewährt wird. Der Unterschied besteht darin, dass der eingeladene Benutzer keine E-Mail-Einladungen erhält, da nach dem Anmelden alle Ressourcenbereiche innerhalb des Abonnements auf dem Dashboard verfügbar sind.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Zuweisen von RBAC-Rollen für den Ressourcengruppenbereich

Das Zuweisen einer RBAC-Rolle für den Bereich einer **Ressourcengruppe** funktioniert genauso wie das Zuweisen einer Rolle auf Abonnementebene. Dies gilt für beide Arten von Benutzern – extern oder intern (zum gleichen Verzeichnis gehörend). Die Benutzer, denen eine RBAC-Rolle zugewiesen wurde, dürfen in ihrer Umgebung nur die Ressourcengruppe sehen, für die ihnen mithilfe des Symbols **Ressourcengruppen** im Azure-Portal Zugriff gewährt wurde.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Zuweisen von RBAC-Rollen für den Ressourcenbereich

Das Zuweisen einer RBAC-Rolle für den Ressourcenbereich in Azure funktioniert genauso wie das Zuweisen einer Rolle auf Abonnement- oder Ressourcengruppenebene. Beide Szenarien verwenden den gleichen Workflow. Auch hier können die Benutzer, denen eine RBAC-Rolle zugewiesen wurde, nur die Elemente anzeigen, für die ihnen auf der Registerkarte **Alle Ressourcen** oder direkt in ihrem Dashboard Zugriff gewährt wurde.

Ein wichtiger Aspekt bei der rollenbasierten Zugriffssteuerung im Ressourcengruppen- oder Ressourcenbereich ist, dass die Benutzer sicherstellen müssen, dass sie sich beim richtigen Verzeichnis anmelden.

![Anmelden beim Verzeichnis im Azure-Portal](./media/role-assignments-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Zuweisen von RBAC-Rollen für eine Azure Active Directory-Gruppe

Alle Szenarien, bei denen die rollenbasierte Zugriffssteuerung in den drei verschiedenen Bereichen in Azure zum Einsatz kommt, bieten den Vorteil, dass ein zugewiesener Benutzer diverse Ressourcen verwalten und bereitstellen kann, ohne ein persönliches Abonnement verwalten zu müssen. Unabhängig davon, ob die RBAC-Rolle für ein Abonnement, eine Ressourcengruppe oder einen Ressourcenbereich zugewiesen wurde – alle Ressourcen, die von den zugewiesenen Benutzern erstellt werden, werden in dem Azure-Abonnement abgerechnet, auf das die Benutzer Zugriff haben. Auf diese Weise hat der Benutzer, der über Administratorberechtigungen für die Abrechnung für das gesamte Azure-Abonnement verfügt, einen vollständigen Übersicht über die Nutzung, unabhängig davon, wer die Ressourcen verwaltet.

In größeren Organisationen können RBAC-Rollen auf die gleiche Weise auf Azure Active Directory-Gruppen angewendet werden. Auch hier möchte der Administrator differenzierten Zugriff für Teams oder ganze Abteilungen gewähren, nicht für jeden einzelnen Benutzer. Aus diesem Grund ist die rollenbasierte Zugriffssteuerung eine ausgesprochen zeit- und verwaltungseffiziente Option. Um dieses Beispiel zu veranschaulichen, wurde die Rolle **Mitwirkender** einer der Gruppen auf Abonnementebene im Mandanten hinzugefügt.

![Zusätzliche RBAC-Rolle für AAD-Gruppen](./media/role-assignments-external-users/14.png)

Bei diesen Gruppen handelt es sich um Sicherheitsgruppen, die nur innerhalb von Azure Active Directory bereitgestellt und verwaltet werden.


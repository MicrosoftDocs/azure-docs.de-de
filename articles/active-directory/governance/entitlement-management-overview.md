---
title: Was ist die Azure AD-Berechtigungsverwaltung? (Vorschau) – Azure Active Directory
description: Verschaffen Sie sich einen Überblick über die Berechtigungsverwaltung von Azure Active Directory, und lesen Sie, wie Sie mit ihr den Zugriff auf Gruppen, Anwendungen und SharePoint Online-Websites für interne und externe Benutzer verwalten können.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: efd3ff8a6e7ddf2aa6242cc322d8a6536a6bd26b
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474066"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>Was ist die Azure AD-Berechtigungsverwaltung? (Vorschau)

> [!IMPORTANT]
> Die Berechtigungsverwaltung von Azure Active Directory (Azure AD) befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mitarbeiter in Organisationen benötigen für ihre Aufgaben Zugriff auf verschiedene Gruppen, Anwendungen und Websites. Die Verwaltung dieses Zugriffs ist nicht einfach. In den meisten Fällen gibt es keine strukturierte Liste aller Ressourcen, die ein Benutzer für ein Projekt benötigt. Der Projektmanager weiß über die erforderlichen Ressourcen, die beteiligten Personen und die Projektdauer Bescheid. Jedoch verfügt der Projektmanager in der Regel nicht über Berechtigungen zum Genehmigen oder Verweigern des Zugriffs anderer Personen. Dieses Szenario wird komplizierter, wenn Sie versuchen, mit externen Personen oder Unternehmen zusammenzuarbeiten.

Die Berechtigungsverwaltung von Azure Active Directory (Azure AD) kann Ihnen helfen, den Zugriff auf Gruppen, Anwendungen und SharePoint Online-Websites für interne Benutzer und Benutzer außerhalb der Organisation zu verwalten.

## <a name="why-use-entitlement-management"></a>Argumente für die Berechtigungsverwaltung

Die Verwaltung des Zugriffs auf Ressourcen stellt Unternehmen häufig vor Herausforderungen:

- Benutzer wissen möglicherweise nicht, welchen Zugriff sie benötigen.
- Benutzern bereitet es möglicherweise Schwierigkeiten, die richtigen Personen oder Ressourcen zu finden.
- Wenn Benutzer den richtigen Zugriff auf eine Ressource erhalten haben, verfügen sie möglicherweise länger über diesen Zugriff, als für die Geschäftszwecke erforderlich ist.

Diese Probleme sind für Benutzer noch größer, die Zugriff aus einem anderen Verzeichnis benötigen, z.B. externe Benutzer in Lieferkettenorganisationen oder bei Geschäftspartnern. Beispiel:

- Organisationen kennen möglicherweise nicht alle der Personen in anderen Verzeichnissen und können die unbekannten Personen nicht einladen.
- Selbst wenn Organisationen diese Benutzer einladen können, versäumen sie es möglicherweise, den Zugriff sämtlicher Benutzer einheitlich zu verwalten.

Diese Herausforderungen können mit der Azure AD-Berechtigungsverwaltung leichter bewältigt werden.

## <a name="what-can-i-do-with-entitlement-management"></a>Welche Möglichkeiten bietet mir die Berechtigungsverwaltung?

Einige Funktionen der Berechtigungsverwaltung:

- Erstellen von Paketen verwandter Ressourcen, die Benutzer anfordern können
- Definieren von Regeln für das Anfordern von Ressourcen und den Ablauf des Zugriffs
- Steuern des Lebenszyklus für den Zugriff interner und externer Benutzer
- Delegieren der Verwaltung von Ressourcen
- Festlegen von genehmigenden Personen für Anforderungen
- Erstellen von Berichten zum Nachverfolgen des Verlaufs

Einen Überblick über Identitätsgovernance und Berechtigungsverwaltung bietet Ihnen das folgende Video von der Ignite 2018:

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>Welche Ressourcen kann ich verwalten?

Sie können mit der Berechtigungsverwaltung die folgenden Typen von Ressourcen verwalten:

- Azure AD-Sicherheitsgruppen
- Office 365-Gruppen
- Azure AD-Enterprise-Anwendungen einschließlich SaaS-Anwendung und benutzerdefinierter integrierter Anwendungen, die Verbund oder Bereitstellung unterstützen
- SharePoint Online-Websitesammlungen und -Websites

Sie können auch den Zugriff auf andere Ressourcen steuern, die auf Azure AD-Sicherheitsgruppen oder Office 365-Gruppen basieren.  Beispiel:

- Sie können Benutzerlizenzen für Microsoft Office 365 mit einer Azure AD-Sicherheitsgruppe in einem Zugriffspaket und Konfigurieren der [gruppenbasierten Lizenzierung](../users-groups-roles/licensing-groups-assign.md) für diese Gruppe vergeben.
- Sie können Benutzern mithilfe einer Azure AD-Sicherheitsgruppe in einem Zugriffspaket und Erstellen einer [Azure-Rollenzuweisung](../../role-based-access-control/role-assignments-portal.md) für diese Gruppe Zugriff zum Verwalten von Azure-Ressourcen erteilen.

## <a name="what-are-access-packages-and-policies"></a>Was sind Zugriffspakete und Zugriffspaketrichtlinien?

Die Berechtigungsverwaltung beinhaltet das Konzept von *Zugriffspaketen*. Ein Zugriffspaket ist ein Bündel aller Ressourcen, die ein Benutzer benötigt, um an einem Projekt zu arbeiten oder seine Aufgaben auszuführen. Die Ressourcen umfassen den Zugriff auf Gruppen, Anwendungen oder Websites. Zugriffspakete dienen zum Steuern des Zugriffs für die internen Mitarbeiter und auch für Benutzer außerhalb Ihrer Organisation. Zugriffspakete sind in Containern definiert, die als *Kataloge* bezeichnet werden.

Zugriffspakete enthalten außerdem eine oder mehrere *Richtlinien*. In einer Richtlinie sind die Regeln oder Leitlinien für den Zugriff auf ein Zugriffspaket definiert. Das Aktivieren eine Richtlinie erzwingt, dass nur den richtigen Benutzern Zugriff auf die richtigen Ressourcen und während des richtigen Zeitraums gewährt wird.

![Zugriffspakete und Zugriffspaketrichtlinien](./media/entitlement-management-overview/elm-overview-access-package.png)

Mit einem Zugriffspaket und seinen Richtlinien definiert der Zugriffspaketmanager Folgendes:

- Ressourcen
- Die Rollen, die Benutzer für die Ressourcen benötigen
- Die internen und externen Benutzer, die zum Anfordern von Zugriff berechtigt sind
- Den Genehmigungsprozess und die Benutzer, die den Zugriff genehmigen oder verweigern können
- Die Dauer des Zugriffs durch den Benutzer

Im folgenden Diagramm wird ein Beispiel für die verschiedenen Elemente der Berechtigungsverwaltung gezeigt. In ihm werden zwei Beispielzugriffspakete dargestellt.

- **Zugriffspaket 1** enthält als Ressource eine einzelne Gruppe. Der Zugriff wird mit einer Richtlinie definiert, die einer Gruppe von Benutzern im Verzeichnis das Anfordern des Zugriffs ermöglicht.
- **Zugriffspaket 2** enthält als Ressourcen eine Gruppe, eine Anwendung und eine SharePoint Online-Website. Der Zugriff wird mit zwei verschiedenen Richtlinien definiert. Die erste Richtlinie ermöglicht einer Gruppe von Benutzern im Verzeichnis das Anfordern des Zugriffs. Die zweite Richtlinie ermöglicht Benutzern in einem externen Verzeichnis das Anfordern des Zugriffs.

![Übersicht über die Berechtigungsverwaltung](./media/entitlement-management-overview/elm-overview.png)

## <a name="external-users"></a>Externe Benutzer

Wenn Sie [Azure AD Business-to-Business (B2B)](../b2b/what-is-b2b.md)-Einladungen verwenden, müssen Sie bereits die E-Mail-Adressen der externen Gastbenutzer kennen, die Sie in das Ressourcenverzeichnis aufnehmen möchten, um mit ihnen zu arbeiten. Dies lässt sich leicht durchführen, wenn Sie an einem kleineren oder kurzfristigen Projekt arbeiten und bereits alle Teilnehmer kennen. Es ist jedoch schwieriger, wenn Sie mit vielen Benutzern arbeiten möchten oder wenn die Teilnehmer im Lauf der Zeit wechseln.  Angenommen, Sie arbeiten mit einer anderen Organisation zusammen und haben einen Ansprechpartner in dieser Organisation, im Laufe der Zeit benötigen jedoch weitere Benutzer in dieser Organisation ebenfalls Zugriff.

Mit der Berechtigungsverwaltung können Sie eine Richtlinie definieren, die Benutzern aus Organisationen, die Sie angeben und die ebenfalls Azure AD verwenden, das Anfordern eines Zugriffspakets ermöglicht. Sie können ein Ablaufdatum für den Zugriff angeben und festlegen, ob eine Genehmigung erforderlich ist. Wenn eine Genehmigung erforderlich ist, können Sie auch einen oder mehrere Benutzer in der externen Organisation, die Sie zuvor eingeladen haben, als genehmigende Person festlegen, da diese wahrscheinlich wissen, welche externen Benutzer in ihrer Organisation Zugriff benötigen. Nachdem Sie das Zugriffspaket konfiguriert haben, können Sie der Kontaktperson in der externen Organisation einen Link zu dem Zugriffspaket senden. Diese Kontaktperson kann den Link für andere Benutzer in der externen Organisation freigeben, und diese können mit diesem Link das Zugriffspaket anfordern.  Benutzer in dieser Organisation, die bereits in Ihr Verzeichnis eingeladen wurden, können diesen Link ebenfalls verwenden.

Wenn eine Anforderung genehmigt wurde, gewährt die Berechtigungsverwaltung dem Benutzer den erforderlichen Zugriff. Dies kann das Einladen des Benutzers umfassen, falls er sich noch nicht in Ihrem Verzeichnis befindet. Azure AD erstellt automatisch ein B2B-Konto für den Benutzer.  Beachten Sie, dass ein Administrator möglicherweise durch das Festlegen einer [B2B-Zulassungs- oder -Verweigerungsliste](../b2b/allow-deny-list.md), die Einladungen an andere Organisationen zulässt oder blockiert, festgelegt hat, welche Organisationen für die Zusammenarbeit zugelassen werden.  Wenn die Zulassungs- oder Verweigerungsliste den Benutzer nicht zulässt, wird er nicht eingeladen.

Da der Zugriff des Benutzers nicht unbefristet sein soll, geben Sie in der Richtlinie ein Ablaufdatum, z.B. 180 Tage, an. Nach 180 Tagen wird sämtlicher mit dem Zugriffspaket verknüpfte Zugriff durch die Zugriffsverwaltung entfernt, sofern er nicht erneuert wird.  Wenn der Benutzer, der über die Berechtigungsverwaltung eingeladen wurde, nach Ende der Zuweisung über keine weiteren Zuweisungen von Zugriffspaketen verfügt, wird die Anmeldung über sein B2B-Konto 30 Tage lang gesperrt, und anschließend wird das Konto entfernt.  Auf diese Weise lassen sich unnötige Konten verhindern.  

## <a name="terminology"></a>Begriff

Machen Sie sich zum besseren Verständnis der Berechtigungsverwaltung und der dazugehörigen Dokumentation mit den folgenden Begriffen vertraut.

| Begriff oder Konzept | BESCHREIBUNG |
| --- | --- |
| Berechtigungsverwaltung | Ein Dienst, der Zugriffspakete zuweist, widerruft und verwaltet. |
| Zugriffspaket | Eine Sammlung von Berechtigungen und Richtlinien für Ressourcen, die Benutzer anfordern können. Zugriffspakete sind immer in einem Katalog enthalten. |
| Zugriffsanforderung | Eine Anforderung des Zugriffs auf ein Zugriffspaket. Eine Anforderung durchläuft in der Regel einen Workflow. |
| policy | Ein Satz von Regeln, die den Zugriffslebenszyklus definieren. Sie legen beispielsweise fest, wie Benutzer Zugriff erhalten, wer den Zugriff genehmigen darf und wie lange Benutzer Zugriff haben. Beispielrichtlinien umfassen Mitarbeiterzugriff und externen Zugriff. |
| catalog | Ein Container verwandter Ressourcen und Zugriffspakete. |
| Katalog „Allgemein“ | Ein integrierter Katalog, der immer verfügbar ist. Um dem Katalog „Allgemein“ Ressourcen hinzuzufügen, sind bestimmte Berechtigungen erforderlich. |
| resource | Ein Objekt oder ein Dienst (z.B. eine Gruppe, Anwendung oder Website), für das bzw. den einem Benutzer Berechtigungen erteilt werden können. |
| Ressourcentyp | Der Typ der Ressource, z.B. Gruppen, Anwendungen und SharePoint Online-Websites. |
| Ressourcenrolle | Eine Sammlung von Berechtigungen, die einer Ressource zugeordnet sind. |
| Ressourcenverzeichnis | Ein Verzeichnis, das mindestens eine Ressource enthält, die freigegeben (geteilt) werden soll. |
| Zugewiesene Benutzer | Eine Zuweisung eines Zugriffspakets zu einem Benutzer oder einer Gruppe. |
| enable | Das Verfügbarmachen eines Zugriffspakets für Anforderungen von Benutzern. |

## <a name="roles-and-permissions"></a>Rollen und Berechtigungen

Die Berechtigungsverwaltung verfügt je nach beruflicher Funktion über unterschiedliche Rollen.

| Rolle | BESCHREIBUNG |
| --- | --- |
| [Benutzeradministrator](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) | Verwalten aller Aspekte der Berechtigungsverwaltung.<br/>Erstellen von Benutzern und Gruppen. |
| Katalogersteller | Erstellen und Verwalten von Katalogen. In der Regel ein IT-Administrator oder Ressourcenbesitzer. Die Person, die einen Katalog erstellt, ist automatisch der erste Besitzer des Katalogs. |
| Katalogbesitzer | Bearbeiten und Verwalten von vorhandenen Katalogen. In der Regel ein IT-Administrator oder Ressourcenbesitzer. |
| Zugriffspaketmanager | Bearbeiten und Verwalten aller in einem Katalog vorhandenen Zugriffspakete. |
| Genehmigende Person | Genehmigen der Anforderungen von Zugriffspaketen. |
| Anforderer | Anfordern von Zugriffspaketen. |

Die folgende Tabelle gibt Aufschluss über die Berechtigungen für die einzelnen Rollen.

| Aufgabe | Benutzeradministrator | Katalogersteller | Katalogbesitzer | Zugriffspaketmanager | Genehmigende Person |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Erstellen eines neuen Zugriffspakets im Katalog „Allgemein“](entitlement-management-access-package-create.md) | :heavy_check_mark: |  :heavy_check_mark: |  |  |  |
| [Erstellen eines neuen Zugriffspakets in einem Katalog](entitlement-management-access-package-create.md) | :heavy_check_mark: |   | :heavy_check_mark: |  |  |
| [Hinzufügen/Entfernen von Ressourcenrollen zu/aus einem Zugriffspaket](entitlement-management-access-package-edit.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Festlegen, wer ein Zugriffspaket anfordern darf](entitlement-management-access-package-edit.md#add-a-new-policy) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Direktes Zuweisen eines Benutzers zu einem Zugriffspaket](entitlement-management-access-package-edit.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Anzeigen, wem ein Zugriffspaket zugewiesen ist](entitlement-management-access-package-edit.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Anzeigen der Anforderungen eines Zugriffspakets](entitlement-management-access-package-edit.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Anzeigen der Fehler bei der Übermittlung einer Anforderung](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Abbrechen einer ausstehenden Anforderung](entitlement-management-access-package-edit.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Ausblenden eines Zugriffspakets](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Löschen eines Zugriffspakets](entitlement-management-access-package-edit.md#delete) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Genehmigen einer Zugriffsanforderung](entitlement-management-request-approve.md) |  |  |  |  | :heavy_check_mark: |
| [Erstellen eines Katalogs](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |  |
| [Hinzufügen/Entfernen von Ressourcen zu/aus dem Katalog „Allgemein“](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  |  |  |  |
| [Hinzufügen/Entfernen von Ressourcen zu/aus einem Katalog](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Hinzufügen von Katalogbesitzern oder Zugriffspaketmanagern](entitlement-management-catalog-create.md#add-catalog-owners-or-access-package-managers) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Bearbeiten/Löschen eines Katalogs](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |

## <a name="license-requirements"></a>Lizenzanforderungen

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Spezielle Clouds, z.B. Azure Government, Azure Deutschland und Azure China 21Vianet, können in dieser Vorschau derzeit nicht verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Erstellen Ihres ersten Zugriffspakets](entitlement-management-access-package-first.md)
- [Gängige Szenarios](entitlement-management-scenarios.md)

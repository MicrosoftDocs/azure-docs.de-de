---
title: Verwenden von Azure AD Identity Governance, um externe Benutzer zu überprüfen und zu entfernen, wenn sie keinen Ressourcenzugriff mehr haben
description: Verwenden Sie Zugriffsüberprüfungen, um den Zugriff von Mitgliedern von Partnerorganisationen zu ermöglichen oder zu entfernen.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/06/2020
ms.author: barclayn
ms.openlocfilehash: 19f88da6a678221cde66bf61668d16ba9ab998a4
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677319"
---
# <a name="use-azure-active-directory-azure-ad-identity-governance-to-review-and-remove-external-users-who-no-longer-have-resource-access"></a>Verwenden von Azure AD (Azure Active Directory) Identity Governance, um externe Benutzer zu überprüfen und zu entfernen, wenn sie keinen Ressourcenzugriff mehr haben

In diesem Artikel werden Features und Methoden beschrieben, mit denen Sie externe Identitäten ermitteln und auswählen können, um Sie zu überprüfen und aus Azure AD zu entfernen, wenn sie nicht mehr benötigt werden. Dank der Cloud ist die Zusammenarbeit mit internen und externen Benutzern einfacher als je zuvor. Unternehmen, die Office 365 verwenden, bemerken eine Zunahme externer Identitäten (einschließlich Gästen), da Benutzer immer mehr gemeinsam an Daten, Dokumenten oder in digitalen Arbeitsbereichen wie Teams arbeiten. Organisationen müssen ein Gleichgewicht zwischen dem Ermöglichen der Zusammenarbeit und sicherer Besprechungen und den Governanceanforderungen finden. Ein Teil dieser Bemühungen sollte das Auswerten und Bereinigen externer Benutzer aus Partnerorganisationen, die zur Zusammenarbeit zu Ihrem Mandanten eingeladen wurden, und das Entfernen aus Ihren Azure AD sein, wenn sie nicht mehr benötigt werden.

>[!NOTE]
>Eine gültige Azure AD Premium P2-, kostenpflichtige Enterprise Mobility + Security E5- oder Testlizenz ist erforderlich, um Azure AD-Zugriffsüberprüfungen verwenden zu können. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](../fundamentals/active-directory-whatis.md).

## <a name="why-review-users-from-external-organizations-in-your-tenant"></a>Warum sollten Sie Benutzer aus externen Organisationen in Ihrem Mandanten überprüfen?

In den meisten Organisationen initiieren Endbenutzer den Vorgang zum Einladen von Geschäftspartnern und Lieferanten zur Zusammenarbeit. Die Notwendigkeit der Zusammenarbeit führt dazu, dass Unternehmen Ressourcenbesitzern und Endbenutzern die Möglichkeit geben, externe Benutzer regelmäßig zu überprüfen und zu bestätigen. Häufig ist der Prozess des Onboardings neuer Partner für die Zusammenarbeit eingeplant und berücksichtigt, da die Zusammenarbeit aber oft nicht klar befristet ist, ist nicht immer offensichtlich, wann ein Benutzer keinen Zugriff mehr benötigt. Außerdem motiviert die Verwaltung des Identitätslebenszyklus Unternehmen dazu, Azure AD zu bereinigen und Benutzer zu entfernen, die keinen Zugriff mehr auf die Ressourcen der Organisation benötigen. Indem Sie nur die relevanten Identitätsverweise für Partner und Lieferanten im Verzeichnis beibehalten, können Sie das Risiko verringern, dass Mitarbeiter versehentlich externe Benutzer, die nicht mehr vorhanden sein sollten, auswählen und ihnen Zugriff gewähren. Dieses Dokument führt Sie durch verschiedene Optionen von Vorschlägen für empfohlene proaktive Maßnahmen bis hin zu reaktiven und Bereinigungsaktivitäten zum Steuern externer Identitäten.

## <a name="use-entitlement-management-to-grant-and-revoke-access"></a>Verwenden der Berechtigungsverwaltung zum Erteilen und Widerrufen des Zugriffs

Berechtigungsverwaltungsfunktionen ermöglichen den [automatisierten Lebenszyklus externer Identitäten](entitlement-management-external-users.md#manage-the-lifecycle-of-external-users) mit Zugriff auf Ressourcen. Durch Einrichten von Prozessen und Verfahren zum Verwalten des Zugriffs über die Berechtigungsverwaltung und Veröffentlichen von Ressourcen über Zugriffspakete wird das Nachverfolgen des Zugriffs externer Benutzer auf Ressourcen deutlich vereinfacht. Wenn Sie den Zugriff über [Zugriffspakete für die Berechtigungsverwaltung](entitlement-management-overview.md) in Azure AD verwalten, kann Ihre Organisation den Zugriff für Ihre Benutzer ebenso wie für Benutzer aus Partnerorganisationen zentral definieren und verwalten. Die Berechtigungsverwaltung verwendet Genehmigungen und Zuweisungen von Zugriffspaketen, um nachzuverfolgen, wo externe Benutzer den Zugriff angefordert haben und dieser ihnen zugewiesen wurde. Wenn alle Zuweisungen eines externen Benutzers entfernt wurden, kann die Berechtigungsverwaltung diesen externen Benutzer automatisch aus dem Mandanten entfernen. 

## <a name="find-guests-not-invited-through-entitlement-management"></a>Suchen nach Gästen, die nicht über die Berechtigungsverwaltung eingeladen wurden

Wenn Mitarbeiter für die Zusammenarbeit mit externen Benutzern autorisiert sind, können sie eine beliebige Anzahl von Benutzern einladen, die nicht ihrer Organisation angehören. Das Suchen und Gruppieren externer Partner in nach Unternehmen unterteilte dynamische Gruppen sowie deren Überprüfung ist möglicherweise nicht praktikabel, da zu viele verschiedene einzelne Unternehmen zu überprüfen sind oder weil es keinen Besitzer oder Sponsor für die Organisation gibt. Microsoft stellt ein PowerShell-Beispielskript bereit, das Sie bei der Analyse der Nutzung externer Identitäten in einem Mandanten unterstützen kann. Das Skript listet externe Identitäten auf und kategorisiert diese. Mithilfe des Skripts können Sie externe Identitäten identifizieren und bereinigen, die möglicherweise nicht mehr benötigt werden. Die Ausgabe des Skriptbeispiels hilft bei der automatisierten Erstellung von Sicherheitsgruppen mit den ermittelten externen Partnern, die keiner Gruppe angehören, um diese eingehender zu analysieren und mit Azure AD-Zugriffsüberprüfungen zu verwenden.
Das Skript ist auf [GitHub](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse) verfügbar. Nachdem das Skript ausgeführt wurde, generiert es eine HTML-Ausgabedatei mit den externen Identitäten, die folgende Kriterien erfüllen:

- Sie gehören keiner Gruppe im Mandanten mehr an.
- Ihnen ist eine privilegierte Rolle im Mandanten zugewiesen.
- Ihnen ist eine Anwendung im Mandanten zugewiesen.

Die Ausgabe enthält auch die einzelnen Domänen für jede dieser externen Identitäten. 

>[!NOTE]
>Das oben verlinkte Skript ist ein Beispielskript, das die Gruppenmitgliedschaft, Rollenzuweisungen und Anwendungszuweisungen in Azure AD prüft. Es gibt möglicherweise andere Zuweisungen in Anwendungen, die externe Benutzer außerhalb von Azure AD erhalten haben, z. B. SharePoint (direkte Zuweisung der Mitgliedschaft), Azure RBAC oder Azure DevOps.

## <a name="review-resources-used-by-external-identities"></a>Überprüfen der von externen Identitäten verwendeten Ressourcen

Wenn Sie über externe Identitäten verfügen, die Ressourcen wie Teams oder andere Anwendungen nutzen, die noch nicht der Berechtigungsverwaltung unterliegen, sollten Sie den Zugriff auf diese Ressourcen ebenfalls regelmäßig überprüfen. Azure AD-[Zugriffsüberprüfungen](create-access-review.md) bieten Ihnen die Möglichkeit, den Zugriff externer Identitäten zu überprüfen, indem Sie den Ressourcenbesitzer, die externen Identitäten selbst oder einen anderen vertrauenswürdigen Delegierten bestätigen lassen, dass der Zugriff weiterhin erforderlich ist. Zugriffsüberprüfungen beziehen sich auf eine Ressource und erstellen eine Prüfaktivität, die auf alle Benutzer, die Zugriff auf die Ressource haben, oder nur auf Gastbenutzer beschränkt ist. Der Prüfer erhält dann die resultierende Liste von Benutzern, die er überprüfen muss: entweder alle Benutzer, einschließlich der Mitarbeiter Ihres Unternehmens, oder nur externe Identitäten.

![Verwenden einer Gruppe zum Überprüfen des Zugriffs](media/access-reviews-external-users/group-members.png)

Ein Überprüfungsansatz, der sich auf die Ressourcenbesitzer konzentriert, ist bei der Steuerung des Zugriffs externer Identitäten hilfreich. Ressourcenbesitzer, die für den Zugriff, die Verfügbarkeit und Sicherheit ihrer Informationen verantwortlich sind, sind in den meisten Fällen am besten in der Lage, Entscheidungen bezüglich des Zugriffs auf ihre Ressourcen zu treffen, und kennen die berechtigten Benutzer besser als ein Sponsor, der eine Vielzahl von externen Identitäten verwaltet.

## <a name="create-access-reviews-for-external-identities"></a>Erstellen von Zugriffsüberprüfungen für externe Identitäten

Benutzer, die keinen Zugriff mehr auf Ressourcen in Ihrem Mandanten haben, können entfernt werden, wenn sie nicht mehr mit Ihrer Organisation arbeiten. Bevor Sie diese externen Identitäten blockieren und löschen, sollten Sie sich an diese externen Benutzer wenden und sicherstellen, dass Sie keine Projekte und Zugriffsberechtigungen übersehen haben, die noch benötigt werden. Wenn Sie eine Gruppe erstellen, die alle externen Identitäten enthält, die keinen Zugriff auf Ressourcen in Ihrem Mandanten mehr haben, können Sie mithilfe von Zugriffsüberprüfungen eine Bestätigung der externen Benutzer selbst einholen, dass sie den Zugriff noch benötigen oder in Zukunft benötigen werden. Im Rahmen der Überprüfung kann der Ersteller der Überprüfung in Zugriffsüberprüfungen die Funktion **Bei Genehmigung Grund anfordern** verwenden, um externe Benutzer aufzufordern, eine Begründung für den Fortbestand des Zugriffs anzugeben. So erfahren Sie, wo und wie sie weiterhin Zugriff in Ihrem Mandanten benötigen. Außerdem können Sie die Einstellung **Zusätzlicher Inhalt für E-Mail an Prüfer** aktivieren, damit Benutzer wissen, dass sie den Zugriff verlieren, wenn sie nicht reagieren oder ggf. keine Begründung angeben. Wenn Sie beschließen, dass die Zugriffsüberprüfung externe Identitäten **deaktivieren und löschen** soll, falls sie nicht reagieren bzw. nicht ausreichend begründen können, warum sie weiterhin Zugriff benötigen, können Sie die Option „Deaktivieren und löschen“ verwenden, wie im nächsten Abschnitt beschrieben.

![Beschränken der Überprüfung auf Gastbenutzer](media/access-reviews-external-users/guest-users-only.png)

Wenn die Überprüfung abgeschlossen ist, werden auf der Seite **Ergebnisse** die Antworten der einzelnen externen Identitäten in einer Übersicht aufgeführt. Sie können auswählen, ob die Ergebnisse automatisch angewendet und Benutzer von der Zugriffsüberprüfung deaktiviert und gelöscht werden sollen. Alternativ können Sie die Antworten überprüfen und selbst entscheiden, ob der Zugriff eines Benutzers entfernt werden soll oder ob Sie bei diesen nachfragen, bevor Sie eine Entscheidung treffen. Wenn einige Benutzer weiterhin auf Ressourcen zugreifen können, die Sie noch nicht geprüft haben, können Sie die Überprüfung als Teil ihrer Ermittlungen verwenden und beim nächsten Überprüfungs- und Bestätigungsdurchlauf heranziehen.

## <a name="disable-and-delete-external-identities-with-azure-ad-access-reviews-preview"></a>Deaktivieren und Löschen externer Identitäten mit Azure AD-Zugriffsüberprüfungen (Vorschau)

Neben der Option zum Entfernen unerwünschter externer Identitäten aus Ressourcen wie Gruppen oder Anwendungen haben Sie die Möglichkeit, externe Identitäten durch Azure AD-Zugriffsüberprüfungen daran zu hindern, dass sie sich bei Ihrem Mandanten anmelden, und nach 30 Tagen aus Ihrem Mandanten zu löschen. Sobald Sie **Anmeldung des Benutzers für 30 Tage blockieren und den Benutzer anschließend vom Mandanten entfernen** aktiviert haben, gilt der Status „Wird übernommen“ für die Überprüfung 30 Tage lang. Während dieses Zeitraums können Einstellungen, Ergebnisse, Prüfer oder Überwachungsprotokolle, die Teil der aktuellen Prüfung sind, nicht angezeigt oder konfiguriert werden. 

![Einstellungen nach Abschluss](media/access-reviews-external-users/upon-completion-settings.png)

Beim Erstellen einer neuen Zugriffsüberprüfung können Sie im Abschnitt „Einstellungen nach Abschluss“ für **Auf verweigerte Benutzer anzuwendende Aktion** festlegen, dass die **Benutzeranmeldung 30 Tage lang blockiert und der Benutzer dann aus dem Mandanten entfernt** wird.
Diese Einstellung, die sich derzeit in der Vorschauphase befindet, ermöglicht Ihnen das Identifizieren, Blockieren und Löschen externer Identitäten aus Ihrem Azure AD-Mandanten. Externe Identitäten, die überprüft wurden und denen der zukünftige Zugriff durch den Prüfer verweigert wurde, werden unabhängig von ihrem Ressourcenzugriff und ihrer Gruppenmitgliedschaft blockiert und gelöscht. Diese Einstellung wird am besten als letzter Schritt verwendet, nachdem Sie sich vergewissert haben, dass die überprüften externen Benutzer keinen Zugriff mehr auf Ressourcen haben und problemlos aus Ihrem Mandanten entfernt werden können, oder wenn Sie sie unabhängig vom aktuellen Zugriff in jedem Fall entfernen möchten. Mit der Funktion „Deaktivieren und löschen“ wird der externe Benutzer zunächst blockiert, sodass er sich nicht mehr bei Ihrem Mandanten anmelden und auf keine Ressourcen mehr zugreifen kann. Der Zugriff auf Ressourcen wird in dieser Phase nicht widerrufen, und wenn Sie die Berechtigungen des externen Benutzers wiederherstellen möchten, kann die Anmeldungsberechtigung neu konfiguriert werden. Wenn Sie keine weitere Aktion durchführt, wird eine blockierte externe Identität nach 30 Tagen aus dem Verzeichnis gelöscht, wobei das Konto und der Zugriff entfernt werden.

## <a name="next-steps"></a>Nächste Schritte

- [Zugriffsüberprüfung: Graph-API](/graph/api/resources/accessreviews-root?view=graph-rest-beta)
- [Berechtigungsverwaltung: Graph-API](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)
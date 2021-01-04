---
title: Verwalten der Einwilligung zu Anwendungen und Auswerten von Einwilligungsanforderungen in Azure Active Directory
description: Erfahren Sie, wie Sie Einwilligungsanforderungen verwalten, wenn die Benutzereinwilligung deaktiviert oder eingeschränkt ist, und wie Sie eine Anforderung für eine mandantenweite Administratoreinwilligung für eine Anwendung in Azure Active Directory auswerten.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 12/27/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.openlocfilehash: 4664bdd515c815dbd742c0d4e05f1e5c03978526
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94649190"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Verwalten der Einwilligung zu Anwendungen und Auswerten von Einwilligungsanforderungen

Microsoft [empfiehlt](../../security/fundamentals/steps-secure-identity.md#restrict-user-consent-operations) das Deaktivieren der Endbenutzereinwilligung für Anwendungen. Dadurch wird der Entscheidungsfindungsprozess beim Sicherheits- und Identitätsadministratorteam Ihres Unternehmens zentralisiert.

Nachdem die Endbenutzereinwilligung deaktiviert oder eingeschränkt wurde, müssen einige wichtige Überlegungen berücksichtigt werden, um sicherzustellen, dass Ihre Organisation sicher bleibt und unternehmenskritische Anwendungen weiterhin genutzt werden können. Diese Schritte sind entscheidend, um die Auswirkungen auf das Supportteam und die IT-Administratoren Ihrer Organisation zu minimieren und gleichzeitig die Verwendung nicht verwalteter Konten in Anwendungen von Drittanbietern zu verhindern.

## <a name="process-changes-and-education"></a>Prozessänderungen und Fortbildung

 1. Aktivieren Sie ggf. den [Workflow zur Administratoreinwilligung (Vorschauversion)](configure-admin-consent-workflow.md), um Benutzern zu gestatten, Administratorgenehmigungen direkt über den Einwilligungsbildschirm anzufordern.

 2. Stellen Sie sicher, dass alle Administratoren die [Berechtigungen und das Einwilligungsframework](../develop/consent-framework.md) verstehen und wissen, wie die [Einwilligungsaufforderung](../develop/application-consent-experience.md) funktioniert und wie [eine Anforderung zur mandantenweiten Administratoreinwilligung](#evaluating-a-request-for-tenant-wide-admin-consent) ausgewertet wird.
 3. Überprüfen Sie die vorhandenen Prozesse Ihrer Organisation darauf, wie Benutzer eine Administratorgenehmigung für eine Anwendung anfordern, und nehmen Sie ggf. Aktualisierungen vor. Wenn Prozesse geändert werden:
    * Aktualisieren Sie die relevante Dokumentation, die Überwachung, die Automatisierung usw.
    * Kommunizieren Sie die Prozessänderungen an alle betroffenen Benutzer, Entwickler, Supportteams und IT-Administratoren.

## <a name="auditing-and-monitoring"></a>Auditing und Überwachung

1. [Überwachen Sie Apps und erteilte Berechtigungen](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions) in Ihrer Organisation, um sicherzustellen, dass nicht berechtigten oder verdächtigen Anwendungen zuvor kein Zugriff auf Daten gewährt wurde.

2. Unter [Erkennen und Korrigieren von unerlaubter Zustimmung in Office 365](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) finden Sie zusätzliche bewährte Methoden und Sicherheitsvorkehrungen gegen verdächtige Anwendungen, die eine OAuth-Einwilligung anfordern.

3. Wenn Ihre Organisation über eine geeignete Lizenz verfügt:

    * Verwenden Sie zusätzliche [Funktionen für die OAuth-Anwendungsüberwachung in Microsoft Cloud App Security](/cloud-app-security/investigate-risky-oauth).
    * Verwenden Sie [Azure Monitor-Arbeitsmappen für die Überwachung von Berechtigungen und Einwilligungen](../reports-monitoring/howto-use-azure-monitor-workbooks.md) und damit im Zusammenhang stehende Aktivitäten. Die Arbeitsmappe *Erkenntnisse aus Einwilligungen* bietet eine Ansicht der Apps nach der Anzahl fehlerhafter Einwilligungsanforderungen. Dies kann hilfreich sein, um Anwendungen für die Überprüfung durch die Administratoren zu priorisieren und zu entscheiden, ob ihnen eine Administratoreinwilligung gewährt werden soll.

### <a name="additional-considerations-for-reducing-friction"></a>Zusätzliche Überlegungen zur Vermeidung von Schwierigkeiten

Um die Auswirkungen auf vertrauenswürdige, unternehmenskritische Anwendungen zu minimieren, die bereits verwendet werden, sollten Sie den Anwendungen, die über eine große Anzahl von gewährten Benutzereinwilligungen verfügen, proaktiv eine Administratoreinwilligung erteilen:

1. Erstellen Sie eine Bestandsaufnahme der bereits in Ihrer Organisation hinzugefügten Apps mit hohen Nutzungszahlen basierend auf den Anmeldeprotokollen oder der Zustimmungsaktivität für Einwilligungen. Mit einem PowerShell-[Skript](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) können Sie schnell und einfach Anwendungen mit einer großen Anzahl von erteilten Benutzereinwilligungen ermitteln.

2. Evaluieren Sie die wichtigsten Anwendungen, denen bisher noch keine Administratoreinwilligung erteilt wurde.

   > [!IMPORTANT]
   > Evaluieren Sie eine Anwendung sorgfältig, bevor Sie eine mandantenweite Administratoreinwilligung erteilen, auch wenn bereits viele Benutzer in der Organisation für sich selbst eingewilligt haben.

3. Erteilen Sie für jede genehmigte Anwendung eine mandantenweite Administratoreinwilligung mithilfe einer der unten beschriebenen Methoden.

4. Denken Sie bei jeder genehmigten Anwendung an das [Einschränken des Benutzerzugriffs](configure-user-consent.md).

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Auswerten einer Anforderung für eine mandantenweite Administratoreinwilligung

Das Erteilen einer mandantenweiten Administratoreinwilligung ist ein sensibler Vorgang.  Berechtigungen werden für die gesamte Organisation erteilt. Dies kann auch Vorgänge mit umfassenden Berechtigungen einschließen. Beispielsweise hat die Rollenverwaltung Vollzugriff auf alle Postfächer oder alle Websites und die Berechtigung für vollständige Benutzeridentitätswechsel.

Vor dem Erteilen einer mandantenweiten Administratoreinwilligung müssen Sie sicherstellen, dass Sie der Anwendung und dem Anwendungsherausgeber im Sinne der zu erteilenden Zugriffsebene vertrauen. Wenn Sie nicht sicher sind, wer die Anwendung steuert und warum die Anwendung die Berechtigungen anfordert, *erteilen Sie keine Einwilligung*.

Die folgende Liste enthält einige Empfehlungen, die Sie beim Auswerten einer Anforderung zum Erteilen einer Administratoreinwilligung beachten sollten.

* **Informieren Sie sich über die [Berechtigungen und das Einwilligungsframework](../develop/consent-framework.md) in Microsoft Identity Platform.**

* **Machen Sie sich mit dem Unterschied zwischen [delegierten Berechtigungen und Anwendungsberechtigungen](../develop/v2-permissions-and-consent.md#permission-types) vertraut.**

   Anwendungsberechtigungen ermöglichen der Anwendung den Zugriff auf die Daten für die gesamte Organisation ohne Benutzerinteraktion. Delegierte Berechtigungen ermöglichen es der Anwendung, im Namen eines Benutzers zu agieren, der zu einem bestimmten Zeitpunkt bei der Anwendung angemeldet war.

* **Informieren Sie sich über die angeforderten Berechtigungen.**

   Die von der Anwendung angeforderten Berechtigungen werden in der [Einwilligungsaufforderung](../develop/application-consent-experience.md) aufgeführt. Wenn Sie den Berechtigungstitel erweitern, wird die Beschreibung der Berechtigung angezeigt. Die Beschreibung von Anwendungsberechtigungen enthält in der Regel „ohne einen angemeldeten Benutzer“. Die Beschreibung von delegierten Berechtigungen enthält in der Regel „im Namen des angemeldeten Benutzers“. Berechtigungen für die Microsoft Graph-API werden in der [Referenz zu Microsoft Graph-Berechtigungen](/graph/permissions-reference) beschrieben. Informationen zu den von anderen APIs verfügbar gemachten Berechtigungen finden Sie in der jeweiligen Dokumentation.

   Wenn Sie die angeforderte Berechtigung nicht verstehen, *erteilen Sie keine Einwilligung*.

* **Informieren Sie sich, welche Anwendung Berechtigungen anfordert und wer die Anwendung veröffentlicht hat.**

   Nehmen Sie sich vor böswilligen Anwendungen in Acht, die versuchen, wie andere Anwendungen zu erscheinen.

   Wenn Sie die Legitimität einer Anwendung oder ihres Herausgebers anzweifeln, *erteilen Sie keine Einwilligung*. Suchen Sie stattdessen nach weiteren Bestätigung (z. B. direkt beim Anwendungsherausgeber).

* **Stellen Sie sicher, dass die angeforderten Berechtigungen den Features entsprechen, die Sie von der Anwendung erwarten.**

   Beispielsweise ist für eine Anwendung, die SharePoint-Websiteverwaltung anbietet, möglicherweise delegierter Zugriff erforderlich, um alle Websitesammlungen zu lesen. Sie benötigt jedoch nicht unbedingt Vollzugriff auf alle Postfächer oder vollständige Berechtigungen für Identitätswechsel im Verzeichnis.

   Wenn Sie vermuten, dass die Anwendung mehr Berechtigungen anfordert, als sie benötigt, *erteilen Sie keine Einwilligung*. Wenden Sie sich an den Anwendungsherausgeber, um weitere Details zu erfahren.

## <a name="granting-consent-as-an-administrator"></a>Erteilen der Einwilligung als Administrator

### <a name="granting-tenant-wide-admin-consent"></a>Erteilen einer mandantenweiten Administratoreinwilligung
Unter [Erteilen einer mandantenweiten Administratoreinwilligung für eine Anwendung](grant-admin-consent.md) finden Sie ausführliche Anleitungen zum Erteilen einer mandantenweiten Administratoreinwilligung über das Azure-Portal, mithilfe von Azure AD PowerShell oder über die Einwilligungsaufforderung selbst.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Erteilen einer Einwilligung im Namen eines bestimmten Benutzers
Anstatt die Einwilligung für die gesamte Organisation zu erteilen, kann ein Administrator auch die [Microsoft Graph-API](/graph/use-the-api) verwenden, um eine Einwilligung für delegierte Berechtigungen im Namen eines einzelnen Benutzers zu erteilen. Weitere Informationen finden Sie unter [Zugreifen im Namen eines Benutzers](/graph/auth-v2-user).

## <a name="limiting-user-access-to-applications"></a>Beschränken des Benutzerzugriffs auf Anwendungen
Der Zugriff von Benutzern auf Anwendungen kann auch dann noch eingeschränkt werden, wenn eine mandantenweite Administratoreinwilligung erteilt wurde. Weitere Informationen dazu, wie Sie eine Benutzerzuweisung zu einer Anwendung erzwingen, finden Sie unter [Methoden zum Zuweisen von Benutzern und Gruppen](./assign-user-or-group-access-portal.md).

Eine umfassendere Übersicht, einschließlich der Behandlung weiterer komplexer Szenarien, finden Sie unter [Verwenden von Azure AD für die Anwendungszugriffsverwaltung](what-is-access-management.md).

## <a name="disable-all-future-user-consent-operations-to-any-application"></a>Deaktivieren aller zukünftigen Vorgänge für die Benutzereinwilligung für jede Anwendung
Die Deaktivierung der Benutzerzustimmung für das gesamte Verzeichnis führt dazu, dass Endbenutzer keiner Anwendung zustimmen können. Administratoren können weiterhin im Namen von Benutzern zustimmen. Weitere Informationen zur Zustimmung zu Anwendungen sowie zum Aktivieren bzw. Deaktivieren der Zustimmung finden Sie unter [Grundlegendes zur Benutzer- und Administratorzustimmung](../develop/howto-convert-app-to-be-multi-tenant.md).

Führen Sie die folgenden Schritte aus, um alle zukünftigen Vorgänge der Benutzerzustimmung in Ihrem gesamten Verzeichnis zu deaktivieren:
1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.
2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.
3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.
4.  Wählen Sie im Navigationsmenü **Benutzer und Gruppen** aus.
5.  Wählen Sie **Benutzereinstellungen** aus.
6.  Deaktivieren Sie alle zukünftigen Vorgänge der Benutzerzustimmung, indem Sie **Benutzer können Apps Zugriff auf ihre Daten gewähren** auf **Nein** festlegen und dann auf die Schaltfläche **Speichern** klicken.

## <a name="next-steps"></a>Nächste Schritte
* [Fünf Schritte zum Sichern Ihrer Identitätsinfrastruktur](../../security/fundamentals/steps-secure-identity.md#before-you-begin-protect-privileged-accounts-with-mfa)
* [Konfigurieren des Workflows für die Administratoreinwilligung (Vorschau)](configure-admin-consent-workflow.md)
* [Konfigurieren der Art der Benutzereinwilligung für eine Anwendung in Azure Active Directory](configure-user-consent.md)
* [Berechtigungen und Zustimmung im Microsoft Identity Platform-Endpunkt](../develop/v2-permissions-and-consent.md)
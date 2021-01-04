---
title: Grundlegendes zur Funktionsweise der Azure AD-Bereitstellung | Microsoft-Dokumentation
description: Grundlegendes zur Funktionsweise der Azure AD-Bereitstellung
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/04/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: contperf-fy21q2
ms.openlocfilehash: 07e3ff76886a935bf0b7b5c83052e0e7cd7a0429
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027423"
---
# <a name="how-provisioning-works"></a>Funktionsweise der Bereitstellung

Die automatische Bereitstellung bezieht sich auf das Erstellen von Benutzeridentitäten und -rollen in den Cloudanwendungen, auf die Benutzer Zugriff benötigen. Zusätzlich zur Erstellung von Benutzeridentitäten umfasst die automatische Bereitstellung auch die Wartung und Entfernung von Benutzeridentitäten, wenn sich der Status oder die Rollen ändern. Bevor Sie eine Bereitstellung starten, können Sie diesen Artikel lesen, um Informationen zur Funktionsweise der Azure AD-Bereitstellung und Konfigurationsempfehlungen zu erhalten. 

Der **Azure AD-Bereitstellungsdienst** stellt Benutzer für SaaS-Apps und andere Systeme bereit, indem er eine Verbindung mit einem Endpunkt der SCIM 2.0-Benutzerverwaltungs-API herstellt, der vom Anwendungsanbieter zur Verfügung gestellt wird. Dieser SCIM-Endpunkt (System for Cross-Domain Identity Management, System für die domänenübergreifende Identitätsverwaltung) ermöglicht Azure AD das programmgesteuerte Erstellen, Aktualisieren und Entfernen von Benutzern. Bei bestimmten Anwendungen kann der Bereitstellungsdienst auch zusätzliche identitätsbezogene Objekte wie Gruppen und Rollen erstellen, aktualisieren und entfernen. Der für die Bereitstellung zwischen Azure AD und der Anwendung verwendete Kanal wird mit HTTPS TLS 1.2 verschlüsselt.


![Azure AD-Bereitstellungsdienst](./media/how-provisioning-works/provisioning0.PNG)
*Abbildung 1: Der Azure AD-Bereitstellungsdienst*

![Workflow der ausgehenden Benutzerbereitstellung](./media/how-provisioning-works/provisioning1.PNG)
*Abbildung 2: Workflow für die ausgehende Benutzerbereitstellung von Azure AD in beliebten SaaS-Anwendungen*

![Workflow der eingehenden Benutzerbereitstellung](./media/how-provisioning-works/provisioning2.PNG)
*Abbildung 3: Workflow für die eingehende Benutzerbereitstellung aus beliebten Personalverwaltungsanwendungen (Human Capital Management, HCM) in Azure Active Directory und Windows Server Active Directory*

## <a name="provisioning-using-scim-20"></a>Bereitstellung mit SCIM 2.0

Der Azure AD-Bereitstellungsdienst verwendet das [SCIM 2.0-Protokoll](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) für die automatische Bereitstellung. Der Dienst stellt eine Verbindung mit dem SCIM-Endpunkt für die Anwendung her und verwendet das SCIM-Benutzerobjektschema und REST-APIs, um die Bereitstellung und Aufhebung der Bereitstellung von Benutzern und Gruppen zu automatisieren. Ein SCIM-basierter Bereitstellungsconnector wird für die meisten Anwendungen im Azure AD-Katalog bereitgestellt. Beim Erstellen von Apps für Azure AD können Entwickler die SCIM 2.0-Benutzerverwaltungs-API verwenden, um einen SCIM-Endpunkt zu erstellen, der Azure AD für die Bereitstellung integriert. Ausführliche Informationen hierzu finden Sie unter [Erstellen eines SCIM-Endpunkts und Konfigurieren der Benutzerbereitstellung](../app-provisioning/use-scim-to-provision-users-and-groups.md).

Wenn Sie einen automatischen Azure AD-Bereitstellungsconnector für eine App anfordern möchten, die derzeit noch nicht über einen verfügt, übermitteln Sie eine [Azure Active Directory-Anwendungsanforderung](https://aka.ms/aadapprequest).

## <a name="authorization"></a>Authorization

Damit Azure AD eine Verbindung mit der Benutzerverwaltungs-API der Anwendung herstellen kann, sind Anmeldeinformationen erforderlich. Wenn Sie die automatische Benutzerbereitstellung für eine Anwendung konfigurieren, müssen Sie gültige Anmeldeinformationen eingeben. Bei Kataloganwendungen finden Sie die Anmeldeinformationstypen und die Anforderungen für die Anwendung im entsprechenden App-Tutorial. Bei nicht im Katalog enthaltenen Anwendungen finden Sie Informationen zu den Anmeldeinformationstypen und Anforderungen in der Dokumentation zu [SCIM](./use-scim-to-provision-users-and-groups.md#authorization-for-provisioning-connectors-in-the-application-gallery). Im Azure-Portal können Sie die Anmeldeinformationen testen, indem Sie Azure AD versuchen lassen, mit den angegebenen Anmeldeinformationen eine Verbindung mit der Bereitstellungs-App der App herzustellen.

## <a name="mapping-attributes"></a>Zuordnen von Attributen

Wenn Sie die Benutzerbereitstellung für eine SaaS-Anwendung eines Drittanbieters aktivieren, steuert das Azure-Portal deren Attributwerte mithilfe von Attributzuordnungen. Zuordnungen bestimmen die Benutzerattribute, die beim Bereitstellen oder Aktualisieren von Benutzerkonten zwischen Azure AD und der Zielanwendung übertragen werden.

Es ist ein vorkonfigurierter Satz von Attributen und Attributzuordnungen zwischen Azure AD-Benutzerobjekten und den Benutzerobjekten der einzelnen SaaS-Apps verfügbar. Einige Apps verwalten neben Benutzern andere Objekttypen wie beispielsweise Gruppen.

Überprüfen und konfigurieren Sie beim Einrichten der Bereitstellung unbedingt die Attributzuordnungen und Workflows, mit denen festgelegt wird, welche Benutzereigenschaften (oder Gruppeneigenschaften) zwischen Azure AD und der Anwendung übertragen werden. Überprüfen und konfigurieren Sie die übereinstimmende Eigenschaft (**Objekte mit diesem Attribut abgleichen**), mit der Benutzer/Gruppen zwischen den beiden Systemen eindeutig identifiziert und abgeglichen werden.

Sie können die Standardattributzuordnungen den Anforderungen Ihres Unternehmens entsprechend anpassen. Dies bedeutet, dass Sie vorhandene Attributzuordnungen ändern oder löschen und neue Attributzuordnungen erstellen können. Ausführliche Informationen hierzu finden Sie unter [Anpassen von Attributzuordnungen für die Benutzerbereitstellung für SaaS-Anwendungen in Azure Active Directory](./customize-application-attributes.md).

Wenn Sie die Bereitstellung für eine SaaS-Anwendung konfigurieren, ist einer der Attributzuordnungstypen, die Sie angeben können, eine Ausdruckszuordnung. Für diese Zuordnungen müssen Sie einen skriptähnlichen Ausdruck schreiben, mit dem Sie die Daten Ihrer Benutzer in Formate transformieren können, die für die SaaS-Anwendung akzeptabler sind. Ausführliche Informationen hierzu finden Sie unter [Schreiben von Ausdrücken für Attributzuordnungen](functions-for-customizing-application-data.md).

## <a name="scoping"></a>Bereichsdefinition 
### <a name="assignment-based-scoping"></a>Zuweisungsbasierte Bereichsdefinition

Bei der Ausgangsbereitstellung von Azure AD für eine SaaS-Anwendung stellt die Verwendung von [Benutzer- oder Gruppenzuweisungen](../manage-apps/assign-user-or-group-access-portal.md) die gängigste Methode dar, um zu bestimmen, für welche Benutzer die Bereitstellung gelten soll. Da Benutzerzuweisungen auch zum Aktivieren des einmaligen Anmeldens verwendet werden, kann dieselbe Methode zum Verwalten des Zugriffs und der Bereitstellung verwendet werden. Die zuweisungsbasierte Bereichsdefinition gilt nicht für Eingangsbereitstellungsszenarien wie Workday und SuccessFactors.

* **Gruppen.** Mit einem Azure AD Premium-Lizenzplan können Sie Gruppen zum Zuweisen des Zugriffs auf eine SaaS-Anwendung verwenden. Wenn der Bereitstellungsbereich auf **Nur zugewiesene Benutzer und Gruppen synchronisieren** festgelegt ist, werden dann Benutzer vom Azure AD-Benutzerbereitstellungsdienst basierend darauf bereitgestellt oder deren Bereitstellungen aufgehoben, ob sie Mitglieder einer der Anwendung zugewiesenen Gruppe sind. Das Gruppenobjekt selbst wird nur bereitgestellt, wenn die Anwendung Gruppenobjekte unterstützt. Stellen Sie sicher, dass die Eigenschaft „SecurityEnabled“ bei Gruppen, die Ihrer Anwendung zugewiesen sind, auf „True“ eingestellt ist.

* **Dynamische Gruppen.** Der Azure AD-Benutzerbereitstellungsdienst kann Benutzer in [dynamischen Gruppen](../enterprise-users/groups-create-rule.md) lesen und bereitstellen. Berücksichtigen Sie die folgenden Einschränkungen und Empfehlungen:

  * Dynamische Gruppen können die Leistung der End-to-End-Bereitstellung von Azure AD in SaaS-Anwendungen beeinträchtigen.

  * Wie schnell ein Benutzer in einer dynamischen Gruppe bereitgestellt oder die Bereitstellung in einer SaaS-Anwendung aufgehoben wird, hängt davon ab, wie schnell die dynamische Gruppe Änderungen an der Mitgliedschaft auswerten kann. Informationen dazu, wie Sie den Verarbeitungsstatus einer dynamischen Gruppe überprüfen, finden Sie unter [Überprüfen des Verarbeitungsstatus für eine Mitgliedschaftsregel](../enterprise-users/groups-create-rule.md).

  * Wenn ein Benutzer die Mitgliedschaft der dynamischen Gruppe verliert, wird dies als Bereitstellungsaufhebungsereignis betrachtet. Berücksichtigen Sie dieses Szenario beim Erstellen von Regeln für dynamische Gruppen.

* **Verschachtelte Gruppen.** Der Azure AD-Benutzerbereitstellungsdienst kann Benutzer in verschachtelten Gruppen lesen oder bereitstellen. Der Dienst kann nur Benutzer lesen und bereitstellen, die direkte Mitglieder einer explizit zugewiesenen Gruppe sind. Diese Einschränkung von „gruppenbasierten Zuweisungen zu Anwendungen“ wirkt sich auch auf das einmalige Anmelden aus (siehe [Verwenden einer Gruppe zum Verwalten des Zugriffs auf SaaS-Anwendungen](../enterprise-users/groups-saasapps.md)). Weisen Sie stattdessen die Gruppen mit den Benutzern, die bereitgestellt werden müssen, direkt zu, oder [definieren Sie den entsprechenden Bereich](define-conditional-rules-for-provisioning-user-accounts.md) auf andere Weise.

### <a name="attribute-based-scoping"></a>Attributbasierte Bereichsdefinition 

Sie können Bereichsdefinitionsfilter verwenden, um attributbasierte Regeln zu definieren, die festlegen, welche Benutzer für eine Anwendung bereitgestellt werden. Diese Methode wird häufig für die Eingangsbereitstellung von HCM-Anwendungen in Azure AD und Active Directory verwendet. Bereichsfilter werden im Rahmen der Attributzuordnungen für jeden Bereitstellungsconnector für Azure AD-Benutzer konfiguriert. Ausführliche Informationen zum Konfigurieren von attributbasierten Bereichsdefinitionsfiltern finden Sie unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](define-conditional-rules-for-provisioning-user-accounts.md).

### <a name="b2b-guest-users"></a>B2B-Benutzer/Gastbenutzer

Der Azure AD-Benutzerbereitstellungsdienst kann zum Bereitstellen von B2B-Benutzern (oder Gastbenutzern) in Azure AD für SaaS-Anwendungen verwendet werden. Damit B2B-Benutzer sich bei der SaaS-Anwendung mithilfe von Azure AD anmelden können, muss die Funktion „SAML-basiertes einmaliges Anmelden“ der SaaS-Anwendung jedoch auf bestimmte Weise konfiguriert sein. Weitere Informationen zum Konfigurieren von SaaS-Anwendungen, sodass sie Anmeldungen von B2B-Benutzern unterstützen, finden Sie unter [Konfigurieren von SaaS-Apps für die B2B-Zusammenarbeit](../external-identities/configure-saas-apps.md).

Beachten Sie, dass der Benutzerprinzipalname (userPrincipalName) für einen Gastbenutzer häufig als „alias#EXText#@domain.com“ gespeichert wird. Wenn „userPrincipalName“ in den Attributzuordnungen als Quellattribut enthalten ist, wird „#EXT#“ vom Benutzerprinzipalnamen entfernt. Wenn #EXT# erhalten bleiben soll, ersetzen Sie „userPrincipalName“ durch „originalUserPrincipalName“ als Quellattribut. 

## <a name="provisioning-cycles-initial-and-incremental"></a>Bereitstellungszyklen: Startzyklus und Inkrementell

Wenn Azure AD das Quellsystem ist, verwendet der Bereitstellungsdienst das[Nachverfolgen von Änderungen bei Microsoft Graph-Daten mithilfe einer Deltaabfrage](/graph/delta-query-overview), um Benutzer und Gruppen zu überwachen. Der Bereitstellungsdienst führt einen ersten Zyklus für das Quellsystem und das Zielsystem aus, gefolgt von regelmäßigen inkrementellen Zyklen.

### <a name="initial-cycle"></a>Erster Zyklus

Nach dem Starten des Bereitstellungsdiensts werden im ersten Zyklus die folgenden Vorgänge ausgeführt:

1. Abfragen aller Benutzer und Gruppen aus dem Quellsystem und Abrufen aller Attribute, die in den [Attributzuordnungen](customize-application-attributes.md) definiert sind.

2. Filtern der zurückgegebenen Benutzer und Gruppen, indem alle konfigurierten [Zuweisungen](../manage-apps/assign-user-or-group-access-portal.md) oder [attributbasierten Bereichsfilter](define-conditional-rules-for-provisioning-user-accounts.md) verwendet werden.

3. Wenn ein Benutzer zugewiesen ist oder in den Bereich für die Bereitstellung fällt, fragt der Dienst das Zielsystem anhand der angegebenen [Zuordnungsattribute](customize-application-attributes.md#understanding-attribute-mapping-properties) nach einem übereinstimmenden Benutzer ab. Beispiel: Wenn der userPrincipal-Name auf dem Quellsystem das übereinstimmende Attribut ist und dem userName auf dem Zielsystem zugeordnet ist, fragt der Bereitstellungsdienst beim Zielsystem userNames ab, die mit den Werten von userPrincipal-Namen auf dem Quellsystem übereinstimmen.

4. Wenn auf dem Zielsystem kein übereinstimmender Benutzer gefunden werden kann, wird ein Benutzer mit den vom Quellsystem zurückgegebenen Attributen erstellt. Nach der Erstellung des Benutzerkontos wird die ID des Zielsystems für den neuen Benutzer vom Bereitstellungsdienst erkannt und zwischengespeichert. Diese ID wird zum Ausführen aller zukünftigen Vorgänge für diesen Benutzer verwendet.

5. Wenn ein übereinstimmender Benutzer gefunden wird, erfolgt eine Aktualisierung mit den vom Quellsystem bereitgestellten Attributen. Nach der Zuordnung des Benutzerkontos wird die ID des Zielsystems für den neuen Benutzer vom Bereitstellungsdienst erkannt und zwischengespeichert. Diese ID wird zum Ausführen aller zukünftigen Vorgänge für diesen Benutzer verwendet.

6. Wenn die Attributzuordnungen „Referenzattribute“ enthalten, führt der Dienst auf dem Zielsystem zusätzliche Aktualisierungen aus, um die referenzierten Objekte zu erstellen und zu verknüpfen. Ein Benutzer kann auf dem Zielsystem beispielsweise über das Attribut „Manager“ verfügen, das mit einem anderen Benutzer verknüpft ist, der im Zielsystem erstellt wurde.

7. Legen Sie am Ende des ersten Zyklus einen Grenzwert fest, der als Startpunkt für die nachfolgenden inkrementellen Zyklen dient.

Einige Anwendungen, z. B. ServiceNow, G Suite und Box, unterstützen nicht nur die Bereitstellung von Benutzern, sondern auch die Bereitstellung von Gruppen und ihrer Mitglieder. Wenn die Gruppenbereitstellung in den [Zuordnungen](customize-application-attributes.md) aktiviert ist, synchronisiert der Bereitstellungsdienst die Benutzer und Gruppen und später dann die Gruppenmitgliedschaften.

### <a name="incremental-cycles"></a>Inkrementelle Zyklen

Nach dem ersten Zyklus führen alle anderen Zyklen folgende Aktionen aus:

1. Fragen Sie vom Quellsystem alle Benutzer und Gruppen ab, die aktualisiert wurden, seitdem der letzte Grenzwert gespeichert wurde.

2. Filtern der zurückgegebenen Benutzer und Gruppen, indem alle konfigurierten [Zuweisungen](../manage-apps/assign-user-or-group-access-portal.md) oder [attributbasierten Bereichsfilter](define-conditional-rules-for-provisioning-user-accounts.md) verwendet werden.

3. Wenn ein Benutzer zugewiesen ist oder in den Bereich für die Bereitstellung fällt, fragt der Dienst das Zielsystem anhand der angegebenen [Zuordnungsattribute](customize-application-attributes.md#understanding-attribute-mapping-properties) nach einem übereinstimmenden Benutzer ab.

4. Wenn auf dem Zielsystem kein übereinstimmender Benutzer gefunden werden kann, wird ein Benutzer mit den vom Quellsystem zurückgegebenen Attributen erstellt. Nach der Erstellung des Benutzerkontos wird die ID des Zielsystems für den neuen Benutzer vom Bereitstellungsdienst erkannt und zwischengespeichert. Diese ID wird zum Ausführen aller zukünftigen Vorgänge für diesen Benutzer verwendet.

5. Wenn ein übereinstimmender Benutzer gefunden wird, erfolgt eine Aktualisierung mit den vom Quellsystem bereitgestellten Attributen. Wenn es sich um ein neu zugewiesenes Konto handelt, für das die Zuordnung erfolgt, wird die ID des Zielsystems für den neuen Benutzer vom Bereitstellungsdienst erkannt und zwischengespeichert. Diese ID wird zum Ausführen aller zukünftigen Vorgänge für diesen Benutzer verwendet.

6. Wenn die Attributzuordnungen „Referenzattribute“ enthalten, führt der Dienst auf dem Zielsystem zusätzliche Aktualisierungen aus, um die referenzierten Objekte zu erstellen und zu verknüpfen. Ein Benutzer kann auf dem Zielsystem beispielsweise über das Attribut „Manager“ verfügen, das mit einem anderen Benutzer verknüpft ist, der im Zielsystem erstellt wurde.

7. Wenn ein Benutzer, der sich zuvor im Bereich für die Bereitstellung befunden hat, aus dem Bereich entfernt und die Zuweisung aufgehoben wird, deaktiviert der Dienst den Benutzer auf dem Zielsystem über eine Aktualisierung.

8. Wenn ein Benutzer, der sich zuvor im Bereich für die Bereitstellung befunden hat, im Quellsystem deaktiviert oder vorläufig gelöscht wird, deaktiviert der Dienst den Benutzer im Zielsystem per Update.

9. Wenn ein Benutzer, der sich zuvor im Bereich für die Bereitstellung befunden hat, im Quellsystem endgültig gelöscht wird, löscht der Dienst den Benutzer im Zielsystem. In Azure AD werden Benutzer 30 Tage nach dem vorläufigen Löschen endgültig gelöscht.

10. Legen Sie am Ende des inkrementellen Zyklus einen neuen Grenzwert fest, der als Startpunkt für die nachfolgenden inkrementellen Zyklen dient.

> [!NOTE]
> Optional können Sie die Vorgänge **Erstellen**, **Aktualisieren** und **Löschen** deaktivieren. Dazu verwenden Sie im Abschnitt **Zuordnungen** die Kontrollkästchen für [Zielobjektaktionen](customize-application-attributes.md). Die Logik zum Deaktivieren eines Benutzers während eines Updates wird ebenfalls per Attributzuordnung über ein Feld wie „accountEnabled“ gesteuert.

Der Bereitstellungsdienst führt aufeinander folgende inkrementelle Zyklen in bestimmten (im [jeweiligen Anwendungstutorial](../saas-apps/tutorial-list.md) angegebenen) Intervallen aus. Die inkrementellen Zyklen werden ausgeführt, bis eines der folgenden Ereignisse auftritt:

- Der Dienst wird mit dem Azure-Portal oder mit dem entsprechenden Microsoft Graph-API-Befehl manuell beendet.
- Ein neuer erster Zyklus wird ausgelöst, indem im Azure-Portal die Option **Clear state and restart** (Status löschen und neu starten) oder der entsprechende Microsoft Graph-API-Befehl verwendet wird. Durch diese Aktion werden alle gespeicherten Grenzwerte gelöscht und alle Quellobjekte erneut ausgewertet.
- Ein neuer erster Zyklus wird aufgrund einer Änderung in den Attributzuordnungen oder Bereichsfiltern ausgelöst. Durch diese Aktion werden auch alle gespeicherten Grenzwerte gelöscht und alle Quellobjekte erneut ausgewertet.
- Der Bereitstellungsprozess wird aufgrund einer hohen Fehlerrate in Quarantäne versetzt (siehe unten) und bleibt mehr als vier Wochen lang in Quarantäne. In diesem Fall wird der Dienst automatisch deaktiviert.

### <a name="errors-and-retries"></a>Fehler und Wiederholungen

Wenn ein einzelner Benutzer aufgrund eines Fehlers des Zielsystems nicht hinzugefügt, aktualisiert oder gelöscht werden kann, wird der jeweilige Vorgang im nächsten Synchronisierungszyklus wiederholt. Falls für den Benutzer weiterhin ein Fehler auftritt, wird die Häufigkeit der Wiederholungen allmählich verringert, bis schließlich nur noch ein Versuch pro Tag durchgeführt wird. Zum Beheben des Fehlers müssen Administratoren die [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) prüfen, um die Grundursache ermitteln und dann entsprechende Maßnahmen ergreifen zu können. Beispiele für häufig auftretende Fehler sind:

- Für Benutzer ist auf dem Quellsystem ein bestimmtes Attribut nicht angegeben, das auf dem Zielsystem erforderlich ist.
- Benutzer verfügen auf dem Quellsystem über einen Attributwert, für den auf dem Zielsystem eine eindeutige Einschränkung besteht, und der gleiche Wert ist auch in einem anderen Benutzerdatensatz vorhanden.

Beheben Sie diese Fehler, indem Sie die Attributwerte für den betroffenen Benutzer auf dem Quellsystem oder die Attributzuordnungen so anpassen, dass sie keine Konflikte verursachen.

### <a name="quarantine"></a>Quarantäne

Wenn die meisten oder alle Aufrufe an das Zielsystem aufgrund eines Fehlers (z. B. bei ungültigen Administratoranmeldeinformationen) dauerhaft nicht erfolgreich sind, wird der Bereitstellungsauftrag in den Zustand „Quarantäne“ versetzt. Dieser Zustand ist im [Zusammenfassungsbericht für die Bereitstellung](./check-status-user-account-provisioning.md) angegeben, und wenn im Azure-Portal E-Mail-Benachrichtigungen konfiguriert wurden, erfolgt auch eine entsprechende Mitteilung per E-Mail.

In der Quarantäne wird die Häufigkeit der inkrementellen Zyklen allmählich auf einmal pro Tag verringert.

Die Quarantäne für den Bereitstellungsauftrag wird aufgehoben, nachdem alle relevanten Fehler behoben wurden, und der nächste Synchronisierungszyklus wird gestartet. Falls der Bereitstellungsauftrag länger als vier Wochen in Quarantäne verbleibt, wird er deaktiviert. Weitere Informationen zum Quarantänestatus finden Sie [hier](./application-provisioning-quarantine-status.md).

### <a name="how-long-provisioning-takes"></a>Dauer der Bereitstellung

Die Leistung ist davon abhängig, ob Ihr Bereitstellungsauftrag einen ersten Bereitstellungszyklus oder einen inkrementellen Zyklus ausführt. Weitere Informationen zur Dauer einer Bereitstellung und zur Überwachung des Status des Bereitstellungsdiensts finden Sie unter [Ermitteln, wann ein bestimmter Benutzer auf eine Anwendung zugreifen kann](application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>Ermitteln, ob Benutzer ordnungsgemäß bereitgestellt werden

Alle vom Benutzerbereitstellungsdienst ausgeführten Vorgänge werden in Azure AD unter [Provisioning logs (preview)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) (Bereitstellungsprotokollen (Vorschau)) erfasst. Die Protokolle enthalten alle Lese- und Schreibvorgänge auf den Quell- und Zielsystemen sowie die Benutzerdaten, die im Rahmen des jeweiligen Vorgangs gelesen oder geschrieben wurden. Informationen zum Lesen der Bereitstellungsprotokolle im Azure-Portal finden Sie unter [Tutorial: Berichterstellung zur automatischen Benutzerkontobereitstellung](./check-status-user-account-provisioning.md).

## <a name="de-provisioning"></a>Aufheben der Bereitstellung
Der Azure AD-Bereitstellungsdienst sorgt für die Synchronisierung von Quell- und Zielsystem, indem die Bereitstellung von Konten aufgehoben wird, wenn Benutzerzugriff entfernt wurde.

Der Bereitstellungsdienst unterstützt sowohl das Löschen als auch das Deaktivieren (manchmal auch als vorläufiges Löschen bezeichnet) von Benutzern. Die genaue Definition von „Deaktivieren“ und „Löschen“ variiert je nach Implementierung der Ziel-App, aber normalerweise ist durch ein „Deaktivieren“ gekennzeichnet, dass sich der Benutzer nicht anmelden kann. Ein „Löschen“ kennzeichnet, dass der Benutzer vollständig aus der Anwendung entfernt wurde. Bei einer SCIM-Anwendungen ist ein Deaktivieren eine Anforderung, die *active*-Eigenschaft für einen Benutzer auf „false“ festzulegen. 

**Konfigurieren Ihrer Anwendung, um einen Benutzer zu deaktivieren**

Vergewissern Sie sich, dass das Kontrollkästchen für Updates aktiviert ist.

Stellen Sie sicher, dass Sie die Zuordnung für *active* für Ihre Anwendung haben. Wenn Sie eine Anwendung aus dem App-Katalog verwenden, kann die Zuordnung etwas unterschiedlich sein. Stellen Sie sicher, dass Sie die standardmäßige/vorgesehene Zuordnung für Kataloganwendungen verwenden.

:::image type="content" source="./media/how-provisioning-works/disable-user.png" alt-text="Deaktivieren eines Benutzers" lightbox="./media/how-provisioning-works/disable-user.png":::


**Konfigurieren Ihrer Anwendung, um einen Benutzer zu löschen**

In den folgenden Szenarien wird ein Deaktivieren oder Löschen ausgelöst: 
* Ein Benutzer wird in Azure AD vorläufig gelöscht (wird in den Papierkorb gesendet, und die AccountEnabled-Eigenschaft wird auf „false“ festgelegt).
    30 Tage nach dem Löschen eines Benutzers in Azure AD wird der Benutzer dauerhaft aus dem Mandanten gelöscht. Dann sendet der Bereitstellungsdienst eine DELETE-Anforderung, um den Benutzer dauerhaft aus der Anwendung zu löschen. Während des Zeitfensters von 30 Tagen können Sie jederzeit [einen Benutzer manuell dauerhaft löschen](../fundamentals/active-directory-users-restore.md), wodurch eine Löschanforderung an die Anwendung gesendet wird.
* Ein Benutzer wird dauerhaft aus dem Papierkorb in Azure AD gelöscht/entfernt.
* Die Zuweisung eines Benutzers zu einer App wird aufgehoben.
* Ein Benutzer wechselt von „im Gültigkeitsbereich“ zu „außerhalb des Gültigkeitsbereichs“ (er durchläuft keinen Bereichsfilter mehr).

:::image type="content" source="./media/how-provisioning-works/delete-user.png" alt-text="Löschen eines Benutzers" lightbox="./media/how-provisioning-works/delete-user.png":::

Standardmäßig werden Benutzer, die sich außerhalb des gültigen Bereichs befinden, vom Azure AD-Bereitstellungsdienst vorläufig gelöscht oder deaktiviert. Wenn Sie dieses Standardverhalten außer Kraft setzen möchten, können Sie ein Flag zum [Überspringen des Löschens von Benutzerkonten außerhalb des gültigen Bereichs](skip-out-of-scope-deletions.md) festlegen.

Wenn eines der obigen vier Ereignisse auftritt und die Zielanwendung vorläufiges Löschen nicht unterstützt, sendet der Bereitstellungsdienst eine DELETE-Anforderung, um den Benutzer dauerhaft aus der App zu löschen.

Wenn Ihre Attributzuordnungen das Attribut „IsSoftDeleted“ enthalten, wird es verwendet, um den Status des Benutzers zu bestimmen und festzulegen, ob eine Aktualisierungsanforderung mit „active = false“ gesendet werden soll, um den Benutzer vorläufig zu löschen.

**Bekannte Einschränkungen**

* Wenn für einen Benutzer, der zuvor vom Bereitstellungsdienst verwaltet wurde, die Zuweisung zu einer App oder einer Gruppe, die einer App zugewiesen ist, aufgehoben wurde, senden wir eine Anforderung zum Deaktivieren. Ab diesem Punkt wird der Benutzer nicht mehr vom Dienst verwaltet, und wir senden keine Löschanforderung, wenn der Benutzer aus dem Verzeichnis gelöscht wird.
* Bereitstellen eines Benutzers, der in Azure AD deaktiviert ist, wird nicht unterstützt. Der Benutzer muss in Azure AD aktiv sein, damit er bereitgestellt werden.
* Wird ein Benutzer von einem vorläufig gelöschten zu einem aktiven Benutzer, aktiviert der Azure AD-Bereitstellungsdienst den Benutzer in der Ziel-App, stellt die Gruppenmitgliedschaften jedoch nicht automatisch wieder her. Die Ziel-App sollte die Gruppenmitgliedschaften für den Benutzer im inaktiven Zustand beibehalten. Wenn dies von der Ziel-App nicht unterstützt wird, können Sie die Bereitstellung neu starten, um die Gruppenmitgliedschaften zu aktualisieren. 

**Empfehlung**

Wenn Sie eine Anwendung entwickeln, sollten in dieser immer sowohl vorläufiges Löschen als auch endgültiges Löschen unterstützt werden. Dies ermöglicht Kunden eine Wiederherstellung, wenn ein Benutzer versehentlich deaktiviert wurde.


## <a name="next-steps"></a>Nächste Schritte

[Planen einer automatischen Benutzerbereitstellung](../app-provisioning/plan-auto-user-provisioning.md)

[Konfigurieren der Bereitstellung für eine Katalog-App](./configure-automatic-user-provisioning-portal.md)

[Erstellen eines SCIM-Endpunkts und Konfigurieren der Bereitstellung beim Erstellen Ihrer eigenen App](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[Beheben von Problemen beim Konfigurieren und Bereitstellen von Benutzern für eine Anwendung](./application-provisioning-config-problem.md)
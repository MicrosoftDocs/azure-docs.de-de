---
title: Bereitstellungsprotokolle im Azure Active Directory-Portal (Vorschau) | Microsoft-Dokumentation
description: Einführung in Bereitstellungsprotokollberichte im Azure Active Directory-Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/07/2020
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2408db2d91740350405f11e2a1250ab9b3a4fe31
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181202"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Bereitstellungsberichte im Azure Active Directory-Portal (Vorschau)

Die Architektur für die Berichterstellung in Azure Active Directory (Azure AD) umfasst die folgenden Komponenten:

- **Aktivität** 
    - **Anmeldungen**: Informationen zur Nutzung von verwalteten Anwendungen und Aktivitäten der Benutzeranmeldung.
    - **Überwachungsprotokolle** - [Überwachungsprotokolle](concept-audit-logs.md) stellen Systemaktivitätsinformationen zu Benutzern und zur Gruppenverwaltung, zu verwalteten Anwendungen und zu Verzeichnisaktivitäten bereit.
    - **Bereitstellungsprotokolle:** enthalten Systemaktivitäten zu Benutzern, Gruppen und Rollen, die mit dem Azure AD-Bereitstellungsdienst bereitgestellt werden. 

- **Security** 
    - **Riskante Anmeldungen**: Eine [riskante Anmeldung](../identity-protection/overview-identity-protection.md) ist ein Indikator für einen Anmeldeversuch von einem Benutzer, der nicht der rechtmäßige Besitzer eines Benutzerkontos ist.
    - **Benutzer mit Risikomarkierung**: Ein [Benutzer mit Risikomarkierung](../identity-protection/overview-identity-protection.md) ist ein Indikator für ein ggf. kompromittiertes Benutzerkonto.

In diesem Thema erhalten Sie einen Überblick über den Bereitstellungsbericht.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="who-can-access-the-data"></a>Wer kann auf die Daten zugreifen?
* Anwendungsbesitzer können Protokolle für Anwendungen in ihrem Besitz anzeigen.
* Benutzer mit den Rollen „Sicherheitsadministrator“, „Sicherheitsleseberechtigter“, „Berichtsleser“, „Anwendungsadministrator“ und „Cloudanwendungsadministrator“
* Globale Administratoren


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Welche Azure AD-Lizenz benötigen Sie für den Zugriff auf Bereitstellungsaktivitäten?

Ihrem Mandanten muss eine Azure AD Premium-Lizenz zugewiesen sein, damit der Gesamtbericht für Bereitstellungsaktivitäten angezeigt werden kann. Unter [Erste Schritte mit Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) erfahren Sie, wie Sie ein Upgrade für Ihre Azure Active Directory-Edition durchführen. 

## <a name="provisioning-logs"></a>Bereitstellungsprotokolle

Die Bereitstellungsprotokolle bieten Antworten auf die folgenden Fragen:

* Welche Gruppen wurden erfolgreich in ServiceNow erstellt?
* Welche Rollen wurden aus Amazon Web Services importiert?
* Welche Benutzer konnten in Dropbox nicht erstellt werden?

Sie können auf die Bereitstellungsprotokolle zugreifen, indem Sie im [Azure-Portal](https://portal.azure.com) auf dem Blatt **Azure Active Directory** im Abschnitt **Überwachung** die Option **Bereitstellungsprotokolle** auswählen. Bei einigen Bereitstellungsdatensätzen kann es bis zu zwei Stunden dauern, bis sie im Portal angezeigt werden.

![Bereitstellungsprotokolle](./media/concept-provisioning-logs/access-provisioning-logs.png "Bereitstellungsprotokolle")


Ein Bereitstellungsprotokoll enthält eine Standardlistenansicht mit folgenden Informationen:

- Identität
- Aktion
- Quellsystem
- Zielsystem
- Status
- Datum


![Standardspalten](./media/concept-provisioning-logs/default-columns.png "Standardspalten")

Sie können die Listenansicht anpassen, indem Sie in der Symbolleiste auf **Spalten** klicken.

![Spaltenauswahl](./media/concept-provisioning-logs/column-chooser.png "Spaltenauswahl")

Sie können dann weitere Felder anzeigen oder Felder entfernen, die bereits angezeigt werden.

![Verfügbare Spalten](./media/concept-provisioning-logs/available-columns.png "Verfügbare Spalten")

Wählen Sie in der Listenansicht ein Element aus, um ausführlichere Informationen zu erhalten.

![Ausführliche Informationen](./media/concept-provisioning-logs/steps.png "Filtern")


## <a name="filter-provisioning-activities"></a>Filtern von Bereitstellungsaktivitäten

Sie können Ihre Bereitstellungsdaten filtern. Einige Filterwerte werden basierend auf Ihrem Mandanten dynamisch aufgefüllt. Wenn im Mandanten beispielsweise keine Erstellungsereignisse vorhanden sind, gibt es keine Filteroptionen für „Erstellen“.
In der Standardansicht können Sie die folgenden Filter auswählen:

- Identity
- Date
- Status
- Aktion


![Hinzufügen von Filtern](./media/concept-provisioning-logs/default-filter.png "Filtern")

Mit dem Filter **Identität** können Sie den Namen oder die Identität angeben, der bzw. die für Sie relevant ist. Diese Identität kann ein Benutzer, eine Gruppe, eine Rolle oder ein anderes Objekt sein. Sie können nach dem Namen oder der ID des Objekts suchen. Die ID variiert je nach Szenario. Bei der Bereitstellung eines Objekts aus Azure AD in Salesforce ist die Quell-ID beispielsweise die Objekt-ID des Benutzers in Azure AD und die Ziel-ID die ID des Benutzers in Salesforce. Wenn die Bereitstellung aus Workday in Active Directory erfolgt, ist die Quell-ID die Mitarbeiter-ID des Workday-Mitarbeiters. Beachten Sie, dass der Name des Benutzers möglicherweise nicht immer in der Spalte „Identität“ enthalten ist. Es gibt jedoch immer eine ID. 


Mit dem Filter **Datum** können Sie einen Zeitrahmen für die zurückgegebenen Daten festlegen.  
Mögliche Werte:

- 1 Monat
- 7 Tage
- 30 Tage
- 24 Stunden
- Benutzerdefiniertes Zeitintervall

Beim Auswählen eines benutzerdefinierten Zeitraums können Sie ein Startdatum und ein Enddatum konfigurieren.


Für den Filter **Zustand** können Sie eine der folgenden Optionen auswählen:

- All
- Erfolg
- Fehler
- Ausgelassen



Mit dem Filter **Aktion** können Sie Folgendes filtern:

- Erstellen 
- Aktualisieren
- Löschen
- Disable
- Andere

Zusätzlich zu den Filtern in der Standardansicht können Sie auch die folgenden Filter festlegen:

- Auftrags-ID
- Zyklus-ID
- Änderungs-ID
- Quell-ID
- Ziel-ID
- Application


![Feld auswählen](./media/concept-provisioning-logs/add-filter.png "Feld auswählen")


- **Auftrags-ID:** Jeder Anwendung, für die Sie die Bereitstellung aktiviert haben, ist eine eindeutige Auftrags-ID zugeordnet.   

- **Zyklus-ID:** eindeutige Identifizierung des Bereitstellungszyklus. Sie können diese ID freigeben, damit nach dem Zyklus gesucht werden kann, in dem das Ereignis aufgetreten ist.

- **Änderungs-ID:** eindeutiger Bezeichner für das Bereitstellungsereignis. Sie können diese ID freigeben, damit nach dem Bereitstellungsereignis gesucht werden kann.   


- **Quellsystem**: Hiermit können Sie angeben, aus welcher Quelle die Identität bereitgestellt wird. Wenn Sie z. B. ein Objekt aus Azure AD in ServiceNow bereitstellen, ist Azure AD das Quellsystem. 

- **Zielsystem**: Hiermit können Sie angeben, in welchem Ziel die Identität bereitgestellt wird. Wenn Sie z. B. ein Objekt aus Azure AD in ServiceNow bereitstellen, ist ServiceNow das Zielsystem. 

- **Anwendung**: Hiermit können Sie nur Datensätze von Anwendungen mit einem Anzeigenamen anzeigen, der eine bestimmte Zeichenfolge enthält.

 

## <a name="provisioning-details"></a>Bereitstellungsdetails 

Wenn Sie ein Element in der Bereitstellungslistenansicht auswählen, erhalten Sie weitere Informationen zu diesem Element.
Die Details werden basierend auf den folgenden Kategorien gruppiert:

- Schritte

- Problembehandlung und Empfehlungen

- Geänderte Eigenschaften

- Zusammenfassung


![Bereitstellungsdetails](./media/concept-provisioning-logs/provisioning-tabs.png "Registerkarten")



### <a name="steps"></a>Schritte

Auf der Registerkarte **Schritte** sind die Schritte zum Bereitstellen eines Objekts aufgeführt. Die Bereitstellung eines Objekts kann aus vier Schritten bestehen: 

- Importieren des Objekts
- Ermitteln, ob das Objekt sich im Bereich befindet
- Abgleichen des Objekts zwischen Quelle und Ziel
- Bereitstellen des Objekts (Ausführen einer Aktion, z. B. Erstellen, Aktualisieren, Löschen oder Deaktivieren)



![Screenshot der Registerkarte „Schritte“ mit den Bereitstellungsschritten.](./media/concept-provisioning-logs/steps.png "Filtern")


### <a name="troubleshoot-and-recommendations"></a>Problembehandlung und Empfehlungen


Auf der Registerkarte **Problembehandlung und Empfehlungen** werden der Fehlercode und der entsprechende Grund angezeigt. Die Fehlerinformationen sind nur im Fall eines Fehlers verfügbar. 


### <a name="modified-properties"></a>Geänderte Eigenschaften

Auf der Registerkarte **Geänderte Eigenschaften** werden der alte und der neue Wert angezeigt. Wenn kein alter Wert vorhanden ist, bleibt die entsprechende Spalte leer. 


### <a name="summary"></a>Zusammenfassung

Die Registerkarte **Zusammenfassung** bietet eine Übersicht über die Vorgänge und die Bezeichner für das Objekt im Quell- und Zielsystem. 

## <a name="what-you-should-know"></a>Wichtige Informationen

- Im Azure-Portal werden gemeldete Bereitstellungsdaten 30 Tage gespeichert, wenn Sie eine Premium-Edition haben, und 7 Tage, wenn Sie eine kostenlose Edition haben. Die Bereitstellungsprotokolle können in [Log Analytics](../app-provisioning/application-provisioning-log-analytics.md) für die Aufbewahrung über 30 Tage hinaus veröffentlicht werden. 

- Sie können das Attribut „Änderungs-ID“ als eindeutigen Bezeichner verwenden. Dies ist beispielsweise bei der Kommunikation mit dem Produktsupport hilfreich.

- Zurzeit gibt es keine Option zum Herunterladen von Bereitstellungsdaten als CSV-Datei. Sie können die Daten jedoch mit [Microsoft Graph](/graph/api/provisioningobjectsummary-list?tabs=http&view=graph-rest-beta) exportieren.

- Möglicherweise werden übersprungene Ereignisse für Benutzer aufgeführt, die nicht dem Gültigkeitsbereich angehören. Dies entspricht dem erwarteten Verhalten, insbesondere dann, wenn der Synchronisierungsbereich auf alle Benutzer und Gruppen festgelegt ist. Der Dienst wertet alle Objekte im Mandanten aus, auch solche, die außerhalb des Gültigkeitsbereichs liegen. 

- Die Bereitstellungsprotokolle sind zurzeit in der Government-Cloud nicht verfügbar. Wenn Sie nicht auf die Bereitstellungsprotokolle zugreifen können, verwenden Sie als temporäre Problemumgehung die Überwachungsprotokolle.  

## <a name="error-codes"></a>Fehlercodes

Anhand der folgenden Tabelle können Sie besser verstehen, wie Sie mögliche Fehler in Bereitstellungsprotokollen beheben. Stellen Sie mithilfe des Links unten auf dieser Seite für alle Fehlercodes, die nicht aufgeführt sind, Feedback bereit. 

|Fehlercode|BESCHREIBUNG|
|---|---|
|Conflict, EntryConflict|Korrigieren Sie die in Konflikt stehenden Attributwerte entweder in Azure AD oder in der Anwendung, oder überprüfen Sie die Konfiguration der übereinstimmenden Attribute, wenn das in Konflikt stehende Benutzerkonto hätte abgeglichen und übernommen werden sollen. Weitere Informationen zum Konfigurieren von übereinstimmenden Attributen finden Sie in der folgenden [Dokumentation](../app-provisioning/customize-application-attributes.md).|
|TooManyRequests|Die Ziel-App hat diesen Versuch zum Aktualisieren des Benutzers abgelehnt, weil sie überlastet ist und zu viele Anforderungen empfängt. Sie brauchen nichts zu tun. Dieser Versuch wird automatisch wiederholt. Außerdem wurde Microsoft über dieses Problem informiert.|
|InternalServerError |Die Ziel-App hat einen unerwarteten Fehler zurückgegeben. Möglicherweise gibt es ein Dienstproblem mit der Zielanwendung, das ein ordnungsgemäßes Funktionieren verhindert. Dieser Versuch wird automatisch in 40 Minuten wiederholt.|
|InsufficientRights, MethodNotAllowed, NotPermitted, Unauthorized| Azure AD konnte sich zwar bei der Zielanwendung authentifizieren, war aber nicht zum Ausführen der Aktualisierung autorisiert. Überprüfen Sie alle Anweisungen, die von der Zielanwendung bereitgestellt werden, sowie das entsprechende [Tutorial](../saas-apps/tutorial-list.md) für die Anwendung.|
|UnprocessableEntity|Die Zielanwendung hat eine unerwartete Antwort zurückgegeben. Die Konfiguration der Zielanwendung ist möglicherweise nicht korrekt, oder es gibt ein Dienstproblem mit der Zielanwendung, das ein ordnungsgemäßes Funktionieren verhindert.|
|WebExceptionProtocolError |Beim Herstellen einer Verbindung mit der Zielanwendung ist ein HTTP-Protokollfehler aufgetreten. Sie brauchen nichts zu tun. Dieser Versuch wird automatisch in 40 Minuten wiederholt.|
|InvalidAnchor|Ein Benutzer, der zuvor vom Bereitstellungsdienst erstellt oder abgeglichen wurde, ist nicht mehr vorhanden. Stellen Sie sicher, dass der Benutzer vorhanden ist. Um einen erneuten Abgleich aller Benutzer zu erzwingen, verwenden Sie die Microsoft Graph-API, um [den Auftrag neu zu starten](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta). Beachten Sie, dass durch den Neustart der Bereitstellung ein Startzyklus auslöst wird, der einige Zeit dauern kann. Außerdem wird der Cache gelöscht, der vom Bereitstellungsdienst für den Betrieb verwendet wird. Dies bedeutet, dass alle Benutzer und Gruppen im Mandanten erneut ausgewertet werden müssen. Möglicherweise werden auch bestimmte Bereitstellungsereignisse gelöscht.|
|NotImplemented | Die Ziel-App hat eine unerwartete Antwort zurückgegeben. Die Konfiguration der App ist möglicherweise nicht korrekt, oder es gibt ein Dienstproblem mit der Ziel-App, das deren ordnungsgemäßes Funktionieren verhindert. Überprüfen Sie alle Anweisungen, die von der Zielanwendung bereitgestellt werden, sowie das entsprechende [Tutorial](../saas-apps/tutorial-list.md) für die Anwendung. |
|MandatoryFieldsMissing, MissingValues |Der Benutzer konnte nicht erstellt werden, da erforderliche Werte fehlten. Korrigieren Sie die fehlenden Attributwerte im Quelldatensatz, oder überprüfen Sie die Konfiguration der übereinstimmenden Attribute, um sicherzustellen, dass die erforderlichen Felder nicht ausgelassen wurden. [Erfahren Sie mehr](../app-provisioning/customize-application-attributes.md) über das Konfigurieren von übereinstimmenden Attributen.|
|SchemaAttributeNotFound |Der Vorgang konnte nicht durchgeführt werden, da ein Attribut angegeben wurde, das in der Zielanwendung nicht vorhanden ist. In der [Dokumentation](../app-provisioning/customize-application-attributes.md) finden Sie Informationen zum Anpassen von Attributen. Stellen Sie außerdem sicher, dass Ihre Konfiguration korrekt ist.|
|InternalError |Im Azure AD-Bereitstellungsdienst ist ein interner Dienstfehler aufgetreten. Sie brauchen nichts zu tun. Dieser Versuch wird automatisch in 40 Minuten wiederholt.|
|InvalidDomain |Der Vorgang konnte aufgrund eines Attributwerts mit einem ungültigen Domänennamen nicht ausgeführt werden. Aktualisieren Sie den Domänennamen für den Benutzer, oder fügen Sie ihn der Liste zulässiger Domänen in der Zielanwendung hinzu. |
|Timeout |Der Vorgang konnte nicht abgeschlossen werden, da die Antwort der Zielanwendung zu lange gedauert hat. Sie brauchen nichts zu tun. Dieser Versuch wird automatisch in 40 Minuten wiederholt.|
|LicenseLimitExceeded|Der Benutzer konnte in der Zielanwendung nicht erstellt werden, da für diesen Benutzer keine Lizenzen verfügbar sind. Erwerben Sie entweder zusätzliche Lizenzen für die Zielanwendung, oder überprüfen Sie die Konfiguration der Benutzerzuweisungen und Attributzuordnungen, um sicherzustellen, dass die richtigen Attribute den richtigen Benutzern zugewiesen sind.|
|DuplicateTargetEntries  |Der Vorgang konnte nicht abgeschlossen werden, da in der Zielanwendung mehr als ein Benutzer mit den konfigurierten übereinstimmenden Attributen gefunden wurde. Entfernen Sie entweder den doppelten Benutzer aus der Zielanwendung, oder konfigurieren Sie die Attributzuordnungen neu, wie [hier](../app-provisioning/customize-application-attributes.md) beschrieben.|
|DuplicateSourceEntries | Der Vorgang konnte nicht abgeschlossen werden, da mehr als ein Benutzer mit den konfigurierten übereinstimmenden Attributen gefunden wurde. Entfernen Sie entweder den doppelten Benutzer, oder konfigurieren Sie die Attributzuordnungen neu, wie [hier](../app-provisioning/customize-application-attributes.md) beschrieben.|
|ImportSkipped | Nachdem jeder Benutzer ausgewertet wurde, wird versucht, die Benutzer aus dem Quellsystem zu importieren. Dieser Fehler tritt häufig auf, wenn für den zu importierenden Benutzer die in den Attributzuordnungen definierte Übereinstimmungseigenschaft fehlt. Wenn für das Benutzerobjekt zum entsprechenden Attribut kein Wert vorhanden ist, können keine Bereichs-, Vergleichs- oder Exportänderungen ausgewertet werden. Beachten Sie, dass dieser Fehler nicht darauf hinweist, dass sich der Benutzer im Gültigkeitsbereich befindet, da der Bereich für den Benutzer noch nicht ausgewertet wurde.|
|EntrySynchronizationSkipped | Der Bereitstellungsdienst hat das Quellsystem erfolgreich abgefragt und den Benutzer identifiziert. Es wurden keine weiteren Aktionen für den Benutzer durchgeführt, und er wurde übersprungen. Dieses Überspringen kann darauf zurückzuführen sein, dass sich der Benutzer außerhalb des Gültigkeitsbereichs befand oder dass er bereits im Zielsystem vorhanden ist und keine Änderungen erforderlich sind.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| Bei einer GET-Anforderung zum Abrufen eines Benutzers oder einer Gruppe haben wir in der Antwort mehrere Benutzer oder Gruppen empfangen. In der Antwort erwarteten wir nur den Empfang eines Benutzers oder einer Gruppe. Wenn wir [beispielsweise](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group) eine GET-Anforderung ausführen, um eine Gruppe abzurufen und einen Filter zum Ausschließen von Mitgliedern bereitzustellen, und Ihr SCIM-Endpunkt die Mitglieder zurückgibt, lösen wir diesen Fehler aus.|

## <a name="next-steps"></a>Nächste Schritte

* [Überprüfen des Status der Benutzerbereitstellung](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Problem beim Konfigurieren der Benutzerbereitstellung für eine Azure AD-Kataloganwendung](../app-provisioning/application-provisioning-config-problem.md)
* [Graph-API für Bereitstellungsprotokolle](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta)
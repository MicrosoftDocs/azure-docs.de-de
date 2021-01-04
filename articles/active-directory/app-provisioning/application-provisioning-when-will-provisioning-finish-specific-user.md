---
title: Ermitteln, wann ein bestimmter Benutzer auf eine App zugreifen kann
description: Ermitteln, wann ein ausgesprochen wichtiger Benutzer auf eine Anwendung zugreifen kann, die Sie für die Benutzerbereitstellung mit Azure AD konfiguriert haben
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/03/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 307a97b71fe453c89617a86a88063e60fcf28fa3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994008"
---
# <a name="check-the-status-of-user-provisioning"></a>Überprüfen des Status der Benutzerbereitstellung

Der Azure AD-Bereitstellungsdienst führt einen ersten Bereitstellungszyklus für das Quellsystem und das Zielsystem aus, dem regelmäßige inkrementelle Zyklen folgen. Bei der Konfiguration der Bereitstellung für eine App können Sie den aktuellen Status des Bereitstellungsdiensts überprüfen und anzeigen, wann ein Benutzer auf eine App zugreifen kann.

## <a name="view-the-provisioning-progress-bar"></a>Anzeigen der Statusanzeige für die Bereitstellung

 Auf der Seite **Bereitstellung** für eine App können Sie den Status des Azure AD-Bereitstellungsdiensts anzeigen. Im Abschnitt **Aktueller Status** unten auf der Seite wird angezeigt, ob ein Bereitstellungszyklus zur Bereitstellung von Benutzerkonten gestartet wurde. Sie können den Status des Zyklus prüfen und sehen, wie viele Benutzer und Gruppen bereitgestellt wurden und wie viele Rollen erstellt werden.

Bei der ersten Konfiguration der automatischen Bereitstellung wird im Abschnitt **Aktueller Status** unten auf der Seite der Status des ersten Bereitstellungszyklus angezeigt. Dieser Abschnitt wird bei jeder Ausführung eines inkrementellen Zyklus aktualisiert. Folgende Details werden angezeigt:
- Der Typ des Bereitstellungszyklus (erster oder inkrementeller Zyklus), der aktuell ausgeführt wird oder zuletzt abgeschlossen wurde.
- Eine **Statusanzeige**, in der der Prozentsatz des abgeschlossenen Bereitstellungszyklus angezeigt wird. Der Prozentsatz gibt die Anzahl der bereitgestellten Seiten an. Beachten Sie, dass jede Seite mehrere Benutzer oder Gruppen enthalten kann, sodass der Prozentsatz nicht direkt der Anzahl der bereitgestellten Benutzer, Gruppen oder Rollen entspricht.
- Die Schaltfläche **Aktualisieren**, über die Sie die Ansicht aktualisieren können.
- Die Anzahl der **Benutzer** und **Gruppen** im Connector-Datenspeicher. Die Anzahl nimmt jedes Mal zu, wenn dem Bereitstellungsbereich ein Objekt hinzugefügt wird. Die Anzahl nimmt nicht ab, wenn ein Benutzer vorläufig oder dauerhaft gelöscht wird, da dabei nicht das Objekt aus dem Connector-Datenspeicher entfernt wird. Die Anzahl wird bei der ersten Synchronisierung nach dem [Zurücksetzen](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta) des Connector-Datenspeichers neu berechnet. 
- Der Link **Überwachungsprotokolle anzeigen**, über den die Azure AD-Bereitstellungsprotokolle mit Details zu allen mit dem Benutzerbereitstellungsdienst ausgeführten Vorgängen geöffnet werden, einschließlich des Bereitstellungsstatus für einzelne Benutzer (siehe Abschnitt [Verwenden von Bereitstellungsprotokollen](#use-provisioning-logs-to-check-a-users-provisioning-status) weiter unten).

Nach Abschluss eines Bereitstellungszyklus wird im Abschnitt **Statistik bis zum heutigen Datum** die kumulierte Anzahl der Benutzer und Gruppen, die bis zum aktuellen Datum bereitgestellt wurden, zusammen mit dem Abschlussdatum und der Dauer des letzten Zyklus angezeigt. Mit der **Aktivitäts-ID** wird der letzte Bereitstellungszyklus eindeutig identifiziert. Die **Auftrags-ID** ist ein eindeutiger Bezeichner für den Bereitstellungsauftrag und bezieht sich auf die App in Ihrem Mandanten.

Der Bereitstellungsstatus kann im Azure-Portal auf der Registerkarte **Azure Active Directory &gt; Unternehmens-Apps &gt; \[Anwendungsname\] &gt; Bereitstellung** angezeigt werden.

![Statusanzeige auf der Seite „Bereitstellung“](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Verwenden von Bereitstellungsprotokollen zum Überprüfen des Bereitstellungsstatus eines Benutzers

Informationen zum Bereitstellungsstatus für einen ausgewählten Benutzer finden Sie in den [Bereitstellungsprotokollen (Vorschau)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) in Azure AD. Alle vom Benutzerbereitstellungsdienst ausgeführten Vorgänge werden in Azure AD in Bereitstellungsprotokollen erfasst. Dies umfasst alle Lese- und Schreibvorgänge in den Quell- und Zielsystemen sowie die Benutzerdaten, die im Rahmen des jeweiligen Vorgangs gelesen oder geschrieben wurden.

Sie können im Azure-Portal auf die Bereitstellungsprotokolle zugreifen. Wählen Sie dazu im Abschnitt **Aktivität** die Option **Azure Active Directory** &gt; **Unternehmensanwendungen** &gt; **Bereitstellungsprotokolle (Vorschau)** aus. Sie können die Bereitstellungsdaten anhand des Benutzernamens oder des Bezeichners entweder im Quell- oder im Zielsystem durchsuchen. Einzelheiten hierzu finden Sie unter [Bereitstellungsprotokolle (Vorschau)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Die Bereitstellungsprotokolle zeichnen alle Vorgänge auf, die vom Bereitstellungsdienst durchgeführt werden, einschließlich der Folgenden:

* Abfragen von Azure AD nach zugewiesenen Benutzern, die sich im Bereitstellungsbereich befinden
* Abfragen der Ziel-App nach dem Vorkommen dieser Benutzer
* Vergleichen der Benutzerobjekte zwischen dem System
* Hinzufügen, Aktualisieren oder Deaktivieren des Benutzerkontos im Zielsystem auf Basis des Vergleichs

Weitere Informationen zum Lesen der Bereitstellungsprotokolle im Azure-Portal finden Sie unter [Tutorial: Berichterstellung zur automatischen Benutzerkontobereitstellung](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Wie lange dauert die Bereitstellung von Benutzern?
Bei Verwendung der automatischen Benutzerbereitstellung mit einer Anwendung stellt Azure AD in regelmäßigen Zeitintervallen (normalerweise alle 40 Minuten) automatisch Benutzerkonten basierend beispielsweise auf der [Benutzer- und Gruppenzuweisung](../manage-apps/assign-user-or-group-access-portal.md) in der App bereit und aktualisiert sie.

Die für die Bereitstellung eines bestimmten Benutzers benötigte Zeit hängt hauptsächlich davon ab, ob mit dem Bereitstellungsauftrag ein Startzyklus oder ein inkrementeller Zyklus durchgeführt wird.

- Bei einem **Startzyklus** hängt die Auftragsdauer von vielen Faktoren ab, beispielsweise von der Anzahl der Benutzer und Gruppen im Bereich für die Bereitstellung und von der Gesamtanzahl der Benutzer und Gruppen im Quellsystem. Die erste Synchronisierung zwischen Azure AD und einer App kann zwischen 20 Minuten und mehreren Stunden dauern. Dies hängt von der Größe des Azure AD-Verzeichnisses und der Anzahl der Benutzer im Bereitstellungsbereich ab. Eine umfassende Liste der Faktoren, die die Leistung von Startzyklen beeinflussen, finden Sie später in diesem Abschnitt.

- **Inkrementelle Zyklen**  nach dem Startzyklus werden in der Regel schneller durchgeführt (z. B. innerhalb von 10 Minuten), da der Bereitstellungsdienst Wasserzeichen speichert, die den Status beider Systeme nach dem Startzyklus darstellen, wodurch die Leistung nachfolgender Synchronisierungen verbessert wird. Die Auftragsdauer hängt davon ab, wie viele Änderungen im jeweiligen Bereitstellungszyklus erkannt werden. Bei weniger als 5.000 Änderungen an Benutzer- oder Gruppenmitgliedschaften kann der Auftrag innerhalb eines einzelnen inkrementellen Bereitstellungszyklus ausgeführt werden. 

Die folgende Tabelle fasst die Synchronisierungsdauer für gängige Bereitstellungsszenarien zusammen. In diesen Szenarien ist das Quellsystem Azure AD und das Zielsystem eine SaaS-Anwendung. Die jeweilige Synchronisierungsdauer ergibt sich aus einer statistischen Analyse der Synchronisierungsaufträge für die SaaS-Anwendungen ServiceNow, Workplace, Salesforce und G Suite.


| Bereichskonfiguration | Benutzer, Gruppen und Mitglieder im Bereich | Dauer des Startzyklus | Dauer des inkrementellen Zyklus |
| -------- | -------- | -------- | -------- |
| Nur zugewiesene Benutzer und Gruppen synchronisieren |  < 1.000 |  < 30 Minuten | < 30 Minuten |
| Nur zugewiesene Benutzer und Gruppen synchronisieren |  1\.000 - 10.000 | 142 - 708 Minuten | < 30 Minuten |
| Nur zugewiesene Benutzer und Gruppen synchronisieren |   10.000 - 100.000 | 1\.170 - 2.340 Minuten | < 30 Minuten |
| Alle Benutzer und Gruppen in Azure AD synchronisieren |  < 1.000 | < 30 Minuten  | < 30 Minuten |
| Alle Benutzer und Gruppen in Azure AD synchronisieren |  1\.000 - 10.000 | < 30 - 120 Minuten | < 30 Minuten |
| Alle Benutzer und Gruppen in Azure AD synchronisieren |  10.000 - 100.000  | 713 - 1.425 Minuten | < 30 Minuten |
| Alle Benutzer in Azure AD synchronisieren|  < 1.000  | < 30 Minuten | < 30 Minuten |
| Alle Benutzer in Azure AD synchronisieren | 1\.000 - 10.000  | 43 - 86 Minuten | < 30 Minuten |

Nur für die Konfiguration **Nur zugewiesene Benutzer und Gruppen synchronisieren** können Sie die folgenden Formeln verwenden, um den ungefähren Mindest- und Höchstwert für die erwartete Dauer des **Startzyklus** zu bestimmen:

- Mindestanzahl von Minuten = 0,01 × [Anzahl zugewiesener Benutzer, Gruppen und Gruppenmitglieder]
- Höchstzahl von Minuten = 0,08 × [Anzahl zugewiesener Benutzer, Gruppen und Gruppenmitglieder]

Zusammenfassung der Faktoren, die sich auf die Dauer bis zum Abschluss eines **Startzyklus** auswirken:

- Die Gesamtanzahl von Benutzern und Gruppen im Bereitstellungsumfang.

- Die Gesamtanzahl von Benutzern, Gruppen und Gruppenmitgliedern im Quellsystem (Azure AD).

- Ob Benutzer im Bereitstellungsumfang mit vorhandenen Benutzern in der Zielanwendung abgeglichen werden oder zum ersten Mal erstellt werden müssen. Synchronisierungsaufträge, für die alle Benutzer zum ersten Mal erstellt werden, dauern etwa *doppelt so lange* wie Synchronisierungsaufträge, für die alle Benutzer mit vorhandenen Benutzern abgeglichen werden.

- Anzahl von Fehlern in den [Bereitstellungsprotokollen](check-status-user-account-provisioning.md). Die Leistung wird zudem beeinträchtigt, wenn viele Fehler auftreten und der Bereitstellungsdienst in den Quarantänezustand versetzt wurde. 

- Fordern Sie die Einschränkung der Datenübertragungsrate an, die vom Zielsystem implementiert wird. Einige Zielsysteme implementieren Anforderungen von Grenzwerten und Einschränkungen der Datenübertragungsrate, die die Leistung bei umfangreichen Synchronisierungsvorgängen beeinträchtigen können. Unter diesen Bedingungen kann eine App, die zu viele Anforderungen zu schnell empfängt, ihre Antwortrate verlangsamen oder die Verbindung trennen. Zur Leistungssteigerung muss der Connector angepasst werden, sodass er die App-Anforderungen nicht schneller sendet, als die App sie verarbeiten kann. Diese Anpassung ist durch die Bereitstellung von Connectors möglich, die von Microsoft erstellt wurden. 

- Die Anzahl und Größe der zugewiesenen Gruppen. Das Synchronisieren zugewiesener Gruppen dauert länger als das Synchronisieren von Benutzern. Sowohl die Anzahl als auch die Größe der zugewiesenen Gruppen beeinflussen die Leistung. Wenn für eine Anwendung [Zuordnungen für die Synchronisierung von Gruppenobjekten aktiviert sind](customize-application-attributes.md#editing-group-attribute-mappings), werden zusätzlich zu den Benutzern auch Gruppeneigenschaften wie Gruppennamen und Mitgliedschaften synchronisiert. Diese zusätzlichen Synchronisierungen dauern länger als die ausschließliche Synchronisierung von Benutzerobjekten.

- Wenn die Leistung zu einem Problem wird und Sie versuchen, die Mehrzahl der Benutzer und Gruppen in Ihrem Mandanten bereitzustellen, verwenden Sie Bereichsfilter. Mit Bereichsfiltern können Sie die Daten, die der Bereitstellungsdienst aus Azure AD extrahiert, fein abstimmen, indem Sie Benutzer anhand bestimmter Attributwerte herausfiltern. Weitere Informationen zu Bereichsfiltern finden Sie unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Nächste Schritte
[Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](user-provisioning.md)
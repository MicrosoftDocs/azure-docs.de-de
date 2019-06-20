---
title: Meldung zur automatischen Benutzerkontobereitstellung in Azure Active Directory für SaaS-Anwendungen | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie den Status von Aufträgen zu automatischen Benutzerkontobereitstellungen überprüfen und Probleme bei der Bereitstellung einzelner Benutzer behandeln.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70ca1e2f4fd831619cc3cd443d98018a35f4e1ef
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65963078"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Tutorial: Berichterstellung zur automatischen Benutzerkontobereitstellung


Azure Active Directory beinhaltet einen [Dienst zur Benutzerkontobereitstellung](user-provisioning.md), mit dem die Bereitstellung bzw. die Aufhebung der Bereitstellung von Benutzerkonten in SaaS-Apps und anderen Systemen zur nahtlosen Verwaltung des Identitätslebenszyklus automatisiert werden kann. Azure AD unterstützt vorab integrierte Benutzerbereitstellungsconnectors für alle Anwendungen und Systeme im Abschnitt „Ausgewählt“ des [Azure AD-Anwendungskatalogs](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

In diesem Artikel wird beschrieben, wie der Status von Bereitstellungsaufträgen nach ihrer Einrichtung überprüft wird, und Probleme bei der Bereitstellung einzelner Benutzer und Gruppen behandelt werden.

## <a name="overview"></a>Übersicht

Bereitstellungsconnectors werden über das [Azure-Portal](https://portal.azure.com) gemäß der [bereitgestellten Dokumentation](../saas-apps/tutorial-list.md) für die unterstützte Anwendung eingerichtet und konfiguriert. Sobald sie konfiguriert wurden und ausgeführt werden, können mit einer der beiden folgenden Methoden Berichte für Bereitstellungsaufträge genutzt werden:

* **Azure-Verwaltungsportal** – In diesem Artikel wird in erster Linie das Abrufen von Berichtsinformationen über das [Azure-Portal](https://portal.azure.com) beschrieben, das einen Zusammenfassungsbericht für die Bereitstellung sowie ausführliche Bereitstellungsüberwachungsprotokolle für eine bestimmte Anwendung bietet.

* **Überwachungs-API** – Azure Active Directory stellt auch eine Überwachungs-API bereit, die den programmgesteuerten Abruf von detaillierten Überwachungsprotokollen für die Bereitstellung ermöglicht. Dokumentation speziell für die Verwendung dieser API finden Sie unter [Referenz zur Überwachungs-API von Azure Active Directory](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit). Zwar wird in diesem Artikel nicht speziell die Verwendung der API behandelt, jedoch werden die Arten von Bereitstellungsereignissen, die im Überwachungsprotokoll erfasst werden, erläutert.

### <a name="definitions"></a>Definitionen

In diesem Artikel werden die folgenden Bezeichnungen verwendet, die wie folgt definiert sind:

* **Quellsystem** – Das Repository von Benutzern, von dem aus der Azure AD-Bereitstellungsdienst synchronisiert. Azure Active Directory ist das Quellsystem für die meisten vorab integrierten Bereitstellungsconnectors, es gibt jedoch einige Ausnahmen (z. B. Workday Inbound Synchronization).

* **Zielsystem** – Das Repository von Benutzern, in dem der Azure AD-Bereitstellungsdienst synchronisiert. Dies ist in der Regel eine SaaS-Anwendung (z. B. Salesforce, ServiceNow, Google Apps, Dropbox für Unternehmen), in manchen Fällen kann es sich jedoch um ein lokales System wie Active Directory handeln (z. B. Workday Inbound Synchronization in Active Directory).


## <a name="getting-provisioning-reports-from-the-azure-management-portal"></a>Abrufen von Bereitstellungsberichten aus dem Azure-Verwaltungsportal

Um Informationen zu Bereitstellungsberichten für eine bestimmte Anwendung abzurufen, starten Sie zunächst das [Azure-Verwaltungsportal](https://portal.azure.com), und durchsuchen Sie die Unternehmensanwendung, für die die Bereitstellung konfiguriert wurde. Wenn Sie beispielsweise Benutzer für LinkedIn Elevate bereitstellen, lautet der Navigationspfad zu den Anwendungsdetails wie folgt:

**Azure Active Directory &gt; Unternehmensanwendungen &gt; Alle Anwendungen &gt; LinkedIn Elevate**

Über diese Ansicht können Sie sowohl auf den Zusammenfassungsbericht für die Bereitstellung als auch die Überwachungsprotokolle für die Bereitstellung zugreifen, wie im Folgenden beschrieben wird.


## <a name="provisioning-summary-report"></a>Zusammenfassungsbericht für die Bereitstellung

Der Zusammenfassungsbericht für die Bereitstellung wird auf der Registerkarte **Bereitstellung** für die jeweilige Anwendung angezeigt. Diese befindet sich im Abschnitt **Synchronisierungsdetails** unterhalb von **Einstellungen** und enthält folgende Informationen:

* Die Gesamtanzahl von Benutzern und/oder -gruppen, die synchronisiert wurden und derzeit von der Bereitstellung zwischen dem Quell- und Zielsystem eingeschlossen werden.

* Der Zeitpunkt, an dem die Synchronisierung zuletzt ausgeführt wurde. Synchronisierungen werden nach Abschluss einer [Erstsynchronisierung](user-provisioning.md#what-happens-during-provisioning) in der Regel alle 20 bis 40 Minuten durchgeführt.

* Die Angabe, ob eine [Erstsynchronisierung](user-provisioning.md#what-happens-during-provisioning) durchgeführt wurde.

* Die Angabe, ob der Bereitstellungsprozess in Quarantäne gesetzt wurde, und den Grund für den Quarantänestatus (etwa Fehler bei der Kommunikation mit dem Zielsystem aufgrund von ungültigen Administratoranmeldeinformationen).

Um die Betriebsintegrität des Zusammenfassungsberichts für die Bereitstellung zu überprüfen, sollten Administratoren zuerst im Zusammenfassungsbericht für die Bereitstellung nachsehen.

 ![Zusammenfassungsbericht](./media/check-status-user-account-provisioning/summary_report.PNG)

## <a name="provisioning-audit-logs"></a>Bereitstellung von Überwachungsprotokollen
Alle Aktivitäten, die vom Bereitstellungsdienst ausgeführt werden, werden in den Azure AD-Überwachungsprotokollen erfasst. Diese können über die Registerkarte **Überwachungsprotokolle** in der Kategorie **Kontobereitstellung** angezeigt werden. Zu den protokollierten Aktivitätsereignistypen zählen Folgende:

* **Ereignisse importieren** – Ein Importereignis wird immer dann erfasst, wenn der Azure AD-Bereitstellungsdienst Informationen über einen einzelnen Benutzer oder eine Gruppe aus einem Quell- oder Zielsystem abruft. Während der Synchronisierung werden Benutzer zuerst aus dem Quellsystem abgerufen, wobei die aufgezeichneten Ergebnisse als Importereignisse erfasst werden. Die übereinstimmenden IDs der abgerufenen Benutzer werden dann mit dem Zielsystem abgeglichen, um zu überprüfen, ob sie vorhanden sind. Die Ergebnisse werden ebenfalls als Importereignisse erfasst. Diese Ereignisse erfassen alle zugeordneten Benutzerattribute und deren Werte, die vom Azure AD-Bereitstellungsdienst zum Zeitpunkt des Ereignisses aufgetreten sind. 

* **Synchronisierungsregelereignisse** – Diese Ereignisse melden die Ergebnisse der Attributzuordnungsregeln und aller konfigurierten Bereichsfilter, nachdem Benutzerdaten aus dem Quell- und Zielsystem importiert und ausgewertet wurden. Wenn ein Benutzer in einem Quellsystem beispielsweise als für die Bereitstellung einzuschließen gilt und nicht im Zielsystem vorhanden ist, meldet dieses Ereignis dann, dass der Benutzer im Zielsystem bereitgestellt wird. 

* **Ereignisse exportieren** – Ein Exportereignis wird immer dann erfasst, wenn der Azure AD-Bereitstellungsdienst ein Benutzerkonto- oder -gruppenobjekt in ein Zielsystem schreibt. Diese Ereignisse erfassen alle Benutzerattribute und deren Werte, die vom Azure AD-Bereitstellungsdienst zum Zeitpunkt des Ereignisses geschrieben wurden. Wenn beim Schreiben des Benutzerkonto- oder -gruppenobjekts in das Zielsystem ein Fehler aufgetreten ist, wird dieser hier angezeigt.

* **Prozesshinterlegungsereignisse** – Prozesshinterlegungen treten auf, wenn der Bereitstellungsdienst bei dem Versuch, einen Vorgang durchzuführen, einen Fehler feststellt und beginnt, den Vorgang in einem Backoffzeitintervall zu wiederholen. Ein Hinterlegungsereignis wird immer dann erfasst, wenn ein Bereitstellungsvorgang erneut versucht wurde.

Bei Bereitstellungsereignissen für einen einzelnen Benutzer treten die Ereignisse in der Regel in folgender Reihenfolge auf:

1. Importereignis: Der Benutzer wird aus dem Quellsystem abgerufen.

2. Importereignis: Das Zielsystem wird abgefragt, um das Vorhandensein des abgerufenen Benutzers zu überprüfen.

3. Synchronisierungsregelereignis: Anhand der konfigurierten Attributzuordnungsregeln und Bereichsfilter werden Benutzerdaten aus Quell- und Zielsystem ausgewertet, um festzustellen, welche Aktion ggf. ausgeführt werden soll.

4. Exportereignis: Wenn das Synchronisierungsregelereignis vorschreibt, dass eine Aktion ausgeführt werden soll (Hinzufügen, Aktualisieren, Löschen), werden die Ergebnisse der Aktion als Exportereignis erfasst.

![Erstellen eines Azure AD-Testbenutzers](./media/check-status-user-account-provisioning/audit_logs.PNG)


### <a name="looking-up-provisioning-events-for-a-specific-user"></a>Suchen nach Bereitstellungsereignissen für einen bestimmten Benutzer

Der häufigste Anwendungsfall für Überwachungsprotokolle für Bereitstellungen ist die Überprüfung des Bereitstellungsstatus eines einzelnen Benutzerkontos. So suchen Sie nach den letzten Bereitstellungsereignissen für einen bestimmten Benutzer:

1. Navigieren Sie zum Abschnitt **Überwachungsprotokolle**.

2. Wählen Sie im Menü **Kategorie** die Option **Kontobereitstellung** aus.

3. Wählen Sie im Menü **Datumsbereich** den Datumsbereich aus, in dem die Suche durchgeführt werden soll.

4. Geben Sie in die **Suchleiste** die Benutzer-ID des Benutzers ein, nach dem gesucht werden soll. Das Format des ID-Werts muss dem Wert entsprechen, den Sie als entsprechende primäre ID in der Attributzuordnungskonfiguration (etwa „userPrincipalName“ oder Personalnummer des Mitarbeiters) ausgewählt haben. Der erforderliche ID-Wert wird in der Spalte „Ziel(e)“ angezeigt.

5. Drücken Sie die Eingabetaste, um den Suchvorgang zu starten. Die neuesten Bereitstellungsereignisse werden zuerst zurückgegeben.

6. Beachten Sie beim Zurückgeben der Ereignisse die Aktivitätstypen und darauf, ob diese erfolgreich waren oder fehlgeschlagen sind. Wenn keine Ergebnisse zurückgegeben werden, bedeutet dies, dass der Benutzer entweder nicht vorhanden ist oder noch nicht vom Bereitstellungsprozess erkannt wurde, wenn noch keine vollständige Synchronisierung durchgeführt wurde.

7. Klicken Sie auf die einzelnen Ereignisse, um erweiterte Details anzuzeigen, einschließlich aller Benutzereigenschaften, die im Rahmen des Ereignisses abgerufen, ausgewertet oder geschrieben wurden.

Die Verwendung der Überwachungsprotokolle wird im folgenden Video veranschaulicht. Überwachungsprotokolle werden ca. ab 5:30 präsentiert:

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>Tipps zum Anzeigen der Überwachungsprotokolle für die Bereitstellung

Wählen Sie der Übersichtlichkeit halber im Azure-Portal die Schaltfläche **Spalten** aus, und anschließend folgende Spalten:

* **Datum** – Gibt das Datum an, an dem das Ereignis aufgetreten ist.
* **Ziel(e)** – Gibt den App-Namen und die Benutzer-ID an, auf die sich das Ereignis bezieht.
* **Aktivität** – Der zuvor beschriebene Aktivitätstyp.
* **Status** – Gibt an, ob das Ereignis erfolgreich war oder fehlgeschlagen ist.
* **Statusgrund** – Ein Überblick über die Vorgänge im Bereitstellungsereignis.


## <a name="troubleshooting"></a>Problembehandlung

Der Zusammenfassungsbericht und die Überwachungsprotokolle für die Bereitstellung spielen eine wichtige Rolle bei der Behandlung verschiedener Probleme in Bezug auf die Benutzerkontobereitstellung.

Einen szenariobasierten Leitfaden zur Problembehandlung bei der automatischen Benutzerbereitstellung finden Sie unter [Probleme bei der Konfiguration und Bereitstellung von Benutzern für eine Anwendung](application-provisioning-config-problem.md).


## <a name="additional-resources"></a>Weitere Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](what-is-single-sign-on.md)

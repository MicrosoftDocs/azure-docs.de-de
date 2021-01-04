---
title: Problem beim Konfigurieren der Benutzerbereitstellung für eine Azure AD-Katalog-App
description: Problembehandlung für häufige Probleme beim Konfigurieren der Benutzerbereitstellung für eine bereits im Azure AD-Anwendungskatalog aufgeführte Anwendung
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/03/2019
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 306c3771c0392bbc97260a726e153cfd385cebcd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994790"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problem beim Konfigurieren der Benutzerbereitstellung für eine Azure AD-Kataloganwendung

Das Konfigurieren der [automatischen Benutzerbereitstellung](user-provisioning.md) für eine App (sofern unterstützt) erfordert, dass bestimmte Anweisungen befolgt werden, um die Anwendung auf die automatische Bereitstellung vorzubereiten. Dann können Sie das Azure-Portal dazu verwenden, um den Bereitstellungsdienst für die Synchronisierung von Benutzerkonten mit der Anwendung zu konfigurieren.

Sie sollten immer damit beginnen, das zum Einrichten der Bereitstellung für Ihre Anwendung spezifische Setuptutorial zu suchen. Befolgen Sie dann die Schritte zum Konfigurieren der App und von Azure AD, um die Verbindung für die Bereitstellung zu erstellen. Eine Liste mit App-Tutorials finden Sie unter [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](../saas-apps/tutorial-list.md).

## <a name="how-to-see-if-provisioning-is-working"></a>Ermitteln, ob die Bereitstellung funktioniert 

Sobald der Dienst konfiguriert ist, erhalten Sie die meisten Einblicke in die Ausführung des Diensts an zwei Stellen:

-   **Bereitstellungsprotokolle (Vorschau)** : Die [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) zeichnen alle vom Bereitstellungsdienst durchgeführten Vorgänge auf, einschließlich der Abfragen von Azure AD für zugewiesene Benutzer, die sich im Bereitstellungsbereich befinden. Fragen Sie die Ziel-App hinsichtlich dem Vorkommen dieser Benutzer ab, wobei die Benutzerobjekte zwischen dem System verglichen werden. Fügen Sie dann auf Basis des Vergleichs das Benutzerkonto zum Zielsystem hinzu oder aktualisieren bzw. deaktivieren Sie es. Sie können im Azure-Portal auf die Bereitstellungsprotokolle zugreifen. Wählen Sie dazu im Abschnitt **Aktivität** die Option **Azure Active Directory** &gt; **Unternehmensanwendungen** &gt; **Bereitstellungsprotokolle (Vorschau)** aus.

-   **Aktueller Status**: Eine Zusammenfassung des letzten Bereitstellungsdurchlaufs für eine bestimmte App wird im Abschnitt **Azure Active Directory &gt; Unternehmens-Apps &gt; \[Anwendungsname\] &gt; Bereitstellung** am unteren Bildschirmrand unter den Diensteinstellungen angezeigt. Im Abschnitt „Aktueller Status“ wird angezeigt, ob ein Bereitstellungszyklus zur Bereitstellung von Benutzerkonten gestartet wurde. Sie können den Status des Zyklus prüfen und sehen, wie viele Benutzer und Gruppen bereitgestellt wurden und wie viele Rollen erstellt werden. Einzelheiten zu evtl. Fehlern finden Sie in den Bereitstellungsprotokollen (../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Zu berücksichtigende allgemeine Problembereiche bei der Bereitstellung

Nachfolgend finden Sie eine Liste der allgemeinen Problembereiche, für die Sie Detailinformationen anzeigen können, wenn Sie wissen, wo Sie starten müssen.

* [Bereitstellungsdienst wird anscheinend nicht gestartet](#provisioning-service-does-not-appear-to-start)
* Konfiguration kann aufgrund nicht funktionierender Anmeldeinformationen für die App nicht gespeichert werden
* [Bereitstellungsprotokolle besagen, dass Benutzer „übersprungen“ und nicht bereitgestellt wurden, obwohl sie zugewiesen sind](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Bereitstellungsdienst wird anscheinend nicht gestartet

Wenn Sie für **Bereitstellungsstatus** im Abschnitt **Azure Active Directory &gt; Unternehmens-Apps &gt; \[Anwendungsname\] &gt;Bereitstellung** des Azure-Portals die Option **Ein** festlegen. Auf dieser Seite werden jedoch nach anschließendem erneuten Laden keine anderen Statusdetails angezeigt. Es ist wahrscheinlich, dass der Dienst ausgeführt wird, aber noch keinen Startzyklus abgeschlossen hat. Überprüfen Sie die oben beschriebenen **Bereitstellungsprotokolle**, um zu ermitteln, welche Vorgänge der Dienst durchführt und ob Fehler aufgetreten sind.

>[!NOTE]
>Ein Startzyklus kann zwischen 20 Minuten und mehreren Stunden dauern. Dies hängt von der Größe des Azure AD-Verzeichnisses und der Anzahl der Benutzer im Bereitstellungsbereich ab. Nachfolgende Synchronisierungen nach dem Startzyklus werden schneller durchgeführt, weil der Bereitstellungsdienst Wasserzeichen speichert, die den Status beider Systeme nach dem Startzyklus darstellen. Dadurch verbessert sich die Leistung nachfolgender Synchronisierungen.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Konfiguration kann aufgrund nicht funktionierender Anmeldeinformationen für die App nicht gespeichert werden

Damit die Bereitstellung funktioniert, erfordert Azure gültige Anmeldeinformationen, mit denen eine Verbindung zu einer Benutzerverwaltungs-API hergestellt werden kann, die von dieser App bereitgestellt wird. Wenn diese Anmeldeinformationen nicht funktionieren oder Sie sie nicht kennen, lesen Sie das Tutorial zum Einrichten dieser App, die zuvor beschrieben wurde.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Bereitstellungsprotokolle besagen, dass Benutzer „übersprungen“ und nicht bereitgestellt wurden, obwohl sie zugewiesen sind

Wenn ein Benutzer in den Bereitstellungsprotokollen als „übersprungen“ angezeigt wird, ist es sehr wichtig, die erweiterten Details in der Protokollmeldung zu lesen, um die Ursache zu ermitteln. Nachfolgend sind häufige Ursachen und Lösungen aufgeführt:

- **Es wurde ein Bereichsfilter konfiguriert,** **der einen Benutzer basierend auf einem Attributwert herausfiltert.** Weitere Informationen finden Sie unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

- **Der Benutzer ist „nicht wirklich berechtigt“.** Wenn diese bestimmte Fehlermeldung angezeigt wird, liegt ein Problem mit dem Benutzerzuweisungseintrag vor, der in Azure AD gespeichert wird. Heben Sie die Zuweisung des Benutzers (oder der Gruppe) zur App auf, und weisen Sie ihn (oder sie) erneut zu, um dieses Problem zu beheben. Weitere Informationen finden Sie unter [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md).

- **Ein erforderliches Attribut fehlt oder wurde für einen Benutzer nicht ausgefüllt.** Es ist beim Einrichten der Bereitstellung wichtig zu beachten, dass die Attributzuordnungen und Workflows überprüft und konfiguriert werden, die festlegen, welche Benutzereigenschaften (oder Gruppeneigenschaften) von Azure AD zur Anwendung gelangen. Dazu zählt das Festlegen der „übereinstimmenden Eigenschaft“, mit der Benutzer/Gruppen zwischen den beiden Systemen eindeutig identifiziert und abgeglichen werden. Weitere Informationen zu diesem wichtigen Prozess finden Sie unter [Anpassen von Attributzuordnungen für die Benutzerbereitstellung für SaaS-Anwendungen in Azure Active Directory](../app-provisioning/customize-application-attributes.md).

  * **Attributzuordnungen für Gruppen:** Die Bereitstellung von Gruppennamen und Gruppendetails zusätzlich zu den Mitgliedern wird für einige Anwendungen unterstützt. Sie können diese Funktionen aktivieren oder deaktivieren, indem Sie die **Zuordnung** für Gruppenobjekte aktivieren oder deaktivieren, die auf der Registerkarte **Bereitstellung** angezeigt wird. Wenn die Bereitstellung von Gruppen aktiviert ist, überprüfen Sie auf jeden Fall die Attributzuordnungen, um sicherzustellen, dass ein entsprechendes Feld für die „übereinstimmende ID“ verwendet wird. Dies kann der Anzeigename oder der E-Mail-Alias sein, da die Gruppe und ihre Mitglieder nicht bereitgestellt werden, wenn die entsprechende Eigenschaft leer ist oder nicht für eine Gruppe in Azure AD gefüllt wurde.

## <a name="next-steps"></a>Nächste Schritte
[Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](user-provisioning.md)

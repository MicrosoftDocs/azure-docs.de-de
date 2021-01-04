---
title: Verwalten von Einstellungen und Voreinstellungen im Azure-Portal
description: Sie können die Standardeinstellungen des Azure-Portals ändern, um sie an Ihre eigenen Vorlieben anzupassen. Zu den Einstellungen gehören das Timeout inaktiver Sitzungen, die Standardansicht, der Menümodus, der Kontrast, das Design, Benachrichtigungen sowie Sprache und regionale Formate.
keywords: Einstellungen, Timeout, Sprache, regional
ms.date: 08/05/2020
ms.topic: how-to
ms.openlocfilehash: 07dd3eb5cb132adf87852f29d867be772294d189
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745637"
---
# <a name="manage-azure-portal-settings-and-preferences"></a>Verwalten von Einstellungen und Voreinstellungen im Azure-Portal

Sie können die Standardeinstellungen des Azure-Portals ändern, um sie an Ihre eigenen Vorlieben anzupassen. Die meisten Einstellungen stehen im Menü **Einstellungen** in der globalen Kopfzeile zur Verfügung.

![Screenshot, der globale Kopfzeilensymbole mit hervorgehobenen Einstellungen zeigt](./media/set-preferences/header-settings.png)


## <a name="choose-your-default-subscription"></a>Auswählen Ihres Standardabonnements

Sie können das Abonnement ändern, das bei der Anmeldung beim Azure-Portal standardmäßig geöffnet wird. Dies ist nützlich, wenn Sie mit einem primären Abonnement arbeiten, aber gelegentlich andere verwenden. 

:::image type="content" source="media/set-preferences/filter-subscription-default-view.png" alt-text="Filtern Sie die Ressourcenliste nach dem Abonnement.":::

1. Wählen Sie das Symbol für den Verzeichnis- und Abonnementfilter in der globalen Kopfzeile aus.

1. Wählen Sie die Abonnements aus, die beim Starten des Portals als Standardabonnements ausgewählt werden sollen. 

    :::image type="content" source="media/set-preferences/default-directory-subscription-filter.png" alt-text="Wählen Sie die Abonnements aus, die beim Starten des Portals als Standardabonnements ausgewählt werden sollen."::: 


## <a name="choose-your-default-view"></a>Auswählen der Standardansicht 

Sie können die Seite ändern, die bei der Anmeldung beim Azure-Portal standardmäßig geöffnet wird.

![Screenshot mit Azure-Portaleinstellungen mit hervorgehobener Standardansicht](./media/set-preferences/default-view.png)

- Die **Startseite** kann nicht angepasst werden.  Sie zeigt Verknüpfungen mit beliebten Azure-Diensten an und listet die Ressourcen auf, die Sie zuletzt verwendet haben. Außerdem werden nützliche Links zu Ressourcen wie Microsoft Learn und der Azure-Roadmap bereitgestellt.

- Dashboards können angepasst werden, um einen Arbeitsbereich zu erstellen, der nur für Sie konzipiert wurde. Beispielsweise können Sie ein Dashboard erstellen, das sich auf Projekte, Aufgaben oder Rollen konzentriert. Wenn Sie **Dashboard** auswählen, verwendet Ihre Standardansicht das zuletzt verwendete Dashboard. Weitere Informationen finden Sie unter [Erstellen und Freigeben von Dashboards im Azure-Portal](azure-portal-dashboards.md).

## <a name="choose-a-portal-menu-mode"></a>Auswählen eines Portalmenümodus

Der Standardmodus für das Portalmenü steuert, wie viel Platz das Portalmenü auf der Seite einnimmt.

![Screenshot der Festlegung des Standardmodus für das Portalmenü](./media/set-preferences/menu-mode.png)

- Wenn sich das Portalmenü im **Flyoutmodus** befindet, wird es ausgeblendet, bis Sie es benötigen. Wählen Sie das Menüsymbol aus, um das Menü zu öffnen oder zu schließen.

- Wenn Sie für das Portalmenü den **angedockten Modus** auswählen, wird es immer angezeigt. Sie können das Menü reduzieren, um den Arbeitsbereich zu vergrößern.

## <a name="choose-a-theme-or-enable-high-contrast"></a>Auswählen eines Designs oder Aktivieren des hohen Kontrasts

Das Design, das Sie auswählen, wirkt sich auf die Hintergrund- und Schriftartfarben aus, die im Azure-Portal angezeigt werden. Sie können eines der vier vordefinierten Farbdesigns auswählen. Wählen Sie die einzelnen Miniaturansichten aus, um das Design zu ermitteln, das Ihnen am besten gefällt.

Alternativ können Sie eines der Designs mit hohem Kontrast auswählen. Die Designs mit hohem Kontrast verbessern die Lesbarkeit des Azure-Portals für Benutzer mit einer Sehbehinderung und haben Vorrang vor jeder anderen Designauswahl.

![Screenshot mit Azure-Portaleinstellungen mit hervorgehobenen Designs](./media/set-preferences/theme.png)

## <a name="enable-or-disable-pop-up-notifications"></a>Aktivieren oder Deaktivieren von Popupbenachrichtigungen

Benachrichtigungen sind Systemmeldungen, die sich auf Ihre aktuelle Sitzung beziehen. Sie enthalten Informationen wie Ihr aktuelles Guthaben, wann die von Ihnen aktuell erstellten Ressourcen verfügbar werden oder bestätigen beispielsweise Ihre letzte Aktion. Wenn Popupbenachrichtigungen aktiviert sind, werden die Meldungen kurzzeitig in der oberen Ecke des Bildschirms angezeigt. 

Wählen Sie **Popupbenachrichtigungen aktivieren** aus oder ab, um Popupbenachrichtigungen zu aktivieren oder zu deaktivieren.

![Screenshot mit Azure-Portaleinstellungen mit hervorgehobenen Popupbenachrichtigungen](./media/set-preferences/popup-notifications.png)

Wenn Sie alle während der aktuellen Sitzung empfangenen Benachrichtigungen lesen möchten, wählen Sie **Benachrichtigungen** aus der globalen Kopfzeile aus.

![Screenshot, der die globale Kopfzeile des Azure-Portals mit hervorgehobenen Benachrichtigungen zeigt](./media/set-preferences/read-notifications.png)

Wenn Sie Benachrichtigungen aus vorherigen Sitzungen lesen möchten, suchen Sie nach Ereignissen im Aktivitätsprotokoll. Weitere Informationen finden Sie unter [Anzeigen des Aktivitätsprotokolls](../azure-monitor/platform/activity-log.md#view-the-activity-log). 

## <a name="change-the-inactivity-timeout-setting"></a>Ändern der Einstellung für das Inaktivitätstimeout

Die Einstellung für das Inaktivitätstimeout schützt Ihre Ressourcen vor nicht autorisiertem Zugriff, wenn Sie vergessen, ihre Arbeitsstation abzusichern. Nachdem Sie eine Weile inaktiv waren, werden Sie automatisch von Ihrer Azure-Portalsitzung abgemeldet. Als Einzelbenutzer können Sie die Timeouteinstellung für sich selbst ändern. Wenn Sie ein Administrator sind, können Sie sie auf Verzeichnisebene für alle Benutzer im Verzeichnis festlegen.

### <a name="change-your-individual-timeout-setting-user"></a>Ändern der eigenen Timeouteinstellung (Benutzer)

Wählen Sie die Dropdownliste unter **Bei Inaktivität abmelden** aus. Wählen Sie die Dauer aus, nach der die Azure-Portalsitzung abgemeldet wird, wenn Sie inaktiv sind.

![Screenshot der Portaleinstellungen mit hervorgehobenen Timeouteinstellungen bei Inaktivität](./media/set-preferences/inactive-signout-user.png)

Die Änderung wird automatisch gespeichert. Wenn Sie inaktiv sind, meldet sich Ihre Azure-Portalsitzung nach der von Ihnen festgelegten Dauer ab.

Wenn Ihr Administrator eine Richtlinie für das Inaktivitätstimeout aktiviert hat, können Sie trotzdem einen eigenen Wert festlegen, sofern dieser kleiner als die Einstellung auf Verzeichnisebene ist. Wählen Sie **Timeoutrichtlinie bei Verzeichnisinaktivität außer Kraft setzen** aus, und legen Sie dann ein Zeitintervall fest.

![Screenshot, der Portaleinstellungen mit hervorgehobener Option „Timeoutrichtlinie bei Verzeichnisinaktivität außer Kraft setzen“ zeigt](./media/set-preferences/inactive-signout-override.png)

### <a name="change-the-directory-timeout-setting-admin"></a>Ändern der Timeouteinstellung auf Verzeichnisebene (Administrator)

Administratoren in der [globalen Administratorrolle](../active-directory/roles/permissions-reference.md#global-administrator--company-administrator) können die maximale Leerlaufzeit erzwingen, nach der eine Sitzung abgemeldet wird. Die Einstellung für das Inaktivitätstimeout wird auf Verzeichnisebene angewandt. Diese Einstellung ist für neue Sitzungen wirksam. Für Benutzer, die bereits angemeldet sind, wird sie nicht sofort angewandt. Weitere Informationen zu Verzeichnissen finden Sie unter [Übersicht über Active Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

Wenn Sie ein globaler Administrator sind und eine Einstellung für das Leerlauftimeout für alle Benutzer des Azure-Portals erzwingen möchten, führen Sie die folgenden Schritte aus:

1. Wählen Sie den Linktext **Timeout auf Verzeichnisebene konfigurieren** aus.

    ![Screenshot der Portaleinstellungen mit hervorgehobenem Linktext](./media/set-preferences/settings-admin.png)

1. Wählen Sie auf der Seite **Timeout für Inaktivität auf Verzeichnisebene konfigurieren** die Option **Leerlauftimeout auf Verzeichnisebene für Azure-Portal aktivieren** aus, um die Einstellung zu aktivieren.

1. Geben Sie als Nächstes die **Stunden** und die **Minuten** für die maximale Zeit ein, die sich ein Benutzer im Leerlauf befinden darf, bevor die Sitzung automatisch abgemeldet wird.

1. Wählen Sie **Übernehmen**.

    ![Screenshot der Seite zum Festlegen des Inaktivitätstimeouts auf Verzeichnisebene](./media/set-preferences/configure.png)

Um zu überprüfen, ob die Richtlinie für das Inaktivitätstimeout ordnungsgemäß festgelegt ist, wählen Sie in der globalen Kopfzeile **Benachrichtigungen** aus. Vergewissern Sie sich, dass eine Erfolgsbenachrichtigung aufgeführt ist.

![Screenshot mit Meldung zur erfolgreichen Benachrichtigung für Inaktivitätstimeout auf Verzeichnisebene](./media/set-preferences/confirmation.png)

## <a name="restore-default-settings"></a>Standardeinstellungen wiederherstellen

Wenn Sie Änderungen an den Azure-Portaleinstellungen vorgenommen haben und diese verwerfen möchten, wählen Sie **Standardeinstellungen wiederherstellen** aus. Alle Änderungen, die Sie an Portaleinstellungen vorgenommen haben, gehen verloren. Diese Option wirkt sich nicht auf Dashboardanpassungen aus.

![Screenshot: Wiederherstellen von Standardeinstellungen](./media/set-preferences/useful-links-restore-defaults.png)

## <a name="export-user-settings"></a>Exportieren von Benutzereinstellungen

Die Informationen zu Ihren benutzerdefinierten Einstellungen werden in Azure gespeichert. Sie können folgende Benutzerdaten exportieren:

* Private Dashboards im Azure-Portal
* Benutzereinstellungen wie bevorzugte Abonnements oder Verzeichnisse oder letztes Anmeldeverzeichnis
* Designs und andere benutzerdefinierte Portaleinstellungen

Es empfiehlt sich, Ihre Einstellungen zu exportieren und zu überprüfen, falls Sie sie löschen möchten. Das Neuerstellen von Dashboards oder das erneute Einrichten von Einstellungen kann zeitaufwendig sein.

Zum Exportieren Ihrer Portaleinstellungen wählen Sie **Alle Einstellungen exportieren** aus.

![Screenshot: Exportieren von Einstellungen](./media/set-preferences/useful-links-export-settings.png)

Beim Exportieren von Einstellungen wird eine *JSON*-Datei erstellt, die Ihre Benutzereinstellungen enthält, z. B. Farbdesign, Favoriten und private Dashboards. Aufgrund der dynamischen Art der Benutzereinstellungen und des Risikos der Datenbeschädigung können Sie keine Einstellungen aus der *JSON*-Datei importieren.

## <a name="delete-user-settings-and-dashboards"></a>Löschen von Benutzereinstellungen und Dashboards

Die Informationen zu Ihren benutzerdefinierten Einstellungen werden in Azure gespeichert. Sie können folgende Benutzerdaten löschen:

* Private Dashboards im Azure-Portal
* Benutzereinstellungen wie bevorzugte Abonnements oder Verzeichnisse oder letztes Anmeldeverzeichnis
* Designs und andere benutzerdefinierte Portaleinstellungen

Es empfiehlt sich, Ihre Einstellungen zu exportieren und zu überprüfen, bevor Sie sie löschen. Das Neuerstellen von Dashboards oder das erneute Einrichten von benutzerdefinierten Einstellungen kann zeitaufwendig sein.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

Zum Löschen Ihrer Portaleinstellungen wählen Sie **Alle Einstellungen und private Dashboards löschen** aus.

![Screenshot: Löschen von Einstellungen](./media/set-preferences/useful-links-delete-settings.png)

## <a name="change-language-and-regional-settings"></a>Ändern der Sprache und der regionalen Einstellungen

Es gibt zwei Einstellungen, mit denen gesteuert wird, wie der Text im Azure-Portal angezeigt wird: 
- Mit der Einstellung **Sprache** wird die Sprache festgelegt, in der Text im Azure-Portal angezeigt wird. 

- **Regionales Format** steuert, wie Datumsangaben, Uhrzeiten, Ziffern und Währungen angezeigt werden.

Zum Ändern der Sprache, die im Azure-Portal verwendet wird, verwenden Sie die Dropdownliste, um aus den verfügbaren Sprachen auszuwählen.

Die Auswahl des regionalen Formats ändert sich, um regionale Optionen nur für die Sprache anzuzeigen, die Sie ausgewählt haben. Um diese automatische Auswahl zu ändern, wählen Sie das gewünschte regionale Format mithilfe der Dropdownliste aus.

Wenn Sie z.B. Englisch als Sprache und dann USA als regionales Format auswählen, wird die Währung in US-Dollar angezeigt. Wenn Sie Englisch als Sprache und dann Europa als regionales Format auswählen, wird die Währung in Euro angezeigt.

Wählen Sie **Anwenden** aus, um Ihre Sprache und die Einstellungen für das regionale Format zu aktualisieren.

   ![Screenshot mit Einstellungen für Sprache und regionales Format](./media/set-preferences/language.png)

>[!NOTE]
>Diese Einstellungen für Sprache und regionales Format wirken sich nur auf das Azure-Portal aus. Dokumentationslinks, die in einer neuen Registerkarte oder in einem neuen Fenster geöffnet werden, verwenden die Spracheinstellungen Ihres Browsers, um die anzuzeigende Sprache zu ermitteln.
>

## <a name="next-steps"></a>Nächste Schritte

- [Tastenkombinationen im Azure-Portal](azure-portal-keyboard-shortcuts.md)
- [Unterstützte Browser und Geräte](azure-portal-supported-browsers-devices.md)
- [Hinzufügen, Entfernen und Neuanordnen von Favoriten](azure-portal-add-remove-sort-favorites.md)
- [Erstellen und Freigeben von benutzerdefinierten Dashboards](azure-portal-dashboards.md)
- [Anleitungsvideos für das Azure-Portal](azure-portal-video-series.md)

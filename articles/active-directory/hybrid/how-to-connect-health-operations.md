---
title: Azure Active Directory Connect Health-Vorgänge
description: In diesem Artikel werden zusätzliche Vorgänge beschrieben, die nach der Bereitstellung von Azure AD Connect Health ausgeführt werden können.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91b7bd6d05ee0b992ee166b9acb13aff2e605a2c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961004"
---
# <a name="azure-active-directory-connect-health-operations"></a>Azure Active Directory Connect Health-Vorgänge
In diesem Thema werden die verschiedenen Vorgänge beschrieben, die Sie mit Azure Active Directory (Azure AD) Connect Health durchführen können.

## <a name="enable-email-notifications"></a>Aktivieren von E-Mail-Benachrichtigungen
Sie können Azure AD Connect Health so konfigurieren, dass E-Mail-Benachrichtigungen gesendet werden, wenn in Warnungen angegeben wird, dass die Identitätsinfrastruktur nicht fehlerfrei ist. Dies tritt auf, wenn eine Warnung generiert und wenn sie gelöst wird.

![Screenshot: Einstellungen für E-Mail-Benachrichtigungen in Azure AD Connect Health](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> E-Mail-Benachrichtigungen sind standardmäßig aktiviert.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>So aktivieren Sie Azure AD Connect Health-E-Mail-Benachrichtigungen
1. Öffnen Sie das Blatt **Warnungen** für den Dienst, für den Sie E-Mail-Benachrichtigungen empfangen möchten.
2. Klicken Sie in der Aktionsleiste auf **Benachrichtigungseinstellungen**.
3. Legen Sie die Einstellung für E-Mail-Benachrichtigungen auf **EIN** fest.
4. Aktivieren Sie das Kontrollkästchen, wenn alle globalen Administratoren E-Mail-Benachrichtigungen erhalten sollen.
5. Wenn E-Mail-Benachrichtigungen an andere E-Mail-Adressen gesendet werden sollen, können Sie diese im Feld **Weitere E-Mail-Empfänger** angeben. Um eine E-Mail-Adresse aus dieser Liste zu entfernen, klicken Sie mit der rechten Maustaste auf den Eintrag und wählen **Löschen**.
6. Klicken Sie zum Abschließen der Änderungen auf **Speichern**. Die Änderungen werden erst nach dem Speichern wirksam.

>[!NOTE] 
> Wenn beim Verarbeiten von Synchronisierungsanforderungen in unserem Back-End-Dienst Probleme auftreten, sendet dieser Dienst eine Benachrichtigungs-E-Mail mit den Details des Fehlers an die E-Mail-Adresse(n) des Administratorkontakts Ihres Mandanten. Wir haben Feedback von Kunden erhalten, dass die Menge dieser Nachrichten in bestimmten Fällen unangemessen groß ist, sodass wir die Art und Weise ändern, wie diese Nachrichten gesendet werden. 
>
> Anstatt bei jedem Auftreten eines Synchronisierungsfehlers eine Nachricht zu senden, wird eine tägliche Auswahl aller Fehler gesendet, die vom Back-End-Dienst zurückgegeben wurden. Dies ermöglicht Kunden, diese Fehler auf effizientere Weise zu verarbeiten und die Anzahl doppelter Fehlermeldungen zu verringern.
>
> Wir planen, dass diese Änderung am 15. Januar 2020 implementiert wird.

## <a name="delete-a-server-or-service-instance"></a>Löschen eines Servers oder einer Serverinstanz

>[!NOTE] 
> Um eine Löschung durchzuführen, ist eine Azure AD Premium-Lizenz erforderlich.

In einigen Fällen möchten Sie unter Umständen einen Server aus der Überwachung entfernen. Hier sind die Informationen angegeben, die Sie zum Entfernen eines Servers aus dem Azure AD Connect Health-Dienst benötigen.

Beim Löschen eines Servers sind folgende Punkte zu beachten:

* Diese Aktion beendet das Erfassen von jeglichen Daten von diesem Server. Der Server wird aus dem Überwachungsdienst entfernt. Nach dieser Aktion können Sie keine neuen Warnungen oder Überwachungs- bzw. Nutzungsanalysedaten für diesen Server mehr anzeigen.
* Bei dieser Aktion wird der Health-Agent nicht von Ihrem Server deinstalliert. Wenn Sie vor Ausführung dieses Schritts den Health-Agent nicht deinstalliert haben, werden auf dem Server unter Umständen Fehler für den Health-Agent angezeigt.
* Bei dieser Aktion werden die Daten, die von diesem Server bereits erfasst wurden, nicht gelöscht. Diese Daten werden in Übereinstimmung mit der Azure-Datenaufbewahrungsrichtlinie gelöscht.
* Wenn Sie nach dem Ausführen dieser Aktion die Überwachung des gleichen Servers erneut starten möchten, müssen Sie den Health-Agent auf diesem Server deinstallieren und dann erneut installieren.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Löschen eines Servers aus dem Azure AD Connect Health-Dienst

>[!NOTE] 
> Um eine Löschung durchzuführen, ist eine Azure AD Premium-Lizenz erforderlich.

Azure AD Connect Health für Active Directory-Verbunddienste (AD FS) und Azure AD Connect (Sync):

1. Öffnen Sie das Blatt **Server**, indem Sie auf dem Blatt **Serverliste** den Namen des Servers auswählen, den Sie entfernen möchten.
2. Klicken Sie auf dem Blatt **Server** in der Aktionsleiste auf **Löschen**.
![Screenshot: Löschen des Servers mit Azure AD Connect Health](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Bestätigen Sie den Vorgang, indem Sie den Servernamen im Bestätigungsfeld eingeben.
4. Klicken Sie auf **Löschen**.

Azure AD Connect Health für Azure Active Directory Domain Services:

1. Öffnen Sie das **Domänencontroller**-Dashboard.
2. Wählen Sie den Domänencontroller aus, der entfernt werden soll.
3. Klicken Sie in der Aktionsleiste auf **Ausgewählte Elemente löschen**.
4. Bestätigen Sie die Aktion zum Löschen des Servers.
5. Klicken Sie auf **Löschen**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Löschen einer Dienstinstanz aus dem Azure AD Connect Health-Dienst
In einigen Fällen kann es erforderlich sein, eine Dienstinstanz zu entfernen. Hier sind die Informationen angegeben, die Sie zum Entfernen einer Dienstinstanz aus dem Azure AD Connect Health-Dienst benötigen.

Beim Löschen einer Dienstinstanz sind folgende Punkte zu beachten:

* Diese Aktion entfernt die aktuelle Dienstinstanz aus dem Überwachungsdienst.
* Diese Aktion deinstalliert oder entfernt den Health-Agent nicht von einem der Server, die im Rahmen dieser Dienstinstanz überwacht wurden. Wenn Sie vor Ausführung dieses Schritts den Health-Agent nicht deinstalliert haben, werden auf dem Server unter Umständen Fehler für den Health-Agent angezeigt.
* Alle Daten dieser Dienstinstanz werden in Übereinstimmung mit der Azure-Datenaufbewahrungsrichtlinie gelöscht.
* Wenn Sie nach dem Durchführen dieser Aktion mit der Überwachung des Diensts beginnen möchten, müssen Sie den Health-Agent auf allen Servern deinstallieren und dann neu installieren. Wenn Sie nach dem Durchführen dieser Aktion die Überwachung des gleichen Servers erneut starten möchten, müssen Sie den Health-Agent auf diesem Server deinstallieren und erneut installieren und ihn dann registrieren.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>So löschen Sie eine Dienstinstanz aus dem Azure AD Connect Health-Dienst
1. Öffnen Sie das Blatt **Dienst**, indem Sie auf dem Blatt **Dienstliste** die ID des Diensts (Name der Farm) auswählen, den Sie entfernen möchten. 
2. Klicken Sie auf dem Blatt **Dienst** in der Aktionsleiste auf **Löschen**. 
![Screenshot: Löschen des Diensts mit Azure AD Connect Health](./media/how-to-connect-health-operations/DeleteServer.png)
3. Bestätigen Sie den Vorgang, indem Sie den Dienstnamen in das Bestätigungsfeld eingeben (z.B. „sts.contoso.com“).
4. Klicken Sie auf **Löschen**.
   <br><br>

[//]: # (Beginn des RBAC-Abschnitts)
## <a name="manage-access-with-azure-rbac"></a>Verwalten des Zugriffs mit Azure RBAC
Die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) für Azure AD Connect Health ermöglicht den Zugriff auf andere Benutzer und Gruppen als die globalen Administratoren. Mit Azure RBAC werden den entsprechenden Benutzern und Gruppen Rollen zugewiesen und ein Mechanismus zum Einschränken der globalen Administratoren in Ihrem Verzeichnis angewandt.

### <a name="roles"></a>Rollen
Azure AD Connect Health unterstützt die folgenden integrierten Rollen:

| Role | Berechtigungen |
| --- | --- |
| Besitzer |Besitzer können innerhalb von Azure AD Connect Health den *Zugriff verwalten* (z.B. einem Benutzer oder einer Gruppe Rollen zuweisen), im Portal *alle Informationen anzeigen* (z.B. Warnungen) und *Einstellungen ändern* (z.B. E-Mail-Benachrichtigungen). <br>Standardmäßig wird diese Rolle globalen Azure AD-Administratoren zugewiesen und kann nicht geändert werden. |
| Mitwirkender |Beitragende können innerhalb von Azure AD Connect Health im Portal *alle Informationen anzeigen* und *Einstellungen ändern* (z.B. E-Mail-Benachrichtigungen). |
| Leser |Leser können innerhalb von Azure AD Connect Health *alle Informationen anzeigen* (z.B. Warnungen). |

Alle anderen Rollen (z.B. „Benutzerzugriffsadministratoren“ oder „DevTest Lab-Benutzer“) haben keine Auswirkung auf den Zugriff innerhalb von Azure AD Connect Health, auch wenn die Rollen in der Portalumgebung verfügbar sind.

### <a name="access-scope"></a>Zugriffsbereich
Azure AD Connect Health unterstützt die Zugriffsverwaltung auf zwei Ebenen:

* **Alle Dienstinstanzen**: Dies ist die empfohlene Vorgehensweise für die meisten Fälle. Der Zugriff wird für alle Dienstinstanzen (z.B. eine AD FS-Farm) über alle Rollentypen hinweg gesteuert, die von Azure AD Connect Health überwacht werden.
* **Dienstinstanz**: In einigen Fällen müssen Sie den Zugriff unter Umständen basierend auf Rollentypen oder nach Dienstinstanz aufteilen. In diesem Fall können Sie den Zugriff auf Dienstinstanzebene verwalten.  

Berechtigungen werden erteilt, wenn ein Benutzer Zugriff auf Verzeichnisebene oder Dienstinstanzebene hat.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Zulassen des Zugriffs auf Azure AD Connect Health für Benutzer oder Gruppen
Die folgenden Schritte veranschaulichen, wie Sie den Zugriff zulassen.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Schritt 1: Auswählen des entsprechenden Zugriffsbereichs
Um einem Benutzer Zugriff auf der Ebene *Alle Dienstinstanzen* innerhalb von Azure AD Connect Health zu gewähren, öffnen Sie das Hauptblatt in Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Schritt 2: Hinzufügen von Benutzern und Gruppen und Zuweisen von Rollen
1. Klicken Sie im Abschnitt **Konfigurieren** auf **Benutzer**.<br>
   ![Screenshot: Seitenleiste für Azure AD Connect Health-Ressourcen](./media/how-to-connect-health-operations/startRBAC.png)
2. Wählen Sie **Hinzufügen**.
3. Wählen Sie im Bereich **Rolle auswählen** eine Rolle aus (z.B. **Besitzer**).<br>
   ![Screenshot: Azure AD Connect Health und Konfigurationsmenü von Azure RBAC](./media/how-to-connect-health-operations/RBAC_add.png)
4. Geben Sie den Namen oder die Kennung des entsprechenden Benutzers oder der Gruppe ein. Sie können einen oder mehrere Benutzer oder Gruppen gleichzeitig auswählen. Klicken Sie auf **Auswählen**.
   ![Screenshot: Azure AD Connect Health und Rollenliste von Azure](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Klicken Sie auf **OK**.<br>
6. Nach Abschluss der Rollenzuweisung werden die Benutzer und Gruppen in der Liste angezeigt.<br>
   ![Screenshot: Azure AD Connect Health und Azure RBAC mit hervorgehobenen neuen Benutzern](./media/how-to-connect-health-operations/RBAC_user_list.png)

Jetzt haben die aufgeführten Benutzer und Gruppen gemäß ihren zugewiesenen Rollen Zugriff.

> [!NOTE]
> * Globale Administratoren haben immer Vollzugriff auf alle Vorgänge, aber globale Administratorkonten sind in der obigen Liste nicht aufgeführt.
> * Das Feature „Benutzer einladen“ wird in Azure AD Connect Health nicht unterstützt.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Schritt 3: Freigeben des Blattspeicherorts für Benutzer oder Gruppen
1. Nachdem Sie Berechtigungen zugewiesen haben, können Benutzer [hier](https://aka.ms/aadconnecthealth) auf Azure AD Connect Health zugreifen.
2. Auf dem Blatt können Benutzer das Blatt oder einzelne Teile davon im Dashboard anheften. Hierfür wird einfach das Symbol **An Dashboard anheften** verwendet.<br>
   ![Screenshot: Azure AD Connect Health und Azure RBAC mit dem Blatt zum Anheften mit Hervorhebung des entsprechenden Symbols](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> Ein Benutzer, dem die Rolle „Leser“ zugewiesen ist, kann die Azure AD Connect Health-Erweiterung nicht über den Azure Marketplace abrufen. Der Benutzer kann den hierfür erforderlichen Erstellungsvorgang nicht durchführen. Dieser Benutzer kann aber trotzdem auf das Blatt zugreifen, indem er den obigen Link verwendet. Für nachfolgende Verwendungen kann der Benutzer das Blatt an das Dashboard anheften.
>
>

### <a name="remove-users-or-groups"></a>Entfernen von Benutzern oder Gruppen
Sie können Benutzer oder Gruppen entfernen, die Azure AD Connect Health und Azure RBAC hinzugefügt wurden. Klicken Sie hierfür einfach mit der rechten Maustaste auf den Benutzer oder die Gruppe, und wählen Sie **Entfernen**.<br>
![Screenshot: Azure AD Connect Health und Azure RBAC mit hervorgehobener Option „Entfernen“](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Ende des RBAC-Abschnitts)

## <a name="next-steps"></a>Nächste Schritte
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Installieren des Azure AD Connect Health-Agents](how-to-connect-health-agent-install.md)
* [Verwenden von Azure AD Connect Health mit AD FS](how-to-connect-health-adfs.md)
* [Verwenden von Azure AD Connect Health für die Synchronisierung](how-to-connect-health-sync.md)
* [Verwenden von Azure AD Connect Health mit AD DS](how-to-connect-health-adds.md)
* [Azure AD Connect Health – FAQ](reference-connect-health-faq.md)
* [Azure AD Connect Health: Versionsverlauf](reference-connect-health-version-history.md)
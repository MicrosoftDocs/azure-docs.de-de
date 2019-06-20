---
title: 'Azure AD Connect: Nächste Schritte und Verwalten von Azure AD Connect | Microsoft-Dokumentation'
description: Erfahren Sie, wie die Standardkonfiguration und Betriebsaufgaben für Azure AD Connect erweitert werden.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c204029557a73dc3f02015afb92c0fdbf0d4d50e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64571317"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Nächste Schritte und Verwalten von Azure AD Connect
Befolgen Sie die in diesem Artikel beschriebenen Verfahren zum Anpassen von Azure Active Directory (Azure AD) Connect an die Bedürfnisse und Anforderungen Ihrer Organisation.  

## <a name="add-additional-sync-admins"></a>Hinzufügen weiterer Synchronisierungsadministratoren
Standardmäßig können nur der Benutzer, der die Installation durchgeführt hat, und die lokalen Administratoren das installierte Synchronisierungsmodul verwalten. Damit weitere Personen auf das Synchronisierungsmodul zugreifen und es verwalten können, suchen Sie auf dem lokalen Server die Gruppe „ADSyncAdmins“, und fügen die Personen dieser Gruppe hinzu.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Zuweisen von Lizenzen zu Benutzern von Azure AD Premium und Enterprise Mobility Suite
Nachdem Ihre Benutzer in der Cloud synchronisiert wurden, müssen Sie ihnen nun eine Lizenz zuweisen, damit sie Cloudanwendungen wie Office 365 nutzen können.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>So weisen Sie eine Azure AD Premium- oder Enterprise Mobility Suite-Lizenz zu

1. Melden Sie sich beim Azure-Portal als Administrator an.
2. Wählen Sie im linken Bereich **Active Directory**aus.
3. Doppelklicken Sie auf der Seite **Active Directory** auf das Verzeichnis mit den Benutzern, die Sie einrichten möchten.
4. Wählen Sie oben auf der Seite des Verzeichnisses die Option **Lizenzen**aus.
5. Wählen Sie auf der Seite **Lizenzen** die Option **Active Directory Premium** oder **Enterprise Mobility Suite** aus, und klicken Sie dann auf **Zuweisen**.
6. Wählen Sie im Dialogfeld die Benutzer aus, denen Sie Lizenzen zuweisen möchten, und klicken Sie dann auf das Häkchen, um die Änderungen zu speichern.

## <a name="verify-the-scheduled-synchronization-task"></a>Überprüfen der geplanten Synchronisierungsaufgabe
Prüfen Sie im Azure-Portal den Status einer Synchronisierung.

### <a name="to-verify-the-scheduled-synchronization-task"></a>So überprüfen Sie die geplante Synchronisierungsaufgabe
1. Melden Sie sich beim Azure-Portal als Administrator an.
2. Wählen Sie im linken Bereich **Active Directory**aus.
3. Wählen Sie im linken Bereich **Azure AD Connect** aus.
4. Achten Sie am oberen Rand der Seite auf die letzte Synchronisierung.

![Zeitpunkt der Verzeichnissynchronisierung](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>Starten einer geplanten Synchronisierungsaufgabe
Wenn Sie eine Synchronisierungsaufgabe ausführen müssen, können Sie Folgendes tun:

1. Doppelklicken Sie auf die Azure AD Connect-Desktopverknüpfung, um den Assistenten zu starten.
2. Klicken Sie auf **Konfigurieren**.
3. Wählen Sie auf der Aufgabenseite die Option **Synchronisierungsoptionen anpassen** aus, und klicken Sie auf **Weiter**.
4. Geben Sie Ihre Azure AD-Anmeldeinformationen ein.
5. Klicken Sie auf **Weiter**. Klicken Sie auf **Weiter**.  Klicken Sie auf **Weiter**.
5.  Vergewissern Sie sich auf der Seite **Bereit zur Konfiguration**, dass das Kontrollkästchen **Starten Sie den Synchronisierungsvorgang, nachdem die Konfiguration abgeschlossen wurde** aktiviert ist.
6.  Klicken Sie auf **Konfigurieren**.

Weitere Informationen zum Scheduler für Azure AD Connect finden Sie unter [Azure AD Connect Sync: Scheduler](how-to-connect-sync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Weitere verfügbare Aufgaben in Azure AD Connect
Nach der ersten Installation von Azure AD Connect können Sie den Assistenten über die Azure AD Connect-Startseite oder die Desktopverknüpfung jederzeit erneut starten.  Sie werden feststellen, dass beim erneuten Durchlaufen des Assistenten unter "Zusätzliche Aufgaben" einige neue Optionen zur Verfügung stehen.  

Die folgende Tabelle enthält eine Zusammenfassung und jeweils eine kurze Beschreibung dieser Aufgaben.

![Liste mit weiteren Aufgaben](./media/how-to-connect-post-installation/addtasks2.png)

| Weitere Aufgabe | BESCHREIBUNG |
| --- | --- |
|**Datenschutzeinstellungen**|Anzeigen der Telemetriedaten, die an Microsoft weitergegeben werden.|
|**Aktuelle Konfiguration anzeigen**|Sie können Ihre aktuelle Azure AD Connect-Lösung anzeigen.  Dazu gehören allgemeine Einstellungen, synchronisierte Verzeichnisse und Synchronisierungseinstellungen. |
| **Synchronisierungsoptionen anpassen** |Sie können die aktuelle Konfiguration ändern, z.B. weitere Aktive Directory-Gesamtstrukturen zur Konfiguration hinzufügen oder Synchronisierungsoptionen wie beispielsweise „Benutzer“, „Gruppe“, „Gerät“ oder „Kennwort zurückschreiben“ aktivieren. |
|**Geräteoptionen konfiguriere**|Für die Synchronisierung verfügbare Geräteoptionen|
|**Verzeichnisschema aktualisieren**|Ermöglicht das Hinzufügen neuer lokaler Verzeichnisobjekte für die Synchronisierung|
|**Stagingmodus konfigurieren** |Informationen zum Staging, die nicht sofort synchronisiert und nicht in Azure AD oder lokales Active Directory exportiert werden.  Mit diesem Feature können Sie die Synchronisierungen in einer Vorschau anzeigen, ehe sie erfolgen. |
|**Benutzeranmeldung ändern**|Ändern der Authentifizierungsmethode, die Benutzer zur Anmeldung verwenden|
|**Verbund verwalten**|Verwalten von AD FS-Infrastruktur, Erneuern von Zertifikaten und Hinzufügen von AD FS-Servern|
|**Problembehandlung**|Hilfe bei der Behandlung von Azure AD Connect-Problemen|

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).

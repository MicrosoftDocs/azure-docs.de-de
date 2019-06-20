---
title: 'Azure AD Connect: Erste Schritte mit Expresseinstellungen | Microsoft-Dokumentation'
description: Informationen zum Herunterladen, Installieren und Ausführen des Setup-Assistenten für Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.assetid: b6ce45fd-554d-4f4d-95d1-47996d561c9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24143f8c94a294da90be84bacfe633db0cd24f85
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60244504"
---
# <a name="getting-started-with-azure-ad-connect-using-express-settings"></a>Erste Schritte mit Azure AD Connect mit Expresseinstellungen
Die **Expresseinstellungen** von Azure AD Connect werden verwendet, wenn Sie eine Topologie mit einer einzelnen Gesamtstruktur und die [Kennworthashsynchronisierung](how-to-connect-password-hash-synchronization.md) für die Authentifizierung verwenden. **Expresseinstellungen** sind die Standardoption und werden für die häufigsten Bereitstellungsszenarien verwendet. Mit nur wenigen Klicks können Sie Ihr lokales Verzeichnis auf die Cloud erweitern.

Vor dem Installieren von Azure AD Connect müssen Sie [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) herunterladen und die im folgenden Artikel beschriebenen Schritte zur Vorbereitung ausführen: [Azure AD Connect: Hardware und Voraussetzungen](how-to-connect-install-prerequisites.md).

Falls Expresseinstellungen nicht zu Ihrer Topologie passen, finden Sie weitere Szenarien in der [verwandten Dokumentation](#related-documentation) .

## <a name="express-installation-of-azure-ad-connect"></a>Expressinstallation von Azure AD Connect
Im Abschnitt [Videos](#videos) können Sie diese Schritte in Aktion sehen.

1. Melden Sie sich als lokaler Administrator auf dem Server an, auf dem Sie Azure AD Connect installieren möchten. Dies sollte der Server sein, der als Synchronisierungsserver verwendet werden soll.
2. Navigieren Sie zu **AzureADConnect.msi**, und doppelklicken Sie darauf.
3. Aktivieren Sie auf der Willkommensseite das Kontrollkästchen zum Zustimmen zu den Lizenzbedingungen, und klicken Sie auf **Weiter**.  
4. Klicken Sie im Bildschirm "Expresseinstellungen" auf **Expresseinstellungen verwenden**.  
   ![Willkommen bei Azure AD Connect](./media/how-to-connect-install-express/express.png)
5. Geben Sie auf dem Bildschirm „Mit Azure AD verbinden“ den Benutzernamen und das Kennwort eines globalen Azure-Administrators für Azure AD ein. Klicken Sie auf **Weiter**.  
   ![Herstellen einer Verbindung mit Azure AD](./media/how-to-connect-install-express/connectaad.png)  
   Falls ein Fehler auftritt und Sie Probleme mit der Konnektivität haben, können Sie unter [Problembehebung bei Konnektivitätsproblemen](tshoot-connect-connectivity.md) nach einer Lösung suchen.
6. Geben Sie auf dem Bildschirm „Mit AD DS verbinden“ den Benutzernamen und das Kennwort für ein Enterprise-Administratorkonto ein. Sie können den Domänenteil entweder im NetBIOS- oder FQDN-Format eingeben, also „FABRIKAM\administrator“ oder „fabrikam.com\administrator“. Klicken Sie auf **Weiter**.  
   ![Herstellen einer Verbindung mit AD DS](./media/how-to-connect-install-express/connectad.png)
7. Die Seite [**Konfiguration der Azure AD-Anmeldung**](plan-connect-user-signin.md#azure-ad-sign-in-configuration) wird nur angezeigt, wenn Sie den Vorgang zum [Überprüfen Ihrer Domänen](../active-directory-domains-add-azure-portal.md) unter [Voraussetzungen](how-to-connect-install-prerequisites.md) nicht abgeschlossen haben.
   ![Nicht überprüfte Domänen](./media/how-to-connect-install-express/unverifieddomain.png)  
   Wenn diese Seite angezeigt wird, sollten Sie jede Domäne überprüfen, die als **Nicht hinzugefügt** und **Nicht überprüft** gekennzeichnet ist. Stellen Sie sicher, dass die verwendeten Domänen in Azure AD überprüft wurden. Klicken Sie auf das Symbol zum Aktualisieren, wenn Sie Ihre Domänen überprüft haben.
8. Klicken Sie im Bildschirm "Bereit zur Konfiguration" auf **Installieren**.
   * Optional können Sie auf der Seite „Bereit zur Konfiguration“ das Kontrollkästchen **Starten Sie den Synchronisierungsvorgang, sobald die Konfiguration abgeschlossen wurde** deaktivieren. Deaktivieren Sie dieses Kontrollkästchen, wenn Sie weitere Konfigurationseinstellungen vornehmen möchten, z.B. [Filtern](how-to-connect-sync-configure-filtering.md). Wenn Sie diese Option deaktivieren, konfiguriert der Assistent die Synchronisierung, der Scheduler bleibt jedoch deaktiviert. Er wird erst ausgeführt, wenn Sie ihn manuell aktivieren, indem Sie den [Installations-Assistenten erneut ausführen](how-to-connect-installation-wizard.md).
   * Bleibt das Kontrollkästchen **Starten Sie den Synchronisierungsvorgang, sobald die Konfiguration abgeschlossen wurde** aktiviert, wird sofort eine vollständige Synchronisierung aller Benutzer, Gruppen und Kontakte mit Azure AD ausgelöst.
   * Wenn Sie in Ihrer lokalen Active Directory-Instanz Exchange nutzen, können Sie auch [**Hybridbereitstellungen in Exchange**](https://technet.microsoft.com/library/jj200581.aspx) aktivieren. Aktivieren Sie diese Option, wenn Sie Exchange-Postfächer gleichzeitig in der Cloud und lokal bereitstellen möchten.
     ![Bereit zur Konfiguration von Azure AD Connect](./media/how-to-connect-install-express/readytoconfigure.png)
9. Klicken Sie nach Abschluss der Installation auf **Beenden**.
10. Melden Sie sich nach Abschluss der Installation ab und erneut wieder an, ehe Sie den Synchronisierungsdienst-Manager oder Synchronisierungsregel-Editor verwenden.

## <a name="videos"></a>Videos
Ein Video zur Expressinstallation finden Sie hier:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Express-Settings/player]
>
>

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie Azure AD Connect installiert haben, können Sie [die Installation überprüfen und Lizenzen zuweisen](how-to-connect-post-installation.md).

Hier finden Sie weitere Informationen zu diesen Features, die bei der Installation aktiviert wurden: [Automatisches Upgrade](how-to-connect-install-automatic-upgrade.md), [Azure AD Connect-Synchronisierung: Verhindern von versehentlichen Löschvorgängen](how-to-connect-sync-feature-prevent-accidental-deletes.md) und [Überwachen der Azure AD Connect-Synchronisierung mit Azure AD Connect Health](how-to-connect-health-sync.md).

Weitere Informationen zu folgenden allgemeinen Themen: [Scheduler und Auslösen der Synchronisierung](how-to-connect-sync-feature-scheduler.md).

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).

## <a name="related-documentation"></a>verwandten Dokumentation

| Thema | Link |
| --- | --- |
| Übersicht über Azure AD Connect | [Integrieren Ihrer lokalen Verzeichnisse in Azure Active Directory](whatis-hybrid-identity.md)
| Installation mit benutzerdefinierten Einstellungen | [Benutzerdefinierte Installation von Azure AD Connect](how-to-connect-install-custom.md) |
| Upgrade von DirSync | [Upgrade von Azure AD-Synchronisierungstools (DirSync)](how-to-dirsync-upgrade-get-started.md)|
| Für die Installation verwendete Konten | [Weitere Informationen zu den Anmeldeinformationen und Berechtigungen von Azure AD Connect](reference-connect-accounts-permissions.md) |

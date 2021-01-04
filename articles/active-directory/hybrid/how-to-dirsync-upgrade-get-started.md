---
title: 'Azure AD Connect: Upgraden von DirSync | Microsoft-Dokumentation'
description: Informationen Sie zum Aktualisieren von DirSync auf Azure AD Connect. In diesem Artikel werden die Schritte für das Upgrade von DirSync auf Azure AD Connect beschrieben.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: baf52da7-76a8-44c9-8e72-33245790001c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8aa45294de4ef644c20ef66b7163706dca9759d3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996524"
---
# <a name="azure-ad-connect-upgrade-from-dirsync"></a>Azure AD Connect: Upgrade von DirSync
Azure AD Connect ist der Nachfolger von DirSync. Dieses Thema beschreibt die Möglichkeiten, die Sie beim Upgrade von DirSync haben. Die Schritte funktionieren nicht für ein Upgrade von einer anderen Version von Azure AD Connect oder von Azure AD Sync.

Vor dem Installieren von Azure AD Connect müssen Sie [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) herunterladen und die im folgenden Artikel beschriebenen Schritte zur Vorbereitung ausführen: [Voraussetzungen für Azure AD Connect](how-to-connect-install-prerequisites.md). Informieren Sie sich insbesondere über Folgendes, da sich diese Bereiche von DirSync unterscheiden:

* Die erforderliche Version von .Net und PowerShell. Im Vergleich zu DirSync müssen sich neuere Versionen auf dem Server befinden.
* Die Konfiguration des Proxyservers. Wenn Sie einen Proxyserver verwenden, um eine Verbindung mit dem Internet herzustellen, muss diese Einstellung vor dem Upgrade konfiguriert werden. DirSync hat immer den Proxyserver verwendet, der für den installierenden Benutzer konfiguriert ist, Azure AD Connect verwendet hingegen die Einstellungen für den Computer.
* Die im Proxyserver zu öffnenden URLs. Für einfache Szenarien, die auch von DirSync unterstützt werden, sind die Anforderungen identisch. Wenn Sie die neuen Features mit Azure AD Connect verwenden möchten, müssen einige neue URLs geöffnet werden.

> [!NOTE]
> Nachdem Sie Ihren neuen Azure AD Connect-Server aktiviert haben, um mit dem Synchronisieren von Änderungen mit Azure AD zu beginnen, dürfen Sie kein Rollback zur Verwendung von DirSync oder Azure AD Sync mehr durchführen. Ein Downgrade von Azure AD Connect auf Legacyclients, z.B. DirSync und Azure AD Sync, wird nicht unterstützt und kann zu Problemen führen, z.B. zu Datenverlust in Azure AD.

Wenn Sie kein Upgrade von DirSync durchführen, hilft Ihnen die Dokumentation zu anderen Szenarien weiter.

## <a name="upgrade-from-dirsync"></a>Upgrade von DirSync
Je nach Ihrer aktuellen DirSync-Bereitstellung gibt es unterschiedliche Optionen für das Upgrade. Wenn die erwartete Upgradezeit weniger als drei Stunden beträgt, lautet die Empfehlung, ein direktes Upgrade durchzuführen. Wenn die erwartete Upgradezeit mehr als drei Stunden beträgt, empfehlen wir Ihnen die Durchführung einer parallelen Bereitstellung auf einem anderen Server. Die Schätzung lautet, dass das Upgrade länger als drei Stunden dauert, wenn Sie über mehr als 50.000 Objekte verfügen.

| Szenario |
| --- |
| [Direktes Upgrade](#in-place-upgrade) |
| [Parallele Bereitstellung](#parallel-deployment) |

> [!NOTE]
> Wenn Sie ein Upgrade von DirSync auf Azure AD Connect planen, sollten Sie DirSync nicht selbst deinstallieren, bevor Sie das Upgrade durchführen. Azure AD Connect liest und migriert die Konfiguration aus DirSync und führt die Deinstallation nach der Untersuchung des Servers durch.

**Direktes Upgrade**  
Die erwartete Zeit zum Durchführen des Upgrades wird vom Assistenten angezeigt. Diese Schätzung basiert auf der Annahme, dass ein Upgrade für eine Datenbank mit 50.000 Objekten (Benutzern, Kontakten und Gruppen) drei Stunden dauert. Wenn die Anzahl von Objekten in der Datenbank unter 50.000 liegt, empfiehlt Azure AD Connect ein direktes Upgrade. Wenn Sie den Vorgang fortsetzen, werden Ihre aktuellen Einstellungen während des Upgrades automatisch angewendet, und Ihr Server setzt die aktive Synchronisierung automatisch fort.

Falls Sie eine Konfigurationsmigration und eine parallele Bereitstellung durchführen möchten, können Sie die Empfehlung des direkten Upgrades ignorieren. Beispielsweise können Sie die Gelegenheit nutzen, um die Hardware und das Betriebssystem zu aktualisieren. Weitere Informationen finden Sie im Abschnitt [Parallele Bereitstellung](#parallel-deployment).

**Parallele Bereitstellung**  
Eine parallele Bereitstellung wird empfohlen, wenn Sie über mehr als 50.000 Objekte verfügen. Bei dieser Bereitstellung werden Verzögerungen für Ihre Benutzer vermieden. Bei der Azure AD Connect-Installation wird die erwartete Ausfallzeit in Verbindung mit dem Upgrade geschätzt, aber wenn Sie DirSync in der Vergangenheit bereits aktualisiert haben, ist Ihre eigene Erfahrung vermutlich ausschlaggebender.

### <a name="supported-dirsync-configurations-to-be-upgraded"></a>Unterstützte zu aktualisierende DirSync-Konfigurationen
Die folgenden Konfigurationsänderungen werden für DirSync-Upgrades unterstützt:

* Filterung von Domänen und Organisationseinheiten
* Alternative ID (UPN)
* Kennwortsynchronisierung und Exchange-Hybrideinstellungen
* Gesamtstruktur-/Domäneneinstellungen und Azure AD-Einstellungen
* Filterung basierend auf Benutzerattributen

Die folgende Änderung kann im Rahmen des Upgrades nicht aktualisiert werden. Bei dieser Konfiguration wird das Upgrade blockiert:

* Nicht unterstützte DirSync-Änderungen, z.B. entfernte Attribute und die Verwendung einer benutzerdefinierten Erweiterungs-DLL

![Upgrade gesperrt](./media/how-to-dirsync-upgrade-get-started/analysisblocked.png)

In diesen Fällen wird empfohlen, einen neuen Azure AD Connect-Server im [Stagingmodus](how-to-connect-sync-staging-server.md) zu installieren und die alte DirSync- und die neue Azure AD Connect-Konfiguration zu überprüfen. Wenden Sie alle Änderungen mithilfe der benutzerdefinierten Konfiguration erneut an, wie unter [Azure AD Connect-Synchronisierung: Anpassen von Synchronisierungsoptionen](how-to-connect-sync-whatis.md) beschrieben.

Die Kennwörter, die von DirSync für die Dienstkonten verwendet werden, können nicht abgerufen werden und werden nicht migriert. Diese Kennwörter werden während des Upgrades zurückgesetzt.

### <a name="high-level-steps-for-upgrading-from-dirsync-to-azure-ad-connect"></a>Allgemeine Schritte des Upgrades von DirSync auf Azure AD Connect
1. Willkommen bei Azure AD Connect
2. Analyse der aktuellen DirSync-Konfiguration
3. Sammeln des globalen Azure AD-Administratorkennworts
4. Sammeln der Anmeldeinformationen für ein Enterprise-Administratorkonto (nur während der Installation von Azure AD Connect verwendet)
5. Installation von Azure AD Connect
   * Deinstallieren von DirSync (oder vorübergehendes Deaktivieren)
   * Installieren von Azure AD Connect
   * Optionales Starten der Synchronisierung

Zusätzliche Schritte sind in folgenden Fällen erforderlich:

* Sie verwenden derzeit eine vollständige SQL Server-Version (lokal oder remote)
* Sie verfügen über mehr als 50.000 Objekte für die Synchronisierung

## <a name="in-place-upgrade"></a>Direktes Upgrade
1. Starten Sie den Azure AD Connect-Installer (MSI).
2. Lesen Sie die Lizenzbedingungen und den Datenschutzhinweis, und stimmen Sie diesen zu.  
   ![Willkommen bei Azure AD](./media/how-to-dirsync-upgrade-get-started/Welcome.png)
3. Klicken Sie auf „Weiter“, um die Analyse der vorhandenen DirSync-Installation zu starten.  
   ![Analysieren der vorhandenen Installation der Verzeichnissynchronisierung](./media/how-to-dirsync-upgrade-get-started/Analyze.png)
4. Nach Abschluss der Analyse werden Empfehlungen dazu angezeigt, wie Sie fortfahren können.  
   * Wenn Sie SQL Server Express verwenden und über weniger als 50.000 Objekte verfügen, wird der folgende Bildschirm angezeigt:  
     ![Abgeschlossene Analyse bereit für die Aktualisierung von DirSync](./media/how-to-dirsync-upgrade-get-started/AnalysisReady.png)
   * Wenn Sie eine SQL Server-Vollversion für DirSync verwenden, wird stattdessen die folgende Seite angezeigt:  
     ![Screenshot: Vorhandener SQL-Datenbank-Server, der gerade verwendet wird](./media/how-to-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)  
     Informationen zum vorhandenen SQL Server-Datenbankserver, der von DirSync verwendet wird, werden angezeigt. Führen Sie bei Bedarf die entsprechenden Anpassungen durch. Klicken Sie auf **Weiter** , um die Installation fortzusetzen.
   * Falls Sie über mehr als 50.000 Objekte verfügen, wird stattdessen der folgende Bildschirm angezeigt:  
     ![Screenshot: Bildschirm, der angezeigt wird, wenn mehr als 50.000 Objekte aktualisiert werden müssen](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)  
     Klicken Sie auf das Kontrollkästchen neben der folgenden Nachricht, um ein direktes Upgrade durchzuführen: **Mit der Aktualisierung von DirSync auf diesem Computer fortfahren.**
     Wenn Sie stattdessen eine [parallele Bereitstellung](#parallel-deployment) durchführen möchten, müssen Sie die DirSync-Konfigurationseinstellungen exportieren und die Konfiguration auf den neuen Server verschieben.
5. Geben Sie das derzeit verwendete Kennwort für das Konto ein, um eine Verbindung zu Azure AD herzustellen. Dies muss das Konto sein, das momentan von DirSync verwendet wird.  
   ![Geben Sie Ihre Azure AD-Anmeldeinformationen ein.](./media/how-to-dirsync-upgrade-get-started/ConnectToAzureAD.png)  
   Falls ein Fehler auftritt und Sie Probleme mit der Konnektivität haben, können Sie unter [Problembehebung bei Konnektivitätsproblemen](tshoot-connect-connectivity.md) nach einer Lösung suchen.
6. Geben Sie ein Unternehmensadministratorkonto für Active Directory an.  
   ![Geben Sie Ihre ADDS-Anmeldeinformationen ein.](./media/how-to-dirsync-upgrade-get-started/ConnectToADDS.png)
7. Sie können nun mit der Konfiguration loslegen. Wenn Sie auf **Upgrade** klicken, wird DirSync deinstalliert und Azure AD Connect konfiguriert. Anschließend wird die Synchronisierung gestartet.  
   ![Bereit zur Konfiguration](./media/how-to-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. Melden Sie sich nach Abschluss der Installation von Windows ab und anschließend wieder an, ehe Sie den Synchronisierungsdienst-Manager oder Synchronisierungsregel-Editor verwenden oder andere Änderungen an der Konfiguration vornehmen.

## <a name="parallel-deployment"></a>Parallele Bereitstellung
### <a name="export-the-dirsync-configuration"></a>Exportieren der DirSync-Konfiguration
**Parallele Bereitstellung mit mehr als 50.000 Objekten**

Wenn Sie über mehr als 50.000 Objekte verfügen, wird von der Azure AD Connect-Installation eine parallele Bereitstellung empfohlen.

Es wird ein Bildschirm angezeigt, der dem folgenden Bildschirm ähnelt:  
![Analyse abgeschlossen](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Wenn Sie die parallele Bereitstellung fortsetzen möchten, müssen Sie die folgenden Schritte ausführen:

* Klicken Sie auf die Schaltfläche **Einstellungen exportieren** . Bei der Installation von Azure AD Connect auf einem separaten Server werden diese Einstellungen von Ihrem aktuellen DirSync zur neuen Azure AD Connect-Installation migriert.

Sobald Sie Ihre Einstellungen erfolgreich exportiert haben, können Sie den Azure AD Connect-Assistenten auf dem DirSync-Server beenden. Fahren Sie mit dem nächsten Schritt fort, um Azure AD Connect auf einem separaten Server zu installieren

**Parallele Bereitstellung mit weniger als 50.000 Objekten**

Gehen Sie wie folgt vor, falls Sie über weniger als 50.000 Objekte verfügen, aber trotzdem eine parallele Bereitstellung durchführen möchten:

1. Führen Sie den Azure AD Connect-Installer aus (MSI).
2. Wenn der Bildschirm **Willkommen bei Azure AD Connect** angezeigt wird, beenden Sie den Installations-Assistenten, indem Sie auf das „X“ oben rechts im Fenster klicken.
3. Öffnen Sie eine Eingabeaufforderung.
4. Führen Sie im Installationsordner von Azure AD Connect (Standard: „C:\Programme\Microsoft Azure Active Directory Connect“) den folgenden Befehl aus: `AzureADConnect.exe /ForceExport`.
5. Klicken Sie auf die Schaltfläche **Einstellungen exportieren** . Bei der Installation von Azure AD Connect auf einem separaten Server werden diese Einstellungen von Ihrem aktuellen DirSync zur neuen Azure AD Connect-Installation migriert.

![Screenshot: Option „Einstellungen exportieren“ zum Migrieren Ihrer Einstellungen zur neuen Azure AD Connect-Installation](./media/how-to-dirsync-upgrade-get-started/forceexport.png)

Sobald Sie Ihre Einstellungen erfolgreich exportiert haben, können Sie den Azure AD Connect-Assistenten auf dem DirSync-Server beenden. Fahren Sie mit dem nächsten Schritt fort, um Azure AD Connect auf einem separaten Server zu installieren.

### <a name="install-azure-ad-connect-on-separate-server"></a>Installieren von Azure AD Connect auf einem separaten Server
Bei der Installation von Azure AD Connect auf einem neuen Server wird davon ausgegangen, dass Sie Azure AD Connect neu installieren möchten. Da Sie die DirSync-Konfiguration verwenden möchten, müssen Sie einige zusätzliche Schritte ausführen:

1. Führen Sie den Azure AD Connect-Installer aus (MSI).
2. Wenn der Bildschirm **Willkommen bei Azure AD Connect** angezeigt wird, beenden Sie den Installations-Assistenten, indem Sie auf das „X“ oben rechts im Fenster klicken.
3. Öffnen Sie eine Eingabeaufforderung.
4. Führen Sie im Installationsordner von Azure AD Connect (Standard: „C:\Programme\Microsoft Azure Active Directory Connect“) den folgenden Befehl aus: `AzureADConnect.exe /migrate`.
   Der Installations-Assistent von Azure AD Connect wird gestartet, und der folgende Bildschirm wird angezeigt:  
   ![Screenshot, der zeigt, wo die Einstellungsdatei beim Upgrade importiert wird](./media/how-to-dirsync-upgrade-get-started/ImportSettings.png)
5. Wählen Sie die Datei mit den Einstellungen, die von Ihrer DirSync-Installation exportiert wurde.
6. Konfigurieren Sie alle erweiterten Optionen, einschließlich:
   * Einen benutzerdefinierten Speicherort für Azure AD Connect.
   * Eine vorhandene Instanz von SQL Server (Standard: Azure AD Connect installiert SQL Server 2012 Express). Verwenden Sie nicht dieselbe Datenbankinstanz wie für Ihren DirSync-Server.
   * Ein Dienstkonto für die Verbindung zu SQL Server (wenn Ihre SQL Server-Datenbank remotegesteuert ist, muss dieses Konto ein Domänendienstkonto sein).
     Auf diesem Bildschirm werden die folgenden Optionen angezeigt:  
     ![Screenshot: „Erweiterte Konfigurationsoptionen“ für das Upgrade von DirSync](./media/how-to-dirsync-upgrade-get-started/advancedsettings.png)
7. Klicken Sie auf **Weiter**.
8. Lassen Sie auf der Seite **Bereit zur Konfiguration** das Kontrollkästchen **Starten Sie den Synchronisierungsvorgang, sobald die Konfiguration abgeschlossen wurde** aktiviert. Der Server befindet sich nun im [Stagingmodus](how-to-connect-sync-staging-server.md), und Änderungen werden nicht nach Azure AD exportiert.
9. Klicken Sie auf **Installieren**.
10. Melden Sie sich nach Abschluss der Installation von Windows ab und anschließend wieder an, ehe Sie den Synchronisierungsdienst-Manager oder Synchronisierungsregel-Editor verwenden oder andere Änderungen an der Konfiguration vornehmen.

> [!NOTE]
> Die Synchronisierung zwischen Windows Server Active Directory und Azure Active Directory wird gestartet, es werden aber keine Änderungen zu Azure AD exportiert. Nur ein Synchronisierungstool kann jeweils aktiv Änderungen exportieren. Dies wird als [Stagingmodus](how-to-connect-sync-staging-server.md) bezeichnet.

### <a name="verify-that-azure-ad-connect-is-ready-to-begin-synchronization"></a>Überprüfen, ob Azure AD Connect bereit für den Beginn der Synchronisierung ist
Um sicherzustellen, dass Azure AD Connect bereit für die Übernahme von DirSync ist, müssen Sie im Startmenü in der Gruppe **Azure AD Connect** die Option **Synchronization Service Manager** öffnen.

Wechseln Sie in der Anwendung zur Registerkarte **Vorgänge** . Überprüfen Sie auf dieser Registerkarte, dass die folgenden Vorgänge abgeschlossen wurden:

* Import auf den AD-Connector
* Import auf den Azure AD-Connector
* Vollständige Synchronisierung auf dem AD-Connector
* Vollständige Synchronisierung auf dem Azure AD-Connector

![Import und Synchronisierung abgeschlossen](./media/how-to-dirsync-upgrade-get-started/importsynccompleted.png)

Sehen Sie sich die Ergebnisse dieser Vorgänge an, und stellen Sie sicher, dass keine Fehler vorhanden sind.

Wenn Sie prüfen möchten, welche Änderungen zu Azure AD exportiert werden, können Sie sich die Informationen zum Überprüfen der Konfiguration unter [Stagingmodus](how-to-connect-sync-staging-server.md) durchlesen. Nehmen Sie die erforderlichen Konfigurationsänderungen vor, bis nichts Unerwartetes mehr angezeigt wird.

Sie können von DirSync zu Azure AD zu wechseln, wenn Sie diese Schritte ausgeführt haben und mit dem Ergebnis zufrieden sind.

### <a name="uninstall-dirsync-old-server"></a>Deinstallieren von DirSync (alter Server)
* Suchen Sie unter **Programme und Funktionen** nach **Microsoft Azure Active Directory-Synchronisierungstool**.
* Deinstallieren Sie das **Microsoft Azure Active Directory-Synchronisierungstool**
* Die Deinstallation kann bis zu 15 Minuten dauern.

Wenn Sie DirSync lieber später deinstallieren möchten, können Sie den Server vorübergehend abschalten oder den Dienst deaktivieren. Wenn ein Fehler auftritt, können Sie ihn so wieder aktivieren. Es ist jedoch nicht davon auszugehen, dass der nächste Schritt fehlschlägt, weshalb dies in der Regel nicht erforderlich ist.

Wenn DirSync deinstalliert oder deaktiviert ist, ist kein aktiver Server vorhanden, der den Export zu Azure AD durchführt. Der nächste Schritt Zum Aktivieren von Azure AD Connect muss abgeschlossen werden, bevor Änderungen in der lokalen Active Directory-Instanz weiter mit Azure AD synchronisiert werden.

### <a name="enable-azure-ad-connect-new-server"></a>Aktivieren von Azure AD Connect (neuer Server)
Wenn Sie Azure AD Connect nach Abschluss der Installation wieder öffnen, können Sie weitere Konfigurationsänderungen vornehmen. Starten Sie **Azure AD Connect** über das Startmenü oder über die Verknüpfung auf dem Desktop. Achten Sie darauf, die MSI-Datei für die Installation nicht erneut auszuführen.

Daraufhin sollte Folgendes angezeigt werden:  
![Zusätzliche Aufgaben](./media/how-to-dirsync-upgrade-get-started/AdditionalTasks.png)

* Wählen Sie **Stagingmodus konfigurieren**.
* Deaktivieren Sie den Stagingmodus durch Klicken auf das Kontrollkästchen **Stagingmodus aktiviert** .

![Screenshot: Option zum Aktivieren des Stagingmodus](./media/how-to-dirsync-upgrade-get-started/configurestaging.png)

* Klicken Sie auf die Schaltfläche **Weiter** .
* Klicken Sie auf der Bestätigungsseite auf die Schaltfläche **Installieren** .

Azure AD Connect ist jetzt unser aktiver Server, und es ist nicht zulässig, zurück zu Ihrem vorhandenen DirSync-Server zu wechseln.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie Azure AD Connect installiert haben, können Sie [die Installation überprüfen und Lizenzen zuweisen](how-to-connect-post-installation.md).

Weitere Informationen zu diesen neuen Features, die nach der Installation zur Verfügung stehen, finden Sie hier: [Automatisches Upgrade](how-to-connect-install-automatic-upgrade.md), [Azure AD Connect-Synchronisierung: Verhindern von versehentlichen Löschvorgängen](how-to-connect-sync-feature-prevent-accidental-deletes.md) und [Überwachen der Azure AD Connect-Synchronisierung mit Azure AD Connect Health](how-to-connect-health-sync.md).

Weitere Informationen zu folgenden allgemeinen Themen: [Scheduler und Auslösen der Synchronisierung](how-to-connect-sync-feature-scheduler.md).

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).

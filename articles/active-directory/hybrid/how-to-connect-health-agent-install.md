---
title: Installieren der Azure Active Directory Connect Health-Agents
description: In diesem Azure AD Connect Health-Artikel wird die Agent-Installation für Active Directory-Verbunddienste (Active Directory Federation Services, AD FS) und für die Synchronisierung beschrieben.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/20/2020
ms.topic: how-to
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b680c275b92340cc7efba187769cb17602b08b45
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973285"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Installieren der Azure AD Connect Health-Agents

In diesem Artikel erfahren Sie, wie Sie die Azure Active Directory (Azure AD) Connect Health-Agents installieren und konfigurieren. Informationen zum Herunterladen der Agents finden Sie [hier](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent).

## <a name="requirements"></a>Requirements (Anforderungen)

In der folgenden Tabelle sind die Anforderungen für die Verwendung von Azure AD Connect Health aufgeführt.

| Anforderung | BESCHREIBUNG |
| --- | --- |
| Azure AD Premium ist installiert. |Azure AD Connect Health ist ein Feature von Azure AD Premium. Weitere Informationen finden Sie unter [Registrieren für Azure AD Premium](../fundamentals/active-directory-get-started-premium.md). <br /><br />Wenn Sie eine 30-tägige kostenlose Testversion nutzen möchten, navigieren Sie zu [Aktivieren der Azure Active Directory Premium-Testversion](https://azure.microsoft.com/trial/get-started-active-directory/). |
| Sie sind ein globaler Administrator in Azure AD. |Standardmäßig können nur globale Administratoren die Health-Agents installieren und konfigurieren, auf das Portal zugreifen und Vorgänge in Azure AD Connect Health ausführen. Weitere Informationen finden Sie unter [Verwalten Ihres Azure AD-Verzeichnisses](../fundamentals/active-directory-whatis.md). <br /><br /> Mit der rollenbasierten Zugriffssteuerung von Azure (Role-Based Access Control, Azure RBAC) können Sie anderen Benutzern in Ihrer Organisation den Zugriff auf Azure AD Connect Health gewähren. Weitere Informationen hierzu finden Sie unter [Azure RBAC für Azure AD Connect Health](how-to-connect-health-operations.md#manage-access-with-azure-rbac). <br /><br />**Wichtig**: Verwenden Sie für die Installation der Agents ein Geschäfts-, Schul- oder Unikonto. Ein Microsoft-Konto kann nicht verwendet werden. Weitere Informationen finden Sie unter [Registrieren für Azure AD als Organisation](../fundamentals/sign-up-organization.md). |
| Der Azure AD Connect Health-Agent ist auf jedem Zielserver installiert. | Die Connect Health-Agents müssen auf den Zielservern installiert und konfiguriert sein, damit sie Daten empfangen und Überwachungs- und Analysefunktionen bereitstellen können. <br /><br />Beispiel: Um Daten aus Ihrer Active Directory-Verbunddienste-Infrastruktur (Active Directory Federation Services, AD FS) abrufen zu können, müssen Sie den Agent auf dem AD FS-Server und auf dem Webanwendungs-Proxyserver installieren. Und um Daten aus Ihrer lokalen Azure AD Domain Services-Infrastruktur (Azure AD DS) abrufen zu können, müssen Sie den Agent auf den Domänencontrollern installieren.  |
| Die Azure-Dienstendpunkte verfügen über ausgehende Verbindungen. | Während der Installation und der Laufzeit erfordert der Agent Verbindungen mit den Endpunkten des Azure AD Connect Health-Diensts. Wenn ausgehende Verbindungen durch Firewalls blockiert werden, fügen Sie der Zulassungsliste die [Endpunkte für ausgehende Verbindungen](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) hinzu. |
|Ausgehende Verbindungen basieren auf IP-Adressen. | Informationen zur Firewallfilterung auf der Grundlage von IP-Adressen finden Sie unter [Azure-IP-Adressbereiche](https://www.microsoft.com/download/details.aspx?id=41653).|
| Die TLS-Überprüfung für ausgehenden Datenverkehr ist gefiltert oder deaktiviert. | Beim Agent-Registrierungsschritt oder bei Datenuploads tritt möglicherweise ein Fehler auf, wenn auf der Netzwerkebene eine TLS-Überprüfung oder Beendigung für ausgehenden Datenverkehr erfolgt. Weitere Informationen finden Sie unter [Einrichten der TLS-Überprüfung](/previous-versions/tn-archive/ee796230(v=technet.10)). |
| Firewallports auf dem Server für die Ausführung des Agents. |Die folgenden Firewallports müssen offen sein, damit der Agent mit den Azure AD Connect Health-Dienstendpunkten kommunizieren kann: <br /><li>TCP-Port 443</li><li>TCP-Port 5671</li> <br />Für die neueste Agent-Version ist Port 5671 nicht erforderlich. Aktualisieren Sie auf die neueste Version, sodass nur Port 443 erforderlich ist. Weitere Informationen finden Sie unter [Erforderliche Ports und Protokolle für die Hybrid-Identität](./reference-connect-ports.md). |
| Wenn die verstärkte Sicherheitskonfiguration für Internet Explorer aktiviert ist, lassen Sie die angegebenen Websites zu.  |Wenn die verstärkte Sicherheitskonfiguration für Internet Explorer aktiviert ist, lassen Sie die folgenden Websites auf dem Server zu, auf dem Sie den Agent installieren:<br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>https:\//aadcdn.msftauth.net</li><li>Verbundserver für Ihre Organisation, dem Azure AD vertraut (z. B. „https:\//sts.contoso.com“)</li> <br />Weitere Informationen finden Sie unter [Konfigurieren von Internet Explorer](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing). Wenn Sie in Ihrem Netzwerk einen Proxy verwenden, lesen Sie den Hinweis am Ende dieser Tabelle.|
| PowerShell-Version 4.0 (oder höher) ist installiert. | Windows Server 2012 enthält PowerShell-Version 3.0. Diese Version reicht für den Agent *nicht* aus.</br></br> Windows Server 2012 R2 und höhere Versionen enthalten eine geeignete PowerShell-Version.|
|FIPS (Federal Information Processing Standard) ist deaktiviert.|FIPS wird von Azure AD Connect Health-Agents nicht unterstützt.|

> [!IMPORTANT]
> Unter Windows Server Core wird die Installation des Azure AD Connect Health-Agents nicht unterstützt.


> [!NOTE]
> Wenn Sie über eine streng geschützte und eingeschränkte Umgebung verfügen, müssen Sie mehr als die in der Tabelle für die verstärkte Sicherheitskonfiguration für Internet Explorer aufgeführten URLs hinzufügen. Fügen Sie auch URLs hinzu, die in der Tabelle im nächsten Abschnitt aufgeführt sind.  


### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Ausgehende Verbindungen zu den Azure-Dienstendpunkten

Während der Installation und zur Laufzeit ist für den Agent die Konnektivität mit den Azure AD Connect Health-Dienstendpunkten erforderlich. Wenn ausgehende Verbindungen durch Firewalls blockiert werden, müssen Sie sicherstellen, dass die in der folgenden Tabelle aufgeführten URLs nicht standardmäßig blockiert werden. 

Deaktivieren Sie nicht die Sicherheitsüberwachung oder die Überprüfung dieser URLs. Lassen Sie diese URLs stattdessen wie anderen Internetdatenverkehr zu. 

Diese URLs ermöglichen die Kommunikation mit den Azure AD Connect Health-Dienstendpunkten. Weiter unten in diesem Artikel erfahren Sie, wie Sie [ausgehende Verbindungen](#test-connectivity-to-azure-ad-connect-health-service) mithilfe von `Test-AzureADConnectHealthConnectivity` testen.

| Domänenumgebung | Erforderliche Azure-Dienstendpunkte |
| --- | --- |
| Öffentlich | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42;.servicebus.windows.net – Port: 5671 (Dieser Endpunkt ist für die neueste Agent-Version nicht erforderlich.)</li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com (Dieser Endpunkt wird nur zu Ermittlungszwecken während der Registrierung verwendet.)</li> |
| Azure Deutschland | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https:\//www.office.de (Dieser Endpunkt wird nur zu Ermittlungszwecken während der Registrierung verwendet.)</li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com (Dieser Endpunkt wird nur zu Ermittlungszwecken während der Registrierung verwendet.)</li> |


## <a name="install-the-agent"></a>Installieren des Agents

Gehen Sie wie folgt vor, um den Azure AD Connect Health-Agent herunterzuladen und zu installieren: 

* Stellen Sie sicher, dass Sie die [Anforderungen](how-to-connect-health-agent-install.md#requirements) für Azure AD Connect Health erfüllen.
* Erste Schritte zur Verwendung von Azure AD Connect Health für AD FS:
    * [Laden Sie den Azure AD Connect Health-Agent für AD FS herunter](https://go.microsoft.com/fwlink/?LinkID=518973).
    * Lesen Sie die [Installationsanweisungen](#install-the-agent-for-ad-fs).
* Erste Schritte mit Azure AD Connect Health für die Sychronisierung:
    * [Laden Sie die aktuelle Version von Azure AD Connect herunter, und installieren Sie sie](https://go.microsoft.com/fwlink/?linkid=615771). Der Health-Agent für die Synchronisierung wird im Rahmen der Installation von Azure AD Connect (Version 1.0.9125.0 oder höher) installiert.
* Erste Schritte zur Verwendung von Azure AD Connect Health für Azure AD DS:
    * [Laden Sie den Azure AD Connect Health-Agent für Azure AD DS herunter](https://go.microsoft.com/fwlink/?LinkID=820540).
    * Lesen Sie die [Installationsanweisungen](#install-the-agent-for-azure-ad-ds).

## <a name="install-the-agent-for-ad-fs"></a>Installieren des Agents für AD FS

> [!NOTE]
> Ihr AD FS-Server sollte sich vom Synchronisierungsserver unterscheiden. Installieren Sie den AD FS-Agent nicht auf Ihrem Synchronisierungsserver.
>

Stellen Sie vor dem Installieren des Agents sicher, dass der Hostname Ihres AD FS-Servers eindeutig und nicht im AD FS-Dienst vorhanden ist.
Um die Installation des Agents zu starten, doppelklicken Sie auf die *EXE*-Datei, die Sie heruntergeladen haben. Wählen Sie im ersten Fenster die Option **Installieren** aus.

![Screenshot des Installationsfensters für den Azure AD Connect Health AD FS-Agent.](./media/how-to-connect-health-agent-install/install1.png)

Wählen Sie nach Abschluss der Installation die Option **Jetzt konfigurieren** aus.

![Screenshot der Bestätigungsmeldung für die Installation des Azure AD Connect Health AD FS-Agents.](./media/how-to-connect-health-agent-install/install2.png)

Es wird ein PowerShell-Fenster zum Starten des Agent-Registrierungsprozesses geöffnet. Melden Sie sich bei entsprechender Aufforderung mit einem Azure AD-Konto an, das über Berechtigungen zum Registrieren des Agents verfügt. Standardmäßig verfügt das globale Administratorkonto über diese Berechtigungen.

![Screenshot des Anmeldefensters für Azure AD Connect Health AD FS.](./media/how-to-connect-health-agent-install/install3.png)

Nachdem Sie sich angemeldet haben, wird der Vorgang in PowerShell fortgesetzt. Nach Abschluss des Vorgangs können Sie PowerShell schließen. Die Konfiguration ist abgeschlossen.

An diesem Punkt sollten die Agent-Dienste automatisch gestartet werden, sodass der Agent die erforderlichen Daten sicher in den Clouddienst hochladen kann.

Wenn Sie nicht alle Voraussetzungen erfüllt haben, werden im PowerShell-Fenster Warnungen angezeigt. Stellen Sie sicher, dass die [Anforderungen](how-to-connect-health-agent-install.md#requirements) erfüllt sind, bevor Sie den Agent installieren. Im folgenden Screenshot sehen Sie ein Beispiel für diese Warnungen.

![Screenshot des Konfigurationsskripts für Azure AD Connect Health AD FS.](./media/how-to-connect-health-agent-install/install4.png)

Suchen Sie auf dem Server nach den folgenden Diensten, um sicherzustellen, dass der Agent installiert wurde. Wenn Sie die Konfiguration durchgeführt haben, sollten sie bereits ausgeführt werden. Andernfalls werden sie gestoppt, bis die Konfiguration abgeschlossen ist.

* Azure AD Connect Health AD FS Diagnostics Service
* Azure AD Connect Health AD FS Insights Service
* Azure AD Connect Health AD FS Monitoring Service

![Screenshot der Azure AD Connect Health AD FS-Dienste.](./media/how-to-connect-health-agent-install/install5.png)


### <a name="enable-auditing-for-ad-fs"></a>Aktivieren der Überwachung für AD FS

> [!NOTE]
> Dieser Abschnitt gilt nur für AD FS-Server. Auf den Webanwendungs-Proxyservern müssen diese Schritte nicht ausgeführt werden.
>

Die Nutzungsanalysefunktion muss Daten erfassen und analysieren. Daher benötigt der Azure AD Connect Health-Agent die Informationen in den AD FS-Überwachungsprotokollen. Diese Protokolle sind standardmäßig nicht aktiviert. Verwenden Sie die folgenden Verfahren, um auf Ihren AD FS-Servern die AD FS-Überwachung zu aktivieren und die AD FS-Überwachungsprotokolle zu ermitteln.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>So aktivieren Sie die Überwachung für AD FS unter Windows Server 2012 R2

1. Öffnen Sie auf dem Startbildschirm die Komponente **Server-Manager**, und öffnen Sie dann **Lokale Sicherheitsrichtlinie**. Oder öffnen Sie auf der Taskleiste die Komponente **Server-Manager**, und wählen Sie dann **Tools > Lokale Sicherheitsrichtlinie** aus.
2. Wechseln Sie zum Ordner *Sicherheitseinstellungen\Lokale Richtlinien\Zuweisen von Benutzerrechten*. Doppelklicken Sie dann auf **Generieren von Sicherheitsüberwachungen**.
3. Vergewissern Sie sich auf der Registerkarte **Lokale Sicherheitseinstellung**, dass das AD FS-Dienstkonto aufgeführt wird. Wenn das Konto nicht aufgeführt ist, wählen Sie **Benutzer oder Gruppe hinzufügen** aus, und fügen Sie es dann der Liste hinzu. Klicken Sie anschließend auf **OK**.
4. Öffnen Sie zum Aktivieren der Überwachung ein Eingabeaufforderungsfenster mit erhöhten Rechten. Führen Sie dann den folgenden Befehl aus: 
    
    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. Schließen Sie **Lokale Sicherheitsrichtlinie**.
    >[!Important]
    >Die folgenden Schritte müssen nur für primäre AD FS-Server ausgeführt werden. 
1. Öffnen Sie das Snap-In **AD FS-Verwaltung**. (Wählen Sie in **Server-Manager** die Optionen **Tools** > **AD FS-Verwaltung** aus.)
1. Wählen Sie im Bereich **Aktionen** die Option **Verbunddiensteigenschaften bearbeiten** aus.
1. Wählen Sie im Dialogfeld **Verbunddiensteigenschaften** die Registerkarte **Ereignisse** aus.
1. Aktivieren Sie die Kontrollkästchen **Erfolgreiche Überprüfungen** und **Fehlerüberprüfungen**, und wählen Sie dann „OK“ aus.
1. Verwenden Sie den folgenden Befehl, um die ausführliche Protokollierung mithilfe von PowerShell zu aktivieren: 

    `Set-AdfsProperties -LOGLevel Verbose`

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>So aktivieren Sie die Überwachung für AD FS unter Windows Server 2016

1. Öffnen Sie auf dem Startbildschirm die Komponente **Server-Manager**, und öffnen Sie dann **Lokale Sicherheitsrichtlinie**. Oder öffnen Sie auf der Taskleiste die Komponente **Server-Manager**, und wählen Sie dann **Tools > Lokale Sicherheitsrichtlinie** aus.
2. Wechseln Sie zum Ordner *Sicherheitseinstellungen\Lokale Richtlinien\Zuweisen von Benutzerrechten*, und doppelklicken Sie dann auf **Generieren von Sicherheitsüberwachungen**.
3. Vergewissern Sie sich auf der Registerkarte **Lokale Sicherheitseinstellung**, dass das AD FS-Dienstkonto aufgeführt wird. Wenn das Konto nicht aufgeführt ist, wählen Sie **Benutzer oder Gruppe hinzufügen** aus, und fügen Sie der Liste das AD FS-Dienstkonto hinzu. Klicken Sie anschließend auf **OK**.
4. Öffnen Sie zum Aktivieren der Überwachung ein Eingabeaufforderungsfenster mit erhöhten Rechten. Führen Sie dann den folgenden Befehl aus: 

    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. Schließen Sie **Lokale Sicherheitsrichtlinie**.
    >[!Important]
    >Die folgenden Schritte müssen nur für primäre AD FS-Server ausgeführt werden.
1. Öffnen Sie das Snap-In **AD FS-Verwaltung**. (Wählen Sie in **Server-Manager** die Optionen **Tools** > **AD FS-Verwaltung** aus.)
1. Wählen Sie im Bereich **Aktionen** die Option **Verbunddiensteigenschaften bearbeiten** aus.
1. Wählen Sie im Dialogfeld **Verbunddiensteigenschaften** die Registerkarte **Ereignisse** aus.
1. Aktivieren Sie die Kontrollkästchen **Erfolgreiche Überprüfungen** und **Fehlerüberprüfungen**, und wählen Sie dann „OK“ aus. Erfolgreiche Überprüfungen und Fehlerüberprüfungen sollten standardmäßig aktiviert sein.
1. Öffnen Sie ein PowerShell-Fenster, und führen Sie den folgenden Befehl aus: 

    `Set-AdfsProperties -AuditLevel Verbose`

Standardmäßig ist die Überwachungsebene „Basic“ aktiviert. Weitere Informationen finden Sie unter [Optimierte AD FS-Überwachung unter Windows Server 2016](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server).


#### <a name="to-locate-the-ad-fs-audit-logs"></a>So ermitteln Sie die AD FS-Überwachungsprotokolle

1. Öffnen Sie die **Ereignisanzeige**.
2. Wechseln Sie zu **Windows-Protokolle**, und wählen Sie dann **Sicherheit** aus.
3. Wählen Sie auf der rechten Seite die Option **Aktuelle Protokolle filtern** aus.
4. Wählen Sie für **Ereignisquellen** den Eintrag **AD FS-Überwachung** aus.

    Weitere Informationen zu Überwachungsprotokollen finden Sie unter [Fragen zum Betrieb](reference-connect-health-faq.md#operations-questions).

    ![Screenshot des Fensters „Aktuelles Protokoll filtern“. Im Feld „Ereignisquellen“ ist die Option „AD FS-Überwachung“ ausgewählt.](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> Die AD FS-Überwachung kann mithilfe einer Gruppenrichtlinie deaktiviert werden. Wenn die AD FS-Überwachung deaktiviert ist, sind keine Nutzungsanalysen für Anmeldeaktivitäten verfügbar. Stellen Sie sicher, dass die AD FS-Überwachung nicht durch eine Gruppenrichtlinie deaktiviert wird.
>


## <a name="install-the-agent-for-sync"></a>Installieren des Agents für die Synchronisierung

Der Azure AD Connect Health-Agent für die Synchronisierung wird in der neuesten Version von Azure AD Connect automatisch installiert. Damit Sie Azure AD Connect für die Synchronisierung verwenden können, müssen Sie [die neueste Version von Azure AD Connect herunterladen](https://www.microsoft.com/download/details.aspx?id=47594) und installieren.

Suchen Sie auf dem Server nach den unten angegebenen Diensten, um sicherzustellen, dass der Agent installiert wurde. Wenn Sie die Konfiguration abgeschlossen haben, sollten die Dienste bereits ausgeführt werden. Andernfalls werden die Dienste gestoppt, bis die Konfiguration abgeschlossen ist.

* Azure AD Connect Health Sync Insights-Dienst
* Azure AD Connect Health Sync-Überwachungsdienst

![Screenshot, in dem die auf dem Server ausgeführten Azure AD Connect Health Sync-Dienste angezeigt werden.](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Bedenken Sie, dass Sie über Azure AD Premium verfügen müssen, um Azure AD Connect Health verwenden zu können. Wenn Sie nicht über Azure AD Premium verfügen, können Sie die Konfiguration nicht im Azure-Portal abschließen. Weitere Informationen finden Sie im Abschnitt [Anforderungen](how-to-connect-health-agent-install.md#requirements).
>
>

## <a name="manually-register-azure-ad-connect-health-for-sync"></a>Manuelles Registrieren von Azure AD Connect Health für die Synchronisierung

Wenn die Registrierung des Azure AD Connect Health-Agents für die Synchronisierung nach der erfolgreichen Installation von Azure AD Connect fehlschlägt, können Sie einen PowerShell-Befehl verwenden, um den Agent manuell zu registrieren.

> [!IMPORTANT]
> Verwenden Sie diesen PowerShell-Befehl nur, wenn die Agent-Registrierung nach der Installation von Azure AD Connect fehlschlägt.
>
>

Führen Sie den folgenden PowerShell-Befehl aus, um den Azure AD Connect Health-Agent für die Synchronisierung manuell zu registrieren. Die Azure AD Connect Health-Dienste werden gestartet, nachdem der Agent erfolgreich registriert wurde.

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

Der Befehl hat folgende Parameter:

* **AttributeFiltering**: `$true` (Standardwert) – wenn Azure AD Connect den Standardattributsatz nicht synchronisiert und für die Verwendung eines gefilterten Attributsatzes angepasst wurde. Verwenden Sie andernfalls `$false`.
* **StagingMode**: `$false` (Standardwert) – wenn sich der Azure AD Connect-Server *nicht* im Stagingmodus befindet. Verwenden Sie `$true`, wenn der Server für den Stagingmodus konfiguriert ist.

Wenn Sie zur Authentifizierung aufgefordert werden, verwenden Sie das globale Administratorkonto (z. B. admin@domain.onmicrosoft.com), das Sie zum Konfigurieren von Azure AD Connect verwendet haben.

## <a name="install-the-agent-for-azure-ad-ds"></a>Installieren des Agents für Azure AD DS

Um die Installation des Agents zu starten, doppelklicken Sie auf die *EXE*-Datei, die Sie heruntergeladen haben. Wählen Sie im ersten Fenster die Option **Installieren** aus.

![Screenshot des Installationsfensters für den Azure AD Connect Health-Agent für AD DS.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

Wählen Sie nach Abschluss der Installation die Option **Jetzt konfigurieren** aus.

![Screenshot des Fensters, in dem die Installation des Azure AD Connect Health-Agents für Azure AD DS abgeschlossen wurde.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

Es wird ein Eingabeaufforderungsfenster geöffnet. PowerShell führt den Befehl `Register-AzureADConnectHealthADDSAgent` aus. Melden Sie sich bei entsprechender Aufforderung bei Azure an.

![Screenshot des Anmeldefensters für den Azure AD Connect Health-Agent für Azure AD DS.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

Nachdem Sie sich angemeldet haben, wird der Vorgang in PowerShell fortgesetzt. Nach Abschluss des Vorgangs können Sie PowerShell schließen. Die Konfiguration ist abgeschlossen.

An diesem Punkt sollten die Dienste automatisch gestartet werden, sodass der Agent die Daten überwachen und erfassen kann. Wenn Sie nicht alle in den vorherigen Abschnitten beschriebenen Voraussetzungen erfüllt haben, werden im PowerShell-Fenster Warnungen angezeigt. Stellen Sie sicher, dass die [Anforderungen](how-to-connect-health-agent-install.md#requirements) erfüllt sind, bevor Sie den Agent installieren. Im folgenden Screenshot sehen Sie ein Beispiel für diese Warnungen.

![Screenshot einer Warnung für die Konfiguration des Azure AD Connect Health-Agents für Azure AD DS.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

Suchen Sie auf dem Domänencontroller nach den folgenden Diensten, um sicherzustellen, dass der Agent installiert wurde:

* Azure AD Connect Health AD DS Insights Service
* Azure AD Connect Health AD DS Monitoring Service

Wenn Sie die Konfiguration durchgeführt haben, sollten diese Dienste ausgeführt werden. Andernfalls werden sie gestoppt, bis die Konfiguration abgeschlossen ist.

![Screenshot, in dem die auf dem Domänencontroller ausgeführten Dienste angezeigt werden.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quickly-install-the-agent-on-multiple-servers"></a>Schnelles Installieren des Agents auf mehreren Servern

1. Erstellen Sie ein Benutzerkonto in Azure AD. Schützen Sie es mit einem Kennwort.
2. Weisen Sie im Portal die Rolle **Besitzer** für dieses lokale Azure AD-Konto in Azure AD Connect Health zu. Führen Sie [folgende Schritte](how-to-connect-health-operations.md#manage-access-with-azure-rbac) aus. Weisen Sie die Rolle allen Dienstinstanzen zu. 
3. Laden Sie die *EXE*-MSI-Datei auf den lokalen Domänencontroller für die Installation herunter.
4. Führen Sie das folgende Skript aus. Ersetzen Sie die Parameter durch Ihr neues Benutzerkonto und das zugehörige Kennwort. 

    ```powershell
    AdHealthAddsAgentSetup.exe /quiet
    Start-Sleep 30
    $userName = "NEWUSER@DOMAIN"
    $secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
    $myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
    import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
     
    Register-AzureADConnectHealthADDSAgent -Credential $myCreds
    
    ```

Wenn Sie damit fertig sind, können Sie den Zugriff für das lokale Konto entfernen, indem Sie mindestens eine der folgenden Aufgaben ausführen: 
* Entfernen Sie die Rollenzuweisung für das lokale Konto für Azure AD Connect Health.
* Rotieren Sie das Kennwort für das lokale Konto. 
* Deaktivieren Sie das lokale Azure AD-Konto.
* Löschen Sie das lokale Azure AD-Konto.  

## <a name="register-the-agent-by-using-powershell"></a>Registrieren des Agents mit PowerShell

Nachdem Sie den entsprechenden Agent (über die Datei *setup.exe*) installiert haben, können Sie den Agent abhängig von der Rolle mithilfe der folgenden PowerShell-Befehle registrieren. Öffnen Sie ein PowerShell-Fenster, und führen Sie den entsprechenden Befehl aus:

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

Diese Befehle akzeptieren `Credential` als Parameter, um die Registrierung nicht interaktiv oder auf einem Computer abzuschließen, auf dem Server Core ausgeführt wird. Bedenken Sie Folgendes:
* Sie können `Credential` in einer PowerShell-Variable erfassen, die als Parameter übergeben wird.
* Sie können eine beliebige Azure AD-Identität angeben, die über die Berechtigungen zum Registrieren der Agents verfügt und für die *keine* mehrstufige Authentifizierung aktiviert ist.
* Standardmäßig verfügen globale Administratoren über die Berechtigungen zum Registrieren der Agents. Sie können auch weniger privilegierten Identitäten die Ausführung dieses Schritts erlauben. Weitere Informationen finden Sie unter [Azure RBAC](how-to-connect-health-operations.md#manage-access-with-azure-rbac).

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Konfigurieren von Azure AD Connect Health-Agents für die Verwendung eines HTTP-Proxys

Sie können Azure AD Connect Health-Agents für die Arbeit mit einem HTTP-Proxy konfigurieren.

> [!NOTE]
> * `Netsh WinHttp set ProxyServerAddress` wird nicht unterstützt. Zum Senden von Webanforderungen verwendet der Agent „System.Net“ anstelle von Windows HTTP-Diensten.
> * Die konfigurierte HTTP-Proxyadresse wird für das Weiterleiten verschlüsselter HTTPS-Nachrichten verwendet.
> * Authentifizierte Proxys (mit HTTPBasic) werden nicht unterstützt.
>
>

### <a name="change-the-agent-proxy-configuration"></a>Ändern der Agent-Proxykonfiguration

Wenn Sie den Azure AD Connect Health-Agent für die Verwendung eines HTTP-Proxy konfigurieren möchten, haben Sie die folgenden Möglichkeiten:
* Sie können vorhandene Proxyeinstellungen importieren.
* Sie können die Proxyadressen manuell angeben.
* Sie können die vorhandene Proxykonfiguration löschen.

> [!NOTE]
> Sie müssen alle Azure AD Connect Health-Agent-Dienste neu starten, damit die Proxyeinstellungen aktualisiert werden. Führen Sie den folgenden Befehl aus:
>
> `Restart-Service AzureADConnectHealth*`

#### <a name="import-existing-proxy-settings"></a>Importieren vorhandener Proxyeinstellungen

Sie können die HTTP-Proxyeinstellungen von Internet Explorer importieren, damit die Azure AD Connect Health-Agents diese Einstellungen verwenden können. Führen Sie den folgenden PowerShell-Befehl auf allen Servern aus, auf denen der Health-Agent ausgeführt wird:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings
```

Sie können die WinHTTP-Proxyeinstellungen importieren, damit die Azure AD Connect Health-Agents diese Einstellungen verwenden können. Führen Sie den folgenden PowerShell-Befehl auf allen Servern aus, auf denen der Health-Agent ausgeführt wird:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp
```

#### <a name="specify-proxy-addresses-manually"></a>Manuelles Angeben von Proxyadressen

Sie können einen Proxyserver manuell angeben. Führen Sie den folgenden PowerShell-Befehl auf allen Servern aus, auf denen der Health-Agent ausgeführt wird:

```powershell
Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port
```

Hier sehen Sie ein Beispiel: 

`Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443`

In diesem Beispiel:
* Als `address`-Einstellung kann ein über DNS auflösbarer Servername oder eine IPv4-Adresse verwendet werden.
* Sie können `port` weglassen. Wenn Sie dies tun, wird Port 443 als Standardport verwendet.

#### <a name="clear-the-existing-proxy-configuration"></a>Löschen der vorhandenen Proxykonfiguration

Sie können die vorhandene Proxykonfiguration löschen, indem Sie den folgenden Befehl ausführen:

```powershell
Set-AzureAdConnectHealthProxySettings -NoProxy
```

### <a name="read-current-proxy-settings"></a>Lesen der aktuellen Proxyeinstellungen

Sie können die aktuellen Proxyeinstellungen lesen, indem Sie den folgenden Befehl ausführen:

```powershell
Get-AzureAdConnectHealthProxySettings
```

## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Testen der Verbindung mit dem Azure AD Connect Health-Dienst

Gelegentlich kann es zu Unterbrechungen der Verbindung zwischen dem Azure AD Connect Health-Agent und dem Azure AD Connect Health-Dienst kommen. Mögliche Ursachen für diese Verbindungsunterbrechungen sind Netzwerkprobleme, Berechtigungsprobleme und verschiedene andere Probleme.

Wenn der Agent für mehr als zwei Stunden keine Daten an den Azure AD Connect Health-Dienst senden kann, wird im Portal die folgende Warnung angezeigt: „Die Daten des Integritätsdiensts sind nicht aktuell.“ 

Durch Ausführen des folgenden PowerShell-Befehls können Sie herausfinden, ob der betroffene Azure AD Connect Health-Agent Daten für den Azure AD Connect Health-Dienst hochladen kann:

```powershell
Test-AzureADConnectHealthConnectivity -Role ADFS
```

Der Rollenparameter akzeptiert derzeit die folgenden Werte:

* ADFS
* Synchronisierung
* ADDS

> [!NOTE]
> Um das Konnektivitätstool verwenden zu können, müssen Sie zunächst den Agent registrieren. Wenn Sie die Agent-Registrierung nicht abschließen können, müssen Sie sicherstellen, dass alle [Anforderungen](how-to-connect-health-agent-install.md#requirements) für Azure AD Connect Health erfüllt sind. Die Konnektivität wird bei der Agent-Registrierung standardmäßig getestet.
>
>

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden verwandten Artikel:

* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health-Vorgänge](how-to-connect-health-operations.md)
* [Verwenden von Azure AD Connect Health mit AD FS](how-to-connect-health-adfs.md)
* [Verwenden von Azure AD Connect Health für die Synchronisierung](how-to-connect-health-sync.md)
* [Verwenden von Azure AD Connect Health mit Azure AD DS](how-to-connect-health-adds.md)
* [Azure AD Connect Health – FAQ](reference-connect-health-faq.md)
* [Azure AD Connect Health: Versionsverlauf](reference-connect-health-version-history.md)

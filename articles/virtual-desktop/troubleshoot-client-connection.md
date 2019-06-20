---
title: Remotedesktop-Clientverbindungen in Windows Virtual Desktop – Azure
description: Informationen zum Beheben von Problemen beim Einrichten von Clientverbindungen in einer Windows Virtual Desktop-Mandantenumgebung
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: f88dee579e44a01dc1a7404ef6a670de34063552
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833569"
---
# <a name="remote-desktop-client-connections"></a>Remotedesktop-Clientverbindungen

Verwenden Sie diesen Artikel zum Beheben von Problemen mit Windows Virtual Desktop-Clientverbindungen.

## <a name="provide-feedback"></a>Feedback geben

Solange sich Windows Virtual Desktop in der Vorschauphase befindet, werden noch keine Supportanfragen angenommen. In der [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) können Sie sich mit dem Produktteam und aktiven Communitymitgliedern über den Windows Virtual Desktop-Dienst austauschen.

## <a name="you-cant-open-a-web-client"></a>Ein Webclient kann nicht geöffnet werden.

Vergewissern Sie sich, dass eine Internetverbindung vorhanden ist, indem Sie eine andere Website öffnen, z. B. [www.Bing.com](https://www.bing.com).

Bestätigen Sie mit **nslookup**, dass das DNS den FQDN auflösen kann:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Versuchen Sie, eine Verbindung mit einem anderen Client herzustellen, z. B. dem Remotedesktopclient für Windows 7 oder Windows 10, und überprüfen Sie, ob Sie den Webclient öffnen können.

### <a name="error-opening-another-site-fails"></a>Fehler Beim Öffnen einer anderen Seite tritt ein Fehler auf.

**Ursache:** Netzwerkprobleme oder Ausfälle

**Behebung:** Wenden Sie sich an den Netzwerksupport.

### <a name="error-nslookup-cannot-resolve-the-name"></a>Fehler Nslookup kann den Namen nicht auflösen.

**Ursache:** Netzwerkprobleme oder Ausfälle

**Behebung:** Wenden Sie sich an den Netzwerksupport.

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>Fehler Sie können keine Verbindung herstellen, aber andere Clients können Verbindungen herstellen.

**Ursache:** Der Browser verhält sich nicht erwartungsgemäß und funktioniert nicht mehr.

**Behebung:** Gehen Sie folgendermaßen vor, um Probleme mit dem Browser zu behandeln.

1. Starten Sie den Browser neu.
2. Löschen Sie die Browsercookies. Weitere Informationen finden Sie unter [Löschen von Cookiedateien in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Löschen Sie den Browsercache. Weitere Informationen finden Sie unter [Leeren des Browsercache für Ihren Browser](https://binged.it/2RKyfdU).
4. Öffnen Sie den Browser im privaten Modus.

## <a name="web-client-stops-responding-or-disconnects"></a>Der Webclient reagiert nicht mehr oder trennt die Verbindung.

Versuchen Sie, eine Verbindung mit einem anderen Browser oder Client herzustellen.

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Fehler Andere Browser und Clients weisen ebenfalls Fehlfunktionen auf oder können nicht geöffnet werden.

**Ursache:** Probleme oder Ausfälle im Netzwerk oder Betriebssystem

**Behebung:** Wenden Sie sich an die Supportteams.

## <a name="web-client-keeps-prompting-for-credentials"></a>Der Webclient fordert immer wieder zur Eingabe von Anmeldeinformationen auf.

Wenn der Webclient immer wieder zur Eingabe von Anmeldeinformationen auffordert, gehen Sie wie folgt vor.

1. Vergewissern Sie sich, dass die URL des Webclients richtig ist.
2. Vergewissern Sie sich, dass die Anmeldeinformationen für die an die URL gebundene Windows Virtual Desktop-Umgebung gültig sind.
3. Löschen Sie die Browsercookies. Weitere Informationen finden Sie unter [Löschen von Cookiedateien in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Löschen Sie den Browsercache. Weitere Informationen finden Sie unter [Leeren des Browsercache für Ihren Browser](https://binged.it/2RKyfdU).
5. Öffnen Sie den Browser im privaten Modus.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Der Remotedesktopclient für Windows 7 oder Windows 10 reagiert nicht mehr oder kann nicht geöffnet werden.

Verwenden Sie die folgenden PowerShell-Cmdlets, um Out-of-Band-Clientregistrierungen (OOB) zu bereinigen.

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Navigieren Sie zu **%AppData%\RdClientRadc**, und löschen Sie den gesamten Inhalt.

Deinstallieren Sie den Remotedesktopclient für Windows 7 und Windows 10, und installieren Sie ihn erneut.

## <a name="troubleshooting-end-user-connectivity"></a>Beheben von Endbenutzer-Konnektivitätsproblemen

Manchmal können Benutzer auf ihre Feeds und lokalen Ressourcen zugreifen, haben aber Probleme mit der Konfiguration, der Verfügbarkeit oder der Leistung, die sie am Zugreifen auf Remoteressourcen hindern. In diesen Fällen erhält der Benutzer Meldungen ähnlich der folgenden:

![Fehlermeldung bei Remotedesktopverbindungen](media/eb76b666808bddb611448dfb621152ce.png)

![Fehlermeldung bei nicht möglicher Verbindung mit Gateway](media/a8fbb9910d4672147335550affe58481.png)

Befolgen Sie für Fehlercodes zu Clientverbindungen diese allgemeinen Problembehandlungsanweisungen.

1. Überprüfen Sie den Benutzernamen und die Uhrzeit beim Auftreten des Problems.
2. Öffnen Sie **PowerShell**, und stellen Sie eine Verbindung mit dem Windows Virtual Desktop-Mandanten her, bei dem das Problem gemeldet wurde.
3. Bestätigen Sie die Verbindung mit den richtigen Mandanten mithilfe von **Get-RdsTenant.**
4. Vergewissern Sie sich mithilfe der Cmdlets **Get-RdsHostPool** und **Get-RdsSessionHost**, dass das Troubleshooting für den richtigen Hostpool ausgeführt wird.
5. Führen Sie den nachstehenden Befehl aus, um eine Liste aller fehlerhaften Aktivitäten des Typs „Verbindung“ für das angegebene Zeitfenster abzurufen:

    ```PowerShell
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. Führen Sie mithilfe der **ActivityId** aus der vorherigen Cmdlet-Ausgabe den nachstehenden Befehl aus:

    ```PowerShell
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

7. Der Befehl erzeugt eine Ausgabe ähnlich der weiter unten angegebenen. Verwenden Sie **ErrorCodeSymbolic** und **ErrorMessage**, um Troubleshooting für die Grundursache auszuführen.

    ```PowerShell
    ErrorSource       : <Source>
    ErrorOperation    : <Operation>
    ErrorCode         : <Error code>
    ErrorCodeSymbolic : <Error code string>
    ErrorMessage      : <Error code message>
    ErrorInternal     : <Internal for the OS>
    ReportedBy        : <Reported by component>
    Time              : <Timestampt>
    ```

### <a name="error-oaddusertogroupfailed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32errornosuchmember"></a>Fehler O_ADD_USER_TO_GROUP_FAILED / Fehler beim Hinzufügen von Benutzer = ≤Benutzername≥ zu Gruppe = Remotedesktop-Benutzer. Ursache: Win32.ERROR_NO_SUCH_MEMBER

**Ursache:** Die VM ist nicht in die Domäne eingebunden, in der sich das Benutzerobjekt befindet.

**Behebung:** Fügen Sie die VM der richtigen Domäne hinzu. Weitere Informationen finden Sie unter [Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Domäne](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).

### <a name="error-nslookup-cannot-resolve-the-name"></a>Fehler Nslookup kann den Namen nicht auflösen.

**Ursache:** Netzwerkprobleme oder Ausfälle

**Behebung:** Wenden Sie sich an den Netzwerksupport.

### <a name="error-connectionfailedclientprotocolerror"></a>Fehler ConnectionFailedClientProtocolError

**Ursache:** Die VMs, mit denen der Benutzer eine Verbindung herstellen möchte, sind nicht in eine Domäne eingebunden.

**Behebung:** Verbinden Sie alle VMs, die Teil eines Hostpools sind, mit dem Domänencontroller.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Der Benutzer stellt eine Verbindung her, aber es wird nichts angezeigt (kein Feed).

Ein Benutzer kann Remotedesktopclients starten und sich authentifizieren, ihm werden jedoch im Websuchfeed keine Symbole angezeigt.

Vergewissern Sie sich, dass der Benutzer, der die Probleme meldet, mithilfe dieser Befehlszeile Anwendungsgruppen zugewiesen wurde:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Vergewissern Sie sich, dass sich der Benutzer mit den richtigen Anmeldeinformationen anmeldet.

Wenn der Webclient verwendet wird, vergewissern Sie sich, dass keine Probleme mit zwischengespeicherten Anmeldeinformationen vorliegen.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Problembehandlung von Windows Virtual Desktop und die Eskalationspfade finden Sie unter [Überblick über Problembehandlung, Feedback und Support](troubleshoot-set-up-overview.md).
- Informationen zur Problembehandlung beim Erstellen eines Mandanten- und Hostpools in einer Windows Virtual Desktop-Umgebung finden Sie unter [Mandanten- und Hostpoolerstellung](troubleshoot-set-up-issues.md).
- Informationen zur Problembehandlung bei der Konfiguration eines virtuellen Computers (VM) in Windows Virtual Desktop finden Sie unter [Konfiguration des virtuellen Sitzungshostcomputers](troubleshoot-vm-configuration.md).
- Informationen zur Problembehandlung bei der Verwendung von PowerShell mit Windows Virtual Desktop finden Sie unter [Windows Virtual Desktop – PowerShell](troubleshoot-powershell.md).
- Weitere Informationen zum Vorschaudienst finden Sie unter [Umgebung der Windows Desktop-Vorschau](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?).
- Ein Tutorial zur Problembehandlung finden Sie unter [Tutorial: Problembehandlung von Bereitstellungen der Resource Manager-Vorlage](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Informationen zur Überwachung von Aktionen finden Sie unter [Überwachen von Vorgängen mit Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Weitere Informationen zu Aktionen zum Bestimmen von Fehlern während der Bereitstellung finden Sie unter [Anzeigen von Bereitstellungsvorgängen mit dem Azure-Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).

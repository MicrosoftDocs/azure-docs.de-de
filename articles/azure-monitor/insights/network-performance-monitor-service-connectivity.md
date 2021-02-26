---
title: Netzwerkleistungsmonitor-Lösung mit Dienstkonnektivitätmonitor – Azure Log Analytics
description: Verwenden Sie den Dienstkonnektivitätsmonitor von Netzwerkleistungsmonitor, um die Netzwerkkonnektivität an einem beliebigen Endpunkt zu überwachen, der über einen geöffneten TCP-Port verfügt.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 5d7c7068c4a8669cdff782267030d38ac0f73584
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832928"
---
# <a name="service-connectivity-monitor"></a>Dienstkonnektivitätsmonitor

> [!IMPORTANT]
> Ab dem 1. Juli 2021 ist es nicht mehr möglich, einem bestehenden Arbeitsbereich neue Tests hinzuzufügen oder einen neuen Arbeitsbereich im Netzwerkleistungsmonitor zu aktivieren. Sie können weiterhin die Tests verwenden, die vor dem 1. Juli 2021 erstellt wurden. [Migrieren Sie Ihre Tests vor dem 29. Februar 2024 aus dem Netzwerkleistungsmonitor zum neuen Verbindungsmonitor](https://docs.microsoft.com/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor) in Azure Network Watcher, um die Dienstunterbrechungen im Zusammenhang mit Ihren aktuellen Workloads zu minimieren.

Mit dem Dienstkonnektivitätsmonitor von [Netzwerkleistungsmonitor](network-performance-monitor.md) können Sie die Netzwerkkonnektivität an einem beliebigen Endpunkt überwachen, der über einen geöffneten TCP-Port verfügt. Zu solchen Endpunkten zählen Websites, SaaS-Anwendungen, PaaS-Anwendungen und SQL-Datenbanken. 

Der Dienstkonnektivitätsmonitor ermöglicht Folgendes: 

- Überwachen der Netzwerkkonnektivität Ihrer Anwendungen und Netzwerkdienste von mehreren Zweigstellen oder Standorten. Zu den Anwendungen und Netzwerkdiensten gehören Microsoft 365, Dynamics CRM, interne Line-of-Business-Anwendungen und SQL-Datenbanken.
- Ausführen integrierter Tests zur Überwachung der Netzwerkkonnektivität mit Microsoft 365- und Dynamics 365-Endpunkten 
- Ermitteln von Antwortzeiten, Netzwerkwartezeiten und Paketverlusten beim Herstellen der Verbindung mit dem Endpunkt
- Ermitteln, ob eine mangelhafte Anwendungsleistung auf das Netzwerk oder auf ein Problem beim Anwendungsanbieter zurückzuführen ist
- Erkennen von Hotspots im Netzwerk, die möglicherweise die Leistung der Anwendung beeinträchtigen, durch Betrachtung der Hop-spezifischen Wartezeiten auf einer Topologiekarte


![Dienstkonnektivitätsmonitor](media/network-performance-monitor-service-endpoint/service-endpoint-intro.png)


## <a name="configuration"></a>Konfiguration 
Öffnen Sie die [Netzwerkleistungsmonitor](network-performance-monitor.md)-Lösung, und wählen Sie die Schaltfläche **Konfigurieren** aus, um die Konfiguration für den Netzwerkleistungsmonitor zu öffnen.

![Konfigurieren des Netzwerkleistungsmonitors](media/network-performance-monitor-service-endpoint/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>Konfigurieren von Log Analytics-Agents für die Überwachung
Aktivieren Sie auf den für die Überwachung verwendeten Knoten die folgenden Firewallregeln, damit die Lösung die Topologie zwischen Ihren Knoten und dem Dienstendpunkt ermitteln kann: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Erstellen von Tests für den Dienstkonnektivitätsmonitor 

Beginnen Sie mit der Erstellung Ihrer Tests zur Überwachung der Netzwerkkonnektivität mit den Dienstendpunkten.

1. Klicken Sie auf die Registerkarte **Dienstkonnektivitätsmonitor**.
2. Klicken Sie auf **Test hinzufügen**, und geben Sie einen Namen und eine Beschreibung für den Test ein. Sie können maximal 450 Tests pro Arbeitsbereich erstellen. 
3. Wählen Sie die Art des Tests aus:<br>

    * Wählen Sie **Web** aus, wenn Sie die Konnektivität mit einem Dienst überwachen möchten, der auf HTTP/S-Anforderungen reagiert, etwa outlook.office365.com oder bing.com.<br>
    * Wählen Sie **Netzwerk** aus, wenn Sie die Konnektivität mit einem Dienst überwachen möchten, der auf TCP-Anforderungen, aber nicht auf HTTP/S-Anforderungen reagiert, etwa einem SQL-Server, FTP-Server oder SSH-Port. 
    * Beispiel: Um einen Webtest für ein Blobspeicherkonto zu erstellen, wählen Sie **Web** aus und geben Sie das Ziel als „*yourstorageaccount*.blob.core.windows.net“ ein. Mit [diesem Link](../../storage/common/storage-account-overview.md#storage-account-endpoints) können Sie auf ähnliche Weise Tests für einen anderen Tabellenspeicher, einen Queue Storage oder Azure Files erstellen.
4. Wenn Sie keine Netzwerkmessung durchführen möchten, z.B. von Netzwerklatenz, Paketverlusten oder Topologieermittlung, deaktivieren Sie das Kontrollkästchen **Netzwerkmessungen durchführen**. Zur optimalen Nutzung der Funktion sollten Sie das Kontrollkästchen aktiviert lassen. 
5. Geben Sie unter **Ziel** die URL/den FQDN/die IP-Adresse ein, für die bzw. den Sie die Netzwerkkonnektivität überwachen möchten.
6. Geben Sie unter **Portnummer** die Portnummer des Zieldiensts ein. 
7. Geben Sie unter **Testhäufigkeit** einen Wert dafür ein, wie häufig der Test ausgeführt werden soll. 
8. Wählen Sie die Knoten aus, von denen aus Sie die Netzwerkkonnektivität mit dem Dienst überwachen möchten. Stellen Sie sicher, dass die Anzahl der pro Test hinzugefügten Agents kleiner als 150 ist. Jeder Agent kann maximal 150 Endpunkte/Agents testen.

    >[!NOTE]
    > Bei serverbasierten Windows-Knoten verwendet die Funktion TCP-basierte Anforderungen für die Netzwerkmessungen. Bei clientbasierten Windows-Knoten verwendet die Funktion ICMP-basierte Anforderungen für die Netzwerkmessungen. In einigen Fällen blockiert die Zielanwendung eingehende ICMP-basierte Anforderungen, wenn die Knoten auf Windows-Clients basieren. Die Lösung kann keine Netzwerkmessungen ausführen. Für solche Fälle empfiehlt sich die Verwendung auf Windows Server basierender Knoten. 

9. Wenn keine Integritätsereignisse für die ausgewählten Elemente erstellt werden sollen, deaktivieren Sie **Enable Health Monitoring in the targets covered by this test** (Integritätsüberwachung für die von diesem Test abgedeckten Ziele aktivieren). 
10. Wählen Sie Überwachungsbedingungen aus. Sie können benutzerdefinierte Schwellenwerte für das Generieren von Integritätsereignissen festlegen, indem Sie Schwellenwerte eingeben. Sobald der Wert einer Bedingung den festgelegten Schwellenwert für das ausgewählte Netzwerk- oder Subnetzpaar überschreitet, wird ein Integritätsereignis generiert. 
11. Wählen Sie zum Speichern der Konfiguration **Speichern** aus. 

    ![Testkonfigurationen für den Dienstkonnektivitätsmonitor](media/network-performance-monitor-service-endpoint/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Exemplarische Vorgehensweise 

Wechseln Sie zur Dashboardansicht des Netzwerkleistungsmonitors. Auf der Seite **Dienstkonnektivitätsmonitor** erhalten Sie eine Zusammenfassung der Integrität der verschiedenen erstellten Tests. 

![Seite „Dienstkonnektivitätsmonitor“](media/network-performance-monitor-service-endpoint/service-endpoint-blade.png)

Wählen Sie die Kachel aus, um Details zu den Tests auf der Seite **Tests** anzuzeigen. Die linke Tabelle gibt Aufschluss über die Integrität und die Werte der Dienstreaktionszeit, der Netzwerkwartezeit und der Paketverluste für alle Tests zu einem bestimmten Zeitpunkt. Mit dem Steuerelement „Network State Recorder“ können Sie die Ansicht der Netzwerkmomentaufnahme für einen anderen Punkt in der Vergangenheit anzeigen. Wählen Sie in der Tabelle den Test aus, den Sie untersuchen möchten. Die Diagramme auf der rechten Seite geben Aufschluss über den historischen Trend von Verlusten, Wartezeiten und Antwortzeiten. Wählen Sie den Link **Testdetails** aus, um die Leistung für die einzelnen Knoten anzuzeigen.

![Dienstkonnektivitätsmonitor-Tests](media/network-performance-monitor-service-endpoint/service-endpoint-tests.png)

In der Ansicht **Testknoten** können Sie die Netzwerkkonnektivität der einzelnen Knoten beobachten. Wählen Sie den Knoten aus, dessen Leistung beeinträchtigt ist. Dies ist der Knoten, an dem eine nicht zufriedenstellende Anwendungsgeschwindigkeit festgestellt wurde.

Ermitteln Sie, ob die mangelhafte Anwendungsleistung auf das Netzwerk oder auf ein Problem beim Anwendungsanbieter zurückzuführen ist. Untersuchen Sie hierzu die Korrelation zwischen der Anwendungsantwortzeit und der Netzwerkwartezeit. 

* **Anwendungsproblem:** Eine Spitze bei der Antwortzeit bei konsistenter Netzwerkwartezeit deutet darauf hin, dass das Netzwerk einwandfrei funktioniert und möglicherweise ein Problem mit der Anwendung vorliegt. 

    ![Anwendungsproblem beim Dienstkonnektivitätsmonitor](media/network-performance-monitor-service-endpoint/service-endpoint-application-issue.png)

* **Netzwerkproblem:** Eine Spitze bei der Antwortzeit in Verbindung mit einer entsprechenden Spitze bei der Netzwerkwartezeit deutet darauf hin, dass die längere Antwortzeit auf eine erhöhte Netzwerkwartezeit zurückzuführen ist. 

    ![Netzwerkproblem beim Dienstkonnektivitätsmonitor](media/network-performance-monitor-service-endpoint/service-endpoint-network-issue.png)

Nachdem Sie festgestellt haben, dass das Problem auf das Netzwerk zurückzuführen ist, wählen Sie den Ansichtslink **Topologie** aus, um den problematischen Hop auf der Topologiekarte zu ermitteln. In der folgenden Abbildung ist ein Beispiel angegeben. Von der Gesamtwartezeit von 105 ms zwischen Knoten und Anwendungsendpunkt entfallen 96 ms auf den rot markierten Hop. Nachdem Sie den problematischen Hop identifiziert haben, können Sie Korrekturmaßnahmen ergreifen. 

![Endpunkttopologie des Dienstkonnektivitätsmonitors](media/network-performance-monitor-service-endpoint/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnose 

Gehen Sie im Fall einer Anomalie wie folgt vor:

* Falls Antwortzeit, Netzwerkverlust und Wartezeit des Diensts als nicht verfügbar angezeigt werden, können folgende Gründe vorliegen:

    - Die Anwendung ist nicht verfügbar.
    - Der Knoten, der zum Überprüfen der Netzwerkkonnektivität mit dem Dienst verwendet wird, ist nicht verfügbar.
    - In die Testkonfiguration wurde ein falsches Ziel eingegeben.
    - Der Knoten verfügt nicht über Netzwerkkonnektivität.

* Falls eine gültige Antwortzeit des Diensts angezeigt wird, aber keine Angaben zu Netzwerkverlust und Wartezeit verfügbar sind, können folgende Gründe vorliegen:

    - Wenn es sich bei dem Knoten, der zum Überprüfen der Netzwerkkonnektivität mit dem Dienst verwendet wird, um einen Windows-Clientcomputer handelt, blockiert der Zieldienst möglicherweise ICMP-Anforderungen, oder eine Netzwerkfirewall blockiert ICMP-Anforderungen des Knotens.
    - Das Kontrollkästchen **Netzwerkmessungen durchführen** ist in der Testkonfiguration deaktiviert. 

* Falls die Antwortzeit des Diensts als nicht verfügbar angezeigt wird, aber gültige Angaben zu Netzwerkverlust und Wartezeit verfügbar sind, ist der Zieldienst möglicherweise keine Webanwendung. Bearbeiten Sie die Testkonfiguration, und wählen Sie für den Testtyp **Netzwerk** anstelle von **Web** aus. 

* Ermitteln Sie im Fall einer langsamen Anwendung, ob die mangelhafte Anwendungsleistung auf das Netzwerk oder auf ein Problem beim Anwendungsanbieter zurückzuführen ist.

## <a name="gcc-office-urls-for-us-government-customers"></a>GCC Office-URLs für US Government-Kunden
Für die Region „US Government Virginia“ sind nur DOD-URLs in NPM integriert. Kunden, die GCC-URLs verwenden, müssen benutzerdefinierte Tests erstellen und jede URL einzeln hinzufügen.

| Feld | GCC |
|:---   |:--- |
| Office 365-Portal und freigegebene | portal.apps.mil |
| Office 365-Authentifizierung und -Identität | * login.microsoftonline.us <br> * api.login.microsoftonline.com <br> * clientconfig.microsoftonline-p.net <br> * login.microsoftonline.com <br> * login.microsoftonline-p.com <br> * login.windows.net <br> * loginex.microsoftonline.com <br> * login-us.microsoftonline.com <br> * nexus.microsoftonline-p.com <br> * mscrl.microsoft.com <br> * secure.aadcdn.microsoftonline-p.com |
| Office Online | * adminwebservice.gov.us.microsoftonline.com <br>  * adminwebservice-s1-bn1a.microsoftonline.com <br> * adminwebservice-s1-dm2a.microsoftonline.com <br> * becws.gov.us.microsoftonline.com <br> * provisioningapi.gov.us.microsoftonline.com <br> * officehome.msocdn.us <br> * prod.msocdn.us <br> * portal.office365.us <br> * webshell.suite.office365.us <br> * www .office365.us <br> * activation.sls.microsoft.com <br> * crl.microsoft.com <br> * go.microsoft.com <br> * insertmedia.bing.office.net <br> * ocsa.officeapps.live.com <br> * ocsredir.officeapps.live.com <br> * ocws.officeapps.live.com <br> * office15client.microsoft.com <br>* officecdn.microsoft.com <br> * officecdn.microsoft.com.edgesuite.net <br> * officepreviewredir.microsoft.com <br> * officeredir.microsoft.com <br> * ols.officeapps.live.com  <br> * r.office.microsoft.com <br> * cdn.odc.officeapps.live.com <br> * odc.officeapps.live.com <br> * officeclient.microsoft.com |
| Exchange Online | * outlook.office365.us <br> * attachments.office365-net.us <br> * autodiscover-s.office365.us <br> * manage.office365.us <br> * scc.office365.us |
| MS-Teams | gov.teams.microsoft.us | 

## <a name="next-steps"></a>Nächste Schritte
Mit [Protokollsuchen](../log-query/log-query-overview.md) können Sie Detaildatensätze mit Netzwerkleistungsdaten anzeigen.

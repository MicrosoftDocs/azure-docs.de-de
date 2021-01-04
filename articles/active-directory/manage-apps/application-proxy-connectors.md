---
title: Grundlegendes zu Azure AD-Anwendungsproxyconnectors | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zu Azure AD-Anwendungsproxyconnectors.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: be5ce5b3eebb2f784469680cf7614df6ca750b55
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658263"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Grundlegendes zu Azure AD-Anwendungsproxyconnectors

Connectors sind die Komponenten, die den Azure AD-Anwendungsproxy erst möglich machen. Sie sind einfach aufgebaut, leicht bereitzustellen und zu verwalten und haben eine hohe Leistungsstärke. Dieser Artikel erläutert, was Connectors sind und wie sie funktionieren, und stellt einige Vorschläge für die Optimierung Ihrer Bereitstellung vor.

## <a name="what-is-an-application-proxy-connector"></a>Was ist ein Anwendungsproxyconnector?

Bei Connectors handelt es sich um einfache Agents, die lokal eingerichtet sind und die ausgehende Verbindung zum Anwendungsproxydienst vereinfachen. Connectors müssen auf einem Windows-Server installiert sein, der auf die Back-End-Anwendung zugreifen kann. Sie können Connectors in Connectorgruppen organisieren, wobei jede Gruppe für den Datenverkehr an bestimmte Anwendungen zuständig ist.

## <a name="requirements-and-deployment"></a>Anforderungen und Bereitstellung

Für die erfolgreiche Bereitstellung des Anwendungsproxys benötigen Sie mindestens einen Connector, aber es wird empfohlen, für größere Resilienz über mindestens zwei zu verfügen. Installieren Sie den Connector auf einem Computer mit Windows Server 2012 R2 oder höher. Der Connector muss mit dem Anwendungsproxydienst und mit den lokalen Anwendungen, die Sie veröffentlichen, kommunizieren können.

### <a name="windows-server"></a>Windows-Server
Sie benötigen einen Server, auf dem Windows Server 2012 R2 oder höher ausgeführt wird und auf dem Sie den Anwendungsproxy-Connector installieren können. Der Server muss eine Verbindung mit den Anwendungsproxydiensten in Azure sowie mit den lokalen Anwendungen herstellen können, die Sie veröffentlichen.

Auf dem Windows-Server muss TLS 1.2 aktiviert werden, bevor Sie den Anwendungsproxy-Connector installieren. So aktivieren Sie TLS 1.2 auf dem Server

1. Legen Sie die folgenden Registrierungsschlüssel fest:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Neustarten des Servers

Weitere Informationen zu den Netzwerkanforderungen für den Connectorserver finden Sie unter [Erste Schritte mit dem Anwendungsproxy und Installieren des Connectors](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Wartung

Die Connectors und der Dienst führen alle Aufgaben in Bezug auf Hochverfügbarkeit aus. Diese können dynamisch hinzugefügt oder entfernt werden. Jedes Mal, wenn eine neue Anforderung eintrifft, wird diese an einen der gerade verfügbaren Connectors geleitet. Falls ein Connector vorübergehend nicht verfügbar ist, reagiert er nicht auf diesen Datenverkehr.

Die Connectors sind zustandslos und verfügen über keine Konfigurationsdaten auf dem Computer. Die einzigen Daten, die sie speichern, sind die Einstellungen für das Verbinden des Diensts mit dessen Authentifizierungszertifikat. Wenn die Verbindung mit dem Dienst hergestellt wird, werden alle erforderlichen Konfigurationsdaten abgerufen und jeweils nach einigen Minuten aktualisiert.

Connectors fragen den Server ab, um zu ermitteln, ob eine neuere Version des Connectors vorhanden ist. Wenn ja, führen die Connectors die Aktualisierung selbst durch.

Sie können Ihre Connectors auf dem Computer überwachen, auf dem sie ausgeführt werden, indem Sie das Ereignisprotokoll und die Leistungsindikatoren verwenden. Alternativ dazu können Sie ihren Status auch im Azure-Portal auf der Seite „Anwendungsproxy“ überwachen:

![Beispiel: Azure AD-Anwendungsproxy-Connectors](./media/application-proxy-connectors/app-proxy-connectors.png)

Sie müssen nicht verwendete Connectors nicht manuell löschen. Wenn ein Connector ausgeführt wird, bleibt er beim Herstellen der Verbindung mit dem Dienst aktiv. Connectors, die nicht verwendet werden, werden als _inaktiv_ gekennzeichnet und nach 10 Tagen Inaktivität entfernt. Wenn Sie einen Connector deinstallieren möchten, müssen Sie jedoch sowohl den Connectordienst als auch den Updatedienst auf dem Server deinstallieren. Starten Sie den Computer neu, um den Dienst vollständig zu entfernen.

## <a name="automatic-updates"></a>Automatische Aktualisierungen

Azure AD bietet automatische Updates für alle von Ihnen bereitgestellten Connectors. Solange der Connectorupdatedienst für den Anwendungsproxy ausgeführt wird, werden Ihre Connectors automatisch aktualisiert. Falls der Connectorupdatedienst auf Ihrem Server nicht angezeigt wird, müssen Sie den [Connector neu installieren](application-proxy-add-on-premises-application.md), um Updates zu erhalten.

Wenn Sie nicht warten möchten, bis ein Connector automatisch aktualisiert wird, können Sie die Aktualisierung manuell vornehmen. Rufen Sie auf dem Server mit dem Connector die [Downloadseite für den Connector](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) auf, und wählen Sie **Herunterladen**. Durch diesen Prozess wird ein Upgrade für den lokalen Connector gestartet.

Bei Mandanten mit mehreren Connectors werden die automatischen Updates nacheinander auf die einzelnen Connectors in jeder Gruppe angewendet, um Ausfallzeiten in Ihrer Umgebung zu vermeiden.

In folgenden Fällen kann es während der Aktualisierung eines Connectors zu Ausfallzeiten kommen:
  
- Sie haben nur einen Connector. Wir empfehlen, einen zweiten Connector zu installieren und eine [Connectorgruppe zu erstellen](application-proxy-connector-groups.md). Dadurch werden Ausfallzeiten vermieden, und die Verfügbarkeit wird erhöht.  
- Zu Beginn der Aktualisierung befand sich ein Connector mitten in einer Transaktion. Obwohl die ursprüngliche Transaktion verloren gegangen ist, sollte Ihr Browser automatisch versuchen, den Vorgang zu wiederholen. Andernfalls können Sie die Seite aktualisieren. Wenn die Anforderung erneut gesendet wird, wird der Datenverkehr an einen Backupconnector weitergeleitet.

Informationen zu zuvor veröffentlichten Versionen und Änderungen, die sie umfassen, finden Sie unter [Application Proxy – Versionsgeschichte](application-proxy-release-version-history.md).

## <a name="creating-connector-groups"></a>Erstellen von Connectorgruppen

Mit Connectorgruppen können Sie Anwendungen bestimmte Connectors zuweisen. Sie können mehrere Connectors gruppieren, und dann jede Anwendung einer Gruppe zuweisen.

Connectorgruppen erleichtern das Verwalten großer Bereitstellungen. Darüber hinaus Verringern Sie Wartezeiten für Mandanten, deren Anwendungen in verschiedenen Regionen gehostet werden, da Sie standortbasierte Connectorgruppen erstellen können, die nur für lokale Anwendungen bestimmt sind.

Weitere Informationen zu Connectorgruppen finden Sie unter [Veröffentlichen von Anwendungen in getrennten Netzwerken und an getrennten Standorten mithilfe von Connectorgruppen](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Kapazitätsplanung

Sie sollten unbedingt sicherstellen, dass Sie ausreichend Kapazität zwischen den Connectors zum Verarbeiten des erwarteten Datenverkehrs geplant haben. Um Hochverfügbarkeit und Skalierung zu gewährleisten, sollten jeder Connectorgruppe mindestens zwei Connectors zugewiesen werden. Mit drei Connectors sind Sie in dem Fall, dass Sie einen Computer warten müssen, optimal aufgestellt.

Im Allgemeinen gilt: Je mehr Benutzer Sie haben, desto größer muss der Computer dimensioniert sein. Die Tabelle weiter unten bietet einen Überblick über das Volumen und die erwartete Latenz bei verschiedenen Computern. Hinweis: Da Nutzungsmuster variieren und sich somit nicht für die Lastprognose eignen, basieren die Angaben nicht auf Benutzern, sondern jeweils auf den erwarteten Transaktionen pro Sekunde (TPS). Abhängig von der Antwortgröße und der Antwortzeit der Back-End-Anwendung ist außerdem mit gewissen Abweichungen zu rechnen: Bei größeren Antworten und längeren Antwortzeiten verringert sich der TPS-Maximalwert. Es wird empfohlen, zusätzliche Computer einzusetzen, um für die auf die Computer verteilte Last immer ausreichend Puffer zur Verfügung zu haben. Die zusätzliche Kapazität sorgt für Hochverfügbarkeit und Resilienz.

|Kerne|RAM|Erwartete Wartezeit (ms) – P99|TPS (maximal)|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1\.190|
|16|64|245|1200*|

\* Dieser Computer hat eine benutzerdefinierte Einstellung verwendet, um einige der Standardgrenzwerte für Verbindungen über die für .NET empfohlenen Einstellungen zu erhöhen. Es wird empfohlen, vor der Kontaktaufnahme mit dem Support einen Test mit den Standardeinstellungen durchzuführen, um diesen Grenzwert für Ihren Mandanten ändern zu lassen.

> [!NOTE]
> Bei Computern mit vier, acht und 16 Kernen ist der Unterschied beim TPS-Maximalwert minimal. Sie unterscheiden sich hauptsächlich bei der erwarteten Wartezeit.
>
> Diese Tabelle nennt auch auf die erwartete Leistung eines Connectors basierend auf dem Computertyp, auf dem er installiert ist. Dies ist unabhängig von den Drosselungsgrenzwerten des Anwendungsproxydiensts, siehe [Dienstgrenzwerte und Einschränkungen](../enterprise-users/directory-service-limits-restrictions.md).

## <a name="security-and-networking"></a>Sicherheit und Netzwerk

Connectors können überall im Netzwerk installiert werden, sodass Anforderungen an den Anwendungsproxydienst gesendet werden können. Wichtig ist nur, dass der Computer, auf dem der Connector ausgeführt ist, ebenfalls Zugriff auf Ihre Apps hat. Sie können die Connectors in Ihrem Unternehmensnetzwerk oder auf einem virtuellen Computer installieren, der in der Cloud ausgeführt wird. Connectors können in einem Umkreisnetzwerk, auch bekannt als demilitarisierte Zone (Demilitarized Zone, DMZ) ausgeführt werden, aber dies ist nicht notwendig, da der gesamte Datenverkehr ausgehend ist und das Netzwerk somit sicher bleibt.

Connectors senden nur ausgehende Anforderungen. Der ausgehende Datenverkehr wird an den Anwendungsproxydienst und die veröffentlichten Anwendungen gesendet. Das Öffnen von eingehenden Ports ist nicht erforderlich, da der Datenverkehr nach dem Einrichten einer Sitzung in beide Richtungen fließt. Auch der eingehende Zugriff über die Firewalls muss nicht konfiguriert werden.

Weitere Informationen zum Konfigurieren von Firewallregeln für ausgehenden Datenverkehr finden Sie unter [Verwenden von vorhandenen lokalen Proxyservern](application-proxy-configure-connectors-with-proxy-servers.md).

## <a name="performance-and-scalability"></a>Leistung und Skalierbarkeit

Die Skalierung für den Anwendungsproxy ist transparent, aber die Skalierung in Bezug auf Connectors ist ein wichtiger Faktor. Sie müssen über eine ausreichende Zahl von Connectors verfügen, um Spitzenlasten verarbeiten zu können. Da Connectors zustandslos sind, wirkt sich die Anzahl von Benutzern oder Sitzungen nicht auf sie aus. Stattdessen wirkt sich die Anzahl von Anforderungen und deren Nutzlastgröße auf sie aus. Bei standardmäßigem Webdatenverkehr kann ein Computer mit durchschnittlicher Leistungsfähigkeit einige Tausend Anforderungen pro Sekunde verarbeiten. Diese Kapazität richtet sich nach den genauen Eigenschaften des jeweiligen Computers.

Die Connectorleistung wird durch die CPU und das Netzwerk bestimmt. Eine gute CPU-Leistung wird für die TLS-Verschlüsselung und -Entschlüsselung benötigt, und die Netzwerkeigenschaften sind wichtig, um eine gute Konnektivität für die Anwendungen und den Online-Dienst in Azure zu erzielen.

Der Arbeitsspeicher ist für Connectors dagegen weniger wichtig. Der Online-Dienst übernimmt einen Großteil der Verarbeitung und den gesamten nicht authentifizierten Datenverkehr. Alle Schritte, die in der Cloud ausgeführt werden können, werden auch in der Cloud ausgeführt.

Sollte der Connector oder der Computer aus irgendeinem Grund nicht mehr zur Verfügung stehen, wird der Datenverkehr an einen anderen Connector in der Gruppe geleitet. Aufgrund dieser Resilienz empfiehlt sich auch die Verwendung mehrerer Connectors.

Ein weiterer Leistungsfaktor ist die Qualität der Netzwerkverbindung zwischen den Connectors, z.B.:

- **Onlinedienst**: Langsame Verbindungen oder Verbindungen mit hoher Wartezeit mit dem Anwendungsproxydienst in Azure beeinflussen die Leistung des Connectors. Stellen Sie für eine optimale Leistung eine Verbindung zwischen Ihrer Organisation und Azure über ExpressRoute her. Andernfalls muss das Netzwerkteam sicherstellen, dass Verbindungen mit Azure so effizient wie möglich verarbeitet werden.
- **Back-End-Anwendungen**: In einigen Fällen sind zwischen dem Connector und den Back-End-Anwendungen zusätzliche Proxys vorhanden, die Verbindungen verlangsamen oder verhindern können. Öffnen Sie zur Behebung des Problems in diesem Szenario einen Browser vom Connectorserver aus und versuchen Sie, auf die Anwendung zuzugreifen. Wenn Sie die Connectors in Azure ausführen, die Anwendungen aber lokal vorliegen, ist die Benutzerfreundlichkeit möglicherweise nicht so hoch wie erwartet.
- **Domänencontroller**: Wenn die Connectors SSO (Single Sign-On, einmaliges Anmelden) per eingeschränkter Kerberos-Delegierung durchführen, nehmen sie vor dem Senden der Anforderung an das Back-End Kontakt mit den Domänencontrollern auf. Die Connectors verfügen zwar über einen Cache mit Kerberos-Tickets, aber in einer Umgebung mit hoher Auslastung kann sich die Reaktionsfähigkeit der Domänencontroller auf die Leistung auswirken. Dieses Problem tritt häufiger bei Connectors auf, die in Azure ausgeführt werden, aber mit Domänencontrollern kommunizieren, die lokal installiert sind.

Weitere Informationen zur Optimierung Ihres Netzwerks finden Sie unter [Aspekte der Netzwerktopologie bei Verwendung des Azure Active Directory-Anwendungsproxys](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Domänenbeitritt

Connectors können auf einem Computer ausgeführt werden, der nicht in eine Domäne eingebunden ist. Wenn Sie jedoch das einmalige Anmelden für Anwendungen einrichten möchten, die die integrierte Windows-Authentifizierung (IWA) nutzen, benötigen Sie einen Computer, der in eine Domäne eingebunden ist. In diesem Fall müssen die Connectorcomputer in eine Domäne eingebunden werden, die die eingeschränkte [Kerberos](https://web.mit.edu/kerberos)-Delegierung im Namen der Benutzer für die veröffentlichten Anwendungen durchführen kann.

Connectors können auch in Domänen oder Gesamtstrukturen mit einer Teilvertrauensstellung oder in schreibgeschützte Domänencontroller eingebunden werden.

## <a name="connector-deployments-on-hardened-environments"></a>Connectorbereitstellungen in festgeschriebenen Umgebungen

In der Regel ist die Connectorbereitstellung unkompliziert und erfordert keine spezielle Konfiguration. Es gibt jedoch einige Bedingungen, die berücksichtigt werden müssen:

- Organisationen, die ausgehenden Datenverkehr einschränken, müssen [erforderliche Ports öffnen](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
- Möglicherweise muss die Konfiguration von FIPS-konformen Computern geändert werden, damit die Connectorprozesse ein Zertifikat generieren und speichern können.
- Organisationen, die ihre Umgebung basierend auf den Prozessen sperren, die die Netzwerkanforderungen ausgeben, müssen sicherstellen, dass für beide Connectordienste der Zugriff auf alle erforderlichen Ports und IPs zugelassen ist.
- In einigen Fällen kann es passieren, dass Proxys für die Weiterleitung des ausgehenden Datenverkehrs die zweistufige Authentifizierung per Zertifikat verhindern, sodass bei der Kommunikation ein Fehler auftritt.

## <a name="connector-authentication"></a>Connectorauthentifizierung

Zur Bereitstellung eines sicheren Diensts müssen sich Connectors beim Dienst authentifizieren, und der Dienst muss sich beim Connector authentifizieren. Für die Authentifizierung werden Client- und Serverzertifikate verwendet, wenn die Connectors die Verbindung initiieren. So wird erreicht, dass der Benutzername und das Kennwort des Administrator nicht auf dem Connectorcomputer gespeichert werden.

Die verwendeten Zertifikate gelten nur für den Anwendungsproxydienst. Sie werden während der ersten Registrierung erstellt und von den Connectors alle paar Monate automatisch verlängert.

Nach der ersten erfolgreichen Zertifikatsverlängerung hat der Azure AD-Anwendungsproxy-Connectordienst (Netzwerkdienst) keine Berechtigung, das alte Zertifikat aus dem Speicher des lokalen Computers zu entfernen. Wenn das Zertifikat abgelaufen ist oder nicht mehr vom Dienst verwendet wird, können Sie es problemlos löschen.

Um Probleme bei der Zertifikatsverlängerung zu vermeiden, müssen Sie sicherstellen, dass die Netzwerkkommunikation zwischen dem Connector und den [dokumentierten Zielen](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) aktiviert ist.

Wenn ein Connector über mehrere Monate hinweg keine Verbindung mit dem Dienst herstellt, sind die Zertifikate möglicherweise veraltet. In diesem Fall müssen Sie den Connector deinstallieren und neu installieren, um die Registrierung auszulösen. Sie können die folgenden PowerShell-Befehle ausführen:

```
Import-module AppProxyPSModule
Register-AppProxyConnector -EnvironmentName "AzureCloud"
```

Verwenden Sie für IP-Adressen von Behörden `-EnvironmentName "AzureUSGovernment"`. Weitere Informationen finden Sie unter [Installieren des Agents für die Azure Government-Cloud](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud).

Weitere Informationen zum Überprüfen des Zertifikats und zur Problembehandlung finden Sie unter [Überprüfen der Unterstützung des Vertrauensstellungszertifikats des Anwendungsproxys durch Computer und Back-End-Komponenten](application-proxy-connector-installation-problem.md#verify-machine-and-backend-components-support-for-application-proxy-trust-certificate).

## <a name="under-the-hood"></a>Hinter den Kulissen

Connectors basieren auf dem Windows Server-Webanwendungsproxy und verwenden daher einen Großteil der gleichen Verwaltungstools wie z. B. Windows-Ereignisprotokolle.

![Verwalten von Ereignisprotokollen mit der Ereignisanzeige](./media/application-proxy-connectors/event-view-window.png)

und Windows-Leistungsindikatoren.

![Hinzufügen von Leistungsindikatoren zum Connector mit dem Leistungsmonitor](./media/application-proxy-connectors/performance-monitor.png)

Die Connectors verfügen über **Administratorprotokolle** und **Sitzungsprotokolle**. Das **Administratorprotokoll** enthält wichtige Ereignisse und die dazugehörigen Fehler. Das **Sitzungsprotokoll** enthält alle Transaktionen und die dazugehörigen Verarbeitungsdetails.

Zum Anzeigen der Protokolle öffnen Sie die **Ereignisanzeige**, und navigieren Sie dann zu **Anwendungs- und Dienstprotokolle** > **Microsoft** > **AadApplicationProxy** > **Connector**. Damit das **Sitzungsprotokoll** im Menü **Ansicht** angezeigt wird, klicken Sie auf **Analytische und Debugprotokolle einblenden**. Das **Sitzungsprotokoll** dient in der Regel zur Problembehandlung und ist standardmäßig definiert. Aktivieren Sie das Protokoll, um mit der Erfassung von Ereignissen zu beginnen, und deaktivieren Sie es, wenn Sie es nicht mehr benötigen.

Sie können den Zustand des Diensts im Fenster „Dienste“ untersuchen. Der Connector besteht aus zwei Windows-Diensten: dem eigentlichen Connector und dem Updatedienst. Beide Dienste müssen immer ausgeführt werden.

 ![Beispiel: Fenster „Dienste“ mit lokalen Azure AD-Diensten](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Nächste Schritte

- [Veröffentlichen von Anwendungen in getrennten Netzwerken und an getrennten Standorten mithilfe von Connectorgruppen](application-proxy-connector-groups.md)
- [Verwenden von vorhandenen lokalen Proxyservern](application-proxy-configure-connectors-with-proxy-servers.md)
- [Beheben von Problemen mit Anwendungsproxys und Connectors](application-proxy-troubleshoot.md)
- [Installieren des Azure AD-Anwendungsproxyconnectors im Hintergrund](application-proxy-register-connector-powershell.md)
---
title: Verwenden von vorhandenen lokalen Proxyservern und Azure Active Directory
description: Hier wird erläutert, wie vorhandene lokale Proxyserver bei Azure Active Directory verwendet werden können.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/07/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: 808357b95f4de904ead0741d848480d548a2e26a
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030075"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Verwenden von vorhandenen lokalen Proxyservern

In diesem Artikel erfahren Sie, wie Sie den Azure AD-Anwendungsproxyconnector (Azure Active Directory) für Proxyserver für ausgehenden Datenverkehr konfigurieren. Er richtet sich an Kunden mit Netzwerkumgebungen, die über vorhandene Proxys verfügen.

Zuerst sehen wir uns die folgenden wichtigsten Bereitstellungsszenarien an:

* Konfigurieren von Connectors zum Umgehen von lokalen Proxys für ausgehenden Datenverkehr
* Konfigurieren von Connectors zum Verwenden eines Proxys für ausgehenden Datenverkehr für den Zugriff auf den Azure AD-Anwendungsproxy
* Konfigurieren der Nutzung eines Proxys zwischen dem Connector und der Back-End-Anwendung

Weitere Informationen zur Funktionsweise von Connectors finden Sie unter [Grundlegendes zu Azure AD-Anwendungsproxyconnectors](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Umgehen von Proxys für ausgehenden Datenverkehr

Connectors verfügen über zugrunde liegende Betriebssystemkomponenten, die ausgehende Anforderungen senden. Diese Komponenten versuchen automatisch, mithilfe der automatischen Ermittlung von Webproxys (Web Proxy Auto-Discovery, WPAD) einen Proxyserver im Netzwerk zu finden.

Die Betriebssystemkomponenten versuchen, einen Proxyserver zu finden, indem eine DNS-Suche nach „wpad.domainsuffix“ durchgeführt wird. Wenn die Suche im DNS erfolgreich ist, wird eine HTTP-Anforderung für die IP-Adresse für „wpad.dat“ gesendet. Diese Anforderung wird zum Proxykonfigurationsskript in Ihrer Umgebung. Der Connector verwendet dieses Skript zum Auswählen eines ausgehenden Proxyservers. Es kann aber sein, dass der Datenverkehr des Connectors trotzdem nicht fließen kann, weil für den Proxy weitere Konfigurationseinstellungen erforderlich sind.

Sie können den Connector so konfigurieren, dass Ihr lokaler Proxy umgangen wird, um sicherzustellen, dass eine direkte Verbindung mit den Azure-Diensten verwendet wird. Sofern Ihre Netzwerkrichtlinie dies zulässt, empfehlen wir diese Vorgehensweise, da Sie eine Konfiguration weniger verwalten müssen.

Wenn Sie für den Connector die Verwendung des Proxys für ausgehenden Datenverkehr deaktivieren möchten, bearbeiten Sie die Datei „C:\Programme\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config“, und fügen Sie den Abschnitt *system.net* aus dem folgenden Codebeispiel hinzu:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Um zu erreichen, dass der Proxy auch vom Connectorupdatedienst umgangen wird, nehmen Sie eine ähnliche Änderung an der Datei „ApplicationProxyConnectorUpdaterService.exe.config“ vor. Sie finden diese Datei unter „C:\Programme\Microsoft AAD App Proxy Connector Updater“.

Achten Sie darauf, dass Sie Kopien der Originaldateien erstellen, falls Sie auf die CONFIG-Standarddateien zurückgreifen müssen.

## <a name="use-the-outbound-proxy-server"></a>Verwenden des Proxyservers für ausgehenden Datenverkehr

Bei einigen Kundenumgebungen muss der gesamte ausgehende Datenverkehr ausnahmslos über einen Proxy für ausgehenden Datenverkehr abgewickelt werden. Das Umgehen des Proxys ist also keine Option.

Sie können den Connectordatenverkehr so konfigurieren, dass er wie im folgenden Diagramm dargestellt über den Proxy für ausgehenden Datenverkehr verläuft:

 ![Konfigurieren des Connectordatenverkehrs für den Verlauf über einen Proxy für ausgehenden Datenverkehr für den Azure AD-Anwendungsproxy](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

Da es nur um ausgehenden Datenverkehr geht, muss kein eingehender Zugriff durch Ihre Firewalls konfiguriert werden.

> [!NOTE]
> Der Anwendungsproxy unterstützt keine Authentifizierung gegenüber anderen Proxys. Die Konten des Connector/Updater-Netzwerkdiensts sollten ohne Authentifizierung eine Verbindung mit dem Proxy herstellen können.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Schritt 1: Konfigurieren der Route über den Proxy für ausgehenden Datenverkehr für den Connector und die dazugehörigen Dienste

Wenn WPAD in der Umgebung aktiviert und richtig konfiguriert ist, kann der Connector den Proxyserver für ausgehenden Datenverkehr automatisch ermitteln und versuchen, ihn zu verwenden. Sie können den Connector aber auch explizit so konfigurieren, dass ein Proxy für ausgehenden Datenverkehr verwendet wird.

Bearbeiten Sie hierfür die Datei „C:\Programme\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config“, und fügen Sie den Abschnitt *system.net* aus dem folgenden Codebeispiel hinzu. Legen Sie *proxyserver:8080* auf den Namen bzw. auf die IP-Adresse Ihres lokalen Proxyservers sowie auf den Port fest, an dem gelauscht wird. Der Wert muss das Präfix „http://“ besitzen, auch wenn Sie eine IP-Adresse verwenden.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Konfigurieren Sie anschließend den Connectorupdatedienst für die Verwendung des Proxys, indem Sie eine ähnliche Änderung an der Datei „C:\Programme\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config“ vornehmen.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Schritt 2: Konfigurieren des Proxys, durch den der Datenverkehr vom Connector und den zugehörigen Diensten geleitet werden soll

Auf dem Proxy für ausgehenden Datenverkehr sind vier Aspekte zu beachten:

* Proxyregeln für ausgehenden Datenverkehr
* Proxyauthentifizierung
* Proxyports
* TLS-Überprüfung

#### <a name="proxy-outbound-rules"></a>Proxyregeln für ausgehenden Datenverkehr

Lassen Sie den Zugriff auf die folgenden URLs zu:

| URL | Port |  Wie diese verwendet wird |
| --- | --- | --- |
| &ast;.msappproxy.net<br>&ast;.servicebus.windows.net | 443/HTTPS | Kommunikation zwischen dem Connector und dem Anwendungsproxy-Clouddienst |
| crl3.digicert.com<br>crl4.digicert.com<br>ocsp.digicert.com<br>crl.microsoft.com<br>oneocsp.microsoft.com<br>ocsp.msocsp.com<br> | 80/HTTP | Der Connector verwendet diese URLs, um Zertifikate zu überprüfen. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>&ast;.microsoftonline.com<br>&ast;.microsoftonline-p.com<br>&ast;.msauth.net<br>&ast;.msauthimages.net<br>&ast;.msecnd.net<br>&ast;.msftauth.net<br>&ast;.msftauthimages.net<br>&ast;.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com | 443/HTTPS | Der Connector verwendet diese URLs während der Registrierung. |
| ctldl.windowsupdate.com | 80/HTTP | Der Connector verwendet diese URL während der Registrierung. |

Wenn für Ihre Firewall oder Ihren Proxy die Konfiguration von DNS-Zulassungslisten möglich ist, können Sie Verbindungen mit „\*.msappproxy.net“ und „\*.servicebus.windows.net“ zulassen.

Wenn Sie die Konnektivität nicht über den FQDN zulassen können und stattdessen IP-Adressbereiche angeben müssen, verwenden Sie diese Optionen:

* Lassen Sie für den Connector den ausgehenden Zugriff für alle Ziele zu.
* Lassen Sie für den Connector den ausgehenden Zugriff auf alle IP-Bereiche des Azure-Rechenzentrums zu. Die Schwierigkeit besteht bei der Verwendung der Liste mit den IP-Bereichen für Azure-Datencenter darin, dass sie wöchentlich aktualisiert wird. Sie müssen einen Prozess implementieren, mit dem sichergestellt wird, dass Ihre Zugriffsregeln entsprechend aktualisiert werden. Wenn nur ein Teil der IP-Adressen verfügbar sind, funktioniert Ihre Konfiguration möglicherweise nicht. Zum Herunterladen der neuesten IP-Adressbereiche des Azure-Rechenzentrums navigieren Sie zu [https://download.microsoft.com](https://download.microsoft.com), und suchen Sie nach „Azure-IP-Adressbereiche und Diensttags“. Wählen Sie dann die relevante Cloud aus. So finden Sie beispielsweise die IP-Adressbereiche für die öffentliche Cloud mit „Azure IP-Adressbereiche und -Diensttags – Öffentliche Cloud“. Die US Government-Cloud finden Sie, indem Sie nach „Azure IP-Adressbereiche und -Diensttags – US Goverment Cloud“ suchen.

#### <a name="proxy-authentication"></a>Proxyauthentifizierung

Die Proxyauthentifizierung wird derzeit nicht unterstützt. Unsere aktuelle Empfehlung ist, für den Connector den anonymen Zugriff auf die Internetziele zuzulassen.

#### <a name="proxy-ports"></a>Proxyports

Der Connector stellt ausgehende TLS-basierte Verbindungen mit der CONNECT-Methode her. Bei dieser Methode wird praktisch ein Tunnel durch den Proxy für ausgehenden Datenverkehr eingerichtet. Konfigurieren Sie den Proxyserver für die Verwendung von Tunneln zu den Ports 443 und 80.

> [!NOTE]
> Wenn die Service Bus-Daten per HTTPS gesendet werden, wird Port 443 verwendet. Standardmäßig wird für Service Bus aber versucht, direkte TCP-Verbindungen herzustellen, und HTTPS wird nur verwendet, wenn für die direkte Verbindung ein Fehler auftritt.

#### <a name="tls-inspection"></a>TLS-Überprüfung

Verwenden Sie die TLS-Überprüfung nicht für den Connectordatenverkehr, da dies zu Problemen beim Connectordatenverkehr führt. Der Connector verwendet ein Zertifikat zur Authentifizierung beim Anwendungsproxydienst, und dieses Zertifikat kann während der TLS-Überprüfung verloren gehen.

## <a name="configure-using-a-proxy-between-the-connector-and-backend-application"></a>Konfigurieren der Nutzung eines Proxys zwischen dem Connector und der Back-End-Anwendung
Die Nutzung eines Weiterleitungsproxys für die Kommunikation mit der Back-End-Anwendung kann in einigen Umgebungen eine besondere Anforderung sein.
Führen Sie die folgenden Schritte aus, um dies zu aktivieren:

### <a name="step-1-add-the-required-registry-value-to-the-server"></a>Schritt 1: Hinzufügen des erforderlichen Registrierungswerts zum Server
1. Fügen Sie zum Aktivieren der Nutzung des Standardproxys dem Registrierungsschlüssel für die Connectorkonfiguration unter „HKEY_LOCAL_MACHINE\Software\Microsoft\Microsoft AAD App Proxy Connector“ den Registrierungswert (DWORD) `UseDefaultProxyForBackendRequests = 1` hinzu.

### <a name="step-2-configure-the-proxy-server-manually-using-netsh-command"></a>Schritt 2: Manuelles Konfigurieren des Proxyservers mit dem Befehl „netsh“
1.  Aktivieren Sie die Gruppenrichtlinie „Proxyeinstellungen pro Computer vornehmen“. Diese finden Sie unter: Computerkonfiguration\Richtlinien\Administrative Vorlagen\Windows-Komponenten\Internet Explorer. Dies muss wie hier angegeben festgelegt werden, anstatt die Richtlinie pro Benutzer festzulegen.
2.  Führen Sie `gpupdate /force` auf dem Server aus, oder starten Sie den Server neu, um sicherzustellen, dass die aktualisierten Einstellungen für die Gruppenrichtlinie verwendet werden.
3.  Starten Sie eine Eingabeaufforderung mit erhöhten Rechten (Administratorrechten), und geben Sie `control inetcpl.cpl` ein.
4.  Konfigurieren Sie die erforderlichen Proxyeinstellungen. 

Mit diesen Einstellungen kann der Connector für die Kommunikation mit Azure und mit der Back-End-Anwendung denselben Weiterleitungsproxy nutzen. Falls für die Kommunikation zwischen Connector und Azure kein oder ein anderer Weiterleitungsproxy benötigt wird, können Sie dies wie folgt einrichten: Ändern Sie die Datei „ApplicationProxyConnectorService.exe.config“, wie dies in den Abschnitten „Umgehen von Proxys für ausgehenden Datenverkehr“ bzw. „Verwenden des Proxyservers für ausgehenden Datenverkehr“ beschrieben ist.

> [!NOTE]
> Es gibt verschiedene Möglichkeiten, den Internetproxy im Betriebssystem zu konfigurieren. Proxyeinstellungen, die über NETSH WINHTTP (führen Sie zur Überprüfung `NETSH WINHTTP SHOW PROXY` aus) konfiguriert werden, setzen die Proxyeinstellungen außer Kraft, die Sie in Schritt 2 konfiguriert haben. 

Der Proxy des Computers wird auch vom Connectorupdatedienst verwendet. Sie können dieses Verhalten ändern, indem Sie die Datei „ApplicationProxyConnectorUpdaterService.exe.config“ bearbeiten.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Problembehandlung für Proxyprobleme des Connectors und Verbindungsprobleme von Diensten

Sie sollten jetzt verfolgen können, dass der gesamte Datenverkehr über den Proxy fließt. Bei Problemen helfen Ihnen die folgenden Informationen zur Problembehandlung weiter.

Die beste Möglichkeit zur Identifizierung und Behebung von Problemen mit der Connectorkonnektivität ist die Erstellung einer Netzwerkerfassung beim Starten des Connectordiensts. Im Folgenden finden Sie einige Tipps zum Erfassen und Filtern von Netzwerkablaufverfolgungen.

Sie können ein Überwachungstool Ihrer Wahl verwenden. Für diesen Artikel haben wir Microsoft Message Analyzer genutzt.

> [!NOTE]
> [Microsoft Message Analyzer (MMA) wurde eingestellt](https://docs.microsoft.com/openspecs/blog/ms-winintbloglp/dd98b93c-0a75-4eb0-b92e-e760c502394f), und die zugehörigen Downloadpakete wurden am 25. November 2019 aus den Microsoft.com-Sites entfernt.  Zurzeit entwickelt Microsoft keinen Ersatz für Microsoft Message Analyzer.  Wenn Sie eine ähnliche Funktionalität benötigen, verwenden Sie ein Drittanbieter-Tool wie Wireshark für die Netzwerkprotokollanalyse.

Die folgenden Beispiele beziehen sich speziell auf Message Analyzer. Die Prinzipien können aber auf alle Analysetools angewendet werden.

### <a name="take-a-capture-of-connector-traffic"></a>Durchführen einer Erfassung des Connectordatenverkehrs

Führen Sie die folgenden Schritte aus, um die erste Problembehandlung durchzuführen:

1. Beenden Sie den Azure AD-Anwendungsproxy-Connectordienst über „services.msc“.

   ![Azure AD-Anwendungsproxy-Connectordienst in „services.msc“](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Führen Sie Message Analyzer als Administrator aus.
1. Wählen Sie **Start local trace** (Lokale Ablaufverfolgung starten) aus.
1. Starten Sie den Azure AD-Anwendungsproxy-Connectordienst.
1. Beenden Sie die Netzwerkerfassung.

   ![Screenshot der Schaltfläche zum Beenden der Netzwerkerfassung](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Überprüfen Sie, ob der Connectordatenverkehr ausgehende Proxys umgeht.

Wenn Sie dem Anwendungsproxyconnector für die Umgehung der Proxyserver und direkte Verbindungen mit dem Anwendungsproxydienst konfiguriert haben, sehen Sie sich die Netzwerkerfassung für fehlerhafte TCP-Verbindungsversuche an.

Verwenden Sie den Filter von Message Analyzer, um diese Versuche zu identifizieren. Geben Sie `property.TCPSynRetransmit` in das Filterfeld ein, und wählen Sie **Anwenden** aus.

Ein SYN-Paket ist das erste Paket, das zum Herstellen einer TCP-Verbindung gesendet wird. Falls von diesem Paket keine Antwort zurückgegeben wird, wird ein neuer Versuch für den SYN-Vorgang gestartet. Sie können den obigen Filter nutzen, um alle erneut übertragenen SYN-Vorgänge anzuzeigen. Anschließend können Sie prüfen, ob diese SYN-Vorgänge Datenverkehr betreffen, der mit dem Connector zusammenhängt.

Wenn Sie erwarten, dass der Connector direkte Verbindungen mit den Azure-Diensten herstellt, sind Antworten von SynRetransmit auf Port 443 ein eindeutiger Hinweis darauf, dass ein Problem mit dem Netzwerk oder der Firewall besteht.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Überprüfen Sie, ob der Connectordatenverkehr ausgehende Proxys verwendet.

Wenn Sie den Anwendungsproxyconnector so konfiguriert haben, dass der Datenverkehr die Proxyserver durchläuft, suchen Sie nach fehlerhaften HTTPS-Verbindungen mit Ihrem Proxy.

Um die Netzwerkerfassung für diese Verbindungsversuche zu filtern, geben Sie im Filter von Message Analyzer `(https.Request or https.Response) and tcp.port==8080` ein, und ersetzen Sie dabei 8080 durch Ihren Dienstproxyport. Wählen Sie **Anwenden** aus, um die Filterergebnisse anzuzeigen.

Mit dem obigen Filter werden nur die HTTPS-Anforderungen und -Antworten zum bzw. vom Proxyport angezeigt. Sie suchen nach den CONNECT-Anforderungen, die eine Kommunikation mit dem Proxyserver anzeigen. Bei erfolgreicher Durchführung erhalten Sie die Antwort „HTTP OK (200)“.

Wenn andere Antwortcodes angezeigt werden, z.B. 407 oder 502, weist dies darauf hin, dass für den Proxy eine Authentifizierung erforderlich ist oder dass der Datenverkehr aus einem anderen Grund nicht zugelassen wird. An diesem Punkt sollten Sie Ihr Proxyserver-Supportteam zu Rate ziehen.

## <a name="next-steps"></a>Nächste Schritte

* [Grundlegendes zu Azure AD-Anwendungsproxyconnectors](application-proxy-connectors.md)
* Wenn Probleme mit der Connectorkonnektivität bestehen, stellen Sie Ihre Frage auf der [Microsoft F&A-Seite für Azure Active Directory](/answers/topics/azure-active-directory.html), oder erstellen Sie ein Ticket für unser Supportteam.

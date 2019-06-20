---
title: Verwenden von vorhandenen lokalen Proxyservern und Azure AD | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie vorhandene lokale Proxyserver verwenden.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cc0b3a9a02c023678691921100443436cdf0011
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66015477"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Verwenden von vorhandenen lokalen Proxyservern

In diesem Artikel erfahren Sie, wie Sie den Azure AD-Anwendungsproxyconnector (Azure Active Directory) für Proxyserver für ausgehenden Datenverkehr konfigurieren. Er richtet sich an Kunden mit Netzwerkumgebungen, die über vorhandene Proxys verfügen.

Zuerst sehen wir uns die folgenden wichtigsten Bereitstellungsszenarien an:
* Konfigurieren von Connectors zum Umgehen von lokalen Proxys für ausgehenden Datenverkehr
* Konfigurieren von Connectors zum Verwenden eines Proxys für ausgehenden Datenverkehr für den Zugriff auf den Azure AD-Anwendungsproxy

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

>[!NOTE]
>Der Anwendungsproxy unterstützt keine Authentifizierung gegenüber anderen Proxys. Die Konten des Connector/Updater-Netzwerkdiensts sollten ohne Authentifizierung eine Verbindung mit dem Proxy herstellen können.

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
* SSL-Überprüfung

#### <a name="proxy-outbound-rules"></a>Proxyregeln für ausgehenden Datenverkehr
Lassen Sie den Zugriff auf die folgenden URLs zu:

| URL | Wie diese verwendet wird |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Kommunikation zwischen dem Connector und dem Anwendungsproxy-Clouddienst |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Azure verwendet diese URLs, um Zertifikate zu überprüfen. |
| login.windows.net<br>login.microsoftonline.com | Der Connector verwendet diese URLs während der Registrierung. |

Wenn für Ihre Firewall oder Ihren Proxy die Konfiguration von DNS-Zulassungslisten möglich ist, können Sie Verbindungen mit „\*.msappproxy.net“ und „\*.servicebus.windows.net“ zulassen. Andernfalls müssen Sie den Zugriff auf die [IP-Adressbereiche für das Azure-Rechenzentrum](https://www.microsoft.com/download/details.aspx?id=41653) zulassen. Die IP-Adressbereiche werden wöchentlich aktualisiert.


Wenn Sie die Konnektivität nicht über den FQDN zulassen können und stattdessen IP-Adressbereiche angeben müssen, verwenden Sie diese Optionen:

* Lassen Sie für den Connector den ausgehenden Zugriff für alle Ziele zu.
* Lassen Sie für den Connector den ausgehenden Zugriff auf alle [IP-Bereiche des Azure-Rechenzentrums](https://www.microsoft.com//download/details.aspx?id=41653) zu. Die Schwierigkeit besteht bei der Verwendung der Liste mit den IP-Bereichen für Azure-Datencenter darin, dass sie wöchentlich aktualisiert wird. Sie müssen einen Prozess implementieren, mit dem sichergestellt wird, dass Ihre Zugriffsregeln entsprechend aktualisiert werden. Wenn nur ein Teil der IP-Adressen verfügbar sind, funktioniert Ihre Konfiguration möglicherweise nicht.

#### <a name="proxy-authentication"></a>Proxyauthentifizierung

Die Proxyauthentifizierung wird derzeit nicht unterstützt. Unsere aktuelle Empfehlung ist, für den Connector den anonymen Zugriff auf die Internetziele zuzulassen.

#### <a name="proxy-ports"></a>Proxyports

Der Connector stellt ausgehende SSL-basierte Verbindungen mit der CONNECT-Methode her. Bei dieser Methode wird praktisch ein Tunnel durch den Proxy für ausgehenden Datenverkehr eingerichtet. Konfigurieren Sie den Proxyserver für die Verwendung von Tunneln zu den Ports 443 und 80.

>[!NOTE]
>Wenn die Service Bus-Daten per HTTPS gesendet werden, wird Port 443 verwendet. Standardmäßig wird für Service Bus aber versucht, direkte TCP-Verbindungen herzustellen, und HTTPS wird nur verwendet, wenn für die direkte Verbindung ein Fehler auftritt.

#### <a name="ssl-inspection"></a>SSL-Überprüfung
Verwenden Sie die SSL-Überprüfung nicht für den Connectordatenverkehr, da dies zu Problemen für den Connectordatenverkehr führt. Der Connector verwendet ein Zertifikat zur Authentifizierung beim Anwendungsproxydienst, und dieses Zertifikat kann während der SSL-Überprüfung verloren gehen. 

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Problembehandlung für Proxyprobleme des Connectors und Verbindungsprobleme von Diensten
Sie sollten jetzt verfolgen können, dass der gesamte Datenverkehr über den Proxy fließt. Bei Problemen helfen Ihnen die folgenden Informationen zur Problembehandlung weiter.

Die beste Möglichkeit zur Identifizierung und Behebung von Problemen mit der Connectorkonnektivität ist die Erstellung einer Netzwerkerfassung beim Starten des Connectordiensts. Im Folgenden finden Sie einige Tipps zum Erfassen und Filtern von Netzwerkablaufverfolgungen.

Sie können ein Überwachungstool Ihrer Wahl verwenden. Für diesen Artikel haben wir Microsoft Message Analyzer genutzt. Sie können es von der [Microsoft-Website](https://www.microsoft.com/download/details.aspx?id=44226) herunterladen.

Die folgenden Beispiele beziehen sich speziell auf Message Analyzer. Die Prinzipien können aber auf alle Analysetools angewendet werden.

### <a name="take-a-capture-of-connector-traffic"></a>Durchführen einer Erfassung des Connectordatenverkehrs

Führen Sie die folgenden Schritte aus, um die erste Problembehandlung durchzuführen:

1. Beenden Sie den Azure AD-Anwendungsproxy-Connectordienst über „services.msc“.

   ![Azure AD-Anwendungsproxy-Connectordienst in „services.msc“](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

2. Führen Sie Message Analyzer als Administrator aus.
3. Wählen Sie **Start local trace** (Lokale Ablaufverfolgung starten) aus.

   ![Starten der Netzwerkerfassung](./media/application-proxy-configure-connectors-with-proxy-servers/start-local-trace.png)

3. Starten Sie den Azure AD-Anwendungsproxy-Connectordienst.
4. Beenden Sie die Netzwerkerfassung.

   ![Beenden der Netzwerkerfassung](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

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

- [Grundlegendes zu Azure AD-Anwendungsproxyconnectors](application-proxy-connectors.md)

- Wenn Probleme mit der Connectorkonnektivität bestehen, stellen Sie Ihre Frage bitte im [Azure Active Directory-Forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD), oder erstellen Sie ein Ticket bei unserem Supportteam.

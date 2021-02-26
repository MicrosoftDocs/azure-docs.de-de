---
title: Behandeln von Problemen mit der Back-End-Integrität in Azure Application Gateway
description: Beschreibt, wie Sie Probleme mit der Back-End-Integrität in Azure Application Gateway behandeln.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/09/2020
ms.author: surmb
ms.openlocfilehash: 95b74e5fc6c5d2c09ff04b3f14e920ae675ab6e1
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592754"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Behandeln von Problemen mit der Back-End-Integrität in Application Gateway
==================================================

<a name="overview"></a>Übersicht
--------

Standardmäßig testet Application Gateway Back-End-Server, um deren Integritätsstatus zu überprüfen und zu prüfen, ob sie bereit sind, Anforderungen zu verarbeiten. Benutzer können auch benutzerdefinierte Tests erstellen, um den Hostnamen, den zu überprüfenden Pfad und die Statuscodes, die als fehlerfrei akzeptiert werden sollen, anzugeben. Wenn der Back-End-Server nicht erfolgreich antwortet, wird der Server von Application Gateway in jedem Fall als fehlerhaft markiert, und die Anforderung wird nicht mehr an den Server weitergeleitet. Nachdem der Server erfolgreich reagiert hat, setzt Application Gateway das Weiterleiten der Anforderungen fort.

### <a name="how-to-check-backend-health"></a>Überprüfen der Back-End-Integrität

Zum Überprüfen der Integrität des Back-End-Pools können Sie die Seite **Back-End-Integrität** im Azure-Portal verwenden. Sie können auch [Azure PowerShell](/powershell/module/az.network/get-azapplicationgatewaybackendhealth), die [CLI](/cli/azure/network/application-gateway#az-network-application-gateway-show-backend-health) oder die [REST-API](/rest/api/application-gateway/applicationgateways/backendhealth) verwenden.

Der Status, der von einer dieser Methoden abgerufen wird, kann einer der folgenden sein:

- Healthy

- Fehlerhaft

- Unknown

Wenn der Back-End-Integritätsstatus für einen Server fehlerfrei ist, bedeutet dies, dass Application Gateway die Anforderungen an diesen Server weiterleitet. Wenn jedoch die Back-End-Integrität für alle Server in einem Back-End-Pool fehlerhaft oder unbekannt ist, kann es zu Problemen kommen, wenn Sie versuchen, auf Anwendungen zuzugreifen. In diesem Artikel werden die Symptome, Ursachen und Lösungen für jeden der dargestellten Fehler beschrieben.

<a name="backend-health-status-unhealthy"></a>Back-End-Integritätsstatus: Fehlerhaft
-------------------------------

Wenn der Back-End-Integritätsstatus „Fehlerhaft“ ist, ähnelt die Portalansicht dem folgenden Screenshot:

![Back-End-Integrität von Application Gateway: Fehlerhaft](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Oder wenn Sie eine Azure PowerShell-, CLI- oder Azure-REST-API-Abfrage verwenden, erhalten Sie eine Antwort ähnlich der folgenden:
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
Nachdem Sie den Back-End-Serverstatus „Fehlerhaft“ für alle Server in einem Back-End-Pool erhalten haben, werden die Anforderungen nicht an die Server weitergeleitet, und Application Gateway gibt den Fehler „502 Bad Gateway“ an den anfordernden Client zurück. Um das Problem zu beheben, überprüfen Sie die Spalte **Details** auf der Registerkarte **Back-End-Integrität**.

Die in der Spalte **Details** angezeigte Meldung bietet ausführlichere Einblicke in das Problem. Basierend auf diesen Informationen können Sie mit der Problembehandlung beginnen.

> [!NOTE]
> Die Standardtestanforderung wird in diesem Format gesendet: \<protocol\>://127.0.0.1:\<port\>/. Beispielsweise http://127.0.0.1:80 für einen HTTP-Test an Port 80. Nur HTTP-Statuscodes von 200 bis 399 gelten als fehlerfrei. Das Protokoll und der Zielport werden von den HTTP-Einstellungen geerbt. Wenn Application Gateway auf ein anderes Protokoll, einen anderen Hostnamen oder einen anderen Pfad testen und einen anderen Statuscode als fehlerfrei erkennen soll, konfigurieren Sie einen benutzerdefinierten Test, und ordnen Sie ihn den HTTP-Einstellungen zu.

<a name="error-messages"></a>Fehlermeldungen
------------------------
#### <a name="backend-server-timeout"></a>Timeout des Back-End-Servers

**Nachricht:** Time taken by the backend to respond to application gateway\'s health probe is more than the time-out threshold in the probe setting. (Die Zeit, die das Back-End für die Reaktion auf den Application Gateway-Integritätstest benötigt, liegt über dem Timeoutschwellenwert in der Testeinstellung.)

**Ursache:** Nachdem Application Gateway eine HTTP(S)-Testanforderung an den Back-End-Server gesendet hat, wird für einen konfigurierten Zeitraum auf eine Antwort vom Back-End-Server gewartet. Wenn der Back-End-Server nicht innerhalb des konfigurierten Zeitraums (Timeoutwerts) antwortet, wird er als fehlerhaft gekennzeichnet, bis er erneut innerhalb des konfigurierten Timeoutzeitraums antwortet.

**Lösung:** Überprüfen Sie, warum der Back-End-Server bzw. die Back-End-Anwendung nicht innerhalb des konfigurierten Timeoutzeitraums antwortet, und überprüfen Sie ebenfalls die Anwendungsabhängigkeiten. Überprüfen Sie beispielsweise, ob in der Datenbank Probleme vorliegen, die möglicherweise eine Verzögerung der Reaktion auslösen können. Wenn Sie das Verhalten der Anwendung kennen und diese nur nach dem Timeoutwert antworten sollte, erhöhen Sie den Timeoutwert in den benutzerdefinierten Testeinstellungen. Sie müssen über einen benutzerdefinierten Test verfügen, um den Wert des Timeouts zu ändern. Informationen zum Konfigurieren eines benutzerdefinierten Tests finden Sie [auf der Dokumentationsseite](./application-gateway-create-probe-portal.md).

Um den Wert des Timeouts zu erhöhen, führen Sie die folgenden Schritte aus:

1.  Greifen Sie direkt auf den Back-End-Server zu, und überprüfen Sie die Zeit, die der Server benötigt, um auf diese Seite zu antworten. Sie können ein beliebiges Tool für den Zugriff auf den Back-End-Server verwenden, einschließlich eines Browsers unter Verwendung der Entwicklertools.

1.  Nachdem Sie die für die Anwendungsantwort benötigte Zeit ermittelt haben, wählen Sie die Registerkarte **Integritätstests** aus, und wählen Sie dann den Test aus, der Ihren HTTP-Einstellungen zugeordnet ist.

1.  Geben Sie einen Timeoutwert ein, der höher als die Antwortzeit der Anwendung in Sekunden ist.

1.  Speichern Sie die benutzerdefinierten Testeinstellungen, und überprüfen Sie, ob die Back-End-Integrität jetzt als „Fehlerfrei“ angezeigt wird.

#### <a name="dns-resolution-error"></a>Fehler bei der DNS-Auflösung

**Nachricht:** Application gateway could not create a probe for this backend. (Application Gateway konnte keinen Test für dieses Back-End erstellen.) This usually happens when the FQDN of the backend has not been entered correctly. (Dies ist normalerweise der Fall, wenn der FQDN des Back-Ends nicht ordnungsgemäß eingegeben wurde.) 

**Ursache:** Wenn der Back-End-Pool den Typ „IP-Adresse/FQDN“ oder „App Service“ aufweist, löst Application Gateway in die IP-Adresse des über das DNS (Domain Name System) eingegebenen FQDN (benutzerdefiniert oder Azure-Standard) auf und versucht, eine Verbindung mit dem Server an dem TCP-Port herzustellen, der in den HTTP-Einstellungen angegeben ist. Wenn aber diese Meldung angezeigt wird, deutet dies darauf hin, dass Application Gateway die IP-Adresse des eingegebenen FQDN nicht erfolgreich auflösen konnte.

**Lösung:**

1.  Überprüfen Sie, ob der in den Back-End-Pool eingegebene FQDN richtig ist und dass es sich um eine öffentliche Domäne handelt. Versuchen Sie dann, den FQDN von Ihrem lokalen Computer aufzulösen.

1.  Wenn Sie die IP-Adresse auflösen können, liegt möglicherweise ein Fehler bei der DNS-Konfiguration im virtuellen Netzwerk vor.

1.  Überprüfen Sie, ob das virtuelle Netzwerk mit einem benutzerdefinierten DNS-Server konfiguriert ist. Wenn dies der Fall ist, überprüfen Sie auf dem DNS-Server, warum er nicht in die IP-Adresse des angegebenen FQDN auflösen kann.

1.  Wenn Sie eine Azure-DNS-Standardadresse verwenden, überprüfen Sie bei Ihrer Domänennamen-Registrierungsstelle, ob eine ordnungsgemäße Zuordnung eines A-Eintrags oder CNAME-Eintrags vorgenommen wurde.

1.  Wenn die Domäne privat oder intern ist, versuchen Sie, Sie von einem virtuellen Computer aus im selben virtuellen Netzwerk aufzulösen. Wenn Sie sie auflösen können, starten Sie Application Gateway neu, und überprüfen Sie es noch mal. Um Application Gateway neu zu starten, müssen Sie die in diesen verlinkten Ressourcen beschriebenen PowerShell-Befehle zum [Beenden (stop)](/powershell/module/azurerm.network/stop-azurermapplicationgateway) und [Starten (start)](/powershell/module/azurerm.network/start-azurermapplicationgateway) verwenden.

#### <a name="tcp-connect-error"></a>TCP-Verbindungsfehler

**Nachricht:** Application Gateway could not connect to the backend. (Application Gateway konnte keine Verbindung mit dem Back-End herstellen.)
Please check that the backend responds on the port used for the probe. (Überprüfen Sie, ob das Back-End am für den Test verwendeten Port antwortet.)
Also check whether any NSG/UDR/Firewall is blocking access to the Ip and port of this backend. (Überprüfen Sie außerdem, ob eine NSG/UDR/Firewall den Zugriff auf die IP-Adresse und den Port dieses Back-Ends blockiert.)

**Ursache:** Nach der DNS-Auflösungsphase versucht Application Gateway, eine Verbindung mit dem Back-End-Server an dem TCP-Port herzustellen, der in den HTTP-Einstellungen konfiguriert ist. Wenn Application Gateway keine TCP-Sitzung am angegebenen Port einrichten kann, wird der Test mit dieser Meldung als fehlerhaft gekennzeichnet.

**Lösung:** Wenn Sie diesen Fehler erhalten, gehen Sie folgendermaßen vor:

1.  Überprüfen Sie, ob Sie mithilfe eines Browsers oder mithilfe von PowerShell eine Verbindung mit dem Back-End-Server am in den HTTP-Einstellungen genannten Port herstellen können. Führen Sie beispielsweise den folgenden Befehl aus: `Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Wenn der angegebene Port nicht der gewünschte Port ist, geben Sie die richtige Portnummer ein, damit Application Gateway eine Verbindung mit dem Back-End-Server herstellen kann.

1.  Wenn Sie auch von Ihrem lokalen Computer aus keine Verbindung über den Port herstellen können:

    a.  Überprüfen Sie die Einstellungen der Netzwerksicherheitsgruppe (NSG) für den Netzwerkadapter und das Subnetz des Back-End-Servers, und ob eingehende Verbindungen an den konfigurierten Port zulässig sind. Wenn sie nicht zulässig sind, erstellen Sie eine neue Regel, um die Verbindungen zuzulassen. Informationen zum Erstellen von NSG-Regeln finden Sie auf der [entsprechenden Seite der Dokumentation](../virtual-network/tutorial-filter-network-traffic.md#create-security-rules).

    b.  Überprüfen Sie, ob die NSG-Einstellungen des Application Gateway-Subnetzes ausgehenden öffentlichen und privaten Datenverkehr zulassen, damit eine Verbindung hergestellt werden kann. Überprüfen Sie die Dokumentseite, die in Schritt 3a bereitgestellt wird, um weitere Informationen zum Erstellen von NSG-Regeln zu erhalten.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Überprüfen Sie die UDR-Einstellungen (benutzerdefinierte Routen) des Subnetzes von Application Gateway und des Subnetzes des Back-End-Servers auf etwaige Routinganomalien. Stellen Sie sicher, dass die UDR keinen Datenverkehr vom Back-End-Subnetz wegleitet. Überprüfen Sie z. B. auf Routen zu virtuellen Netzwerkgeräten oder Standardrouten, die dem Application Gateway-Subnetz über Azure ExpressRoute und/oder VPN angekündigt werden.

    d.  Zum Überprüfen der effektiven Routen und Regeln für einen Netzwerkadapter können Sie die folgenden PowerShell-Befehle verwenden:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Wenn Sie keine Probleme mit der NSG oder UDR finden, überprüfen Sie Ihren Back-End-Server auf anwendungsbezogene Probleme, die Clients am Einrichten einer TCP-Sitzung an den konfigurierten Ports hindern. Folgendes sollte überprüft werden:

    a.  Öffnen Sie eine Eingabeaufforderung (WIN+R-\> cmd), geben Sie `netstat` ein, und drücken Sie die EINGABETASTE.

    b.  Überprüfen Sie, ob der Server am konfigurierten Port lauscht. Beispiel:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Wenn er nicht am konfigurierten Port lauscht, überprüfen Sie Ihre Webservereinstellungen. Beispielsweise: Sitebindungen in IIS, Server Block in NGINX und virtuellen Host in Apache.

    d.  Überprüfen Sie die Firewalleinstellungen Ihres Betriebssystems, um sicherzustellen, dass eingehender Datenverkehr am Port zulässig ist.

#### <a name="http-status-code-mismatch"></a>HTTP-Statuscodekonflikt

**Nachricht:** Status code of the backend\'s HTTP response did not match the probe setting. (Der Statuscode der HTTP-Antwort des Back-Ends stimmte nicht mit der Testeinstellung überein.) Expected:{HTTPStatusCode0} Received:{HTTPStatusCode1}. (Erwartet: {HTTPStatusCode0}, Empfangen: {HTTPStatusCode1}.)

**Ursache:** Nachdem die TCP-Verbindung hergestellt und ein TLS-Handshake ausgeführt wurde (wenn TLS aktiviert ist), sendet Application Gateway den Test als HTTP GET-Anforderung an den Back-End-Server. Wie zuvor beschrieben, wird für den Standardtest \<protocol\>://127.0.0.1:\<port\>/ verwendet, und die Antwortstatuscodes im Bereich 200 bis 399 werden als fehlerfrei angesehen. Wenn der Server einen anderen Statuscode zurückgibt, wird er mit dieser Meldung als fehlerhaft gekennzeichnet.

**Lösung:** Abhängig vom Antwortcode des Back-End-Servers können Sie die folgenden Schritte ausführen. Einige der allgemeinen Statuscodes sind hier aufgeführt:

| **Fehler** | **Aktionen** |
| --- | --- |
| Probe status code mismatch: (Teststatuscode-Konflikt:) Received 401 (401 empfangen) | Überprüfen Sie, ob der Back-End-Server Authentifizierung erfordert. Application Gateway-Tests können keine Anmeldeinformationen für die Authentifizierung übergeben. Lassen Sie \"HTTP 401\" in einer Teststatuscode-Übereinstimmung zu, oder testen Sie einen Pfad, in dem der Server keine Authentifizierung erfordert. | |
| Probe status code mismatch: (Teststatuscode-Konflikt:) Received 403 (403 empfangen) | Access forbidden. (Zugriff verweigert.) Überprüfen Sie, ob der Zugriff auf den Pfad auf dem Back-End-Server zulässig ist. | |
| Probe status code mismatch: (Teststatuscode-Konflikt:) Received 404 (404 empfangen) | Page not found. (Seite nicht gefunden.) Überprüfen Sie, ob der Zugriff auf den Hostnamenpfad auf dem Back-End-Server zulässig ist. Ändern Sie den Hostnamen- oder Pfadparameter in einen Wert, auf den zugegriffen werden kann. | |
| Probe status code mismatch: (Teststatuscode-Konflikt:) Received 405 (405 empfangen) | Die Testanforderungen für Application Gateway verwenden die HTTP GET-Methode. Überprüfen Sie, ob Ihr Server diese Methode zulässt. | |
| Probe status code mismatch: (Teststatuscode-Konflikt:) Received 500 (500 empfangen) | Interner Serverfehler. Überprüfen Sie die Integrität des Back-End-Servers, und stellen Sie fest, ob die Dienste ausgeführt werden. | |
| Probe status code mismatch: (Teststatuscode-Konflikt:) Received 503 (503 empfangen) | Service unavailable. (Dienst nicht verfügbar.) Überprüfen Sie die Integrität des Back-End-Servers, und stellen Sie fest, ob die Dienste ausgeführt werden. | |

Oder, wenn Sie der Ansicht sind, dass die Antwort legitim ist und Sie möchten, dass Application Gateway andere Statuscodes als fehlerfrei akzeptiert, können Sie einen benutzerdefinierten Test erstellen. Dieser Ansatz ist in Situationen hilfreich, in denen die Back-End-Website Authentifizierung benötigt. Da die Testanforderungen keine Benutzeranmeldeinformationen enthalten, schlagen sie fehl, und vom Back-End-Server wird ein HTTP 401-Statuscode zurückgegeben.

Um einen benutzerdefinierten Test zu erstellen, führen Sie die [diese Schritte](./application-gateway-create-probe-portal.md) aus.

#### <a name="http-response-body-mismatch"></a>Konflikt im HTTP-Antworttext

**Nachricht:** Body of the backend\'s HTTP response did not match the probe setting. (Der Text der HTTP-Antwort des Back-Ends stimmte nicht mit der Testeinstellung überein.) Received response body does not contain {string}. (Der empfangene Antworttext enthält nicht {Zeichenfolge}.)

**Ursache:** Beim Erstellen eines benutzerdefinierten Tests haben Sie die Möglichkeit, einen Back-End-Server als fehlerfrei zu markieren, indem Sie eine Zeichenfolge aus dem Antworttext abgleichen. Beispielsweise können Sie Application Gateway so konfigurieren, dass „unauthorized“ (nicht autorisiert) als abzugleichende Zeichenfolge angenommen wird. Wenn die Antwort des Back-End-Servers für die Testanforderung die Zeichenfolge **unauthorized** (nicht autorisiert) enthält, wird sie als „Fehlerfrei“ gekennzeichnet. Andernfalls wird sie mit dieser Meldung als „Fehlerhaft“ gekennzeichnet.

**Lösung:** Gehen Sie folgendermaßen vor, um das Problem zu beheben:

1.  Greifen Sie auf den Back-End-Server lokal oder über einen Clientcomputer im Testpfad zu, und überprüfen Sie den Antworttext.

1.  Überprüfen Sie, ob der Antworttext in der benutzerdefinierten Testkonfiguration von Application Gateway mit den konfigurierten Einstellungen übereinstimmt.

1.  Wenn dies nicht der Fall ist, ändern Sie die Testkonfiguration in den richtigen Zeichenfolgenwert, der akzeptiert werden soll.

Weitere Informationen zum [Testabgleich von Application Gateway](./application-gateway-probe-overview.md#probe-matching).

>[!NOTE]
> Bei Fehlermeldungen im Zusammenhang mit TLS finden Sie weitere Informationen zum SNI-Verhalten und zu den Unterschieden zwischen der v1-SKU und der v2-SKU auf der Seite mit der [Übersicht über TLS](ssl-overview.md).


#### <a name="backend-server-certificate-invalid-ca"></a>Ungültige Zertifizierungsstelle für das Zertifikat des Back-End-Servers

**Nachricht:** The server certificate used by the backend is not signed by a well-known Certificate Authority (CA). (Das vom Back-End verwendete Serverzertifikat ist nicht von einer bekannten Zertifizierungsstelle (CA) signiert.) Allow the backend on the Application Gateway by uploading the root certificate of the server certificate used by the backend. (Lassen Sie das Back-End für Application Gateway zu, indem Sie das Stammzertifikat des Serverzertifikats hochladen, das vom Back-End verwendet wird.)

**Ursache:** End-to-End-SSL mit Application Gateway v2 erfordert, dass das Zertifikat des Back-End-Servers überprüft wird, damit der Server als fehlerfrei angesehen wird.
Damit ein TLS/SSL-Zertifikat als vertrauenswürdig eingestuft wird, muss dieses Zertifikat des Back-End-Servers von einer Zertifizierungsstelle ausgestellt werden, die im vertrauenswürdigen Speicher von Application Gateway enthalten ist. Wenn das Zertifikat nicht von einer vertrauenswürdigen Zertifizierungsstelle ausgestellt wurde (z. B. bei Verwendung eines selbstsignierten Zertifikats), sollten Benutzer das Zertifikat des Ausstellers in Application Gateway hochladen.

**Lösung:** Führen Sie diese Schritte aus, um das vertrauenswürdige Stammzertifikat zu exportieren und in Application Gateway hochzuladen. (Diese Schritte gelten für Windows-Clients.)

1.  Melden Sie sich bei dem Computer an, auf dem Ihre Anwendung gehostet wird.

1.  Drücken Sie WIN+R, oder klicken Sie mit der rechten Maustaste auf die Schaltfläche **Start**, und wählen Sie dann **Ausführen** aus.

1.  Geben Sie `certmgr.msc` ein, und drücken Sie dann die EINGABETASTE. Sie können auch im **Start** menü nach dem Zertifikat-Manager suchen.

1.  Suchen Sie das Zertifikat, in der Regel in `\Certificates - Current User\\Personal\\Certificates\`, und öffnen Sie es.

1.  Wählen Sie das Stammzertifikat und dann **Zertifikat anzeigen** aus.

1.  Wählen Sie in den „Zertifikateigenschaften“ die Registerkarte **Details** aus.

1.  Wählen Sie auf der Registerkarte **Details** die Option **In Datei kopieren** aus, und speichern Sie die Datei im Base-64-codierten X.509-Format (CER-Format).

1.  Öffnen Sie die HTTP-**Einstellungen** von Application Gateway im Azure-Portal.

1. Öffnen Sie die HTTP-Einstellungen, wählen Sie **Zertifikat hinzufügen** aus, und suchen Sie die Zertifikatdatei, die Sie gerade gespeichert haben.

1. Wählen Sie **Speichern** aus, um die HTTP-Einstellungen zu speichern.

Alternativ können Sie das Stammzertifikat von einem Clientcomputer exportieren, indem Sie mit einem Browser (unter Umgehung von Application Gateway) direkt auf den Server zugreifen, um das Stammzertifikat aus dem Browser zu exportieren.

Weitere Informationen zum Extrahieren und Hochladen vertrauenswürdiger Stammzertifikate in Application Gateway finden Sie unter [Exportieren eines 0vertrauenswürdigen Stammzertifikats (für v2-SKU)](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Konflikt eines vertrauenswürdigen Stammzertifikats

**Nachricht:** The root certificate of the server certificate used by the backend does not match the trusted root certificate added to the application gateway. (Das Stammzertifikat des Serverzertifikats, das vom Back-End verwendet wird, stimmt nicht mit dem vertrauenswürdigen Stammzertifikat überein, das Application Gateway hinzugefügt wurde.) Ensure that you add the correct root certificate to allowlist the backend. (Stellen Sie sicher, dass Sie das richtige Stammzertifikat hinzufügen, um das Back-End in die Zulassungsliste aufzunehmen.)

**Ursache:** End-to-End-SSL mit Application Gateway v2 erfordert, dass das Zertifikat des Back-End-Servers überprüft wird, damit der Server als fehlerfrei angesehen wird.
Damit ein TLS/SSL-Zertifikat als vertrauenswürdig eingestuft wird, muss das Zertifikat des Back-End-Servers von einer Zertifizierungsstelle ausgestellt werden, die im vertrauenswürdigen Speicher von Application Gateway enthalten ist. Wenn das Zertifikat nicht von einer vertrauenswürdigen Zertifizierungsstelle ausgestellt wurde (z. B. bei Verwendung eines selbstsignierten Zertifikats), sollten Benutzer das Zertifikat des Ausstellers in Application Gateway hochladen.

Das Zertifikat, das in die HTTP-Einstellungen von Application Gateway hochgeladen wurde, muss mit dem Stammzertifikat des Back-End-Serverzertifikats übereinstimmen.

**Lösung:** Wenn Ihnen diese Fehlermeldung angezeigt wird, stimmt das Zertifikat, das in Application Gateway hochgeladen wurde, nicht mit dem, das auf den Back-End-Server hochgeladen wurde, überein.

Führen Sie die Schritte 1 bis 11 aus der vorangehenden Methode aus, um das richtige vertrauenswürdige Stammzertifikat in Application Gateway hochzuladen.

Weitere Informationen zum Extrahieren und Hochladen vertrauenswürdiger Stammzertifikate in Application Gateway finden Sie unter [Exportieren eines 0vertrauenswürdigen Stammzertifikats (für v2-SKU)](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> Dieser Fehler kann auch auftreten, wenn der Back-End-Server nicht die gesamte Kette des Zertifikats (einschließlich Root -> Intermediate (sofern zutreffend) -> Leaf) im Rahmen des TLS-Handshakes austauscht. Um dies zu überprüfen, können Sie OpenSSL-Befehle von jedem beliebigen Client aus verwenden und mithilfe der konfigurierten Einstellungen im Application Gateway-Test eine Verbindung mit dem Back-End-Server herstellen.

Beispiel:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Wenn die Ausgabe nicht die Rückgabe der gesamten Kette des Zertifikats enthält, exportieren Sie das Zertifikat erneut mit der vollständigen Kette (einschließlich des Stammzertifikats). Konfigurieren Sie dieses Zertifikat auf Ihrem Back-End-Server. 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

#### <a name="backend-certificate-invalid-common-name-cn"></a>Ungültiger allgemeiner Name (CN) des Back-End-Zertifikats

**Nachricht:** The Common Name (CN) of the backend certificate does not match the host header of the probe. (Der allgemeine Name (CN) des Back-End-Zertifikats stimmt nicht mit dem Hostheader des Tests überein.)

**Ursache:** Application Gateway überprüft, ob der in der HTTP-Einstellung des Back-Ends angegebene Hostname mit dem allgemeinen Namen (Common Name, CN) übereinstimmt, der vom TLS/SSL-Zertifikat des Back-End-Servers angegeben wird. Dies ist das Verhalten der v2-SKUs „Standard_v2“ und „WAF_v2“. Die SNI (Server Name Indication, Servernamensanzeige) der Standard- und WAF-SKU (v1) wird als FQDN in der Adresse des Back-End-Pools festgelegt. Weitere Informationen zum SNI-Verhalten und zu den Unterschieden zwischen der v1-SKU und der v2-SKU finden Sie unter [Übersicht über TLS-Beendigung und End-to-End-TLS mit Application Gateway](ssl-overview.md).

Wenn ein Standardtest vorliegt (es wurde kein benutzerdefinierter Test konfiguriert und zugeordnet), wird in der V2-SKU die SNI aus dem in den HTTP-Einstellungen angegebenen Hostnamen festgelegt. Oder für den Fall, dass „Pick hostname from backend address“ (Hostnamen aus Back-End-Adresse entnehmen) in den HTTP-Einstellungen erwähnt wird, wobei der Back-End-Adresspool einen gültigen FQDN enthalten muss, wird diese Einstellung angewendet.

Wenn den HTTP-Einstellungen ein benutzerdefinierter Test zugeordnet ist, wird die SNI aus dem in der Konfiguration des benutzerdefinierten Tests angegebenen Hostnamen festgelegt. Oder wenn **Pick hostname from backend HTTP settings** (Hostnamen aus Back-End-HTTP-Einstellungen entnehmen) im benutzerdefinierten Test ausgewählt ist, wird der SNI aus dem in den HTTP-Einstellungen angegebenen Hostnamen festgelegt.

Für den Fall, dass **Pick hostname from backend address** (Hostnamen aus Back-End-Adresse entnehmen) in den HTTP-Einstellungen festgelegt ist, muss der Back-End-Adresspool einen gültigen FQDN enthalten.

Wenn Sie diese Fehlermeldung erhalten, stimmt der allgemeine Name (Common Name, CN) des Back-End-Zertifikats nicht mit dem im benutzerdefinierten Test oder den HTTP-Einstellungen (für den Fall, dass **Pick Hostname from Backend HTTP Settings** (Hostnamen aus den Back-End-HTTP-Einstellungen entnehmen) ausgewählt ist) konfigurierten Hostnamen überein. Wenn Sie einen Standardtest verwenden, wird der Hostname als **127.0.0.1** festgelegt. Wenn dies kein gewünschter Wert ist, sollten Sie einen benutzerdefinierten Test erstellen und den HTTP-Einstellungen zuordnen.

**Lösung:**

Gehen Sie folgendermaßen vor, um das Problem zu beheben:

Windows:

1.  Melden Sie sich bei dem Computer an, auf dem Ihre Anwendung gehostet wird.

1.  Drücken Sie WIN+R, oder klicken Sie mit der rechten Maustaste auf die Schaltfläche **Start**, und wählen Sie **Ausführen** aus.

1.  Geben Sie **certmgr.msc** ein, und drücken Sie die EINGABETASTE. Sie können auch im **Start** menü nach dem Zertifikat-Manager suchen.

1.  Suchen Sie das Zertifikat (in der Regel in `\Certificates - Current User\\Personal\\Certificates`), und öffnen Sie es.

1.  Überprüfen Sie auf der Registerkarte **Details** den **Antragsteller** des Zertifikats.

1.  Überprüfen Sie den CN des Zertifikats aus den Details, und geben Sie im Feld „Hostname“ des benutzerdefinierten Tests oder in den HTTP-Einstellungen (falls **Pick hostname from backend HTTP settings** ausgewählt ist) den selben Namen ein. Wenn dies nicht der gewünschte Hostname für Ihre Website ist, benötigen Sie ein Zertifikat für diese Domäne, oder geben Sie den richtigen Hostnamen in der Konfiguration des benutzerdefinierten Tests oder der HTTP-Einstellung ein.

Für Linux mit OpenSSL:

1.  Führen Sie diesen Befehl in OpenSSL aus:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  Suchen Sie in den angezeigten Eigenschaften den CN des Zertifikats, und geben Sie im Feld „Hostname“ der HTTP-Einstellungen denselben Wert ein. Wenn dies nicht der gewünschte Hostname für Ihre Website ist, benötigen Sie ein Zertifikat für diese Domäne, oder geben Sie den richtigen Hostnamen in der Konfiguration des benutzerdefinierten Tests oder der HTTP-Einstellung ein.

#### <a name="backend-certificate-is-invalid"></a>Back-End-Zertifikat ungültig

**Nachricht:** Backend certificate is invalid. (Das Back-End-Zertifikat ist ungültig.) Current date is not within the \"Valid from\" and \"Valid to\" date range on the certificate. (Das aktuelle Datum entspricht nicht den Datumsbereichen „Gültig ab“ und „Gültig bis“ für das Zertifikat.)

**Ursache:** Jedes Zertifikat verfügt über einen Gültigkeitsdauerbereich, und die HTTPS-Verbindung ist nur dann sicher, wenn das TLS/SSL-Zertifikat des Servers gültig ist. Die aktuellen Daten müssen sich innerhalb des Bereichs **Gültig ab** und **Gültig bis** befinden. Wenn dies nicht der Fall ist, wird das Zertifikat als ungültig eingestuft und führt so zu einem Sicherheitsproblem, bei dem der Back-End-Server von Application Gateway als fehlerhaft markiert wird.

**Lösung:** Wenn Ihr TLS/SSL-Zertifikat abgelaufen ist, erneuern Sie das Zertifikat bei Ihrem Hersteller, und aktualisieren Sie die Servereinstellungen mit dem neuen Zertifikat. Wenn es sich um ein selbstsigniertes Zertifikat handelt, müssen Sie ein gültiges Zertifikat generieren und das Stammzertifikat in die HTTP-Einstellungen von Application Gateway hochladen. Gehen Sie dazu folgendermaßen vor:

1.  Öffnen Sie Ihre HTTP-Einstellungen von Application Gateway im Portal.

1.  Wählen Sie die Einstellung mit dem abgelaufenen Zertifikat aus, wählen Sie **Zertifikat hinzufügen** aus, und öffnen Sie die neue Zertifikatdatei.

1.  Entfernen Sie das alte Zertifikat mithilfe des Symbols **Löschen** neben dem Zertifikat, und wählen Sie dann **Speichern** aus.

#### <a name="certificate-verification-failed"></a>Fehler bei der Zertifikatüberprüfung

**Nachricht:** The validity of the backend certificate could not be verified. (Die Gültigkeit des Back-End-Zertifikats konnte nicht bestätigt werden.) To find out the reason, check Open SSL diagnostics for the message associated with error code {errorCode}. (Um den Grund zu ermitteln, überprüfen Sie die OpenSSL-Diagnose für die Meldung, die dem Fehlercode {fehlerCode} zugeordnet ist.)

**Ursache:** Dieser Fehler tritt auf, wenn Application Gateway nicht in der Lage ist, die Gültigkeit des Zertifikats zu überprüfen.

**Lösung:** Um dieses Problem zu beheben, überprüfen Sie, ob das Zertifikat auf dem Server ordnungsgemäß erstellt wurde. Beispielsweise können Sie mit [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) das Zertifikat und seine Eigenschaften überprüfen und dann versuchen, das Zertifikat erneut in die HTTP-Einstellungen von Application Gateway hochzuladen.

<a name="backend-health-status-unknown"></a>Back-End-Integritätsstatus „Unbekannt“
-------------------------------
Wenn die Back-End-Integrität als „Fehlerhaft“ angezeigt wird, ähnelt die Portalansicht dem folgenden Screenshot:

![Back-End-Integrität von Application Gateway: Unbekannt](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Dieses Verhalten kann aus einem oder mehreren der folgenden Gründe auftreten:

1.  Die Netzwerksicherheitsgruppe (NSG) im Application Gateway-Subnetz blockiert den eingehenden Zugriff auf die Ports 65503 bis 65534 (v1-SKU) oder 65200 bis 65535 (v2-SKU) aus dem „Internet“.
1.  Die UDR im Application Gateway-Subnetz ist auf die Standardroute (0.0.0.0/0) festgelegt, und der nächste Hop ist nicht als „Internet“ angegeben.
1.  Die Standardroute wird von einer ExpressRoute-/VPN-Verbindung in einem virtuellen Netzwerk über BGP angekündigt.
1.  Der benutzerdefinierte DNS-Server ist in einem virtuellen Netzwerk konfiguriert, das keine öffentlichen Domänennamen auflösen kann.
1.  Application Gateway befindet sich in einem fehlerhaften Zustand.

**Lösung:**

1.  Überprüfen Sie, ob Ihre NSG den Zugriff auf die Ports 65503 bis 65534 (v1-SKU) oder 65200 bis 65535 (v2-SKU) aus dem **Internet** blockiert:

    a.  Wählen Sie auf der Registerkarte **Übersicht** von Application Gateway den Link **Virtuelles Netzwerk/Subnetz** aus.

    b.  Wählen Sie auf der Registerkarte **Subnetze** ihres virtuellen Netzwerks das Subnetz aus, in dem Application Gateway bereitgestellt wurde.

    c.  Überprüfen Sie, ob eine NIC konfiguriert ist.

    d.  Wenn eine NSG konfiguriert ist, suchen Sie nach dieser NSG-Ressource auf der Registerkarte **Suche** oder unter **Alle Ressourcen**.

    e.  Fügen Sie im Abschnitt **Eingehende Regeln** eine Regel für eingehenden Datenverkehr hinzu, um den Zielportbereich 65503 bis 65534 für die v1-SKU oder 65200 bis 65535 für die v2-SKU mit der **Quelle** gleich **Beliebig** oder **Internet** zuzulassen.

    f.  Wählen Sie **Speichern** aus, und überprüfen Sie, ob Sie die Integrität des Back-Ends als „Fehlerfrei“ anzeigen können. Alternativ dazu können Sie hierzu auch die [PowerShell/CLI](../virtual-network/manage-network-security-group.md) verwenden.

1.  Überprüfen Sie, ob Ihre UDR über eine Standardroute (0.0.0.0/0) verfügt, wobei der nächste Hop nicht auf **Internet** festgelegt ist:
    
    a.  Führen Sie die Schritte 1a und 1b aus, um Ihr Subnetz zu ermitteln.

    b.  Überprüfen Sie, ob eine UDR konfiguriert ist. Wenn dies der Fall ist, suchen Sie nach der Ressource über die Suchleiste oder unter **Alle Ressourcen**.

    c.  Überprüfen Sie, ob es Standardrouten (0.0.0.0/0) gibt, wobei der nächste Hop nicht auf **Internet** festgelegt ist. Wenn die Einstellung entweder **Virtuelles Gerät** oder **Virtual Network Gateway** ist, müssen Sie sicherstellen, dass Ihr virtuelles Gerät oder das lokale Gerät das Paket ordnungsgemäß zurück an das Internetziel weiterleiten kann, ohne das Paket zu ändern.

    d.  Andernfalls ändern Sie den nächsten Hop in **Internet**, wählen Sie **Speichern** aus, und überprüfen Sie die Back-End-Integrität.

1.  Von einer ExpressRoute-/VPN-Verbindung in einem virtuellen Netzwerk über BGP angekündigte Standardroute:

    a.  Wenn Sie über eine ExpressRoute-/VPN-Verbindung mit dem virtuellen Netzwerk über BGP verfügen und eine Standardroute ankündigen, müssen Sie sicherstellen, dass das Paket zurück an das Internetziel weitergeleitet wird, ohne es zu ändern. Sie können dies überprüfen, indem Sie die Option **Problembehandlung für Verbindung** im Application Gateway-Portal verwenden.

    b.  Wählen Sie das Ziel manuell als beliebige, internetroutingfähige IP-Adresse wie „1.1.1.1“ aus. Legen Sie den Zielport auf einen beliebigen Wert fest, und überprüfen Sie die Konnektivität.

    c.  Wenn der nächste Hop „Virtual Network Gateway“ ist, ist möglicherweise eine Standardroute vorhanden, die über ExpressRoute oder VPN angekündigt wird.

1.  Wenn ein benutzerdefinierter DNS-Server im virtuellen Netzwerk konfiguriert ist, überprüfen Sie, ob der (oder die) Server öffentliche Domänen auflösen kann (können). Die Auflösung öffentlicher Domänennamen ist möglicherweise in Szenarien erforderlich, in denen Application Gateway beispielsweise externe Domänen wie OCSP-Server erreichen oder den Sperrstatus des Zertifikats überprüfen muss.

1.  Um zu überprüfen, ob Application Gateway fehlerfrei ist und ausgeführt wird, wechseln Sie im Portal zur Option **Ressourcenintegrität**, und überprüfen Sie, ob der Zustand **Fehlerfrei** lautet. Wenn Sie den Zustand **Fehlerhaft** oder [Beeinträchtigt](https://azure.microsoft.com/support/options/) sehen, **wenden Sie sich an den Support**.

<a name="next-steps"></a>Nächste Schritte
----------

Weitere Informationen zu [Diagnose und Protokollen in Application Gateway](./application-gateway-diagnostics.md).

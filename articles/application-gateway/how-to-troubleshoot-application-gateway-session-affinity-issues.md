---
title: Behandeln von Problemen mit der Sitzungsaffinität
titleSuffix: Azure Application Gateway
description: Dieser Artikel enthält Informationen zur Behebung von Problemen mit der Sitzungsaffinität in Azure Application Gateway.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 548bda36ed2b167c159d32a575b63ecbf10b16dd
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397568"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Behandeln von Problemen mit der Azure Application Gateway-Sitzungsaffinität

Erfahren Sie, wie Sie Probleme mit der Sitzungsaffinität in Azure Application Gateway diagnostizieren und beheben.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Übersicht

Das Feature „Cookiebasierte Sitzungsaffinität“ ist hilfreich, wenn eine Benutzersitzung auf dem gleichen Server bleiben soll. Mithilfe von durch das Gateway verwalteten Cookies kann Application Gateway weiteren Datenverkehr einer Benutzersitzung zur Verarbeitung an den gleichen Server weiterleiten. Dies ist hilfreich, wenn der Sitzungsstatus für eine Benutzersitzung lokal auf dem Server gespeichert wird.

## <a name="possible-problem-causes"></a>Mögliche Problemursachen

Das Problem bei der Aufrechterhaltung der cookiebasierten Sitzungsaffinität kann folgende Hauptursachen haben:

- Einstellung „Cookiebasierte Affinität“ ist nicht aktiviert
- Ihre Anwendung kann cookiebasierte Affinität nicht verarbeiten
- Anwendung verwendet cookiebasierte Affinität, Anforderungen werden dennoch zwischen Back-End-Servern weitergeleitet

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Überprüfen, ob die Einstellung „Cookiebasierte Affinität“ aktiviert ist

Manchmal sind die Probleme mit der Sitzungsaffinität darauf zurückzuführen, dass Sie vergessen haben, die Einstellung „Cookiebasierte Affinität“ zu aktivieren. Um zu überprüfen, ob Sie die Einstellung „Cookiebasierte Affinität“ auf der Registerkarte „HTTP-Einstellungen“ im Azure-Portal aktiviert haben, gehen Sie wie folgt vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.

2. Klicken Sie im **linken Navigationsbereich** auf **Alle Ressourcen**. Klicken Sie auf dem Blatt „Alle Ressourcen“ auf den Anwendungsgatewaynamen. Wenn das ausgewählte Abonnement bereits mehrere Ressourcen enthält, können Sie den Anwendungsgatewaynamen im Feld **Nach Name filtern...** eingeben und komfortabel auf das Anwendungsgateway zugreifen.

3. Wählen Sie unter **EINSTELLUNGEN** die Registerkarte **HTTP-Einstellungen** aus.

   ![Ein Screenshot, der „EINSTELLUNGEN“ mit ausgewählten „H T T P“-Einstellungen zeigt.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Klicken Sie auf der rechten Seite auf **appGatewayBackendHttpSettings** , um zu überprüfen, ob Sie **Aktiviert** für „Cookiebasierte Affinität“ ausgewählt haben.

   ![Ein Screenshot, der die Gatewayeinstellungen für ein App-Gateway zeigt, einschließlich, ob cookiebasierte Affinität ausgewählt ist.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



Sie können auch mithilfe einer der folgenden Methoden überprüfen, ob der Wert von **CookieBasedAffinity** unter **backendHttpSettingsCollection** auf *Enabled* festgelegt ist:

- Ausführen von [Get-AzApplicationGatewayBackendHttpSetting](/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) in PowerShell
- Durchsuchen der JSON-Datei mithilfe der Azure Resource Manager-Vorlage

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>Die Anwendung kann cookiebasierte Affinität nicht verarbeiten

#### <a name="cause"></a>Ursache

Das Anwendungsgateway kann sitzungsbasierte Affinität nur unter Verwendung eines Cookies ausführen.

#### <a name="workaround"></a>Problemumgehung

Wenn die Anwendung cookiebasierte Affinität nicht verarbeiten kann, müssen Sie einen externen oder internen Azure Load Balancer oder eine andere Drittanbieterlösung verwenden.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>Anwendung verwendet cookiebasierte Affinität, Anforderungen werden dennoch zwischen Back-End-Servern weitergeleitet

#### <a name="symptom"></a>Symptom

Sie haben die Einstellung „Cookiebasierte Affinität“ aktiviert. Wenn Sie in Internet Explorer über eine Kurznamen-URL auf das Anwendungsgateway zugreifen, z. B. `http://website`, wird die Anforderung dennoch zwischen Back-End-Servern weitergeleitet.

Gehen Sie zum Beheben dieses Problems wie folgt vor:

1. Erstellen Sie eine Webdebugger-Ablaufverfolgung für den „Client“, über den hinter dem Anwendungsgateway eine Verbindung mit der Anwendung hergestellt wird. (In diesem Beispiel wird Fiddler verwendet.)
    **Tipp:** Wenn Sie mit der Verwendung von Fiddler nicht vertraut sind, aktivieren Sie die Option **I want to collect network traffic and analyze it using web debugger** (Ich möchte Netzwerkdatenverkehr mithilfe des Webdebuggers erfassen und analysieren) am unteren Rand.

2. Überprüfen und analysieren Sie die Sitzungsprotokolle, um zu ermitteln, ob die vom Client bereitgestellten Cookies die ARRAffinity-Details enthalten. Wenn Sie die ARRAffinity-Details, z. B. **ARRAffinity=** *ARRAffinityValue* , im festgelegten Cookie nicht finden, bedeutet das, dass der Client nicht mit dem ARRA-Cookie antwortet, das vom Anwendungsgateway bereitgestellt wird.
    Beispiel:

    ![Ein Screenshot, der ein Sitzungsprotokoll mit einem einzelnen hervorgehobenen Eintrag zeigt.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![Ein Screenshot, der Anforderungsheader für H T T P zeigt, einschließlich Cookieinformationen.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

In der Anwendung wird weiterhin versucht, das Cookie für jede Anforderung festzulegen, bis eine Antwort empfangen wird.

#### <a name="cause"></a>Ursache

Dieses Problem tritt auf, da das Cookie mit einer Kurznamen-URL in Internet Explorer und anderen Browsern möglicherweise nicht gespeichert oder verwendet wird.

#### <a name="resolution"></a>Lösung

Um dieses Problem zu beheben, sollten Sie unter Verwendung eines vollqualifizierten Domänennamens (FQDN) auf das Anwendungsgateway zugreifen. Verwenden Sie beispielsweise [http://website.com](https://website.com/) oder [http://appgw.website.com](http://website.com/).

## <a name="additional-logs-to-troubleshoot"></a>Zusätzliche Protokolle für die Problembehandlung

Sie können zusätzliche Protokolle erfassen und analysieren, um die Probleme in Bezug auf die cookiebasierte Sitzungsaffinität zu beheben.

### <a name="analyze-application-gateway-logs"></a>Analysieren von Application Gateway-Protokollen

Gehen Sie zum Erfassen der Application Gateway-Protokolle wie folgt vor:

Ermöglichen der Protokollierung über das Azure-Portal

1. Suchen Sie im [Azure-Portal](https://portal.azure.com/) nach Ihrer Ressource, und klicken Sie auf **Diagnoseprotokolle**.

   Für Application Gateway sind drei Protokolle verfügbar: Zugriffsprotokolle, Leistungsprotokolle und Firewallprotokolle

2. Klicken Sie auf **Diagnose aktivieren** , um mit der Erfassung von Daten zu beginnen.

   ![Ein Screenshot, der ein Anwendungsgateway mit ausgewählten Diagnoseprotokollen zeigt.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. Auf dem Blatt **Diagnoseeinstellungen** werden die Einstellungen für die Diagnoseprotokolle angegeben. In diesem Beispiel werden die Protokolle in Log Analytics gespeichert. Klicken Sie unter **Log Analytics** auf **Konfigurieren** , um den Arbeitsbereich festzulegen. Sie können auch Event Hubs und ein Speicherkonto verwenden, um die Diagnoseprotokolle zu speichern.

   ![Ein Screenshot, der den Bereich „Diagnoseeinstellungen“ mit ausgewähltem „Log Analytics konfigurieren“ zeigt.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Überprüfen Sie die Einstellungen, und klicken Sie auf **Speichern**.

   ![Ein Screenshot, der den Bereich „Diagnoseeinstellungen“ mit ausgewählter Schaltfläche „Speichern“ zeigt.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Anzeigen und Analysieren der Application Gateway-Zugriffsprotokolle

1. Wählen Sie im Azure-Portal in der Application Gateway-Ressourcenansicht im Abschnitt **ÜBERWACHUNG** die Option **Diagnoseprotokolle** aus.

   ![Ein Screenshot, der „ÜBERWACHUNG“ mit ausgewählten Diagnoseprotokollen zeigt.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. Wählen Sie auf der rechten Seite in der Dropdownliste unter **Protokollkategorien** den Eintrag **ApplicationGatewayAccessLog** aus.  

   ![Ein Screenshot, der die Dropdownliste „Protokollkategorien“ mit ausgewähltem „ApplicationGatewayAccessLog“ zeigt.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Klicken Sie in der Liste der Application Gateway-Zugriffsprotokolle auf das Protokoll, das Sie analysieren und exportieren möchten, und exportieren Sie dann die JSON-Datei.

4. Konvertieren Sie die in Schritt 3 exportierte JSON-Datei in eine CSV-Datei, und zeigen Sie diese in Excel, Power BI oder einem anderen Datenvisualisierungstool an.

5. Überprüfen Sie die folgenden Daten:

- **ClientIP:** die Client-IP-Adresse des Clients, der die Verbindung herstellt.
- **ClientPort:** der Quellport des Clients, der die Verbindung herstellt, für die Anforderung.
- **RequestQuery:** gibt den Zielserver an, auf dem die Anforderung empfangen wird.
- **Server-Routed:** Back-End-Poolinstanz, in der die Anforderung empfangen wird.
- **X-AzureApplicationGateway-LOG-ID:** Korrelations-ID, die für die Anforderung verwendet wurde. Kann für die Behandlung von Datenverkehrsproblemen auf den Back-End-Servern verwendet werden. Beispiel: X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **SERVER-STATUS:** HTTP-Antwortcode, den Application Gateway vom Back-End empfangen hat.

  ![Ein Screenshot, der den Serverstatus in unformatiertem Text zeigt, überwiegend verdeckt, mit hervorgehobenem „clientPort“ und „SERVER-ROUTED“.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Wenn zwei Elemente mit derselben Client-ID vom selben Clientport eingehen und an denselben Back-End-Server gesendet werden, bedeutet das, dass das Anwendungsgateway ordnungsgemäß konfiguriert ist.

Wenn zwei Elemente mit derselben Client-ID vom selben Clientport eingehen und an unterschiedliche Back-End-Server gesendet werden, bedeutet das, dass die Anforderung zwischen den Back-End-Servern weitergeleitet wird. Wählen Sie **Application is using cookie-based affinity but requests still bouncing between back-end servers** (Anwendung verwendet cookiebasierte Affinität, Anforderungen werden dennoch zwischen Back-End-Servern weitergeleitet) unten im Fenster aus, um das Problem zu beheben.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Erfassen und Analysieren des HTTP- oder HTTPS-Datenverkehrs mit dem Webdebugger

Mit Webdebuggingtools wie Fiddler können Sie Webanwendungen debuggen, indem Sie den Netzwerkdatenverkehr zwischen dem Internet und Testcomputern erfassen. Mit diesen Tools können Sie eingehende und ausgehende Daten überprüfen, wenn sie im Browser empfangen oder gesendet werden. Fiddler (das in diesem Beispiel verwendete Tool) umfasst eine Option zur HTTP-Wiederholung, über die Sie clientseitige Probleme bei Webanwendungen beheben können, vor allem Probleme bei der Authentifizierung.

Sie können einen Webdebugger Ihrer Wahl verwenden. In diesem Beispiel wird Fiddler verwendet, um HTTP- oder HTTPS-Datenverkehr zu erfassen und zu analysieren. Gehen Sie wie folgt vor:

1. Laden Sie Fiddler unter <https://www.telerik.com/download/fiddler> herunter.

    > [!NOTE]
    > Wählen Sie Fiddler4 aus, wenn auf dem Computer, auf dem die Erfassung erfolgt, .NET 4 installiert ist. Wählen Sie andernfalls Fiddler2 aus.

2. Klicken Sie mit der rechten Maustaste auf die ausführbare Setupdatei, und führen Sie die Installation als Administrator aus.

    ![Ein Screenshot, der das Installationsprogramm des Fiddler-Tools mit einem Kontextmenü zeigt, worin „Als Administrator ausführen“ ausgewählt ist.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Wenn Sie Fiddler öffnen, sollte die Erfassung von Datenverkehr automatisch gestartet werden. (Beachten Sie die Option „Capturing“ (Erfassen) in der linken unteren Ecke.) Drücken Sie F12, um die Erfassung von Datenverkehr zu starten oder zu beenden.

    ![Ein Screenshot, der den Fiddler-Webdebugger mit hervorgehobenem Erfassungsindikator zeigt.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. In den meisten Fällen möchten Sie wahrscheinlich entschlüsselten HTTPS-Datenverkehr erfassen. Sie können die HTTP-Entschlüsselung aktivieren, indem Sie **Tools** > **Fiddler Options** (Tools > Fiddler-Optionen) auswählen und das Kontrollkästchen **Decrypt HTTPS traffic** (HTTPS-Datenverkehr entschlüsseln) aktivieren.

    ![Ein Screenshot, der „Optionen“ in Fiddler mit ausgewähltem H T T P und Datenverkehr durch „HTTPS-Datenverkehr entschlüsseln“ zeigt.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. Sie können vorherige nicht verknüpfte Sitzungen entfernen, bevor Sie das Problem reproduzieren, indem Sie wie im folgenden Screenshot gezeigt auf **X** (Symbol) > **Remove All** (Alle entfernen) klicken: 

    ![Ein Screenshot, der das ausgewählte X-Symbol zeigt, das die Option „Alle entfernen“ anzeigt.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. Nachdem Sie das Problem reproduziert haben, speichern Sie die Datei zur Überprüfung, indem Sie **File** > **Save** > **All Sessions...** (Datei > Speichern > Alle Sitzungen...) auswählen. 

    ![Ein Screenshot, der die ausgewählte „Datei“-Option „Alle Sitzungen speichern" zeigt.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Überprüfen und analysieren Sie die Sitzungsprotokolle, um das Problem zu ermitteln.

    Beispiele:

- **Beispiel A:** Sie finden ein Sitzungsprotokoll, in dem angegeben ist, dass die Anforderung vom Client an die öffentliche IP-Adresse des Anwendungsgateways gesendet wird. Klicken Sie auf dieses Protokoll, um die Details anzuzeigen.  Auf der rechten Seite sind im unteren Feld die Daten aufgeführt, die das Anwendungsgateway an den Client zurückgibt. Wählen Sie die Registerkarte „RAW“ aus, um zu ermitteln, ob der Client die Rückgabe **Set-Cookie: ARRAffinity=** *ARRAffinityValue*. Wenn kein Cookie vorhanden ist, ist die Sitzungsaffinität nicht festgelegt, oder das Anwendungsgateway sendet das Cookie nicht zurück an den Client.

   > [!NOTE]
   > Dieser ARRAffinity-Wert ist die Cookie-ID, die das Anwendungsgateway für den Client festlegt und die dieser an einen bestimmten Back-End-Server senden muss.

   ![Ein Screenshot, der ein Beispiel für Details eines Protokolleintrags mit hervorgehobenem „Set-Cookie“-Wert zeigt.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Beispiel B:** Im nächsten Sitzungsprotokoll ist angegeben, dass der Client dem Anwendungsgateway, das den ARRAAFFINITY-Wert festgelegt hat, antwortet. Wenn die ARRAffinity-Cookie-ID übereinstimmt, sollte das Paket an den gleichen Back-End-Server gesendet werden, der zuvor verwendet wurde. Sehen Sie sich die nächsten Zeilen der HTTP-Kommunikation an, um zu überprüfen, ob sich das ARRAffinity-Cookie des Clients ändert.

   ![Ein Screenshot, der ein Beispiel für Details eines Protokolleintrags mit hervorgehobenem „Cookie“ zeigt.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> In einer Kommunikationssitzung sollte sich das Cookie nicht ändern. Überprüfen Sie das obere Feld auf der rechten Seite. Wählen Sie die Registerkarte „Cookies“ aus, um zu überprüfen, ob der Client das Cookie verwendet und zurück an das Anwendungsgateway sendet. Wenn dies nicht der Fall ist, wird das Cookie im Clientbrowser nicht beibehalten und nicht für die Kommunikation verwendet. In einigen Fällen kann der Client falsche Informationen angeben.

 

## <a name="next-steps"></a>Nächste Schritte

Sollte sich das Problem mit den oben genannten Schritten nicht beheben lassen, erstellen Sie ein [Supportticket](https://azure.microsoft.com/support/options/).
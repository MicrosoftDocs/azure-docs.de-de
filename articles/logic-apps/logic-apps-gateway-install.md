---
title: Installieren eines lokalen Datengateways – Azure Logic Apps | Microsoft-Dokumentation
description: Bevor Sie über Azure Logic Apps lokal auf Daten zugreifen können, müssen Sie das lokale Datengateway herunterladen und installieren.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 10a6e5c33f6a3c23d98e6eb3380de0d6dc6ac216
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544479"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Installieren des lokalen Datengateways für Azure Logic Apps

Bevor Sie eine Verbindung mit lokalen Datenquellen aus Azure Logic Apps herstellen können, müssen Sie das lokale Datengateway herunterladen und auf einem lokalen Computer installieren. Das Gateway fungiert als Brücke, die schnelle Datenübertragung und Verschlüsselung zwischen lokalen (nicht cloudbasierten) Datenquellen und Ihren Logik-Apps bereitstellt. In diesem Artikel wird gezeigt, wie Sie Ihr lokales Datengateway herunterladen, installieren und einrichten können. 

Sie können die gleiche Gatewayinstallation für andere Dienste verwenden, etwa für Power BI, Microsoft Flow, PowerApps und Azure Analysis Services. Erfahren Sie mehr darüber, [wie das Datengateway funktioniert](#gateway-cloud-service).

<a name="supported-connections"></a>

Das Gateway unterstützt [lokale Connectors](../connectors/apis-list.md#on-premises-connectors) in Azure Logic Apps für die folgenden Datenquellen:

*   BizTalk Server 2016
*   Dateisystem
*   IBM DB2  
*   IBM Informix
*   IBM MQ
*   MySQL
*   Oracle-Datenbank
*   PostgreSQL
*   SAP-Anwendungsserver 
*   SAP-Nachrichtenserver
*   SharePoint Server
*   SQL Server
*   Teradata

Informationen zum Verwenden des Gateways mit anderen Diensten finden Sie in den folgenden Artikeln:

* [Microsoft Power BI | Lokales Datengateway](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Power Apps lokales Datengateway](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Microsoft Flow | Lokales Datengateway](https://flow.microsoft.com/documentation/gateway-manage/)
* [Azure Analysis Services | Lokales Datengateway](../analysis-services/analysis-services-gateway.md)

<a name="requirements"></a>

## <a name="prerequisites"></a>Voraussetzungen

* Ein [Geschäfts-, Schul- oder Unikonto](../active-directory/fundamentals/sign-up-organization.md) mit einem [Azure-Abonnement](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access) 

  Während der Gatewayinstallation melden Sie sich bei diesem Konto an und können die Gatewayinstallation Ihrem Azure-Abonnement zuordnen. 
  Beim Erstellen einer Azure-Ressource für Ihre Gatewayinstallation im Azure-Portal verwenden Sie später ebenfalls das gleiche Konto. 
  Wenn Sie noch kein Azure-Abonnement haben, <a href="https://azure.microsoft.com/free/" target="_blank">melden Sie sich für ein kostenloses Azure-Konto an</a>.

* Dies sind die Anforderungen für den lokalen Computer:

  **Mindestanforderungen**

  * .NET Framework 4.5.2
  * 64-Bit-Version von Windows 7 oder Windows Server 2008 R2 (oder höher)

  **Empfohlen**

  * 8-Kern-CPU
  * 8 GB Arbeitsspeicher
  * 64-Bit-Version von Windows Server 2012 R2 (oder höher)

* **Wichtige Überlegungen**

  * Sie können das lokale Datengateway nur auf einem lokalen Computer und nicht auf einem Domänencontroller installieren. Sie müssen das Gateway jedoch nicht auf dem Computer installieren, auf dem sich die Datenquelle befindet. Darüber hinaus benötigen Sie nur ein Gateway für alle Ihre Datenquellen, Sie müssen das Gateway also nicht für jede Datenquelle installieren.

    > [!TIP]
    > Um Wartezeiten zu minimieren, können Sie das Gateway so nah wie möglich zur Datenquelle oder auf demselben Computer installieren, vorausgesetzt, Sie haben die Berechtigungen.

  * Installieren Sie das Gateway auf einem Computer, der mit dem Internet verbunden ist, immer eingeschaltet ist und sich *nicht* im Energiesparmodus befindet. Andernfalls kann das Gateway nicht ausgeführt werden. 
  Darüber hinaus kann die Leistung bei Einsatz eines Drahtlosnetzwerks ggf. beeinträchtigt werden.

  * Während der Installation können Sie sich nur mit einem von Azure Active Directory (Azure AD) verwalteten [Geschäfts-, Schul- oder Unikonto](../active-directory/sign-up-organization.md) anmelden, beispielsweise @contoso.onmicrosoft.com, aber nicht mit einem Azure-B2B-(Gast-)Konto oder einem persönlichen Microsoft-Konto, wie etwa @hotmail.com oder @outlook.com. 
  Achten Sie darauf, zur Registrierung Ihrer Gatewayinstallation im Azure-Portal durch Erstellen einer Gatewayressource das gleiche Anmeldekonto zu verwenden. 
  Anschließend können Sie diese Gatewayressource auswählen, wenn Sie die Verbindung zwischen Ihrer Logik-App und der lokalen Datenquelle herstellen. 
  [Warum muss ich ein Azure AD-Geschäfts-, -Schul- oder -Unikonto verwenden?](#why-azure-work-school-account)

  > [!TIP]
  > Wenn Sie sich für ein Office 365-Angebot registriert und nicht Ihre tatsächliche Geschäfts-E-Mail-Adresse angegeben haben, haben Sie unter Umständen eine Anmeldeadresse, die beispielsweise wie folgt aussieht: `username@domain.onmicrosoft.com`. 
  >
  > Wenn Sie ein Microsoft-Konto mit einem [Visual Studio-Standardabonnement](https://visualstudio.microsoft.com/vs/pricing/) verwenden möchten, [erstellen Sie zuerst ein Verzeichnis (Mandant) in Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md) mit Ihrem Microsoft-Konto, oder verwenden Sie das Standardverzeichnis. 
  > Fügen Sie dem Verzeichnis einen Benutzer mit einem Kennwort hinzu, und erteilen Sie diesem Benutzer Zugriff auf Ihr Abonnement. 
  > Sie können sich dann während der Installation des Gateways mit diesem Benutzernamen und Kennwort anmelden.

  * Die für die Gatewayinstallation ausgewählte Region bestimmt den Standort, an dem Sie Ihr Gateway später durch Erstellung einer Azure-Ressource in Azure registrieren. 
  Beim Erstellen dieser Gatewayressource in Azure müssen Sie *denselben* Standort wie für die Gatewayinstallation auswählen. Als Standardregion ist der gleiche Standort wie für den Azure AD-Mandanten angegeben, der Ihr Azure-Konto verwaltet. Sie können den Standort jedoch während der Gatewayinstallation ändern.

  * Wenn Sie bereits ein Gateway besitzen, das Sie mit einem Installer eingerichtet haben, dessen Version älter ist als 14.16.6317.4, können Sie den Ort Ihres Gateways nicht durch Ausführung des neuesten Installers ändern. Sie können aber den neuesten Installer verwenden, um ein neues Gateway mit dem gewünschten Ort einzurichten.
  
    Wenn Sie einen Gateway-Installer mit einer älteren Version als 14.16.6317.4 besitzen, aber Ihr Gateway noch nicht installiert haben, können Sie stattdessen den neuesten Installer herunterladen und verwenden.

## <a name="high-availability-support"></a>Unterstützung für Hochverfügbarkeit

Das lokale Datengateway unterstützt die Hochverfügbarkeit, wenn Sie mehrere Gatewayinstallationen besitzen und diese als Cluster eingerichtet haben. Wenn bereits ein Gateway vorhanden ist, können Sie beim Erstellen eines weiteren Gateways optional Hochverfügbarkeitscluster erstellen. Diese Cluster gliedern Gateways in Gruppen, mit deren Hilfe Single Points of Failure vermieden werden können. Außerdem unterstützen jetzt alle lokalen Datengatewayconnectors Hochverfügbarkeit.

Wenn Sie das lokale Datengateway verwenden möchten, sehen Sie sich die folgenden Anforderungen und Überlegungen an:

* Sie müssen bereits über mindestens eine Gatewayinstallation im gleichen Azure-Abonnement wie das primäre Gateway sowie über den Wiederherstellungsschlüssel für diese Installation verfügen. 

* Für das primäre Gateway muss das Gatewayupdate vom November 2017 oder ein höheres Update ausgeführt werden.

Wenn diese Anforderungen erfüllt sind, wählen Sie beim Erstellen des nächsten Gateways **Add to an existing gateway cluster** (Zu einem vorhandenen Gatewaycluster hinzufügen) und dann das primäre Gateway für den Cluster aus. Geben Sie anschließend den Wiederherstellungsschlüssel für dieses primäre Gateway an.
Weitere Informationen finden Sie unter [Hochverfügbarkeitscluster für lokale Datengateways](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Installieren eines Datengateways

1. [Laden Sie den Gateway-Installer auf einen lokalen Computer herunter, speichern Sie ihn, und führen Sie ihn aus.](https://aka.ms/on-premises-data-gateway-installer)

2. Übernehmen Sie den Standardinstallationspfad, oder geben Sie den Speicherort auf Ihrem Computer an, an dem das Gateway installiert werden soll.

3. Lesen und akzeptieren Sie die Nutzungsbedingungen und Datenschutzbestimmungen, und klicken Sie dann auf **Installieren**.

   ![Akzeptieren der Nutzungsbedingungen und Datenschutzbestimmungen](./media/logic-apps-gateway-install/accept-terms.png)

4. Geben Sie nach der Installation des Gateways die E-Mail-Adresse Ihres Geschäfts-, Schul- oder Unikontos ein, und klicken Sie auf **Anmelden**.

   ![Anmelden mit einem Geschäfts-, Schul- oder Unikonto](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. Klicken Sie auf **Register a new gateway on this computer** (Neues Gateway auf diesem Computer registrieren) > **Weiter**. Dadurch wird Ihre Gatewayinstallation beim [Gatewayclouddienst](#gateway-cloud-service) registriert. 

   ![Registrieren des Gateways](./media/logic-apps-gateway-install/register-new-gateway.png)

6. Geben Sie die folgenden Informationen für Ihre Gatewayinstallation an:

   * Den für die Installation gewünschten Namen 

   * Den zu erstellenden Wiederherstellungsschlüssel (mindestens acht Zeichen)

     > [!IMPORTANT]
     > Bewahren Sie Ihren Wiederherstellungsschlüssel an einem sicheren Ort auf. Sie benötigen diesen Schlüssel, wenn Sie den Ort des Gateways ändern oder ein vorhandenes Gateway migrieren, wiederherstellen oder übernehmen.

   * Bestätigung für den Wiederherstellungsschlüssel 

     ![Einrichten des Gateways](./media/logic-apps-gateway-install/set-up-gateway.png)

7. Überprüfen Sie die für den Gatewayclouddienst und die Azure Service Bus-Instanz ausgewählte Region, die von Ihrer Gatewayinstallation verwendet wird. 

   ![Überprüfen der Region](./media/logic-apps-gateway-install/check-region.png)

   > [!IMPORTANT]
   > Wenn Sie diese Region nach Abschluss der Gatewayinstallation ändern möchten, benötigen Sie den Wiederherstellungsschlüssel für diese Gatewayinstallation. Darüber hinaus müssen Sie das Gateway deinstallieren und neu installieren. Weitere Informationen finden Sie unter [Ändern des Orts oder Migrieren, Wiederherstellen oder Übernehmen eines vorhandenen Gateways](#update-gateway-installation).

   *Warum sollte ich die Region für die Gatewayinstallation ändern?* 

   Sie können die Region des Gateways in die Region der Logik-App ändern, um beispielsweise die Wartezeit zu verringern. 
   Alternativ können Sie die Region auswählen, die der lokalen Datenquelle am nächsten liegt. 
   Ihre *Gatewayressource in Azure* und Ihre Logik-App können unterschiedliche Standorte haben.

8. Klicken Sie zum Übernehmen der Standardregion auf **Konfigurieren**. Führen Sie zum Ändern der Standardregion die folgenden Schritte aus:

   1. Klicken Sie neben der aktuellen Region auf **Region ändern**. 

      ![Ändern der Region](./media/logic-apps-gateway-install/change-region.png)

   2. Öffnen Sie auf der nächsten Seite die Liste **Region auswählen**, wählen Sie die gewünschte Region aus, und klicken Sie auf **Fertig**.

      ![Auswählen einer anderen Region](./media/logic-apps-gateway-install/select-region-gateway-install.png)

9. Klicken Sie auf der Bestätigungsseite auf **Schließen**. 

   Der Installer bestätigt, dass Ihr Gateway nun online und einsatzbereit ist.

   ![Fertiges Gateway](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

10. Registrieren Sie nun Ihr Gateway in Azure, indem Sie [eine Azure-Ressource für Ihre Gatewayinstallation erstellen](../logic-apps/logic-apps-gateway-connection.md). 

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Ändern des Orts oder Migrieren, Wiederherstellen oder Übernehmen eines vorhandenen Gateways

Wenn Sie den Ort Ihres Gateways ändern, die Gatewayinstallation auf einen neuen Computer verschieben, ein beschädigtes Gateway wiederherstellen oder den Besitz eines vorhandenen Gateways übernehmen müssen, benötigen Sie den Wiederherstellungsschlüssel, der bei der Gatewayinstallation bereitgestellt wurde. Bei dieser Aktion wird das alte Gateway getrennt.

1. Klicken Sie auf Ihrem Computer in der **Systemsteuerung** auf **Programme und Features**. Klicken Sie in der Liste der Programme auf **Lokales Datengateway** und dann auf **Deinstallieren**.

2. [Installieren Sie das lokale Datengateway neu.](https://aka.ms/on-premises-data-gateway-installer)

3. Wenn der Installer geöffnet wurde, melden Sie sich mit dem gleichen Geschäfts-, Schul- oder Unikonto an, das auch zuvor zum Installieren des Gateways verwendet wurde.

4. Aktivieren Sie **Migrate, restore, or takeover an existing gateway** (Migrieren, Wiederherstellen oder Übernehmen eines vorhandenen Gateways), und klicken Sie dann auf **Weiter**.

   ![Aktivieren von „Migrate, restore, or takeover an existing gateway“ (Migrieren, Wiederherstellen oder Übernehmen eines vorhandenen Gateways)](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

5. Wählen Sie unter **Verfügbare Gateways** bzw. **Verfügbare Gatewaycluster**, die Gatewayinstallation aus, die Sie ändern möchten. Geben Sie den Wiederherstellungsschlüssel für die Gatewayinstallation ein. 

   ![Auswählen des primären Gateways](./media/logic-apps-gateway-install/select-existing-gateway.png)

6. Wählen Sie zum Ändern der Region **Region ändern** und dann die neue Region.

7. Wenn Sie fertig sind, wählen Sie **Konfigurieren** aus.

## <a name="configure-proxy-or-firewall"></a>Konfigurieren eines Proxys oder einer Firewall

Das lokale Datengateway stellt eine ausgehende Verbindung mit [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) her. Wenn in Ihrer Arbeitsumgebung der Datenverkehr über einen Proxy an das Internet weitergeleitet werden muss, verhindert diese Einschränkung unter Umständen, dass das Datengateway eine Verbindung mit dem Gatewayclouddienst herstellt. Um zu überprüfen, ob Ihr Netzwerk einen Proxy verwendet, lesen Sie die Informationen im folgenden Artikel auf superuser.com: 

[How do I know what proxy server I'm using? (SuperUser.com)](https://superuser.com/questions/346372/how-do-i-know-what-proxy-server-im-using) (Wie ermittle ich, welchen Proxyserver ich verwende? (SuperUser.com)) 

Informationen dazu, wie Sie Proxyinformationen für Ihr Gateway bereitstellen, finden Sie unter [Konfigurieren von Proxyeinstellungen für Power BI Gateways](https://docs.microsoft.com/power-bi/service-gateway-proxy). Um zu überprüfen, ob der Proxy oder die Firewall Verbindungen möglicherweise blockiert, vergewissern Sie sich, dass der Computer tatsächlich eine Verbindung mit dem Internet und [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) herstellen kann. Führen Sie an einer PowerShell-Eingabeaufforderung folgenden Befehl aus:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> Dieser Befehl testet nur die Netzwerkverbindung und die Verbindung mit dem Azure Service Bus. Der Befehl führt keine Aktion für das Gateway oder den Gatewayclouddienst aus, der Ihre Anmeldeinformationen und Gatewaydetails verschlüsselt und speichert. 
>
> Außerdem ist dieser Befehl ist nur unter Windows Server 2012 R2 oder höher und Windows 8.1 oder höher verfügbar. In früheren Betriebssystemversionen können Sie Telnet verwenden, um Konnektivität zu testen. Erfahren Sie mehr über [Azure Service Bus und Hybridlösungen](../service-bus-messaging/service-bus-messaging-overview.md).

Die Ergebnisse sollten etwa wie das folgende Beispiel aussehen. Dabei muss **TcpTestSucceeded** auf **True** festgelegt sein:

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Ist **TcpTestSucceeded** nicht auf **True** festgelegt, wird Ihr Gateway möglicherweise durch eine Firewall blockiert. Wenn Sie alles abdecken möchten, können Sie die Werte von **ComputerName** und **Port** durch die Werte unter [Konfigurieren von Ports](#configure-ports) in diesem Artikel ersetzen.

Es kann auch sein, dass die Firewall Verbindungen blockiert, die von Azure Service Bus mit Azure-Rechenzentren hergestellt werden. Trifft dies zu, gehen Sie so vor, dass Sie alle IP-Adressen für diese Rechenzentren in Ihrer Region genehmigen (Blockierung aufheben). Für diese IP-Adressen können Sie [die Azure-IP-Adressenliste hier abrufen](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>Konfigurieren von Ports

Das Gateway erstellt eine ausgehende Verbindung mit [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) und kommuniziert über Ports für ausgehenden Datenverkehr: TCP 443 (Standard), 5671, 5672, 9350 bis 9354. Das Gateway benötigt keine eingehenden Ports. Erfahren Sie mehr über [Azure Service Bus und Hybridlösungen](../service-bus-messaging/service-bus-messaging-overview.md).

Das Gateway verwendet die folgenden vollqualifizierten Domänennamen:

| Domänennamen | Ausgehende Ports | BESCHREIBUNG | 
| ------------ | -------------- | ----------- | 
| *. analysis.windows.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.microsoftonline-p.com | 443 | Wird je nach Konfiguration für die Authentifizierung verwendet. | 
| *.msftncsi.com | 443 | Wird verwendet, um die Internetkonnektivität zu testen, wenn der Power BI-Dienst das Gateway nicht erreicht. | 
| *.servicebus.windows.net | 443, 9350-9354 | Listener auf Service Bus Relay per TCP (443 für Access Control-Tokenbeschaffung erforderlich) | 
| *.servicebus.windows.net | 5671-5672 | Advanced Message Queuing Protocol (AMQP) | 
| login.microsoftonline.com | 443 | HTTPS | 
||||

In einigen Fällen werden Azure Service Bus-Verbindungen nicht mithilfe von vollständig qualifizierten Domänennamen hergestellt, sondern mit IP-Adressen. Es wird daher empfohlen, die IP-Adressen für Ihren Datenbereich in die Whitelist der Firewall aufzunehmen. Wenn Sie eine Whitelist der IP-Adressen anstelle der Domänen aufstellen möchten, können Sie die [Microsoft Azure Datacenter IP-Bereicheliste](https://www.microsoft.com/download/details.aspx?id=41653) herunterladen und verwenden. Die IP-Adressen in dieser Liste finden Sie in der [CIDR-Notation](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) (Classless Inter-Domain Routing, klassenloses domänenübergreifendes Routing).

### <a name="force-https-communication-with-azure-service-bus"></a>Erzwingen von HTTPS-Kommunikation mit Azure Service Bus

Einige Proxys lassen Datenverkehr nur an Port 80 und 443 durch. Standardmäßig erfolgt die Kommunikation mit Azure Service Bus über andere Ports als 443.
Sie können erzwingen, dass das Gateway mit Azure Service Bus über HTTPS kommuniziert. Dies kann allerdings die Leistung erheblich beeinträchtigen. Führen Sie diese Aufgabe in folgenden Schritten aus:

1. Navigieren Sie zum Speicherort für den Client des lokalen Datengateways. In der Regel befindet er sich hier: ```C:\Program Files\On-premises data gateway\Microsoft.PowerBI.EnterpriseGateway.exe```

   Öffnen Sie zum Ermitteln des Clientspeicherorts andernfalls die Dienstkonsole auf dem gleichen Computer, suchen Sie **On-premises data gateway service** (Dienst „Lokales Datengateway“), und sehen Sie sich die Eigenschaft **Path to executable** (Pfad zur ausführbaren Datei) an.

2. Öffnen Sie die folgende *Konfigurationsdatei*: **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

3. Ändern Sie den Wert **ServiceBusSystemConnectivityModeString** von **AutoDetect** in **Https**:

   ```html
   <setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
      <value>Https</value>
   </setting>
   ```

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Windows-Dienstkonto

Auf dem Computer, auf dem Sie das lokale Datengateway installieren, wird das Gateway als Windows-Dienstkonto namens „On-premises data gateway service“ (Lokaler Datengatewaydienst) ausgeführt. Für die Kontoanmeldeinformationen vom Typ „Anmelden als“ verwendet das Gateway jedoch den Namen „NT SERVICE\PBIEgwService“. Auf dem Computer, auf dem Sie das Gateway installieren, verfügt das Gateway standardmäßig über Berechtigungen vom Typ „Anmelden als Dienst“. Das Windows-Dienstkonto für das Gateway unterscheidet sich in der Regel von dem Konto, das Sie zum Herstellen einer Verbindung mit lokalen Datenquellen verwenden, sowie von dem Geschäfts-, Schul- oder Unikonto, das Sie für die Anmeldung bei Clouddiensten verwenden.

Zum Erstellen und Verwalten des Gateways im Azure-Portal muss dieses Windows-Dienstkonto mindestens über Berechtigungen vom Typ **Mitwirkender** verfügen. Informationen zum Überprüfen dieser Berechtigungen finden Sie unter [Verwalten des Zugriffs mit RBAC und dem Azure-Portal](../role-based-access-control/role-assignments-portal.md). 

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Neustarten des Gateways

Das Datengateway wird als Windows-Dienst ausgeführt, und Sie können das Gateway wie jeden anderen Windows-Dienst auf verschiedene Arten starten und beenden. Beispielsweise können Sie eine Eingabeaufforderung mit erhöhten Berechtigungen auf dem Computer öffnen, auf dem das Gateway ausgeführt wird, und einen der folgenden Befehle ausführen:

* Führen Sie zum Beenden des Diensts den folgenden Befehl aus:
  
  `net stop PBIEgwService`

* Führen Sie zum Starten des Diensts den folgenden Befehl aus:
  
  `net start PBIEgwService`

## <a name="tenant-level-administration"></a>Verwaltung auf Mandantenebene 

Derzeit können Mandantenadministratoren nicht alle Gateways, die andere Benutzer installiert und konfiguriert haben, zentral verwalten. Als Mandantenadministrator sollten Sie die Benutzer in Ihrer Organisation bitten, Sie jedem Gateway als Administrator hinzuzufügen, das sie installieren. So können Sie alle Gateways in Ihrer Organisation über die Seite „Gatewayeinstellungen“ oder über [PowerShell-Befehle](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters) verwalten. 

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-gateway-work"></a>Wie funktioniert das Gateway?

Das Datengateway ermöglicht die schnelle und sichere Kommunikation zwischen Ihrer Logik-App, dem Gatewayclouddienst und Ihrer lokalen Datenquelle. Der Gatewayclouddienst verschlüsselt und speichert Ihre Anmeldeinformationen für Datenquellen und Ihre Gatewaysdetails. Der Dienst leitet außerdem Abfragen und deren Ergebnisse zwischen Ihrer Logik-App, dem lokalen Datengateway und Ihrer lokalen Datenquelle weiter. 

Das Gateway funktioniert mit Firewalls und verwendet nur ausgehende Verbindungen. Der gesamte Datenverkehr stammt als sicherer ausgehender Datenverkehr vom Gateway-Agent. Das Gateway überträgt Daten aus lokalen Quellen durch verschlüsselte Kanäle über Azure Service Bus. Diese Service Bus-Instanz erstellt einen Kanal zwischen dem Gateway und dem aufrufenden Dienst, speichert jedoch keine Daten. Alle über das Gateway übertragenen Daten sind verschlüsselt.

![Datenflussdiagramm-für-lokales-Datengateway](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

In den folgenden Schritten wird beschrieben, was geschieht, wenn ein Benutzer in der Cloud mit einem Element interagiert, das mit Ihrer lokalen Datenquelle verbunden ist:

1. Der Gatewayclouddienst erstellt zusammen mit den verschlüsselten Anmeldeinformationen für die Datenquelle eine Abfrage und sendet die Abfrage an die Warteschlange zur Verarbeitung durch das Gateway.

2. Der Gatewayclouddienst analysiert die Abfrage und überträgt die Anforderung per Push an den Azure Service Bus.

3. Das lokale Datengateway fragt den Azure Service Bus nach ausstehenden Anforderungen ab.

4. Das Gateway ruft die Abfrage ab, entschlüsselt die Anmeldeinformationen und stellt mit diesen Anmeldeinformationen eine Verbindung mit der Datenquelle her.

5. Das Gateway sendet die Abfrage zur Ausführung an die Datenquelle.

6. Die Ergebnisse werden aus der Datenquelle zurück an das Gateway und dann an den Gatewayclouddienst gesendet. Der Gatewayclouddienst verwendet dann die Ergebnisse.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="general"></a>Allgemein

**F:** Benötige ich ein Gateway für Datenquellen in der Cloud (etwa für Azure SQL-Datenbank)? <br/>
**A:** Nein. Das Gateway stellt nur Verbindungen mit lokalen Datenquellen her.

**F:** Muss das Gateway auf dem gleichen Computer installiert sein wie die Datenquelle? <br/>
**A:** Nein. Das Gateway stellt mithilfe der bereitgestellten Verbindungsinformationen eine Verbindung mit der Datenquelle her. Stellen Sie sich das Gateway in diesem Sinne als Clientanwendung vor. Für das Gateway ist nur die Funktion zum Herstellen der Verbindung mit dem angegebenen Servernamen erforderlich.

<a name="why-azure-work-school-account"></a>

**F:** Warum muss ich ein Geschäfts-, Schul- oder Unikonto verwenden, um mich anzumelden? <br/>
**A:** Sie können beim Installieren des lokalen Datengateways ausschließlich ein Geschäfts-, Schul- oder Unikonto verwenden. Ihr Anmeldekonto ist in einem Mandanten gespeichert, der von Azure Active Directory (Azure AD) verwaltet wird. Normalerweise entspricht der Benutzerprinzipalname (UPN) Ihres Azure AD-Kontos der E-Mail-Adresse.

**F:** Wo werden meine Anmeldeinformationen gespeichert? <br/>
**A:** Die Anmeldeinformationen, die Sie für eine Datenquelle eingeben, werden verschlüsselt und im Gatewayclouddienst gespeichert. Die Anmeldeinformationen werden im lokalen Datengateway entschlüsselt.

**F:** Gibt es Anforderungen an die Netzwerkbandbreite? <br/>
**A:** Überprüfen Sie, ob die Netzwerkverbindung einen guten Durchsatz ermöglicht. Jede Umgebung ist anders, und die Menge der zu sendenden Daten kann sich auf die Ergebnisse auswirken. Probieren Sie [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) aus, um eine Durchsatzstufe zwischen der lokalen Datenquelle und den Azure-Datencentern zu gewährleisten. Sie können mithilfe eines externen Tools wie Azure Speed Test messen, wie hoch der Durchsatz ist.

**F:** Mit welcher Wartezeit ist beim Ausführen von Abfragen für eine Datenquelle über das Gateway zu rechnen? Welche Architektur ist die beste? <br/>
**A:** Installieren Sie das Gateway möglichst nah an der Datenquelle, um die Netzwerkwartezeit zu reduzieren. Wenn Sie das Gateway auf der tatsächlichen Datenquelle installieren können, wird die Wartezeit durch diese Nähe minimiert. Berücksichtigen Sie außerdem die Nähe zu Azure-Datencentern. Wenn für Ihren Dienst beispielsweise das Rechenzentrum „USA, Westen“ verwendet wird und Sie SQL Server auf einer Azure-VM hosten, sollte sich die Azure-VM ebenfalls in der Region „USA, Westen“ befinden. Aufgrund dieser Nähe wird die Wartezeit verringert, und es werden Gebühren für ausgehenden Datenverkehr auf der Azure-VM vermieden.

**F:** Wie werden Ergebnisse an die Cloud zurückgesendet? <br/>
**A:** Die Ergebnisse werden über Azure Service Bus gesendet.

**F:** Gibt es eingehende Verbindungen mit dem Gateway aus der Cloud? <br/>
**A:** Nein. Das Gateway verwendet ausgehende Verbindungen mit Azure Service Bus.

**F:** Was geschieht, wenn ich ausgehende Verbindungen blockiere? Was muss ich öffnen? <br/>
**A:** Die Ports und Hosts, die das Gateway verwendet.

**F:** Wie heißt der eigentliche Windows-Dienst? <br/>
**A:** Auf der Registerkarte „Dienste“ im Task-Manager lautet der Dienstname „PBIEgwService“ oder Power BI Enterprise Gateway-Dienst. In der Dienstkonsole lautet der Dienstname „On-premises data gateway service“ (Dienst „Lokales Datengateway“). Der Windows-Dienst verwendet „NT SERVICE\PBIEgwService“ als Dienst-SID (SSID).

**F:** Kann der Windows-Gatewaydienst mit einem Azure Active Directory-Konto ausgeführt werden? <br/>
**A:** Nein. Der Windows-Dienst benötigt ein gültiges Windows-Konto.

### <a name="disaster-recovery"></a>Notfallwiederherstellung

**F:** Welche Optionen sind für die Notfallwiederherstellung verfügbar? <br/>
**A:** Sie können den Wiederherstellungsschlüssel verwenden, um ein Gateway wiederherzustellen oder zu verschieben. Wenn Sie das Gateway installieren, geben Sie den Wiederherstellungsschlüssel an.

**F:** Welchen Vorteil bietet der Wiederherstellungsschlüssel? <br/>
**A:** Der Wiederherstellungsschlüssel bietet eine Möglichkeit zum Migrieren oder Wiederherstellen Ihrer Gatewayeinstellungen nach einem Notfall.

## <a name="troubleshooting"></a>Problembehandlung

In diesem Abschnitt werden einige allgemeine Probleme behandelt, die beim Einrichten und Verwenden des lokalen Datengateways auftreten können.

**F:** Warum ist bei der Gatewayinstallation ein Fehler aufgetreten? <br/>
**A:** Dieses Problem kann auftreten, wenn die Virenschutzsoftware auf dem Zielcomputer veraltet ist. Sie können die Virenschutzsoftware entweder aktualisieren oder für die Dauer der Gatewayinstallation deaktivieren. Aktivieren Sie die Software anschließend wieder.

**F:** Warum sehe ich meine Gatewayinstallation nicht, wenn ich die Gatewayressource in Azure erstelle? <br/>
**A:** Dieses Problem kann folgende Ursachen haben:

* Ihre Gatewayinstallation wurde bereits registriert und von einer anderen Gatewayressource in Azure beansprucht. Gatewayinstallationen werden in der Instanzenliste nicht angezeigt, nachdem Gatewayressourcen für sie erstellt wurden.
Sehen Sie sich zum Überprüfen der Gatewayregistrierungen im Azure-Portal alle Azure-Ressourcen mit dem Typ **Lokales Datengateway** für *alle* Azure-Abonnements an. 

* Die Azure AD-Identität der Person, die das Gateway installiert hat, unterscheidet sich von der Person, die sich beim Azure-Portal angemeldet hat. Vergewissern Sie sich, dass Sie mit der gleichen Identität angemeldet sind, mit der das Gateway installiert wurde.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**F:** Wo befinden sich die Gatewayprotokolle? <br/>
**A:** Informationen hierzu finden Sie weiter unten in diesem Artikel im [Abschnitt **Protokolle**](#logs).

**F:** Wie kann ich feststellen, welche Abfragen an die lokale Datenquelle gesendet werden? <br/>
**A:** Sie können die Abfrageablaufverfolgung aktivieren. Diese enthält auch die gesendeten Abfragen. Denken Sie daran, die Abfrageablaufverfolgung nach Abschluss der Problembehandlung wieder auf den ursprünglichen Wert zurückzusetzen. Wenn Sie die Abfrageablaufverfolgung aktiviert lassen, werden größere Protokolle erstellt.

Sie können auch Tools anzeigen, die Ihre Datenquelle für die Verfolgung von Abfrageabläufen bietet. Sie können z.B. Erweiterte Ereignisse oder SQL Profiler für SQL Server und Analysis Services verwenden.

### <a name="outdated-gateway-version"></a>Veraltete Gatewayversion

Es können vermehrt Probleme auftreten, wenn die Gatewayversion veraltet ist. Achten Sie daher am besten darauf, dass Sie immer die aktuelle Version verwenden. Wenn Sie das Gateway für einen Monat oder länger nicht aktualisiert haben, sollten Sie erwägen, die neueste Version des Gateways zu installieren und zu ermitteln, ob Sie das Problem reproduzieren können.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Fehler Fehler beim Hinzufügen des Benutzers zur Gruppe. (-2147463168 PBIEgwService Leistungsprotokollbenutzer)

Dieser Fehler wird ggf. angezeigt, wenn Sie versuchen, das Gateway auf einem Domänencontroller zu installieren, der nicht unterstützt wird. Stellen Sie sicher, dass Sie das Gateway auf einem Computer bereitstellen, der kein Domänencontroller ist.

<a name="logs"></a>

### <a name="logs"></a>Protokolle

Sammeln Sie zur Problembehandlung stets die Gatewayprotokolle, und sehen Sie sich diese an. Sie haben mehrere Optionen zum Sammeln der Protokolle. Die einfachste Möglichkeit ist jedoch die Erfassung über die Benutzeroberfläche des Gateway-Installers nach der Gatewayinstallation. 

1. Öffnen Sie auf Ihrem Computer den Installer für das lokale Datengateway.
2. Wählen Sie im Menü links **Diagnose** aus.
3. Klicken Sie unter **Gatewayprotokolle** auf **Protokolle exportieren**.

   ![Exportieren von Protokollen über den Gateway-Installer](./media/logic-apps-gateway-install/export-logs.png)

Nachfolgend sehen Sie weitere Speicherorte mit verschiedenen Protokollen:

| Protokolltyp | Location | 
|----------|----------| 
| **Installationsprotokolle** | %localappdata%\Temp\On-premises_data_gateway_<*jjjjmmtt*>.<*Nummer*>.log | 
| **Konfigurationsprotokolle** | C:\Users\<*Benutzername*>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator<*jjjjmmtt*>.<*Nummer*>.log | 
| **Enterprise-Gatewaydienstprotokolle** | C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\Gateway<*jjjjmmtt*>.<*Nummer*>.log | 
||| 

**Ereignisprotokolle**

Führen Sie die folgenden Schritte aus, um zu den Ereignisprotokollen für das Gateway zu navigieren:

1. Öffnen Sie auf dem Computer mit der Gatewayinstallation die **Ereignisanzeige**. 
2. Erweitern Sie **Ereignisanzeige (lokal)** > **Anwendungs- und Dienstprotokolle**. 
3. Wählen Sie **On-premises data gateway service** (Dienst „Lokales Datengateway“) aus.

   ![Anzeigen von Ereignisprotokollen für das Gateway](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="review-slow-query-performance"></a>Überprüfen bei beeinträchtigter Abfrageleistung

Falls Abfragen über das Gateway langsam ausgeführt werden, können Sie zusätzliche Protokolle aktivieren, die Abfragen und ihre Dauer ausgeben. Anhand dieser Protokolle können Sie unter Umständen die Abfragen mit beeinträchtigter Leistung oder langer Ausführungsdauer ermitteln. Um die Abfrageleistung zu verbessern, müssen Sie möglicherweise Ihre Datenquelle ändern und beispielsweise Indizes für SQL Server-Abfragen anpassen.

Führen Sie die folgenden Schritte aus, um die Dauer einer Abfrage zu ermitteln:

1. Navigieren Sie zum Speicherort für den Gatewayclient. In der Regel befindet er sich hier: ```C:\Program Files\On-premises data gateway```

   Öffnen Sie zum Ermitteln des Clientspeicherorts andernfalls die Dienstkonsole auf dem gleichen Computer, suchen Sie **On-premises data gateway service** (Dienst „Lokales Datengateway“), und sehen Sie sich die Eigenschaft **Path to executable** (Pfad zur ausführbaren Datei) an.

2. Öffnen Sie die folgenden Konfigurationsdateien, und bearbeiten Sie sie wie hier beschrieben:

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     Ändern Sie in dieser Datei den Wert **EmitQueryTraces** von **false** in **true**, damit Ihr Gateway Abfragen protokollieren kann, die von einem Gateway an eine Datenquelle gesendet werden:

     ```html
     <setting name="EmitQueryTraces" serializeAs="String">
        <value>true</value>
     </setting>
     ```

     > [!IMPORTANT]
     > Wenn Sie die Einstellung „EmitQueryTraces“ aktivieren, erhöht sich dadurch basierend auf der Gatewaynutzung die Protokollgröße unter Umständen erheblich. Lassen Sie den Wert „EmitQueryTraces“ nicht langfristig aktiviert, sondern setzen Sie ihn nach der Überprüfung der Protokolle unbedingt auf **false** zurück.

   * **Microsoft.PowerBI.DataMovement.Pipeline.Diagnostics.dll.config**

     Damit vom Gateway ausführliche Einträge (einschließlich Einträge zur Dauer) protokolliert werden, ändern Sie den Wert **TracingVerbosity** von **4** in **5**. Führen Sie dazu einen der folgenden Schritte aus: 

     * Ändern Sie in dieser Konfigurationsdatei den Wert **TracingVerbosity** von **4** in **5**. 

       ```html
       <setting name="TracingVerbosity" serializeAs="String">
          <value>5</value>
       </setting>
       ```

     * Öffnen Sie den Gateway-Installer, klicken Sie auf **Diagnose**, und aktivieren Sie **Zusätzliche Protokollierung**. Klicken Sie anschließend auf **Übernehmen**:

       ![Aktivieren der zusätzlichen Protokollierung](./media/logic-apps-gateway-install/turn-on-additional-logging.png)

     > [!IMPORTANT]
     > Wenn Sie die Einstellung „TracingVerbosity“ aktivieren, erhöht sich dadurch basierend auf der Gatewaynutzung die Protokollgröße unter Umständen erheblich. Deaktivieren Sie nach der Überprüfung der Protokolle die Option **Zusätzliche Protokollierung** im Gateway-Installer unbedingt wieder, oder setzen Sie „TracingVerbosity“ in der Konfigurationsdatei auf **4** zurück, und lassen Sie diese Einstellung nicht langfristig aktiviert.

3. Führen Sie die folgenden Schritte aus, um die Dauer einer Abfrage zu ermitteln:

   1. [Exportieren](#logs) und öffnen Sie das Gatewayprotokoll.

   2. Suchen Sie zum Ermitteln einer Abfrage nach einem Aktivitätstyp. Beispiel: 

      | Aktivitätstyp | BESCHREIBUNG | 
      |---------------|-------------| 
      | MGEQ | Über ADO.NET ausgeführte Abfragen | 
      | MGEO | Über OLEDB ausgeführte Abfragen | 
      | MGEM | Über die Mashup-Engine ausgeführte Abfragen | 
      ||| 

   3. Notieren Sie die zweite GUID. Dabei handelt es sich um die Anforderungs-ID.

   4. Suchen Sie weiter nach dem Aktivitätstyp, bis Sie den Eintrag „FireActivityCompletedSuccessfullyEvent“ gefunden haben, für den eine Dauer in Millisekunden angegeben ist. 
   Vergewissern Sie sich, dass der Eintrag die gleiche Anforderungs-ID hat, etwa:

      ```text 
      DM.EnterpriseGateway Verbose: 0 : 2016-09-26T23:08:56.7940067Z DM.EnterpriseGateway    baf40f21-2eb4-4af1-9c59-0950ef11ec4a    5f99f566-106d-c8ac-c864-c0808c41a606    MGEQ    21f96cc4-7496-bfdd-748c-b4915cb4b70c    B8DFCF12 [DM.Pipeline.Common.TracingTelemetryService] Event: FireActivityCompletedSuccessfullyEvent (duration=5004)
      ```

      > [!NOTE] 
      > Der Eintrag „FireActivityCompletedSuccessfullyEvent“ ist ein ausführlicher Eintrag, der nur protokolliert wird, wenn die Einstellung „TracingVerbosity“ auf 5 festgelegt ist.

### <a name="trace-traffic-with-fiddler"></a>Überwachen des Datenverkehrs mit Fiddler

[Fiddler](https://www.telerik.com/fiddler) ist ein kostenloses Tool von Telerik, das HTTP-Datenverkehr überwacht. Sie können diesen Datenverkehr mit dem Power BI-Dienst auf dem Clientcomputer überprüfen. Für den Dienst werden ggf. Fehler und andere zugehörige Informationen angezeigt.

## <a name="next-steps"></a>Nächste Schritte
    
* [Herstellen einer Verbindung mit dem lokalen Datengateway für Logik-Apps](../logic-apps/logic-apps-gateway-connection.md)
* [Unternehmensintegrationsfeatures](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Connectors für Azure Logic Apps](../connectors/apis-list.md)

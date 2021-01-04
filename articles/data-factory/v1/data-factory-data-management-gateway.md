---
title: Datenverwaltungsgateway für Data Factory
description: Verwenden Sie das Datenverwaltungsgateway in Azure Data Factory zum Verschieben Ihrer Daten.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 94c1bed8d94f73bc7794037b307618f4c36c4518
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450606"
---
# <a name="data-management-gateway"></a>Gateway zur Datenverwaltung
> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Selbstgehostete Integration Runtime](../create-self-hosted-integration-runtime.md).

> [!NOTE]
> Das Datenverwaltungsgateway wurde jetzt in selbstgehostete Integration Runtime umbenannt.

Das Datenverwaltungsgateway ist ein Client-Agent, den Sie in Ihrer lokalen Umgebung installieren müssen, um Daten zwischen Ihren Cloudspeichern und lokalen Datenspeichern kopieren zu können. Die lokalen von Data Factory unterstützten Datenspeicher werden im Abschnitt [Unterstützte Datenquellen](data-factory-data-movement-activities.md#supported-data-stores-and-formats) aufgeführt.

Dieser Artikel ergänzt die exemplarische Vorgehensweise im Artikel [Verschieben von Daten zwischen lokalen Datenspeichern und Clouddatenspeichern](data-factory-move-data-between-onprem-and-cloud.md) . In der exemplarischen Vorgehensweise erstellen Sie eine Pipeline, die das Gateway verwendet, um Daten aus einer SQL Server-Datenbank in ein Azure-Blob zu verschieben. Dieser Artikel enthält ausführliche Informationen zur Verwendung des Datenverwaltungsgateways.

Sie können ein Datenverwaltungsgateway aufskalieren, indem Sie ihm mehrere lokale Computer zuordnen. Sie können hochskalieren, indem Sie die Anzahl von Datenverschiebungsaufträgen erhöhen, die auf einem Knoten gleichzeitig ausgeführt werden können. Diese Funktion ist auch für ein logisches Gateway mit einem einzelnen Knoten verfügbar. Ausführliche Informationen hierzu finden Sie im Artikel [Data Management Gateway – high availability and scalability (Preview)](data-factory-data-management-gateway-high-availability-scalability.md) (Datenverwaltungsgateway – Hohe Verfügbarkeit und Skalierbarkeit (Vorschauversion)).

> [!NOTE]
> Das Gateway unterstützt derzeit nur die Kopieraktivität und die Aktivität mit gespeicherten Prozeduren in Data Factory. Das Gateway kann nicht über eine benutzerdefinierte Aktivität verwendet werden, um auf lokale Datenquellen zuzugreifen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Übersicht
### <a name="capabilities-of-data-management-gateway"></a>Funktionen des Datenverwaltungsgateways
Das Datenverwaltungsgateway bietet die folgenden Funktionen:

* Modellieren von lokalen Datenquellen und Clouddatenquellen innerhalb einer Data Factory und Verschieben von Daten.
* Nutzen einer zentralen Konsole für die Überwachung und Verwaltung mit Informationen zum Gatewaystatus über die Data Factory-Seite.
* Sicheres Verwalten des Zugriffs auf lokale Datenquellen.
  * An der Unternehmensfirewall ist keine Änderung erforderlich. Das Gateway stellt nur ausgehende HTTP-basierte Verbindungen mit dem offenen Internet her.
  * Verschlüsseln Sie die Anmeldeinformationen für Ihre lokalen Datenspeicher mit dem Zertifikat.
* Effizientes Verschieben von Daten – Daten werden parallel verschoben, und zwar mit Ausfallsicherheit bei vorübergehenden Netzwerkproblemen dank automatischer Wiederholungslogik.

### <a name="command-flow-and-data-flow"></a>Befehls- und Datenfluss
Wenn Sie Daten mithilfe einer Kopieraktivität zwischen der lokalen Umgebung und der Cloud kopieren, verwendet diese Aktivität ein Gateway, um die Daten aus einer lokalen Datenquelle in die Cloud und umgekehrt zu übertragen.

Im Anschluss finden Sie eine Darstellung des allgemeinen Datenflusses sowie eine Zusammenfassung der Schritte für das Kopieren mit dem Datengateway: ![Datenfluss über ein Gateway](./media/data-factory-data-management-gateway/data-flow-using-gateway.png).

1. Der Datenentwickler erstellt entweder über das [Azure-Portal](https://portal.azure.com) oder mit einem [PowerShell-Cmdlet](/powershell/module/az.datafactory/) ein Gateway für Azure Data Factory.
2. Der Datenentwickler erstellt durch Angabe des Gateways einen verknüpften Dienst für einen lokalen Datenspeicher. Als Teil der Einrichtung des verknüpften Diensts verwendet der Datenentwickler die Anwendung „Anmeldeinformationen festlegen“, um Authentifizierungstypen und Anmeldeinformationen anzugeben. Die Anwendung „Anmeldeinformationen festlegen“ kommuniziert mit dem Datenspeicher, um die Verbindung zu testen, und mit dem Gateway, um die Anmeldeinformationen zu speichern.
3. Das Gateway verschlüsselt die Anmeldeinformationen mit dem Zertifikat, das dem Gateway zugeordnet wurde (bereitgestellt vom Datenentwickler), bevor die Anmeldeinformationen in der Cloud gespeichert werden.
4. Der Data Factory-Dienst kommuniziert für die Planung und Verwaltung von Aufträgen mit dem Gateway. Dazu wird ein Steuerungskanal genutzt, der eine freigegebene Azure Service Bus-Warteschlange verwendet. Wenn ein Auftrag der Kopieraktivität gestartet werden muss, reiht Data Factory die Anforderung zusammen mit Anmeldeinformationen in die Warteschlange ein. Das Gateway startet den Auftrag, nachdem die Warteschlange abgerufen wurde.
5. Das Gateway entschlüsselt die Anmeldeinformationen mit dem gleichen Zertifikat und stellt dann mit dem richtigen Authentifizierungstyp und den Anmeldeinformationen eine Verbindung mit dem lokalen Datenspeicher her.
6. Das Gateway kopiert Daten aus einem lokalen Speicher in einen Cloudspeicher oder umgekehrt, je nachdem, wie die Kopieraktivität in der Datenpipeline konfiguriert ist. Für diesen Schritt kommuniziert das Gateway über einen sicheren Kanal (HTTPS) direkt mit einem cloudbasierten Speicherdienst wie z.B. Azure Blob Storage.

### <a name="considerations-for-using-gateway"></a>Überlegungen zur Verwendung des Gateways
* Ein einzelne Instanz des Datenverwaltungsgateways kann für mehrere lokale Datenquellen verwendet werden. Allerdings ist **eine einzelne Gatewayinstanz nur an eine Azure Data Factory gebunden** und kann nicht mit einer anderen Data Factory gemeinsam genutzt werden.
* Es darf **nur eine Instanz des Datenverwaltungsgateways** auf einem Computer installiert sein. Wenn Sie beispielsweise über zwei Data Factorys verfügen, die auf lokale Datenquellen zugreifen müssen, dann müssen Sie auf zwei lokalen Computern jeweils ein Gateway installieren. Anders gesagt: Jedes Gateway ist an eine spezifische Data Factory gebunden.
* Das **Gateway muss sich nicht auf dem gleichen Computer befinden wie die Datenquelle**. Wenn Sie das Gateway jedoch in der Nähe der Datenquelle platzieren, verkürzen Sie die Zeit, die das Gateway benötigt, um eine Verbindung mit der Datenquelle herzustellen. Es wird empfohlen, das Gateway auf einem anderen Computer als dem Computer zu installieren, auf dem die lokale Datenquelle gehostet wird. Wenn sich Gateway und Datenquelle auf unterschiedlichen Computern befinden, konkurriert das Gateway nicht mit der Datenquelle um Ressourcen.
* Sie können über **mehrere Gateways auf verschiedenen Computern verfügen, die eine Verbindung mit der gleichen lokalen Datenquelle herstellen**. Sie können z. B. über zwei Gateways verfügen, die zwei Data Factorys mit Daten versorgen, wobei jedoch die lokale Datenquelle für die beiden Data Factorys registriert ist.
* Falls Sie auf Ihrem Computer bereits ein Gateway installiert haben, das für ein **Power BI**-Szenario verwendet wird, installieren Sie auf einem anderen Computer ein **separates Gateway für die Azure Data Factory**.
* Sie müssen das Gateway selbst dann verwenden, wenn Sie **ExpressRoute** einsetzen.
* Behandeln Sie Ihre Datenquelle wie eine lokale Datenquelle (die sich hinter einer Firewall befindet), selbst wenn Sie **ExpressRoute** verwenden. Verwenden Sie das Gateway, um eine Verbindung zwischen dem Dienst und der Datenquelle herzustellen.
* Sie müssen auch dann **das Gateway verwenden**, wenn der Datenspeicher sich in der Cloud auf einer **Azure IaaS-VM** befindet.

## <a name="installation"></a>Installation
### <a name="prerequisites"></a>Voraussetzungen
* Die unterstützten **Betriebssystemversionen** sind Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 und Windows Server 2012 R2. Die Installation des Datenverwaltungsgateways auf einem Domänencontroller wird derzeit nicht unterstützt.
* Sie benötigen mindestens .NET Framework 4.5.1. Wenn Sie das Gateway auf einem Windows 7-Computer installieren, installieren Sie mindestens .NET Framework 4.5. Ausführlichere Informationen finden Sie unter [Systemanforderungen für .NET Framework](/dotnet/framework/get-started/system-requirements) .
* Die empfohlene **Konfiguration** für den Gatewaycomputer lautet: mindestens 2 GHz, 4 Kerne, 8 GB RAM und 80 GB Festplattenspeicher.
* Wenn sich der Hostcomputer im Ruhezustand befindet, reagiert das Gateway nicht auf Datenanforderungen. Aus diesem Grund sollten Sie vor der Installation des Gateways einen entsprechenden **Energiesparplan** auf dem Computer konfigurieren. Wenn der Computer für den Ruhezustand konfiguriert ist, wird bei der Gatewayinstallation eine Meldung angezeigt.
* Sie müssen ein Administrator auf dem Computer sein, um das Datenverwaltungsgateway erfolgreich installieren und konfigurieren zu können. Sie können der lokalen Windows-Gruppe **Datenverwaltungsgateway-Benutzer** zusätzliche Benutzer hinzufügen. Die Mitglieder dieser Gruppe können das Gateway mithilfe des Tools **Datenverwaltungsgateway-Konfigurations-Manager** konfigurieren.

Da die Kopieraktivität mit einer bestimmten Häufigkeit ausgeführt wird, folgt die Ressourcenverwendung (CPU, Arbeitsspeicher) auf dem Computer dem gleichen Muster mit Spitzen- und Leerlaufzeiten. Die Ressourcenverwendung hängt auch stark von der Datenmenge ab, die verschoben wird. Wenn mehrere Kopieraufträge in Bearbeitung sind, steigt die Ressourcenverwendung zu Spitzenzeiten an.

### <a name="installation-options"></a>Installationsoptionen
Das Datenverwaltungsgateway kann auf folgende Weise installiert werden:

* Durch Herunterladen eines MSI-Setuppakets aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Die MSI-Datei kann auch verwendet werden, um ein vorhandenes Datenverwaltungsgateway auf die neueste Version zu aktualisieren, wobei alle Einstellungen beibehalten werden.
* Durch Klicken auf den Link **Datengateway herunterladen und installieren** unter MANUELLES SETUP oder **Direkt auf diesem Computer installieren** unter EXPRESS-SETUP. Im Artikel [Verschieben von Daten zwischen lokalen Quellen und der Cloud](data-factory-move-data-between-onprem-and-cloud.md) finden Sie schrittweise Anweisungen zur Verwendung des Express-Setups. Durch den manuellen Schritt gelangen Sie zum Download Center. Wie Sie das Gateway aus dem Download Center herunterladen und installieren, erfahren Sie im nächsten Abschnitt.

### <a name="installation-best-practices"></a>Bewährte Methoden für die Installation:
1. Konfigurieren Sie den Energiesparplan auf dem Hostcomputer für das Gateway, sodass der Computer nicht in den Ruhezustand wechselt. Wenn sich der Hostcomputer im Ruhezustand befindet, reagiert das Gateway nicht auf Datenanforderungen.
2. Sichern Sie das Zertifikat, das dem Gateway zugeordnet ist.

### <a name="install-the-gateway-from-download-center"></a>Installieren des Gateways aus dem Download Center
1. Navigieren Sie zur [Downloadseite des Microsoft-Datenverwaltungsgateways](https://www.microsoft.com/download/details.aspx?id=39717).
2. Klicken Sie auf **Herunterladen**, wählen Sie die **64-Bit**-Version (32-Bit wird nicht unterstützt) aus, und wählen Sie **Weiter** aus.
3. Führen Sie **MSI** direkt aus, oder speichern Sie es zur späteren Ausführung auf Ihrer Festplatte.
4. Wählen Sie auf der **Willkommensseite** eine **Sprache** aus, und klicken Sie auf **Weiter**.
5. **Akzeptieren** Sie den Endbenutzer-Lizenzvertrag, und klicken Sie auf **Weiter**.
6. Wählen Sie den **Ordner** zur Installation des Gateways aus, und klicken Sie auf **Weiter**.
7. Klicken Sie auf der Seite **Bereit zur Installation** auf **Installieren**.
8. Klicken Sie auf **Fertig stellen** , um die Installation abzuschließen.
9. Ermitteln Sie den Schlüssel im Azure-Portal. Im nächsten Abschnitt finden Sie schrittweise Anweisungen.
10. Führen Sie im **Konfigurations-Manager des Datenverwaltungsgateways**, der auf Ihrem Computer ausgeführt wird, auf der Seite **Gateway registrieren** die folgenden Schritte aus:
    1. Fügen Sie den Schlüssel in das Textfeld ein.
    2. Optional klicken Sie auf **Gatewayschlüssel anzeigen** , um den Schlüsseltext anzuzeigen.
    3. Klicken Sie auf **Registrieren**.

### <a name="register-gateway-using-key"></a>Registrieren des Gateways mit dem Schlüssel
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Wenn Sie nicht bereits ein logisches Gateway im Portal erstellt haben
Um im Portal ein Gateway zu erstellen und auf der Seite **Konfigurieren** den Schlüssel zu ermitteln, führen Sie die Schritte der exemplarischen Vorgehensweise aus dem Artikel [Verschieben von Daten zwischen lokalen Quellen und der Cloud](data-factory-move-data-between-onprem-and-cloud.md) aus.

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Wenn Sie bereits ein logisches Gateway im Portal erstellt haben
1. Wechseln Sie im Azure-Portal zur Seite **Data Factory**, und klicken Sie auf die Kachel **Verknüpfte Dienste**.

    ![Seite „Data Factory“](media/data-factory-data-management-gateway/data-factory-blade.png)
2. Wählen Sie auf der Seite **Verknüpfte Dienste** das logische **Gateway** aus, das Sie im Portal erstellt haben.

    ![Logisches Gateway](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
3. Klicken Sie auf der Seite **Datengateway** auf **Datengateway herunterladen und installieren**.

    ![Link zum Herunterladen im Portal](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
4. Klicken Sie auf der Seite **Konfigurieren** auf **Schlüssel neu erstellen**. Klicken Sie in der Warnmeldung auf „Ja“, nachdem Sie sie sorgfältig gelesen haben.

    ![Schaltfläche „Schlüssel neu erstellen“](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Klicken Sie neben dem Schlüssel auf die Schaltfläche „Kopieren“. Der Schlüssel wird in die Zwischenablage kopiert.

    ![Kopieren des Schlüssels](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Symbole und Benachrichtigungen in der Taskleiste
Die folgende Abbildung enthält einige Symbole, die auf der Taskleiste angezeigt werden.

![Taskleistensymbole](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Wenn Sie den Cursor auf der Taskleiste auf das Symbol oder die Benachrichtigung bewegen, werden in einem Popupfenster Details zum Zustand des Gateways bzw. des Aktualisierungsvorgangs angezeigt.

### <a name="ports-and-firewall"></a>Ports und Firewall
Zwei Firewalls müssen berücksichtigt werden: Die **Unternehmensfirewall**, die auf dem zentralen Router des Unternehmens ausgeführt wird, und die **Windows-Firewall**, die als Daemon auf dem lokalen Computer konfiguriert ist, auf dem das Gateway installiert ist.

![Firewalls](./media/data-factory-data-management-gateway/firewalls2.png)

Auf Ebene der Unternehmensfirewall müssen Sie die folgenden Domänen und ausgehenden Ports konfigurieren:

| Domänennamen | Ports | BESCHREIBUNG |
| --- | --- | --- |
| *.servicebus.windows.net |443 |Wird für die Kommunikation mit dem Back-End für den Datenverschiebungsdienst verwendet |
| *.core.windows.net |443 |Wird für das gestaffelte Kopieren mit einem Azure-Blob (sofern konfiguriert) verwendet|
| *.frontend.clouddatahub.net |443 |Wird für die Kommunikation mit dem Back-End für den Datenverschiebungsdienst verwendet |
| *.servicebus.windows.net |9350-9354, 5671 |Optionales Service Bus Relay über TCP, das vom Kopier-Assistenten verwendet wird |

Auf Ebene der Windows-Firewall sind diese ausgehenden Ports normalerweise aktiviert. Falls nicht, können Sie die Domänen und Ports auf dem Gatewaycomputer entsprechend konfigurieren.

> [!NOTE]
> 1. Je nach Ihrer Quelle und Ihren Senken müssen Sie unter Umständen in Ihrer Unternehmens-/Windows-Firewall zusätzliche Domänen und ausgehende Ports zulassen.
> 2. Für einige Clouddatenbanken (z.B. [Azure SQL-Datenbank](../../azure-sql/database/firewall-configure.md), [Azure Data Lake](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access) usw.) müssen Sie in der Firewallkonfiguration ggf. die IP-Adresse des Gatewaycomputers zulassen.
>
>

#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Kopieren von Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher
Stellen Sie sicher, dass die Firewallregeln für die Unternehmensfirewall, die Windows-Firewall auf dem Gatewaycomputer und den Datenspeicher selbst richtig aktiviert sind. Indem Sie diese Regeln aktivieren, kann das Gateway sowohl mit der Quelle als auch mit der Senke erfolgreich eine Verbindung herstellen. Aktivieren Sie die Regeln für jeden Datenspeicher, der am Kopiervorgang beteiligt ist.

Führen Sie beispielsweise die folgenden Schritte aus, um Daten aus **einem lokalen Datenspeicher in eine Azure SQL-Datenbank-Senke oder eine Azure Synapse Analytics-Senke** zu kopieren:

* Lassen Sie ausgehende **TCP**-Kommunikation an Port **1433** sowohl für die Windows-Firewall als auch die Unternehmensfirewall zu.
* Konfigurieren Sie die Firewalleinstellungen der logischen SQL Server-Instanz so, dass die IP-Adresse des Gatewaycomputers in die Liste der zulässigen IP-Adressen aufgenommen wird.

> [!NOTE]
> Wenn Ihre Firewall den ausgehenden Port 1433 nicht zulässt, kann das Gateway nicht direkt auf Azure SQL zugreifen. In diesem Fall können Sie das [gestaffelte Kopieren](./data-factory-copy-activity-performance.md#staged-copy) in SQL-Datenbank/SQL Managed Instance/Azure SQL Data Warehouse verwenden. Sie benötigen in einem solchen Szenario nur HTTPS (Port 443) für das Verschieben der Daten.
>
>

### <a name="proxy-server-considerations"></a>Proxyserver-Aspekte
Wenn die Netzwerkumgebung Ihres Unternehmens einen Proxyserver für den Internetzugriff verwendet, konfigurieren Sie das Datenverwaltungsgateway mit den geeigneten Proxyeinstellungen. Sie können den Proxy während der anfänglichen Registrierungsphase festlegen.

![Einrichten des Proxys während der Registrierung](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Das Gateway verwendet den Proxyserver, um eine Verbindung mit dem Clouddienst herzustellen. Klicken Sie während des Anfangssetups auf **Ändern** . Sie sehen das Dialogfeld **Proxyeinstellung** .

![Einrichten des Proxys mithilfe des Konfigurations-Managers 1](media/data-factory-data-management-gateway/SetProxySettings.png)

Es gibt drei Konfigurationsoptionen:

* **Proxy nicht verwenden**: Das Gateway verwendet nicht explizit einen Proxy, um eine Verbindung mit den Clouddiensten herzustellen.
* **Systemproxy verwenden**: Das Gateway verwendet die in „diahost.exe.config“ und „diawp.exe.config“ konfigurierten Proxyeinstellungen. Wenn in „diahost.exe.config“ und „diawp.exe.config“ kein Proxy konfiguriert ist, stellt das Gateway die Verbindung mit den Clouddiensten nicht über einen Proxy, sondern direkt her.
* **Benutzerdefinierten Proxy verwenden**: Konfigurieren Sie die für das Gateway zu verwendenden HTTP-Proxyeinstellungen hier, anstatt in den Dateien „diahost.exe.config“ und „diawp.exe.config“. Adresse und Port sind erforderlich. Benutzername und Kennwort sind je nach den Authentifizierungseinstellungen Ihres Proxys optional. Alle Einstellungen werden mit dem Zertifikat für Anmeldeinformationen des Gateways verschlüsselt und lokal auf dem Gatewayhostcomputer gespeichert.

Der Datenverwaltungsgateway-Hostdienst wird automatisch neu gestartet, nachdem Sie die aktualisierten Proxyeinstellungen gespeichert haben.

Wenn Sie die Proxyeinstellungen nach der erfolgreichen Registrierung des Gateways anzeigen oder aktualisieren möchten, verwenden Sie den Datenverwaltungsgateway-Konfigurations-Manager.

1. Starten Sie den **Datenverwaltungsgateway-Konfigurations-Manager**.
2. Wechseln Sie zur Registerkarte **Einstellungen**.
3. Klicken Sie im Abschnitt **HTTP-Proxy** auf den Link **Ändern**, um das Dialogfeld **HTTP-Proxy festlegen** zu öffnen.
4. Nachdem Sie auf die Schaltfläche **Weiter** geklickt haben, wird ein Dialogfeld mit einer Warnung angezeigt, in dem Sie bestätigen müssen, dass die Proxyeinstellung gespeichert und der Gatewayhostdienst neu gestartet werden soll.

Sie können den HTTP-Proxy im Konfigurations-Manager anzeigen und aktualisieren.

![Einrichten des Proxys mithilfe des Konfigurations-Managers 2](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Wenn Sie einen Proxyserver mit NTLM-Authentifizierung einrichten, wird der Gatewayhostdienst im Domänenkonto ausgeführt. Wenn Sie das Kennwort für das Domänenkonto später ändern, denken Sie daran, die Konfigurationseinstellungen für den Dienst entsprechend zu aktualisieren und neu zu starten. Aufgrund dieser Anforderung empfiehlt es sich, ein dediziertes Domänenkonto für den Zugriff auf den Proxyserver zu verwenden, in dem das Kennwort nicht regelmäßig geändert werden muss.
>
>

### <a name="configure-proxy-server-settings"></a>Konfigurieren von Proxyservereinstellungen
Wenn Sie die Einstellung **Systemproxy verwenden** für den HTTP-Proxy auswählen, verwendet das Gateway die Proxyeinstellungen in „diahost.exe.config“ und „diawp.exe.config“. Wenn in „diahost.exe.config“ und „diawp.exe.config“ kein Proxy angegeben ist, stellt das Gateway die Verbindung mit den Clouddiensten nicht über einen Proxy, sondern direkt her. Das folgende Verfahren enthält Anweisungen für die Aktualisierung der Datei „diahost.exe.config“.

1. Erstellen Sie im Datei-Explorer eine sichere Kopie von *C:\\\\Programme\\Microsoft-Datenverwaltungsgateway\\2.0\\Shared\\diahost.exe.config*, um die Originaldatei zu sichern.
2. Starten Sie „Notepad.exe“ als Administrator, und öffnen Sie die Textdatei *C:\\\\Programme\\Microsoft-Datenverwaltungsgateway\\2.0\\Shared\\diahost.exe.config*. Sie finden das Standardtag für „system.net“, wie im folgenden Code gezeigt:

    ```
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Anschließend können Sie die Informationen zum Proxyserver wie im folgenden Beispiel gezeigt hinzufügen:

    ```
    <system.net>
        <defaultProxy enabled="true">
            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Zusätzliche Eigenschaften sind im Proxytag zulässig, um die erforderlichen Einstellungen anzugeben, z. B. scriptLocation. Informationen zur Syntax finden Sie unter [-proxy-Element (Netzwerkeinstellungen)](/dotnet/framework/configure-apps/file-schema/network/proxy-element-network-settings).

    ```
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Speichern Sie die Konfigurationsdatei am ursprünglichen Speicherort, und starten Sie den Datenverwaltungsgateway-Dienst, der die Änderungen übernimmt. Um den Dienst neu zu starten, verwenden Sie das Applet „Dienste“ in der Systemsteuerung, oder klicken Sie im **Datenverwaltungsgateway-Konfigurations-Manager** auf die Schaltfläche **Dienst beenden** und anschließend auf **Dienst starten**. Wenn der Dienst nicht gestartet wird, wurde der bearbeiteten Anwendungskonfigurationsdatei wahrscheinlich eine falsche XML-Tagsyntax hinzugefügt.

> [!IMPORTANT]
> Vergessen Sie nicht, **beide** Dateien („diahost.exe.config“ und „diawp.exe.config“) zu konfigurieren.

Zusätzlich zu diesen Punkten müssen Sie auch sicherstellen, dass Microsoft Azure in der Zulassungsliste Ihres Unternehmens aufgeführt ist. Sie können die Liste mit den gültigen Microsoft Azure-IP-Adressen im [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653)herunterladen.

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Mögliche Symptome für Probleme im Zusammenhang mit der Firewall und dem Proxyserver
Wenn Sie ähnliche Fehler wie die unten aufgeführten feststellen, liegt dies meist an einer unsachgemäßen Konfiguration der Firewall oder des Proxyservers, die verhindert, dass das Gateway eine Verbindung mit der Data Factory herstellt, um sich zu authentifizieren. Überprüfen Sie den vorherigen Abschnitt, um sicherzustellen, dass die Firewall und der Proxyserver richtig konfiguriert sind.

1. Beim Versuch, das Gateway zu registrieren, erhalten Sie den folgenden Fehler: „Fehler beim Registrieren des Gatewayschlüssels. Prüfen Sie, ob sich das Datenverwaltungsgateway im Status „Verbunden“ befindet und der Datenverwaltungsgateway-Hostdienst gestartet wurde, bevor Sie versuchen, den Gatewayschlüssel erneut zu registrieren.“
2. Wenn Sie den Konfigurations-Manager öffnen, wird als Status „Getrennt“ oder „Verbindung wird hergestellt“ angezeigt. Wenn Sie Windows-Ereignisprotokolle anzeigen, sehen Sie unter „Ereignisanzeige“ > „Anwendungs- und Dienstprotokolle“ > „Datenverwaltungsgateway“ Fehlermeldungen wie die folgende: `Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Öffnen von Port 8050 für die Verschlüsselung der Anmeldeinformationen
Die Anwendung **Anmeldeinformationen festlegen** verwendet den eingehenden Port **8050**, um die Anmeldeinformationen an das Gateway weiterzugeben, wenn Sie im Azure-Portal einen lokalen verknüpften Dienst einrichten. Während der Einrichtung des Gateways wird es von der Gatewayinstallation standardmäßig auf dem Gatewaycomputer geöffnet.

Bei Verwendung einer Drittanbieterfirewall können Sie den Port 8050 manuell öffnen. Falls beim Einrichten des Gateways ein Firewallproblem auftritt, können Sie versuchen, den folgenden Befehl zu verwenden, um das Gateway ohne Konfiguration der Firewall zu installieren.

```cmd
msiexec /q /i DataManagementGateway.msi NOFIREWALL=1
```

Falls Sie den Port 8050 auf dem Gatewaycomputer nicht öffnen, verwenden Sie andere Verfahren als die Anwendung **Anmeldeinformationen festlegen** , um Anmeldeinformationen für den Datenspeicher zu konfigurieren. Beispielsweise können Sie das PowerShell-Cmdlet [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) verwenden. Informationen zum Festlegen von Datenspeicher-Anmeldeinformationen finden Sie unter „Festlegen von Anmeldeinformationen und Sicherheit“.

## <a name="update"></a>Aktualisieren
Standardmäßig wird das Datenverwaltungsgateway automatisch aktualisiert, wenn eine neuere Version des Gateways verfügbar ist. Das Gateway wird erst aktualisiert, nachdem alle geplanten Aufgaben erledigt wurden. Vom Gateway werden keine weiteren Aufgaben verarbeitet, bevor der Aktualisierungsvorgang abgeschlossen ist. Wenn die Aktualisierung fehlschlägt, wird für das Gateway ein Rollback auf die alte Version durchgeführt.

Sie sehen die geplante Aktualisierungszeit an folgenden Stellen:

* Auf der Seite mit den Gatewayeigenschaften im Azure-Portal.
* Auf der Startseite des Datenverwaltungsgateway-Konfigurations-Managers.
* In einer Benachrichtigung in der Taskleiste.

Auf der Registerkarte „Startseite“ des Datenverwaltungsgateway-Konfigurations-Managers werden der Aktualisierungszeitplan und der Zeitpunkt der letzten Installation bzw. Aktualisierung des Gateways angezeigt.

![Planen von Updates](media/data-factory-data-management-gateway/UpdateSection.png)

Sie können das Update sofort installieren oder warten, bis das Gateway zum geplanten Zeitpunkt automatisch aktualisiert wird. Die folgende Abbildung zeigt beispielsweise die Benachrichtigung, die im Datenverwaltungsgateway-Konfigurations-Manager zusammen mit der Schaltfläche „Aktualisieren“ angezeigt wird, auf die Sie zum sofortigen Installieren klicken können.

![Update im Datenverwaltungsgateway-Konfigurations-Manager](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

Die Benachrichtigung in der Taskleiste sieht wie in der folgenden Abbildung aus:

![Meldung in der Taskleiste](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Der Status des Aktualisierungsvorgangs (manuell oder automatisch) wird auf der Taskleiste angezeigt. Wenn Sie den Datenverwaltungsgateway-Konfigurations-Manager das nächste Mal öffnen, wird auf der Benachrichtigungsleiste eine Meldung mit dem Hinweis eingeblendet, dass das Gateway aktualisiert wurde. Außerdem ist ein Link zu [Neuigkeiten](data-factory-gateway-release-notes.md) angegeben.

### <a name="to-disableenable-auto-update-feature"></a>So deaktivieren/aktivieren Sie das Feature für die automatische Aktualisierung
Sie können das Feature für die automatische Aktualisierung wie folgt deaktivieren und aktivieren:

[Für Gateways mit einem Knoten]
1. Starten Sie Windows PowerShell auf dem Gatewaycomputer.
2. Wechseln Sie zum Ordner *C:\\\\Programme\\Microsoft Integration Runtime\\3.0\\PowerShellScript\\* .
3. Führen Sie den folgenden Befehl aus, um das Feature für die automatische Aktualisierung zu deaktivieren.

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off
    ```
4. So aktivieren Sie das Feature wieder

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on
    ```
   [Für hochverfügbare und skalierbare Gateways mit mehreren Knoten](data-factory-data-management-gateway-high-availability-scalability.md)
1. Starten Sie Windows PowerShell auf dem Gatewaycomputer.
2. Wechseln Sie zum Ordner *C:\\\\Programme\\Microsoft Integration Runtime\\3.0\\PowerShellScript\\* .
3. Führen Sie den folgenden Befehl aus, um das Feature für die automatische Aktualisierung zu deaktivieren.

    Für ein Gateway mit Hochverfügbarkeit ist ein zusätzlicher AuthKey-Parameter erforderlich.
    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off -AuthKey <your auth key>
    ```
4. So aktivieren Sie das Feature wieder

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on -AuthKey <your auth key>
    ```

## <a name="configuration-manager"></a>Konfigurations-Manager
Nach der Installation des Gateways können Sie den Datenverwaltungsgateway-Konfigurations-Manager auf eine der folgenden Arten starten:

1. Geben Sie im Fenster **Suchen** den Begriff **Datenverwaltungsgateway** ein, um auf dieses Hilfsprogramm zuzugreifen.
2. Führen Sie die ausführbare Datei *ConfigManager.exe* im folgenden Ordner aus: *C:\\\\Programme\\Microsoft-Datenverwaltungsgateway\\2.0\\Shared*.

### <a name="home-page"></a>Startseite
Auf der Startseite können Sie die folgenden Aktionen ausführen:

* Anzeigen des Gatewaystatus (mit Clouddienst verbunden oder Ähnliches)
* **Registrieren** mithilfe eines Schlüssels aus dem Portal
* **Beenden** und Starten des **Datenverwaltungsgateway-Hostdiensts** auf dem Gatewaycomputer
* **Planen von Updates** zu einem bestimmten Zeitpunkt an den Tagen
* Anzeigen des Datums, zu dem das Gateway **zuletzt aktualisiert** wurde

### <a name="settings-page"></a>Seite "Einstellungen"
Auf der Seite „Einstellungen“ können Sie die folgenden Aktionen ausführen:

* Anzeigen, Ändern und Exportieren des **Zertifikats**, das vom Gateway verwendet wird Dieses Zertifikat dient zum Verschlüsseln von Anmeldeinformationen für Datenquellen.
* Ändern des **HTTPS-Ports** für den Endpunkt Das Gateway öffnet einen Port für die Festlegung der Datenquellen-Anmeldeinformationen.
* **Status** des Endpunkts
* Anzeigen des **SSL-Zertifikats** wird für die TLS/SSL-Kommunikation zwischen Portal und Gateway zum Festlegen von Anmeldeinformationen für Datenquellen verwendet.

### <a name="remote-access-from-intranet"></a>Remotezugriff über das Intranet
Diese Funktion wird erst später aktiviert. Im Rahmen der nächsten Updates (v3.4 oder höher) wird eine Funktion zum Aktivieren/Deaktivieren von Remoteverbindungen bereitgestellt, die derzeit über Port 8050 (siehe Abschnitt weiter oben) hergestellt werden. Zum Verschlüsseln der Anmeldeinformationen können Sie PowerShell oder die Anwendung für die Anmeldeinformationsverwaltung verwenden.

### <a name="diagnostics-page"></a>Seite „Diagnose“
Auf der Seite „Diagnose“ können Sie die folgenden Aktionen ausführen:

* Aktivieren der ausführlichen **Protokollierung**, Anzeigen von Protokollen in der Ereignisanzeige und Senden von Protokollen an Microsoft, falls ein Fehler aufgetreten ist
* **Testen der Verbindung** mit einer Datenquelle

### <a name="help-page"></a>Hilfeseite
Auf der Seite „Hilfe“ werden die folgenden Informationen angezeigt:

* Kurzbeschreibung des Gateways.
* Versionsnummer
* Links zu Onlinehilfe, Datenschutzbestimmungen und Lizenzvertrag

## <a name="monitor-gateway-in-the-portal"></a>Überwachen des Gateways im Portal
Im Azure-Portal können Sie auf einem Gatewaycomputer nahezu in Echtzeit Momentaufnahmen der Ressourcenverwendung (CPU, Arbeitsspeicher, Netzwerk (Eingang/Ausgang) usw.) anzeigen.

1. Navigieren Sie im Azure-Portal zur Data Factory-Startseite, und klicken Sie auf die Kachel **Verknüpfte Dienste**.

    ![Data Factory-Startseite](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png)
2. Wählen Sie auf der Seite **Verknüpfte Dienste** das **Gateway** aus.

    ![Seite „Verknüpfte Dienste“](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. Auf der Seite **Gateway** wird die Arbeitsspeicher- und CPU-Nutzung des Gateways angezeigt.

    ![CPU- und Arbeitsspeicherauslastung des Gateways](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png)
4. Aktivieren Sie die Option **Erweiterte Einstellungen**, um weitere Details anzuzeigen, z.B. die Netzwerkauslastung.
    
    ![Erweiterte Überwachung des Gateways](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

Die folgende Tabelle enthält Beschreibungen von Spalten in der Liste **Gatewayknoten**:

Überwachungseigenschaft | BESCHREIBUNG
:------------------ | :----------
Name | Name des logischen Gateways und der Knoten, die dem Gateway zugeordnet sind. Der Knoten ist ein lokaler Windows-Computer, auf dem das Gateway installiert ist. Informationen zur Verwendung von mehr als einem Knoten (bis zu vier Knoten) auf einem einzelnen logischen Gateway finden Sie unter [Datenverwaltungsgateway – Hochverfügbarkeit und Skalierbarkeit](data-factory-data-management-gateway-high-availability-scalability.md).
Status | Status des logischen Gateways und der Gatewayknoten. Beispiel: Online/Offline/Eingeschränkt usw. Informationen zu diesen Status finden Sie im Abschnitt [Gatewaystatus](#gateway-status).
Version | Zeigt die Version des logischen Gateways und jedes Gatewayknotens an. Die Version des logischen Gateways wird basierend auf der Version bestimmt, die die meisten Knoten der Gruppe aufweisen. Wenn Knoten mit unterschiedlichen Versionen am Setup des logischen Gateways beteiligt sind, funktionieren nur die Knoten mit der gleichen Versionsnummer wie beim logischen Gateway richtig. Andere Knoten befinden sich im eingeschränkten Modus und müssen manuell aktualisiert werden (nur für den Fall, dass die automatische Aktualisierung fehlschlägt).
Verfügbarer Arbeitsspeicher | Verfügbarer Arbeitsspeicher auf einem Gatewayknoten. Dieser Wert steht für eine Momentaufnahme nahezu in Echtzeit.
CPU-Auslastung | CPU-Auslastung eines Gatewayknotens. Dieser Wert steht für eine Momentaufnahme nahezu in Echtzeit.
Netzwerk (Eingang/Ausgang) | Netzwerkauslastung eines Gatewayknotens. Dieser Wert steht für eine Momentaufnahme nahezu in Echtzeit.
Gleichzeitige Aufträge (ausgeführt/Limit) | Anzahl von Aufträgen oder Aufgaben, die auf den einzelnen Knoten ausgeführt werden. Dieser Wert steht für eine Momentaufnahme nahezu in Echtzeit. Mit „Limit“ wird angegeben, wie viele Aufträge für einen Knoten jeweils gleichzeitig ausgeführt werden können. Dieser Wert wird basierend auf der Größe des Computers definiert. Sie können das Limit erhöhen, um die Ausführung von gleichzeitigen Aufträgen in erweiterten Szenarien hochzuskalieren, in denen CPU, Arbeitsspeicher und Netzwerk nicht voll ausgelastet sind, aber Zeitüberschreitungen für Aktivitäten auftreten. Diese Funktion ist auch für ein Gateway mit nur einem Knoten verfügbar (auch wenn die Skalierbarkeits- und Verfügbarkeitsfunktion nicht aktiviert ist).
Role | Bei einem Gateway mit mehreren Knoten gibt es zwei Arten von Rollen: Dispatcher und Worker. Alle Knoten sind Worker. Dies bedeutet, dass alle Knoten zum Ausführen von Aufträgen verwendet werden können. Es ist nur ein Verteilerknoten vorhanden, der zum Durchführen der Pullvorgänge für Aufgaben bzw. Aufträge von Clouddiensten und Verteilen an die einzelnen Workerknoten (einschließlich sich selbst) genutzt wird.

Auf dieser Seite werden einige Einstellungen angezeigt, die mehr Sinn ergeben, wenn das Gateway mindestens zwei Knoten enthält (Szenario mit Aufskalieren). Ausführliche Informationen zum Einrichten eines Gateways mit mehreren Knoten finden Sie unter [Data Management Gateway – high availability and scalability (Preview)](data-factory-data-management-gateway-high-availability-scalability.md) (Datenverwaltungsgateway – Hochverfügbarkeit und Skalierbarkeit (Vorschauversion)).

### <a name="gateway-status"></a>Gatewaystatus
Die folgende Tabelle enthält die möglichen Status eines **Gatewayknotens**:

Status  | Kommentare/Szenarien
:------- | :------------------
Online | Knoten, der mit dem Data Factory-Dienst verbunden ist.
Offline | Knoten ist offline.
Wird aktualisiert | Der Knoten wird automatisch aktualisiert.
Eingeschränkt | Es besteht ein Konnektivitätsproblem. Dies kann ein Problem mit HTTP-Port 8050, mit der Service Bus-Konnektivität oder mit der Synchronisierung von Anmeldeinformationen sein.
Inaktiv | Die Konfiguration des Knotens unterscheidet sich von der Konfiguration anderer Mehrheitsknoten.<br/><br/> Ein Knoten kann inaktiv sein, wenn er keine Verbindungen mit anderen Knoten herstellen kann.

Die folgende Tabelle enthält die möglichen Status eines **logischen Gateways**. Der Gatewaystatus hängt von den Status der Gatewayknoten ab.

Status | Kommentare
:----- | :-------
Registrierung erforderlich | Für dieses logische Gateway ist noch kein Knoten registriert.
Online | Gatewayknoten sind online.
Offline | Kein Knoten befindet sich im Onlinestatus.
Eingeschränkt | Nicht alle Knoten dieses Gateways befinden sich in einem fehlerfreien Zustand. Dieser Status ist eine Warnung, dass ein Knoten unter Umständen ausgefallen ist. <br/><br/>Die Ursache kann ein Problem mit der Synchronisierung von Anmeldeinformationen auf einem Verteiler- oder Workerknoten sein.

## <a name="scale-up-gateway"></a>Hochskalieren des Gateways
Sie können die Anzahl von **gleichzeitigen Datenverschiebungsaufträgen** konfigurieren, die auf einem Knoten ausgeführt werden können, um die Voraussetzungen zum Verschieben von Daten zwischen lokalen und Clouddatenspeichern zu verbessern.

Wenn die Auslastung des verfügbaren Arbeitsspeichers und der CPU nicht hoch ist, aber die Kapazität im Leerlauf den Wert „0“ hat, sollten Sie hochskalieren. Erhöhen Sie hierfür die Anzahl von gleichzeitigen Aufträgen, die auf einem Knoten ausgeführt werden können. Es kann auch hilfreich sein, das Hochskalieren durchzuführen, wenn für Aktivitäten eine Zeitüberschreitung auftritt, weil das Gateway überlastet ist. In den erweiterten Einstellungen eines Gatewayknotens können Sie die maximale Kapazität für einen Knoten erhöhen.

## <a name="troubleshooting-gateway-issues"></a>Problembehandlung bei Gateways
Im Artikel [Behandeln von Problemen bei der Verwendung des Datenverwaltungsgateways](data-factory-troubleshoot-gateway-issues.md) finden Sie Informationen/Tipps zur Behandlung von Problemen mit dem Datenverwaltungsgateway.

## <a name="move-gateway-from-one-machine-to-another"></a>Verschieben eines Gateways von einem Computer auf einen anderen
Dieser Abschnitt enthält Anweisungen zum Verschieben des Gatewayclients von einem Computer auf einen anderen.

1. Wechseln Sie im Portal zur **Data Factory-Startseite**, und klicken Sie auf die Kachel **Verknüpfte Dienste**.

    ![Link „Datengateways“](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Wählen Sie auf der Seite **Verknüpfte Dienste** im Abschnitt **DATENGATEWAYS** Ihr Gateway aus.

    ![Seite „Verknüpfte Dienste“ mit ausgewähltem Gateway](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. Klicken Sie auf der Seite **Datengateway** auf **Datengateway herunterladen und installieren**.

    ![Link „Gateway herunterladen“](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. Klicken Sie auf der Seite **Konfigurieren** auf **Datengateway herunterladen und installieren**, und befolgen Sie die Anweisungen, um das Datengateway auf dem Computer zu installieren.

    ![Konfigurieren, Seite](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Lassen Sie den **Microsoft-Datenverwaltungsgateway-Konfigurations-Manager** geöffnet.

    ![Konfigurations-Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)
6. Klicken Sie im Portal auf der Seite **Konfigurieren** in der Befehlsleiste auf **Schlüssel neu erstellen** und anschließend in der Warnmeldung auf **Ja**. Klicken Sie auf die Schaltfläche **Kopieren** neben dem Schlüsseltext, um den Schlüssel in die Zwischenablage zu kopieren. Sobald Sie den Schlüssel neu erstellen, funktioniert das Gateway auf dem alten Computer nicht mehr.

    ![Schlüssel neu erstellen 2](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Fügen Sie auf Ihrem Computer im **Datenverwaltungsgateway-Konfigurations-Manager** auf der Seite **Gateway registrieren** den **Schlüssel** in das Textfeld ein. (Optional) Aktivieren Sie das Kontrollkästchen **Gatewayschlüssel anzeigen**, um den Schlüsseltext anzuzeigen.

    ![Schlüssel kopieren und registrieren](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Klicken Sie auf **Registrieren** , um das Gateway beim Clouddienst zu registrieren.
9. Klicken Sie auf der Registerkarte **Einstellungen** auf **Ändern**, um das gleiche Zertifikat auszuwählen, das mit dem alten Gateway verwendet wurde. Geben Sie dann das **Kennwort** ein, und klicken Sie auf **Fertig stellen**.

   ![Zertifikat angeben](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   Ein Zertifikat des alten Gateways können Sie wie folgt exportieren: Starten Sie auf dem alten Computer den Konfigurations-Manager des Datenverwaltungsgateways, wechseln Sie zur Registerkarte **Zertifikat**, klicken Sie auf **Exportieren**, und befolgen Sie die Anweisungen.
10. Nach erfolgreicher Registrierung des Gateways sollten auf der Startseite des Gateway-Konfigurations-Managers **Registrierung** auf **Registriert** und die Option **Status** auf **Gestartet** festgelegt sein.

## <a name="encrypting-credentials"></a>Verschlüsseln der Anmeldeinformationen
Zum Verschlüsseln der Anmeldeinformationen im Data Factory-Editor gehen Sie wie folgt vor:

1. Starten Sie auf dem **Gatewaycomputer** einen Webbrowser, und navigieren Sie zum [Azure-Portal](https://portal.azure.com). Suchen Sie ggf. nach Ihrer Data Factory, und öffnen Sie die Data Factory auf der Seite **DATA FACTORY**. Klicken Sie anschließend auf **Erstellen und bereitstellen**, um den Data Factory-Editor zu starten.
2. Klicken Sie in der Strukturansicht auf einen vorhandenen **verknüpften Dienst**, um dessen JSON-Definition anzuzeigen, oder erstellen Sie einen verknüpften Dienst, für den ein Datenverwaltungsgateway erforderlich ist (z.B. SQL Server oder Oracle).
3. Geben Sie im JSON-Editor für die **gatewayName** -Eigenschaft den Namen des Gateways ein.
4. Geben Sie den Servernamen für die **Data Source**-Eigenschaft in **connectionString** ein.
5. Geben Sie den Datenbanknamen für die **Initial Catalog**-Eigenschaft in **connectionString** ein.
6. Klicken Sie auf der Befehlsleiste auf **Verschlüsseln**, um die ClickOnce-Anwendung **Anmeldeinformationsverwaltung** zu starten. Das Dialogfeld **Anmeldeinformationen festlegen** wird angezeigt.

    ![Dialogfeld „Anmeldeinformationen festlegen“](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. Gehen Sie im Dialogfeld **Anmeldeinformationen festlegen** folgendermaßen vor:
   1. Wählen Sie die **Authentifizierung** aus, die der Data Factory-Dienst für die Verbindung mit der Datenbank verwenden soll.
   2. Geben Sie für die Einstellung **BENUTZERNAME** den Namen des Benutzers ein, der auf die Datenbank zugreifen kann.
   3. Geben Sie für die Einstellung **KENNWORT** das Kennwort für den Benutzer ein.
   4. Klicken Sie auf **OK** , um Anmeldeinformationen zu verschlüsseln, und schließen Sie das Dialogfeld.
8. Sie sollten nun in **connectionString** eine **encryptedCredential**-Eigenschaft sehen.

    ```JSON
    {
        "name": "SqlServerLinkedService",
        "properties": {
            "type": "OnPremisesSqlServer",
            "description": "",
            "typeProperties": {
                "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
                "gatewayName": "adftutorialgateway"
            }
        }
    }
    ```
   Wenn Sie auf das Portal auf einem Computer zugreifen, der sich vom Computer mit dem Gateway unterscheidet, müssen Sie sicherstellen, dass die Anwendung "Anmeldeinformations-Manager" eine Verbindung mit dem Gatewaycomputer herstellen kann. Falls die Anwendung den Computer mit dem Gateway nicht erreichen kann, können Sie keine Anmeldeinformationen für die Datenquelle festlegen und die Verbindung mit der Datenquelle nicht testen.

Wenn Sie die Anwendung **Anmeldeinformationen festlegen** verwenden, verschlüsselt das Portal die Anmeldeinformationen mit dem Zertifikat, das Sie auf der Registerkarte **Zertifikat** des **Datenverwaltungsgateway-Konfigurations-Managers** auf dem Gatewaycomputer angegeben haben.

Wenn Sie einen API-basierten Ansatz zum Verschlüsseln der Anmeldeinformationen nutzen möchten, können Sie das PowerShell-Cmdlet [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) verwenden, um Anmeldeinformationen zu verschlüsseln. Das Cmdlet verwendet das Zertifikat, für dessen Verwendung dieses Gateway konfiguriert ist, um die Anmeldeinformationen zu verschlüsseln. Sie können dem Element **EncryptedCredential** von **connectionString** in JSON verschlüsselte Anmeldeinformationen hinzufügen. Verwenden Sie JSON mit dem [New-AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice)-Cmdlet oder im Data Factory-Editor.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Es gibt eine weitere Möglichkeit zum Festlegen der Anmeldeinformationen mit dem Data Factory-Editor. Wenn Sie einen verknüpften SQL Server-Dienst mit dem Editor erstellen und Anmeldeinformationen im Nur-Text-Format eingeben, werden die Anmeldeinformationen mit einem Zertifikat verschlüsselt, das der Data Factory-Dienst besitzt. Es wird NICHT das Zertifikat verwendet, für dessen Verwendung dieses Gateway konfiguriert ist. Dieser Ansatz ist möglicherweise etwas schneller, in einigen Fällen aber weniger sicher. Daher empfehlen wir, dass Sie diesen Ansatz nur für die Entwicklung/Tests verwenden.

## <a name="powershell-cmdlets"></a>PowerShell-Cmdlets
Dieser Abschnitt beschreibt das Erstellen und Registrieren eines Gateways mit Azure PowerShell-Cmdlets.

1. Starten Sie **Azure PowerShell** im Administratormodus.
2. Melden Sie sich bei Ihrem Azure-Konto an, indem Sie den folgenden Befehl ausführen und Ihre Azure-Anmeldeinformationen eingeben.

    ```powershell
    Connect-AzAccount
    ```
3. Verwenden Sie das **New-AzDataFactoryGateway**-Cmdlet, um wie folgt ein logisches Gateway zu erstellen:

    ```powershell
    $MyDMG = New-AzDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Beispiel für eine Befehlszeile und Ausgabe**:

    ```
    PS C:\> $MyDMG = New-AzDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description "gateway for walkthrough"

    Name              : MyGateway
    Description       : gateway for walkthrough
    Version           :
    Status            : NeedRegistration
    VersionStatus     : None
    CreateTime        : 9/28/2014 10:58:22
    RegisterTime      :
    LastConnectTime   :
    ExpiryTime        :
    ProvisioningState : Succeeded
    Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI
    ```

1. Wechseln Sie in Azure PowerShell zum Ordner: *C:\\\\Programme\\Microsoft Integration Runtime\\3.0\\PowerShellScript\\* . Führen Sie *RegisterGateway.ps1* mit der lokalen Variable **$Key** aus, wie im folgenden Befehl gezeigt. Dieses Skript registriert den auf dem Computer installierten Client-Agent bei dem logischen Gateway, das Sie zuvor erstellt haben.

    ```powershell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Sie können das Gateway auf einem Remotecomputer registrieren, indem Sie den Parameter „IsRegisterOnRemoteMachine“ verwenden. Beispiel:

    ```powershell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Mit dem **Get-AzDataFactoryGateway**-Cmdlet können Sie die Liste mit Gateways in Ihrer Data Factory abrufen. Wenn der **Status** **online** angezeigt wird, bedeutet dies, dass Ihr Gateway einsatzbereit ist.

    ```powershell        
    Get-AzDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
   Sie können ein Gateway mit dem **Remove-AzDataFactoryGateway**-Cmdlet entfernen und die Beschreibung für das Gateway mithilfe der **Set-AzDataFactoryGateway**-Cmdlets aktualisieren. Syntax und andere Details zu diesen Cmdlets finden Sie in der Data Factory-Cmdlet-Referenz.  

### <a name="list-gateways-using-powershell"></a>Auflisten von Gateways mit PowerShell

```powershell
Get-AzDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Entfernen von Gateways mit PowerShell

```powershell
Remove-AzDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```

## <a name="next-steps"></a>Nächste Schritte
* Informationen finden Sie unter [Verschieben von Daten zwischen lokalen Quellen und der Cloud mit dem Datenverwaltungsgateway](data-factory-move-data-between-onprem-and-cloud.md) . In der exemplarischen Vorgehensweise erstellen Sie eine Pipeline, die das Gateway verwendet, um Daten aus einer SQL Server-Datenbank in ein Azure-Blob zu verschieben.
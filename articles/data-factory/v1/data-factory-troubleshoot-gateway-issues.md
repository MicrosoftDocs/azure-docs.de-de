---
title: Beheben von Problemen mit dem Datenverwaltungsgateway
description: Tipps zur Problembehandlung im Zusammenhang mit dem Datenverwaltungsgateway.
services: data-factory
author: nabhishek
manager: anandsub
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 7b12ff431b2d164baf4f70fa5341f538b16bca51
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896564"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Behandeln von Problemen bei der Verwendung des Datenverwaltungsgateways
Dieser Artikel enthält Informationen zur Problembehandlung bei der Verwendung des Datenverwaltungsgateways.

> [!NOTE]
> Dieser Artikel bezieht sich auf Version 1 von Azure Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Selbstgehostete Integration Runtime in Data Factory](../create-self-hosted-integration-runtime.md).

Ausführliche Informationen zum Gateway finden Sie im Artikel [Datenverwaltungsgateway](data-factory-data-management-gateway.md). Im Artikel [Verschieben von Daten zwischen lokalen Quellen und der Cloud mit dem Datenverwaltungsgateway](data-factory-move-data-between-onprem-and-cloud.md) finden Sie eine exemplarische Vorgehensweise zum Verschieben von Daten aus einer SQL Server-Datenbank in Microsoft Azure Blob Storage mithilfe des Gateways.

## <a name="failed-to-install-or-register-gateway"></a>Fehler beim Installieren oder Registrieren des Gateways
### <a name="1-problem"></a>1. Problem
Diese Fehlermeldung wird beim Installieren und Registrieren eines Gateways angezeigt, insbesondere beim Herunterladen der Installationsdatei für das Gateway.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Ursache
Auf dem Computer, auf dem das Gateway installiert werden soll, konnte die aktuelle Gateway-Installationsdatei aufgrund eines Netzwerkfehlers nicht aus dem Download Center heruntergeladen werden.

#### <a name="resolution"></a>Lösung
Überprüfen Sie die Firewall-/Proxyservereinstellungen, um festzustellen, ob die Einstellungen die Netzwerkverbindung zwischen dem Computer und dem [Download Center](https://download.microsoft.com/) blockieren, und aktualisieren Sie sie ggf. entsprechend.

Alternativ können Sie die Installationsdatei für das neueste Gateway aus dem [Download Center](https://www.microsoft.com/download/details.aspx?id=39717) auf andere Computer herunterladen, die auf das Download Center zugreifen können. Anschließend können Sie die Installationsdatei auf den Hostcomputer des Gateways kopieren und manuell ausführen, um das Gateway zu installieren und zu aktualisieren.

### <a name="2-problem"></a>2. Problem
Dieser Fehler wird angezeigt, wenn Sie versuchen, ein Gateway zu installieren, indem Sie im Azure-Portal auf **Direkt auf diesem Computer installieren** klicken.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Ursache
Auf dem Computer wurde bereits ein Gateway installiert.

#### <a name="resolution"></a>Lösung
Deinstallieren Sie das vorhandene Gateway auf dem Computer, und klicken Sie erneut auf den Link **Direkt auf diesem Computer installieren**.

### <a name="3-problem"></a>3. Problem
Dieser Fehler kann angezeigt werden, wenn Sie ein neues Gateway registrieren.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Ursache
Diese Meldung wird ggf. aus einem der folgenden Gründe angezeigt:

* Das Format des Gatewayschlüssels ist ungültig.
* Der Gatewayschlüssel ist ungültig geworden.
* Der Gatewayschlüssel wurde vom Portal erneut generiert.  

#### <a name="resolution"></a>Lösung
Überprüfen Sie, ob Sie den richtigen Gatewayschlüssel aus dem Portal verwenden. Generieren Sie ggf. den Schlüssel erneut, und registrieren Sie das Gateway mit dem Schlüssel.

### <a name="4-problem"></a>4. Problem
Möglicherweise wird beim Registrieren eines Gateways die folgende Fehlermeldung angezeigt.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Inhalt oder Format des Schlüssels ist ungültig](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Ursache
Der Inhalt oder das Format des eingegebenen Gatewayschlüssels ist falsch. Mögliche Gründe sind, dass Sie nur einen Teil des Texts aus dem Portal kopiert haben oder einen ungültigen Schlüssel verwenden.

#### <a name="resolution"></a>Lösung
Generieren Sie einen Gatewayschlüssel im Portal, und klicken Sie auf die Schaltfläche „Kopieren“, um den gesamten Schlüssel zu kopieren. Fügen Sie ihn dann in dieses Fenster ein, um das Gateway zu registrieren.

### <a name="5-problem"></a>5. Problem
Möglicherweise wird beim Registrieren eines Gateways die folgende Fehlermeldung angezeigt.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Screenshot mit hervorgehobener Fehlermeldung, derzufolge der Gatewayschlüssel ungültig oder leer ist.](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Ursache
Der Gatewayschlüssel wurde neu generiert, oder das Gateway wurde im Azure-Portal gelöscht. Es ist auch möglich, dass das Datenverwaltungsgateway nicht das neueste Setup aufweist.

#### <a name="resolution"></a>Lösung
Überprüfen Sie, ob das Datenverwaltungsgateway das neueste Setup aufweist. Sie finden die neueste Version im [Microsoft Download Center](https://go.microsoft.com/fwlink/p/?LinkId=271260).

Wenn das Setup aktuell und das Gateway im Portal noch vorhanden ist, generieren Sie den Gatewayschlüssel erneut im Azure-Portal, und verwenden Sie die Schaltfläche „Kopieren“, um den gesamten Schlüssel zu kopieren. Fügen Sie den Schlüssel dann in diesem Fenster ein, um das Gateway zu registrieren. Andernfalls erstellen Sie das Gateway neu, und beginnen Sie erneut.

### <a name="6-problem"></a>6. Problem
Möglicherweise wird beim Registrieren eines Gateways die folgende Fehlermeldung angezeigt.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![Gatewayschlüssel ist ungültig oder leer](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Ursache
Dieser Fehler kann auftreten, wenn das Gateway gelöscht wurde oder der zugehörige Gatewayschlüssel neu generiert wurde.

#### <a name="resolution"></a>Lösung
Wenn das Gateway gelöscht wurde, erstellen Sie das Gateway über das Portal erneut, klicken Sie auf **Registrieren** , kopieren Sie den Schlüssel aus dem Portal, fügen Sie ihn ein, und wiederholen Sie das Registrieren des Gateways.

Wenn das Gateway noch vorhanden ist, aber der Schlüssel neu generiert wurde, verwenden Sie den neuen Schlüssel, um das Gateway zu registrieren. Wenn Sie nicht über den Schlüssel verfügen, generieren Sie den Schlüssel erneut über das Portal.

### <a name="7-problem"></a>7. Problem
Wenn Sie ein Gateway registrieren, müssen Sie ggf. den Pfad und das Kennwort für ein Zertifikat angeben.

![Screenshot, der zeigt, wo der Pfad und das Kennwort für das Zertifikat eingeben werden](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Ursache
Das Gateway wurde zuvor auf anderen Computern registriert. Bei der ersten Registrierung eines Gateways wurde ihm ein Verschlüsselungszertifikat zugeordnet. Das Zertifikat kann durch das Gateway selbst generiert oder vom Benutzer bereitgestellt werden.  Mit diesem Zertifikat werden die Anmeldeinformationen des Datenspeichers (verknüpfter Dienst) verschlüsselt.  

![Exportieren des Zertifikats](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Beim Wiederherstellen des Gateways auf einem anderen Hostcomputer fragt der Registrierungsassistent nach diesem Zertifikat zum Entschlüsseln von Anmeldeinformationen, die zuvor mit diesem Zertifikat verschlüsselt wurden.  Ohne dieses Zertifikat können die Anmeldeinformationen nicht vom neuen Gateway entschlüsselt werden, und das Ausführen von nachfolgenden Kopiervorgängen im Zusammenhang mit diesem neuen Gateway führt zu Fehlern.  

#### <a name="resolution"></a>Lösung
Wenn Sie das Zertifikat für die Anmeldeinformationen vom ursprünglichen Gatewaycomputer über die Schaltfläche „Exportieren“ der Registerkarte **Einstellungen** des **Datenverwaltungsgateway-Konfigurations-Managers** exportiert haben, verwenden Sie das Zertifikat hier.

Sie können diesen Schritt beim Wiederherstellen eines Gateways nicht überspringen. Wenn das Zertifikat nicht vorhanden ist, müssen Sie das Gateway aus dem Portal löschen und ein neues Gateway erstellen.  Darüber hinaus müssen Sie alle verknüpften Dienste im Zusammenhang mit dem Gateway aktualisieren, indem Sie ihre Anmeldeinformationen erneut eingeben.

### <a name="8-problem"></a>8. Problem
Es wird möglicherweise die folgende Fehlermeldung angezeigt.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Ursache
Dieser Fehler tritt auf, wenn das Gateway sich in einer Umgebung befindet, in der ein HTTP-Proxy für den Zugriff auf Internetressourcen benötigt wird, oder wenn das Authentifizierungskennwort des Proxys geändert, aber nicht entsprechend im Gateway aktualisiert wurde.

#### <a name="resolution"></a>Lösung
Führen Sie die Anweisungen im Abschnitt „Aspekte zu Proxyservern“ in diesem Artikel aus, und konfigurieren Sie die Proxyeinstellungen mit dem Datenverwaltungsgateway-Konfigurations-Manager.

## <a name="gateway-is-online-with-limited-functionality"></a>Gateway ist online mit eingeschränkter Funktionalität
### <a name="1-problem"></a>1. Problem
Als Status des Gateways wird „online mit eingeschränkter Funktionalität“ angezeigt.

#### <a name="cause"></a>Ursache
Sie sehen, dass der Status des Gateways aus einem der folgenden Gründe als „online mit eingeschränkter Funktionalität angezeigt“ wird:

* Das Gateway kann über Azure Service Bus keine Verbindung mit dem Clouddienst herstellen.
* Der Clouddienst kann über Azure Service Bus keine Verbindung mit dem Gateway herstellen.

Wenn das Gateway mit eingeschränkter Funktionalität online ist, können Sie möglicherweise nicht mithilfe des Assistenten zum Kopieren von Data Factory Datenpipelines zum Kopieren von Daten in einen bzw. aus einem lokalen Datenspeicher erstellen. Um dieses Problem zu umgehen, können Sie den Data Factory-Editor im Portal, Visual Studio oder Azure PowerShell verwenden.

#### <a name="resolution"></a>Lösung
Die Lösung dieses Problems (online mit eingeschränkter Funktionalität) hängt davon ab, ob das Gateway nicht mit dem Clouddienst oder auf andere Art und Weise eine Verbindung herstellen kann. In den folgenden Abschnitten werden diese Lösungen beschrieben.

### <a name="2-problem"></a>2. Problem
Sie erhalten den folgenden Fehler.

`Error: Gateway cannot connect to cloud service through service bus`

![Das Gateway kann keine Verbindung mit dem Clouddienst herstellen.](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Ursache
Das Gateway kann über Service Bus keine Verbindung mit dem Clouddienst herstellen.

#### <a name="resolution"></a>Lösung
Um das Gateway wieder online zu schalten, gehen Sie wie folgt vor:

1. Lassen Sie Regeln für ausgehende IP-Adressen auf dem Gatewaycomputer und in der Unternehmensfirewall zu. Sie finden IP-Adressen im Windows-Ereignisprotokoll (ID == 401): Es wurde versucht, auf eine Socket in einer Weise zuzugreifen, die gemäß ihren Zugriffsberechtigungen XX.XX.XX.XX:9350 unzulässig ist.
1. Konfigurieren Sie die Proxyeinstellungen auf dem Gateway. Weitere Informationen finden Sie im Abschnitt „Aspekte zu Proxyservern“.
1. Aktivieren Sie die ausgehenden Ports 5671 und 9350-9354 in der Windows-Firewall sowohl auf dem Gatewaycomputer als auch in der Unternehmensfirewall. Weitere Informationen finden Sie im Abschnitt „Ports und Firewall“. Dieser Schritt ist optional, wird jedoch für die Leistungsoptimierung empfohlen.

### <a name="3-problem"></a>3. Problem
Sie erhalten den folgenden Fehler.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Ursache
Ein vorübergehender Fehler bei der Netzwerkkonnektivität.

#### <a name="resolution"></a>Lösung
Um das Gateway wieder online zu schalten, gehen Sie wie folgt vor:

1. Warten Sie einige Minuten. Die Verbindung wird automatisch wiederhergestellt, wenn der Fehler nicht mehr vorhanden ist.
1. Wenn der Fehler weiterhin auftritt, starten Sie den Gatewaydienst neu.

## <a name="failed-to-author-linked-service"></a>Fehler beim Erstellen des verknüpften Diensts
### <a name="problem"></a>Problem
Dieser Fehler wird möglicherweise angezeigt, wenn Sie versuchen, mit der Anmeldeinformationsverwaltung im Portal Anmeldeinformationen für einen neuen verknüpften Dienst einzugeben oder die Anmeldeinformationen für einen vorhandenen verknüpften Dienst zu aktualisieren.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Wenn dieser Fehler angezeigt wird, kann die Seite „Einstellungen“ des Datenverwaltungsgateway-Konfigurations-Managers wie der folgende Screenshot aussehen.

![Die Datenbank wurde nicht gefunden](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Ursache
Möglicherweise ist das TLS/SSL-Zertifikat auf dem Gatewaycomputer nicht mehr vorhanden. Das derzeit für die TLS-Verschlüsselung verwendete Zertifikat kann vom Gatewaycomputer nicht geladen werden. Sie erhalten möglicherweise auch eine Fehlermeldung im Ereignisprotokoll, die der folgenden Meldung ähnlich ist.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Lösung
Führen Sie die folgenden Schritte aus, um das Problem zu beheben:

1. Starten Sie den Datenverwaltungsgateway-Konfigurations-Manager.
2. Wechseln Sie zur Registerkarte **Einstellungen**.  
3. Klicken Sie auf die Schaltfläche **Ändern** , um das TLS/SSL-Zertifikat zu ändern.

   ![Schaltfläche „Zertifikat ändern“](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Wählen Sie ein neues Zertifikat als das TLS/SSL-Zertifikat aus. Sie können ein beliebiges TLS/SSL-Zertifikat verwenden, das von Ihnen oder einer beliebigen Organisation generiert wurde.

   ![Angeben des Zertifikats](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Fehler bei Kopieraktivität
### <a name="problem"></a>Problem
Möglicherweise tritt nach dem Einrichten einer Pipeline im Portal der folgende „UserErrorFailedToConnectToSqlserver“-Fehler auf.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Ursache
Dies kann aus verschiedenen Gründen geschehen, und die Fehlerbehebung variiert entsprechend.

#### <a name="resolution"></a>Lösung
Lassen Sie ausgehende TCP-Verbindungen über den Port „TCP/1433“ auf der Clientseite des Datenverwaltungsgateways zu, bevor Sie eine Verbindung mit einer SQL-Datenbank herstellen.

Wenn sich die Zieldatenbank in Azure SQL-Datenbank befindet, überprüfen Sie auch die SQL Server-Firewalleinstellungen für Azure.

Im folgenden Abschnitt finden Sie Anweisungen zum Testen der Verbindung mit dem lokalen Datenspeicher.

## <a name="data-store-connection-or-driver-related-errors"></a>Fehler bei der Datenspeicherverbindung oder den Datenspeichertreibern
Wenn Sie Fehler bei der Datenspeicherverbindung oder den Datenspeichertreibern bemerken, führen Sie folgende Schritte aus:

1. Starten Sie den Datenverwaltungsgateway-Konfigurations-Manager auf dem Gatewaycomputer.
2. Wechseln Sie zur Registerkarte **Diagnose** .
3. Fügen Sie in **Verbindung testen** die Werte der Gatewaygruppe hinzu.
4. Klicken Sie auf **Testen** , um festzustellen, ob Sie vom Gatewaycomputer aus mithilfe der Verbindungs- und Anmeldeinformationen eine Verbindung mit der lokalen Datenquelle herstellen können. Wenn das Testen der Verbindung weiter misslingt, nachdem Sie einen Treiber installiert haben, starten Sie das Gateway neu, damit es die letzte Änderung übernimmt.

![„Verbindung testen“ auf der Registerkarte „Diagnose“](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Gatewayprotokolle
### <a name="send-gateway-logs-to-microsoft"></a>Senden der Gatewayprotokolle an Microsoft
Wenn Sie sich im Zusammenhang mit Gatewayproblemen an den Microsoft-Support wenden, werden Sie möglicherweise aufgefordert, Ihre Gatewayprotokolle freizugeben. In dieser Version des Gateways können Sie die erforderlichen Gatewayprotokolle komfortabel über zwei Mausklicks im Datenverwaltungsgateway-Konfigurations-Manager freigeben.    

1. Wechseln Sie im Datenverwaltungsgateway-Konfigurations-Manager zur Registerkarte **Diagnose**.

    ![Datenverwaltungsgateway: Registerkarte „Diagnose“](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Klicken Sie auf den Link **Protokolle senden** , um das folgende Dialogfeld anzuzeigen.

    ![Datenverwaltungsgateway: Senden von Protokollen](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (Optional) Klicken Sie auf **Protokolle anzeigen** , um Protokolle in der Ereignisanzeige durchzusehen.
4. (Optional) Klicken Sie auf **Datenschutz** , um die Datenschutzbestimmungen der Onlinedienste von Microsoft anzuzeigen.
5. Wenn Sie zum Hochladen bereit sind, klicken Sie auf **Protokolle senden** , um Protokolle der letzten sieben Tage zur Problembehandlung an Microsoft zu senden. Daraufhin sollte der Status des Vorgangs „Protokolle senden“ wie in der folgenden Abbildung angezeigt werden.

    ![Screenshot, der zeigt, wo der Status des Protokollsendevorgangs angezeigt werden kann](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. Nach Abschluss des Vorgangs wird ein Dialogfeld wie in der folgenden Abbildung angezeigt.

    ![Datenverwaltungsgateway: Senden des Status von Protokollen](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Notieren Sie sich die **Berichts-ID** , und teilen Sie sie dem Microsoft Support mit. Die Berichts-ID wird verwendet, um die Gatewayprotokolle zu suchen, die Sie zur Problembehandlung hochgeladen haben.  Die Berichts-ID wird auch in der Ereignisanzeige gespeichert.  Sie finden Sie anhand der Ereignis-ID „25“ sowie von Datum und Uhrzeit.

    ![Datenverwaltungsgateway: Senden der Berichts-ID von Protokollen](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Archivieren von Gatewayprotokollen auf dem Gatewayhostcomputer
Es gibt einige Szenarien, in denen Gatewayprobleme auftreten, und Sie Gatewayprotokolle nicht direkt freigeben können:

* Sie installieren und registrieren das Gateway manuell.
* Sie versuchen, das Gateway mit einem neu generierten Schlüssel im Datenverwaltungsgateway-Konfigurations-Manager zu registrieren.
* Sie versuchen, Protokolle zu senden, und es kann keine Verbindung mit dem Gatewayhostdienst hergestellt werden.

Bei diesen Szenarien können Sie Gatewayprotokolle als ZIP-Datei speichern und später beim Kontakt mit dem Microsoft Support freigeben. Beispiel: Sie erhalten beim Registrieren des Gateways eine Fehlermeldung wie in der folgenden Abbildung dargestellt.   

![Datenverwaltungsgateway: Fehler bei der Registrierung](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Klicken Sie auf den Link **Gatewayprotokolle archivieren** , um Protokolle zu archivieren und zu speichern und dann die ZIP-Datei für den Microsoft Support freizugeben.

![Datenverwaltungsgateway: Archivieren von Protokollen](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Suchen von Gatewayprotokollen
Ausführliche Informationen hierzu finden Sie in den Gateway-Protokollinformationen und Windows-Ereignisprotokollen.

1. Starten Sie die Windows- **Ereignisanzeige**.
2. Suchen Sie die Protokolle im Ordner **Anwendungs- und Dienstprotokolle** > **Datenverwaltungsgateway**.

   Wenn Sie Probleme mit dem Gateway beheben müssen, suchen Sie in der Ereignisanzeige nach Ereignissen des Typs „Fehler“.

![Datenverwaltungsgateway: Protokolle in der Ereignisanzeige](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)

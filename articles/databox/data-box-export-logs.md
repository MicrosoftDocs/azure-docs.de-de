---
title: Erfassen und Protokollieren von Azure Data Box- und Azure Data Box Heavy-Ereignissen für Exportaufträge | Microsoft-Dokumentation
description: In diesem Artikel wird beschreiben, wie Ereignisse in den verschiedenen Phasen Ihres Azure Data Box- oder Azure Data Box Heavy-Exportauftrags nachverfolgt und protokolliert werden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 3a915ac8de83a5e183660ec4a3d05044eafff4a9
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337507"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-export-orders"></a>Nachverfolgung und Ereignisprotokollierung für Azure Data Box und Azure Data Box Heavy-Exportaufträge

Ein Data Box- oder Data Box Heavy-Exportauftrag durchläuft die folgenden Schritte: Bestellen, Einrichten, Kopieren von Daten, Zurücksenden und Löschen von Daten. Für jeden Schritt des Auftrags können Sie mehrere Aktionen durchführen, um den Zugriff auf den Auftrag zu steuern, die Ereignisse zu überwachen, den Auftrag zu verfolgen und die verschiedenen Protokolle zu interpretieren, die generiert werden.

In diesem Artikel werden die verschiedenen verfügbaren Verfahren und Tools detailliert beschrieben, mit denen Exportaufträge für Data Box oder Data Box Heavy nachverfolgt und überwacht werden können. Die Informationen in diesem Artikel gelten sowohl für Data Box als auch für Data Box Heavy. In den folgenden Abschnitten gelten alle Verweise auf Data Box auch für Data Box Heavy.

Die folgende Tabelle enthält eine Übersicht der Schritte eines Data Box-Exportauftrags und die verfügbaren Tools zum Nachverfolgen und Überwachen des Auftrags in jedem Schritt.

| Phase des Data Box-Exportauftrags       | Tool zum Nachverfolgen und Überwachen                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Erstellung des Auftrags               | [Einrichten der Zugriffssteuerung für den Auftrag über Azure RBAC](#set-up-access-control-on-the-order) <br> [Aktivieren ausführlicher Protokolle in der Bestellung](#enable-verbose-log-in-the-order)                                                    |
| Auftrag verarbeitet            | [Nachverfolgen des Auftrags](#track-the-order) über <ul><li> Azure-Portal </li><li> Website des Spediteurs </li><li>E-Mail-Benachrichtigungen</ul> |
| Einrichten des Geräts              | In [Aktivitätsprotokollen](#query-activity-logs-during-setup) protokollierter Zugriff auf Geräteanmeldeinformation              |
| Daten vom Gerät kopieren        | [Überprüfen des Kopierens von Protokollen](#copy-log) <br> [Überprüfen der ausführlichen Protokolle](#verbose-log) vor dem Kopieren von Daten            |
| Löschen von Daten vom Gerät   | [Anzeigen der Kette von Protokollen zur Rückverfolgbarkeit](#get-chain-of-custody-logs-after-data-erasure) einschließlich von Überwachungsprotokollen und Auftragsverlauf                |


## <a name="set-up-access-control-on-the-order"></a>Einrichten der Zugriffssteuerung für den Auftrag

Sie können beim Erstellen Ihres Auftrags festlegen, wer auf diesen Auftrag zugreifen kann. Richten Sie Azure-Rollen in verschiedenen Bereichen ein, um den Zugriff auf den Data Box-Auftrag zu steuern. Eine Azure-Rolle bestimmt die Art des Zugriffs: Lese-/Schreibzugriff, schreibgeschützter Zugriff, Lese-/Schreibzugriff auf eine Teilmenge von Vorgängen.

Für den Azure Data Box-Dienst können die folgenden beiden Rollen definiert werden:

- **Data Box-Leser**: Schreibgeschützter Zugriff auf Aufträge, wie im Bereich definiert. Hiermit können lediglich die Details eines Auftrags angezeigt werden. Es ist nicht möglich, auf weitere Details im Zusammenhang mit Speicherkonten zuzugreifen oder die Auftragsdetails zu bearbeiten, z.B. Adresse usw.
- **Data Box-Mitwirkender**: Kann nur einen Auftrag zum Übertragen von Daten in ein bestimmtes Speicherkonto erstellen, *wenn bereits Schreibzugriff auf ein Speicherkonto gewährt wurde*. Falls kein Zugriff auf ein Speicherkonto gewährt wurde, kann mit dieser Rolle kein Data Box-Auftrag zum Kopieren von Daten in das Konto erstellt werden. Diese Rolle definiert keine Berechtigungen für Speicherkonten und gewährt keinen Zugriff auf Speicherkonten.  

Um den Zugriff auf einen Auftrag zu beschränken, können Sie folgende Aktionen ausführen:

- Weisen Sie eine Rolle auf Auftragsebene zu. Der Benutzer verfügt nur über diese durch die Rollen definierten Berechtigungen für die Interaktion mit diesem spezifischen Data Box-Auftrag und kann nichts anderes ausführen.
- Weisen Sie eine Rolle auf Ebene der Ressourcengruppe zu. Der Benutzer hat Zugriff auf alle Data Box-Aufträge innerhalb einer Ressourcengruppe.

Weitere Informationen zur vorgeschlagenen Azure RBAC-Nutzung finden Sie unter [Bewährte Methoden für Azure RBAC](../role-based-access-control/best-practices.md).

## <a name="enable-verbose-log-in-the-order"></a>Aktivieren ausführlicher Protokolle in der Bestellung

Wenn Sie einen Exportauftrag für Data Box erstellen, haben Sie die Möglichkeit, die Sammlung eines ausführlichen Protokolls zu aktivieren. Auf der folgenden Abbildung sehen Sie die Oberfläche für Aufträge, über die Sie ausführliche Protokolle aktivieren können:

![Exportoption auswählen](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

Wenn Sie die Option **Include verbose log** (Ausführliches Protokoll hinzufügen) auswählen, wird eine ausführliche Protokolldatei erstellt, wenn Sie Daten aus Ihrem Azure Storage-Konto kopieren. Dieses Protokoll enthält eine Liste aller Dateien, die erfolgreich exportiert wurden.

Weitere Informationen zu Exportaufträgen finden Sie unter [Erstellen eines Exportauftrags für Data Box](data-box-deploy-export-ordered.md).

## <a name="track-the-order"></a>Nachverfolgen der Bestellung

Sie können Ihren Auftrag über das Azure-Portal und über die Website des Spediteurs nachverfolgen. Mit den folgenden vorhandenen Verfahren können Sie den Data Box-Auftrag jederzeit nachverfolgen:

- Um den Auftrag nachzuverfolgen, wenn sich das Gerät im Azure-Rechenzentrum oder in Ihrer lokalen Umgebung befindet, wechseln Sie im Azure-Portal zu **Data Box-Auftrag > Übersicht**.

    ![Anzeigen von Auftragsstatus und Nachverfolgungsnummer](media/data-box-logs/overview-view-status-1.png)

- Um den Auftrag nachzuverfolgen, während das Gerät unterwegs ist, wechseln Sie zur Website des regionalen Spediteurs, zum Beispiel zur UPS-Website in den USA. Geben Sie die Nachverfolgungsnummer an, die Ihrem Auftrag zugeordnet ist.
- Data Box sendet außerdem bei jeder Änderung des Auftragsstatus E-Mail-Benachrichtigungen auf Grundlage der E-Mail-Adressen, die beim Erstellen des Auftrags angegeben wurden. Eine Statusliste für Data Box-Aufträge finden Sie unter [Auftragsstatus anzeigen](data-box-portal-admin.md#view-order-status). Informationen über das Ändern der Benachrichtigungseinstellungen für den Auftrag finden Sie unter [Bearbeiten der Benachrichtigungsdetails](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Abfragen von Aktivitätsprotokollen während der Einrichtung

- Ihre Data Box trifft gesperrt bei Ihnen ein. Sie können die im Azure-Portal verfügbaren Geräteanmeldeinformationen für Ihren Auftrag verwenden.  

    Wenn eine Data Box eingerichtet wird, müssen Sie möglicherweise wissen, wer auf die Geräteanmeldeinformationen zugegriffen hat. Um zu ermitteln, wer auf das Blatt **Geräteanmeldeinformationen** zugegriffen hat, können Sie die Aktivitätsprotokolle abfragen.  Jede Aktion, bei der auf das Blatt **Gerätedetails > Anmeldeinformationen** zugegriffen wurde, wird in den Aktivitätsprotokollen als `ListCredentials`-Aktion protokolliert.

    ![Abfragen von Aktivitätsprotokollen](media/data-box-logs/query-activity-log-1.png)

- Jede Anmeldung bei der Data Box wird in Echtzeit protokolliert. Diese Informationen sind jedoch erst nach erfolgreichem Abschluss des Auftrags in den [Überwachungsprotokollen zur Rückverfolgbarkeit](#chain-of-custody-audit-logs) verfügbar.

## <a name="view-logs-during-data-copy"></a>Anzeigen von Protokollen während des Datenkopiervorgangs

Vor dem Kopieren von Daten aus Data Box können Sie das *Kopierprotokoll* und das *ausführliche Protokoll* für die Daten herunterladen und prüfen, die in Data Box kopiert wurden. Diese Protokolle werden generiert, wenn die Daten aus Ihrem Azure Storage-Konto in Ihre Data Box-Instanz kopiert werden. 

### <a name="copy-log"></a>Kopierprotokoll

Bevor Sie die Daten aus Ihrer Data Box-Instanz kopieren, laden Sie das Kopierprotokoll auf der Seite **Verbindung herstellen und Daten kopieren** herunter.

Nachfolgend finden Sie eine Beispielausgabe des *Kopierprotokolls* für den Fall, dass keine Fehler aufgetreten sind und alle Dateien während des Kopiervorgangs aus Azure auf das Data Box-Gerät kopiert wurden.

```output
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
  <TotalFiles_Blobs>5521</TotalFiles_Blobs>
  <FilesErrored>0</FilesErrored>
</CopyLog>
``` 
    
Hier eine Beispielausgabe für den Fall, dass das *Kopierprotokoll* Fehler beinhaltet und einige der Dateien nicht aus Azure kopiert werden konnten.

```output
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>    
```

Sie haben die folgenden Möglichkeiten, um diese Dateien zu exportieren: 

- Sie können die Dateien übertragen, die nicht über das Netzwerk kopiert werden konnten. 
- Wenn die Datengröße über der verwendbaren Gerätekapazität liegt, wird eine Teilkopie erstellt und alle Dateien, die nicht kopiert wurden, werden in diesem Protokoll aufgeführt. Sie können dieses Protokoll als Eingabe-XML-Code verwenden, um einen neuen Data Box-Auftrag zu erstellen und diese Dateien anschließend zu kopieren.

### <a name="verbose-log"></a>Ausführliches Protokoll

Das *ausführliche Protokoll* enthält eine Liste aller Dateien, die erfolgreich aus dem Azure Storage-Konto exportiert wurden. Darüber hinaus enthält das Protokoll die Dateigröße und die Prüfsummenberechnung.

Das ausführliche Protokoll enthält die Informationen im folgenden Format:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Hier sehen Sie eine Beispielausgabe des ausführlichen Protokolls: 

```powershell
  <File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820">
  </File>
  ``````

Die ausführlichen Protokolle werden auch in das Azure Storage-Konto kopiert. Standardmäßig werden Protokolle in einen Container mit dem Namen `copylog` geschrieben. Die Protokolle werden mit der folgenden Benennungskonvention gespeichert:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Der Pfad des Kopierprotokolls wird auch auf dem Blatt **Übersicht** für das Portal angezeigt.

<!-- add a screenshot-->

Mithilfe dieser Protokolle können Sie überprüfen, ob von Azure kopierte Dateien den Daten entsprechen, die auf Ihren lokalen Server kopiert wurden. 

Verwendungsweisen der ausführlichen Protokolldatei:

- Überprüfen anhand der Namen und der Anzahl der Dateien, die aus der Data Box-Instanz kopiert wurden
- Überprüfen der tatsächliche Größe der Dateien
- Überprüfen, ob *crc64* einer Zeichenfolge ungleich Null entspricht Während des Exports aus Azure wird eine CRC-Berechnung (Cyclic Redundancy Check) durchgeführt. Die CRC aus dem Export können verglichen werden, nachdem die Daten aus der Data Box-Instanz auf den lokalen Server kopiert wurden. Wenn die CRC-Werte nicht übereinstimmen, weist dies darauf hin, dass das Kopieren der entsprechenden Dateien fehlgeschlagen ist.


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Abrufen der Kette von Protokollen zur Rückverfolgbarkeit nach dem Löschen von Daten

Nachdem die Daten gemäß den NIST-Richtlinien (SP 800-88 Revision 1) vom Data Box-Datenträger gelöscht wurden, ist die Kette von Protokollen zur Rückverfolgbarkeit verfügbar. Diese Protokolle enthalten die Überwachungsprotokolle zur Rückverfolgbarkeit und den Auftragsverlauf. Die BOM- oder Manifestdateien werden ebenfalls mit den Überwachungsprotokollen kopiert.

### <a name="chain-of-custody-audit-logs"></a>Überwachungsprotokolle zur Rückverfolgbarkeit

Überwachungsprotokolle zur Rückverfolgbarkeit enthalten Informationen zum Einschalten von und Zugreifen auf Freigaben auf Data Box oder Data Box Heavy außerhalb eines Azure-Rechenzentrums. Die Protokolle befinden sich hier: `storage-account/azuredatabox-chainofcustodylogs`

Hier finden Sie ein Beispiel für das Überwachungsprotokoll einer Data Box:

```output
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```

## <a name="download-order-history"></a>Herunterladen des Auftragsverlaufs

Der Auftragsverlauf ist im Azure-Portal verfügbar. Wenn der Auftrag und die Gerätebereinigung (Löschung der Daten von den Datenträgern) abgeschlossen sind, wechseln Sie zum Geräteauftrag, und wählen Sie **Auftragsdetails** aus. Die Option **Bestellverlauf herunterladen** ist verfügbar. Weitere Informationen finden Sie unter [Bestellverlauf herunterladen](data-box-portal-admin.md#download-order-history).

Wenn Sie durch den Auftragsverlauf scrollen, sehen Sie Folgendes:

- Nachverfolgungsinformationen des Spediteurs für Ihr Gerät.
- Ereignisse mit der Aktivität *SecureErase*. Diese Ereignisse entsprechen der Löschung der Daten auf dem Datenträger.
- Data Box-Protokolllinks. Die Pfade für die *Überwachungsprotokolle*, *Kopierprotokolle* und *BOM*-Dateien werden angezeigt.

Hier finden Sie ein Beispiel für das Auftragsverlaufsprotokoll im Azure-Portal:

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Behandeln von Problemen mit Azure Data Box und Azure Data Box Heavy](data-box-troubleshoot.md).

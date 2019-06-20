---
title: Erfassen und Protokollieren von Azure Data Box- und Azure Data Box Heavy-Ereignissen| Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie Ereignisse in den verschiedenen Phasen Ihres Azure Data Box- oder Azure Data Box Heavy-Auftrags nachverfolgt und protokolliert werden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 108d17d3e0ca5f32648f9d4f6cf4b5f9a2984d0c
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495820"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Nachverfolgung und Ereignisprotokollierung für Azure Data Box und Azure Data Box Heavy

Ein Data Box- oder Data Box Heavy-Auftrag durchläuft die folgenden Schritte: Bestellen, Einrichten, Kopieren von Daten, Zurücksenden, Hochladen in Azure und Überprüfen sowie Löschen von Daten. Für jeden Schritt des Auftrags können Sie mehrere Aktionen durchführen, um den Zugriff auf den Auftrag zu steuern, die Ereignisse zu überwachen, den Auftrag zu verfolgen und die verschiedenen Protokolle zu interpretieren, die generiert werden.

Die folgende Tabelle zeigt eine Übersicht über die Schritte eines Data Box- oder Data Box Heavy-Auftrags und die verfügbaren Tools zum Nachverfolgen und Überwachen des Auftrags in jedem Schritt.

| Phase eines Data Box-Auftrags       | Tool zum Nachverfolgen und Überwachen                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Erstellung des Auftrags               | [Einrichten der Zugriffssteuerung für den Auftrag über RBAC](#set-up-access-control-on-the-order)                                                    |
| Auftrag verarbeitet            | [Nachverfolgen des Auftrags](#track-the-order) über <ul><li> Azure-Portal </li><li> Website des Spediteurs </li><li>E-Mail-Benachrichtigungen</ul> |
| Einrichten des Geräts              | In [Aktivitätsprotokollen](#query-activity-logs-during-setup) protokollierter Zugriff auf Geräteanmeldeinformation                                              |
| Kopieren von Daten auf ein Gerät        | [Anzeigen von *error.xml*-Dateien](#view-error-log-during-data-copy) für das Kopieren von Daten                                                             |
| Vorbereiten des Versands            | [Überprüfen der BOM-Dateien](#inspect-bom-during-prepare-to-ship) oder der Manifestdateien auf dem Gerät                                      |
| Datenupload in Azure       | [Überprüfen von *Kopierprotokollen*](#review-copy-log-during-upload-to-azure) auf Fehler beim Datenupload im Azure-Rechenzentrum                         |
| Löschen von Daten vom Gerät   | [Anzeigen der Kette von Protokollen zur Rückverfolgbarkeit](#get-chain-of-custody-logs-after-data-erasure) einschließlich von Überwachungsprotokollen und Auftragsverlauf                                                   |

In diesem Artikel werden die verschiedenen verfügbaren Verfahren und Tools detailliert beschrieben, mit denen Data Box- oder Data Box Heavy-Aufträge nachverfolgt und überwacht werden können. Die Informationen in diesem Artikel gelten sowohl für Data Box als auch für Data Box Heavy. In den folgenden Abschnitten gelten alle Verweise auf Data Box auch für Data Box Heavy.

## <a name="set-up-access-control-on-the-order"></a>Einrichten der Zugriffssteuerung für den Auftrag

Sie können beim Erstellen Ihres Auftrags festlegen, wer auf diesen Auftrag zugreifen kann. Richten Sie RBAC-Rollen (Rollenbasierte Zugriffssteuerung) in verschiedenen Bereichen ein, um den Zugriff auf den Data Box-Auftrag zu steuern. Eine RBAC-Rolle bestimmt die Art des Zugriffs: Lese-/Schreibzugriff, schreibgeschützt, Lese-/Schreibzugriff auf eine Teilmenge von Vorgängen.

Für den Azure Data Box-Dienst können die folgenden beiden Rollen definiert werden:

- **Data Box-Leser**: Schreibgeschützter Zugriff auf Aufträge, wie im Bereich definiert. Hiermit können lediglich die Details eines Auftrags angezeigt werden. Es ist nicht möglich, auf weitere Details im Zusammenhang mit Speicherkonten zuzugreifen oder die Auftragsdetails zu bearbeiten, z.B. Adresse usw.
- **Data Box-Mitwirkender**: Kann nur einen Auftrag zum Übertragen von Daten in ein bestimmtes Speicherkonto erstellen, *wenn bereits Schreibzugriff auf ein Speicherkonto gewährt wurde*. Falls kein Zugriff auf ein Speicherkonto gewährt wurde, kann mit dieser Rolle kein Data Box-Auftrag zum Kopieren von Daten in das Konto erstellt werden. Diese Rolle definiert keine Berechtigungen für Speicherkonten und gewährt keinen Zugriff auf Speicherkonten.  

Um den Zugriff auf einen Auftrag zu beschränken, können Sie folgende Aktionen ausführen:

- Weisen Sie eine Rolle auf Auftragsebene zu. Der Benutzer verfügt nur über diese durch die Rollen definierten Berechtigungen für die Interaktion mit diesem spezifischen Data Box-Auftrag und kann nichts anderes ausführen.
- Weisen Sie eine Rolle auf Ebene der Ressourcengruppe zu. Der Benutzer hat Zugriff auf alle Data Box-Aufträge innerhalb einer Ressourcengruppe.

Weitere Informationen über die vorgeschlagene RBAC-Nutzung finden Sie unter [Bewährte Methoden für RBAC](../role-based-access-control/overview.md#best-practice-for-using-rbac).

## <a name="track-the-order"></a>Nachverfolgen der Bestellung

Sie können Ihren Auftrag über das Azure-Portal und über die Website des Spediteurs nachverfolgen. Mit den folgenden vorhandenen Verfahren können Sie den Data Box-Auftrag jederzeit nachverfolgen:

- Um den Auftrag nachzuverfolgen, wenn sich das Gerät im Azure-Rechenzentrum oder in Ihrer lokalen Umgebung befindet, wechseln Sie im Azure-Portal zu **Data Box-Auftrag > Übersicht**.

    ![Anzeigen von Auftragsstatus und Nachverfolgungsnummer](media/data-box-logs/overview-view-status-1.png)

- Um den Auftrag nachzuverfolgen, während das Gerät unterwegs ist, wechseln Sie zur Website des regionalen Spediteurs, zum Beispiel zur UPS-Website in den USA. Geben Sie die Nachverfolgungsnummer an, die Ihrem Auftrag zugeordnet ist.
- Data Box sendet außerdem bei jeder Änderung des Auftragsstatus E-Mail-Benachrichtigungen auf Grundlage der E-Mail-Adressen, die beim Erstellen des Auftrags angegeben wurden. Eine Statusliste für Data Box-Aufträge finden Sie unter [Auftragsstatus anzeigen](data-box-portal-admin.md#view-order-status). Informationen über das Ändern der Benachrichtigungseinstellungen für den Auftrag finden Sie unter [Bearbeiten der Benachrichtigungsdetails](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Abfragen von Aktivitätsprotokollen während der Einrichtung

- Ihre Data Box trifft gesperrt bei Ihnen ein. Sie können die im Azure-Portal verfügbaren Geräteanmeldeinformationen für Ihren Auftrag verwenden.  

    Wenn eine Data Box eingerichtet wird, müssen Sie möglicherweise wissen, wer auf die Geräteanmeldeinformationen zugegriffen hat. Um zu ermitteln, wer auf das Blatt **Geräteanmeldeinformationen** zugegriffen hat, können Sie die Aktivitätsprotokolle abfragen.  Jede Aktion, bei der auf das Blatt **Gerätedetails > Anmeldeinformationen** zugegriffen wurde, wird in den Aktivitätsprotokollen als `ListCredentials`-Aktion protokolliert.

    ![Abfragen von Aktivitätsprotokollen](media/data-box-logs/query-activity-log-1.png)

- Jede Anmeldung bei der Data Box wird in Echtzeit protokolliert. Diese Informationen sind jedoch erst nach erfolgreichem Abschluss des Auftrags in den [Überwachungsprotokollen](#audit-logs) verfügbar.

## <a name="view-error-log-during-data-copy"></a>Anzeigen des Fehlerprotokolls beim Kopieren von Daten

Während des Kopiervorgangs von Daten auf die Data Box oder Data Box Heavy wird eine Fehlerdatei generiert, falls beim Kopieren der Daten Probleme auftreten.

### <a name="errorxml-file"></a>Datei „Error.xml“

Vergewissern Sie sich, dass die Kopieraufträge ohne Fehler abgeschlossen wurden. Sollten während des Kopiervorgangs Fehler aufgetreten sein, laden Sie die Protokolle von der Seite  **Verbindung herstellen und Daten kopieren**  herunter.

- Falls Sie eine Datei kopiert haben, die in einem Ordner für verwaltete Datenträger in Ihrer Data Box keinem ganzzahligen Vielfachen von 512 Byte entspricht, wird die Datei nicht als Seitenblob in Ihr Stagingspeicherkonto hochgeladen. Die Protokolle enthalten dann einen Fehler. Entfernen Sie die Datei, und kopieren Sie eine Datei, die einem ganzzahligen Vielfachen von 512 Bytes entspricht.
- Wenn Sie eine VHDX, eine dynamische VHD oder eine differenzierende VHD (nicht unterstützt) kopiert haben, wird ein Fehler protokolliert.

Hier finden Sie ein Beispiel für die Datei *error.xml* für verschiedene Fehler beim Kopieren auf verwaltete Datenträger.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Hier finden Sie ein Beispiel für die Datei *error.xml* für verschiedene Fehler beim Kopieren in Seitenblobs.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Hier finden Sie ein Beispiel für die Datei *error.xml* für verschiedene Fehler beim Kopieren in Blockblobs.

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

Hier finden Sie ein Beispiel für die Datei *error.xml* für verschiedene Fehler beim Kopieren in Azure Files.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

In jedem der oben aufgeführten Fälle müssen Sie die Fehler beheben, bevor Sie mit dem nächsten Schritt fortfahren. Weitere Informationen zu den Fehlern beim Kopieren von Daten auf die Data Box über SMB- oder NFS-Protokolle finden Sie unter [Behandeln von Problemen mit Azure Data Box und Azure Data Box Heavy](data-box-troubleshoot.md). Weitere Informationen über die Fehler beim Kopieren von Daten in Data Box über REST finden Sie unter [Behandeln von Problemen mit dem Data Box-Blobspeicher](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>BOM-Überprüfung während der Vorbereitung für den Versand

Bei der Vorbereitung auf den Versand wird eine Dateiliste erstellt, die als BOM (Bill of Materials, Stückliste) bezeichnet wird.

- Überprüfen Sie anhand dieser Datei die Namen und die Anzahl der Dateien, die in die Data Box kopiert wurden.
- Verwenden Sie diese Datei, um die tatsächliche Größe der Dateien zu überprüfen.
- Überprüfen Sie, ob *crc64* einer Zeichenfolge ungleich Null entspricht. <!--A null value for crc64 indicates that there was a reparse point error)-->

Weitere Informationen zu den Fehlern während der Vorbereitung auf den Versand finden Sie unter [Behandeln von Problemen mit Azure Data Box und Azure Data Box Heavy](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>BOM- oder Manifestdatei

Die BOM- oder Manifestdatei enthält die Liste aller Dateien, die auf das Data Box-Gerät kopiert werden. Die BOM-Datei enthält die Dateinamen und die entsprechenden Größen sowie die Prüfsumme. Eine separate BOM-Datei wird für die Blockblobs, Seitenblobs, Azure Files, für das Kopieren über die REST-APIs und für das Kopieren auf verwaltete Datenträger in der Data Box erstellt. Sie können die BOM-Dateien während der Vorbereitung auf den Versand über die lokale Webbenutzeroberfläche des Geräts herunterladen.

Diese Dateien befinden sich auch auf dem Data Box-Gerät und werden in das zugehörige Speicherkonto im Azure-Rechenzentrum hochgeladen.

### <a name="bom-file-format"></a>Format der BOM-Datei

Die BOM- oder Manifestdatei hat das folgende allgemeine Format:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Hier finden Sie ein Beispiel für ein Manifest, das generiert wurde, als die Daten in die Freigabe für Blockblobs in der Data Box kopiert wurden.

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

Die BOM- oder Manifestdateien werden auch in das Azure-Speicherkonto kopiert. Sie können die BOM- oder Manifestdateien verwenden, um sicherzustellen, dass die in Azure hochgeladenen Dateien mit den Daten übereinstimmen, die in die Data Box kopiert wurden.

## <a name="review-copy-log-during-upload-to-azure"></a>Überprüfen von Kopierprotokollen beim Upload in Azure

Beim Datenupload in Azure wird ein *Kopierprotokoll* erstellt.

### <a name="copylog"></a>Kopierprotokoll

Für jeden verarbeiteten Auftrag erstellt der Data Box-Dienst ein *Kopierprotokoll* im zugeordneten Speicherkonto. Das *Kopierprotokoll* enthält die Gesamtanzahl der hochgeladenen Dateien sowie die Anzahl der Dateien, bei denen während des Kopierens aus der Data Box in Ihr Azure-Speicherkonto Fehler aufgetreten sind.

Während des Uploads in Azure wird eine CRC-Berechnung (Cyclic Redundancy Check) durchgeführt. Die CRC-Werte des Datenkopiervorgangs und werden mit denen nach dem Datenupload verglichen. Wenn die CRC-Werte nicht übereinstimmen, weist dies darauf hin, dass der Upload der entsprechenden Dateien fehlgeschlagen ist.

Standardmäßig werden Protokolle in einen Container mit dem Namen „copylog“ geschrieben. Die Protokolle werden mit der folgenden Benennungskonvention gespeichert:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Der Pfad des Kopierprotokolls wird auch auf dem Blatt **Übersicht** für das Portal angezeigt.

![Pfad zum Kopierprotokoll nach Abschluss auf dem Blatt „Übersicht“](media/data-box-logs/copy-log-path-1.png)

Im folgenden Beispiel wird das allgemeine Format einer Kopierprotokolldatei für einen Data Box-Upload beschrieben, der erfolgreich abgeschlossen wurde:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

Der Upload in Azure kann auch mit Fehlern abgeschlossen werden.

![Der Pfad zum Kopierprotokoll nach Abschluss mit Fehlern auf dem Blatt „Übersicht“](media/data-box-logs/copy-log-path-2.png)

Hier finden Sie ein Beispiel für ein Kopierprotokoll, nachdem der Upload mit Fehlern abgeschlossen wurde:

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Abrufen der Kette von Protokollen zur Rückverfolgbarkeit nach dem Löschen von Daten

Nachdem die Daten gemäß den NIST-Richtlinien (SP 800-88 Revision 1) vom Data Box-Datenträger gelöscht wurden, ist die Kette von Protokollen zur Rückverfolgbarkeit verfügbar. Diese Protokolle enthalten die Überwachungsprotokolle und den Auftragsverlauf. Die BOM- oder Manifestdateien werden ebenfalls mit den Überwachungsprotokollen kopiert.

### <a name="audit-logs"></a>Überwachungsprotokolle

Überwachungsprotokolle enthalten Informationen zum Einschalten und Freigeben des Zugriffs auf die Data Box oder Data Box Heavy außerhalb eines Azure-Rechenzentrums. Die Protokolle befinden sich hier: `storage-account/azuredatabox-chainofcustodylogs`

Hier finden Sie ein Beispiel für das Überwachungsprotokoll einer Data Box:

```
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

Der Auftragsverlauf ist im Azure-Portal verfügbar. Wenn der Auftrag und die Gerätebereinigung (Löschung der Daten von den Datenträgern) abgeschlossen sind, wechseln Sie zu **Auftragsdetails**. Die Option  **Bestellverlauf herunterladen**  ist verfügbar. Weitere Informationen finden Sie unter [Bestellverlauf herunterladen](data-box-portal-admin.md#download-order-history).

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

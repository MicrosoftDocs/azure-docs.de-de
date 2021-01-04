---
title: Verwalten von Azure Data Box/Azure Data Box Heavy mit der lokalen Webbenutzeroberfläche
description: In diesem Artikel wird beschrieben, wie Sie Ihre Data Box- und Data Box Heavy-Geräte über die lokale Webbenutzeroberfläche verwalten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/07/2020
ms.author: alkohli
ms.openlocfilehash: 859eb6abd138f0660407618bba4550daf9158ab9
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855004"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box-and-data-box-heavy"></a>Verwalten von Data Box und Data Box Heavy über die lokale Webbenutzeroberfläche

In diesem Artikel werden einige der Konfigurations- und Verwaltungsaufgaben beschrieben, die auf Data Box- und Data Box Heavy-Geräten ausgeführt werden. Sie können Data Box- und Data Box Heavy-Geräte über die Azure-Portaloberfläche oder die lokale Webbenutzeroberfläche für das Gerät verwalten. Dieser Artikel konzentriert sich auf Aufgaben, die über die lokale Webbenutzeroberfläche ausgeführt werden.

Die lokale Webbenutzeroberfläche für Data Box und Data Box Heavy dient zur Erstkonfiguration des Geräts. Sie können die lokale Webbenutzeroberfläche auch verwenden, um das Gerät herunterzufahren oder neu zu starten, Diagnosetests durchzuführen, Software zu aktualisieren, Kopierprotokolle anzuzeigen, lokale Daten vom Gerät zu löschen und ein Unterstützungspaket für den Microsoft-Support zu generieren. Bei einem Data Box Heavy-Gerät mit zwei unabhängigen Knoten können Sie für jeden Knoten des Geräts auf eine separate lokale Webbenutzeroberfläche zugreifen.

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="generate-support-package"></a>Erstellen eines Unterstützungspakets

Wenn Sie Geräteprobleme haben, können Sie anhand der Systemprotokolle ein Unterstützungspaket erstellen. Der Microsoft-Support verwendet dieses Paket, um das Problem zu beheben.

Führen Sie die folgenden Schritte aus, um ein Supportpaket zu generieren:

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche zu **Support kontaktieren**. Wählen Sie optional **Speicherabbilder einschließen** aus. Wählen Sie dann **Unterstützungspaket erstellen** aus.

    Bei einem Speicherabbild handelt es sich um den Speicherinhalt Ihres Geräts, der nach einem Systemfehler gespeichert wird.

    Sie sollten die Option **Speicherabbilder einschließen** nur auswählen, wenn Sie vom Support darum gebeten werden. Es dauert sehr lange, ein Unterstützungspaket zusammenzustellen, das Speicherabbilder enthält, und zudem sind sensible Daten enthalten.

    ![Unterstützungspaket erstellen 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

    Ein Unterstützungspaket wird zusammengestellt. Wenn Sie nur Systemprotokolle einschließen, dauert dieser Vorgang einige Minuten. Wenn Sie Speicherabbilder einschließen, dauert er viel länger.

    ![Unterstützungspaket erstellen 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

2. Sobald die Erstellung des Unterstützungspakets abgeschlossen ist, wählen Sie **Unterstützungspaket herunterladen** aus.

    ![Unterstützungspaket erstellen 3](media/data-box-local-web-ui-admin/create-support-package-3.png)

3. Navigieren Sie zum Speicherort des Downloads. Öffnen Sie den Ordner, um den Inhalt anzuzeigen.

    ![Unterstützungspaket erstellen 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

## <a name="erase-local-data-from-your-device"></a>Löschen lokaler Daten vom Gerät

Über die lokale Webbenutzeroberfläche können Sie lokale Daten von Ihrem Gerät löschen, bevor Sie es an das Azure-Rechenzentrum zurücksenden.

> [!IMPORTANT]
> Eine Datenlöschung kann nicht rückgängig gemacht werden. Stellen Sie sicher, dass die Dateien gesichert werden, bevor Sie lokale Daten von Ihrem Gerät löschen.

Führen Sie die folgenden Schritte aus, um lokale Daten von Ihrem Gerät zu löschen:

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche zu **Datenlöschung**.
2. Geben Sie das Gerätekennwort ein, und wählen Sie **Daten löschen** aus.

    ![Option zur Datenlöschung für ein Gerät](media/data-box-local-web-ui-admin/erase-local-data-1.png)

3. Wählen Sie bei der Bestätigungsaufforderung **Ja** aus, um den Vorgang fortzusetzen. Das Löschen von Daten kann bis zu 50 Minuten dauern.

   Vergewissern Sie sich, dass Sie die lokalen Daten sichern, bevor Sie sie vom Gerät löschen. Eine Datenlöschung kann nicht rückgängig gemacht werden.

    ![Bestätigungsaufforderung für Datenlöschung](media/data-box-local-web-ui-admin/erase-local-data-2.png)

## <a name="shut-down-or-restart-your-device"></a>Herunterfahren oder Neustarten des Geräts

Sie können Ihr Gerät über die lokale Webbenutzeroberfläche herunterfahren oder neu starten. Wir empfehlen, vor dem Neustart die Freigaben auf dem Host und dann auf dem Gerät offline zu schalten. Dadurch wird das Risiko einer Datenbeschädigung minimiert. Vergewissern Sie sich, dass beim Herunterfahren des Geräts kein Datenkopiervorgang durchgeführt wird.

Führen Sie die folgenden Schritte aus, um Ihr Gerät herunterzufahren.

1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Herunterfahren oder neu starten**.

2. Wählen Sie **Herunterfahren** aus.

    ![Data Box herunterfahren 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **OK**, um fortzufahren.

    ![Data Box herunterfahren 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

Sobald das Gerät heruntergefahren ist, schalten Sie es über den Netzschalter auf der Vorderseite ein.

Führen Sie zum Neustarten Ihres Data Box-Geräts die folgenden Schritte aus.

1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Herunterfahren oder neu starten**.
2. Wählen Sie **Neu starten** aus.

    ![Data Box neu starten 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **OK**, um fortzufahren.

   Das Gerät wird heruntergefahren und anschließend neu gestartet.

## <a name="download-bom-or-manifest-files"></a>Herunterladen von BOM- oder Manifestdateien

Die BOM- oder Manifestdateien enthalten die Liste der Dateien, die auf das Data Box- oder Data Box Heavy-Gerät kopiert werden. Diese Dateien werden beim Vorbereiten des Geräts für den Versand für einen Importauftrag generiert.

Führen Sie zunächst zum Herunterladen der BOM- oder Manifestdateien für Ihren Importauftrag die folgenden Schritte aus:

1. Rufen Sie die lokale Webbenutzeroberfläche für Ihr Gerät auf. Vergewissern Sie sich, dass für Ihr Gerät der Schritt **Versandvorbereitung** ausgeführt wurde. Wenn die Vorbereitung des Geräts abgeschlossen ist, ändert sich der Gerätestatus in **Bereit für den Versand**.

    ![Gerät bereit für den Versand](media/data-box-local-web-ui-admin/prepare-to-ship-3.png)

2. Wählen Sie **Dateiliste herunterladen** aus, um die Liste der Dateien herunterzuladen, die auf Ihre Data Box kopiert wurden.

    <!-- ![Select Download list of files](media/data-box-portal-admin/download-list-of-files.png) -->

3. Im Datei-Explorer werden separate Dateilisten generiert. Ausschlaggebend hierfür sind das Protokoll, das für die Verbindungsherstellung mit dem Gerät verwendet wird, und der verwendete Azure Storage-Typ.

    <!-- ![Files for storage type and connection protocol](media/data-box-portal-admin/files-storage-connection-type.png) -->
    ![Dateien für Speichertyp und Verbindungsprotokoll](media/data-box-local-web-ui-admin/prepare-to-ship-5.png)

   In der folgenden Tabelle sind die Dateinamen dem verwendeten Azure Storage-Typ und dem verwendeten Verbindungsprotokoll zugeordnet.

    |Dateiname  |Azure Storage-Typ  |Verwendetes Verbindungsprotokoll |
    |---------|---------|---------|
    |utSAC1_202006051000_BlockBlob-BOM.txt     |Blockblobs         |SMB/NFS         |
    |utSAC1_202006051000_PageBlob-BOM.txt     |Seitenblobs         |SMB/NFS         |
    |utSAC1_202006051000_AzFile-BOM.txt    |Azure Files         |SMB/NFS         |
    |utsac1_PageBlock_Rest-BOM.txt     |Seitenblobs         |REST        |
    |utsac1_BlockBlock_Rest-BOM.txt    |Blockblobs         |REST         |

Nach dem Zurücksenden der Data Box an das Azure-Datencenter können Sie anhand dieser Liste die in das Azure Storage-Konto hochgeladenen Dateien überprüfen. Nachfolgend sehen Sie eine Beispielmanifestdatei:

> [!NOTE]
> Bei einem Data Box Heavy-Gerät sind zwei Sätze mit Dateilisten (BOM-Dateien) vorhanden, entsprechend den beiden Knoten auf dem Gerät.

```xml
<file size="52689" crc64="0x95a62e3f2095181e">\databox\media\data-box-deploy-copy-data\prepare-to-ship2.png</file>
<file size="22117" crc64="0x9b160c2c43ab6869">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer2.png</file>
<file size="57159" crc64="0x1caa82004e0053a4">\databox\media\data-box-deploy-copy-data\verify-used-space-dashboard.png</file>
<file size="24777" crc64="0x3e0db0cd1ad438e0">\databox\media\data-box-deploy-copy-data\prepare-to-ship5.png</file>
<file size="162006" crc64="0x9ceacb612ecb59d6">\databox\media\data-box-cable-options\cabling-dhcp-data-only.png</file>
<file size="155066" crc64="0x051a08d36980f5bc">\databox\media\data-box-cable-options\cabling-2-port-setup.png</file>
<file size="150399" crc64="0x66c5894ff328c0b1">\databox\media\data-box-cable-options\cabling-with-switch-static-ip.png</file>
<file size="158082" crc64="0xbd4b4c5103a783ea">\databox\media\data-box-cable-options\cabling-mgmt-only.png</file>
<file size="148456" crc64="0xa461ad24c8e4344a">\databox\media\data-box-cable-options\cabling-with-static-ip.png</file>
<file size="40417" crc64="0x637f59dd10d032b3">\databox\media\data-box-portal-admin\delete-order1.png</file>
<file size="33704" crc64="0x388546569ea9a29f">\databox\media\data-box-portal-admin\clone-order1.png</file>
<file size="5757" crc64="0x9979df75ee9be91e">\databox\media\data-box-safety\japan.png</file>
<file size="998" crc64="0xc10c5a1863c5f88f">\databox\media\data-box-safety\overload_tip_hazard_icon.png</file>
<file size="5870" crc64="0x4aec2377bb16136d">\databox\media\data-box-safety\south-korea.png</file>
<file size="16572" crc64="0x05b13500a1385a87">\databox\media\data-box-safety\taiwan.png</file>
<file size="999" crc64="0x3f3f1c5c596a4920">\databox\media\data-box-safety\warning_icon.png</file>
<file size="1054" crc64="0x24911140d7487311">\databox\media\data-box-safety\read_safety_and_health_information_icon.png</file>
<file size="1258" crc64="0xc00a2d5480f4fcec">\databox\media\data-box-safety\heavy_weight_hazard_icon.png</file>
<file size="1672" crc64="0x4ae5cfa67c0e895a">\databox\media\data-box-safety\no_user_serviceable_parts_icon.png</file>
<file size="3577" crc64="0x99e3d9df341b62eb">\databox\media\data-box-safety\battery_disposal_icon.png</file>
<file size="993" crc64="0x5a1a78a399840a17">\databox\media\data-box-safety\tip_hazard_icon.png</file>
<file size="1028" crc64="0xffe332400278f013">\databox\media\data-box-safety\electrical_shock_hazard_icon.png</file>
<file size="58699" crc64="0x2c411d5202c78a95">\databox\media\data-box-deploy-ordered\data-box-ordered.png</file>
<file size="46816" crc64="0x31e48aa9ca76bd05">\databox\media\data-box-deploy-ordered\search-azure-data-box1.png</file>
<file size="24160" crc64="0x978fc0c6e0c4c16d">\databox\media\data-box-deploy-ordered\select-data-box-option1.png</file>
<file size="115954" crc64="0x0b42449312086227">\databox\media\data-box-disk-deploy-copy-data\data-box-disk-validation-tool-output.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
<file size="2823" crc64="0x63db1ada6fcdc672">\databox\index.yml</file>
<file size="4364" crc64="0x62b5710f58f00b8b">\databox\data-box-local-web-ui-admin.md</file>
<file size="3603" crc64="0x7e34c25d5606693f">\databox\TOC.yml</file>
```

Diese Datei enthält die Liste aller Dateien, die auf das Data Box- oder Data Box Heavy-Gerät kopiert wurden. In dieser Datei bezieht sich der Wert *crc64* auf die für die entsprechende Datei generierte Prüfsumme.

## <a name="view-available-capacity-of-the-device"></a>Anzeigen der verfügbaren Kapazität des Geräts

Sie können auf dem Dashboard des Geräts die verfügbare und belegte Kapazität des Geräts anzeigen.

1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Dashboard anzeigen**.
2. Unter **Verbindung herstellen und Daten kopieren** wird der freie und belegte Speicherplatz auf dem Gerät angezeigt.

    ![Anzeigen der verfügbaren Kapazität](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)

## <a name="skip-checksum-validation"></a>Überspringen der Überprüfung der Prüfsumme

Prüfsummen werden standardmäßig für Ihre Daten generiert, wenn Sie den Versand vorbereiten. In bestimmten seltenen Fällen kann die Leistung je nach Datentyp (kleine Dateigrößen) schlecht sein. In solchen Fällen können Sie die Prüfsumme überspringen.

Die Prüfsummenberechnung während der Vorbereitung für den Versand wird nur für Importaufträge durchgeführt, nicht für Exportaufträge.

Es wird dringend empfohlen, die Prüfsumme nicht zu deaktivieren, es sei denn, die Leistung wird erheblich beeinträchtigt.

1. Klicken Sie auf der lokalen Webbenutzeroberfläche für Ihr Gerät in der oberen rechten Ecke auf **Einstellungen**.

    ![Prüfsumme deaktivieren](media/data-box-local-web-ui-admin/disable-checksum.png)

2. **Deaktivieren** Sie die Überprüfung der Prüfsumme.
3. Wählen Sie **Übernehmen**.

> [!NOTE]
> Die Option zum Überspringen der Prüfsummenberechnung ist nur verfügbar, wenn das Azure Data Box-Gerät entsperrt ist. Die Option wird nicht angezeigt, wenn das Gerät gesperrt ist.

## <a name="enable-smb-signing"></a>Aktivieren von SMB-Signaturen

Mithilfe der Funktion für SMB-Signaturen (Server Message Block) kann die Kommunikation unter Verwendung von SMB auf Paketebene digital signiert werden. Diese Signaturen verhindern Angriffe, durch die SMB-Pakete während der Übertragung manipuliert werden könnten.

Weitere Informationen zu SMB-Signaturen finden Sie in der [Übersicht zu Server Message Block-Signaturen](https://support.microsoft.com/help/887429/overview-of-server-message-block-signing).

So aktivieren Sie SMB-Signaturen auf Ihrem Azure-Gerät

1. Wählen Sie in der lokalen Webbenutzeroberfläche für Ihr Gerät in der oberen rechten Ecke **Einstellungen** aus.

    ![Öffnen der Einstellungen](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Aktivieren** Sie SMB-Signaturen.

    ![Aktivieren von SMB-Signaturen](media/data-box-local-web-ui-admin/data-box-smb-signing-1.png)

3. Wählen Sie **Übernehmen**.
4. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Herunterfahren oder neu starten**.
5. Wählen Sie **Neu starten** aus.

## <a name="enable-backup-operator-privileges"></a>Aktivieren der Berechtigungen des Typs „Sicherungsoperator“

Die Benutzer der Webbenutzeroberfläche verfügen standardmäßig über Berechtigungen des Typs „Sicherungsoperator“ für SMB-Freigaben. Wenn Sie dies nicht möchten, aktivieren oder deaktivieren Sie die Berechtigungen unter **Enable Back Operator privileges** (Berechtigungen des Typs „Sicherungsoperator“ aktivieren).

Weitere Informationen finden Sie unter „Sicherungsoperatoren“ im Artikel [Active Directory-Sicherheitsgruppen](/windows/security/identity-protection/access-control/active-directory-security-groups#backup-operators).

So aktivieren Sie die Berechtigungen des Typs „Sicherungsoperator“ auf Ihrem Azure-Gerät:

1. Wählen Sie in der lokalen Webbenutzeroberfläche für Ihr Gerät in der oberen rechten Ecke **Einstellungen** aus.

   ![Öffnen der Data Box-Einstellungen 1](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Aktivieren** Sie die Berechtigungen des Typs „Sicherungsoperator“.

   ![Aktivieren der Berechtigungen des Typs „Sicherungsoperator“](media/data-box-local-web-ui-admin/data-box-backup-operator-privileges-1.png)

3. Klicken Sie auf **Anwenden**.
4. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Herunterfahren oder neu starten**.
5. Wählen Sie **Neu starten** aus.

## <a name="enable-acls-for-azure-files"></a>Aktivieren von Zugriffssteuerungslisten für Azure Files

Metadaten für Dateien werden standardmäßig übertragen, wenn Benutzer über das SMB-Protokoll Daten in Ihre Data Box hochladen. Die Metadaten enthalten Zugriffssteuerungslisten (Access Control Lists, ACLs), Dateiattribute und Zeitstempel. Wenn Sie dies nicht möchten, aktivieren oder deaktivieren Sie dieses Feature über **ACLs für Azure-Dateien**.

<!--For more information about metadata that is transferred, see [Preserving the ACLs and metadata with Azure Data Box](./data-box-local-web-ui-admin.md#enable-backup-operator-privileges) - IN DEVELOPMENT-->

> [!Note]
> Zum Übertragen von Metadaten mit Dateien müssen Sie ein Sicherungsoperator sein. Wenn Sie dieses Feature verwenden, sollten Sie sicherstellen, dass es sich bei den lokalen Benutzern der Webbenutzeroberfläche um Sicherungsoperatoren handelt. Weitere Informationen finden Sie unter [Aktivieren von Berechtigungen des Typs „Sicherungsoperator“](#enable-backup-operator-privileges).

So aktivieren Sie die Übertragung von ACLs für Azure-Dateien:

1. Wählen Sie in der lokalen Webbenutzeroberfläche für Ihr Gerät in der oberen rechten Ecke **Einstellungen** aus.

    ![Öffnen der Data Box-Einstellungen 2](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Aktivieren** Sie ACLs für Azure-Dateien.

     ![Aktivieren von ACLs für Azure-Dateien](media/data-box-local-web-ui-admin/data-box-acls-for-azure-files-1.png)
  
3. Wählen Sie **Übernehmen**.
4. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Herunterfahren oder neu starten**.
5. Wählen Sie **Neu starten** aus.

## <a name="enable-tls-11"></a>Aktivieren von TLS 1.1

Azure Data Box nutzt standardmäßig Transport Layer Security (TLS) 1.2 für die Verschlüsselung, da diese eine höhere Sicherheit als TSL 1.1 bietet. Wenn Sie oder Ihre Clients für den Datenzugriff jedoch einen Browser verwenden, der TLS 1.2 nicht unterstützt, können Sie TLS 1.1 aktivieren.

Weitere Informationen zu TLS finden Sie unter [Azure Data Box Gateway – Sicherheit](../databox-gateway/data-box-gateway-security.md).

Führen Sie zum Aktivieren von TLS 1.1 auf Ihrem Azure-Gerät die folgenden Schritte aus:

1. Wählen Sie in der lokalen Webbenutzeroberfläche für Ihr Gerät in der oberen rechten Ecke **Einstellungen** aus.

    ![Öffnen der Data Box-Einstellungen 3](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Aktivieren** Sie TLS 1.1.

    ![Aktivieren von TLS 1.1](media/data-box-local-web-ui-admin/data-box-tls-1-1.png)

3. Wählen Sie **Übernehmen**.
4. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Herunterfahren oder neu starten**.
5. Wählen Sie **Neu starten** aus.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr zum [Verwalten von Data Box und Data Box Heavy im Azure-Portal](data-box-portal-admin.md).

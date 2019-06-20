---
title: Abrufen des Shared Access Signature-URI für Ihr Microsoft Azure-basiertes VM-Image | Azure Marketplace
description: In diesem Artikel wird erläutert, wie Sie den Shared Access Signature-URI (SAS) für Ihre VM-Images abrufen.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 4da82b2f6aaa3fc664d2e91b80722329533b0cd0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938666"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Abrufen des Shared Access Signature-URI für Ihr VM-Image

Während des Veröffentlichungsprozesses müssen Sie einen Uniform Resource Identifier (URI) für jede virtuelle Festplatte (Virtual Hard Disk, VHD) bereitstellen, die Ihren SKUs zugeordnet ist. Microsoft benötigt während des Zertifizierungsprozesses Zugriff auf diese VHDs. In diesem Artikel wird erläutert, wie Sie einen Shared Access Signature-URI (SAS) für jede virtuelle Festplatte generieren. Sie müssen diesen URI im Cloud-Partnerportal auf der Registerkarte **SKUs** eingeben. 

Beachten Sie beim Generieren der SAS-URIs für Ihre virtuellen Festplatten folgende Anforderungen:

- Es werden nur nicht verwaltete virtuelle Festplatten unterstützt.
- `List`- und `Read`-Berechtigungen sind ausreichend. Stellen Sie *keinen* `Write`- oder `Delete`-Zugriff bereit.
- Die Zugriffsdauer (*Ablaufdatum*) sollte mindestens drei Wochen betragen, gerechnet ab dem Erstellungsdatum des SAS-URI.
- Um Fehler aufgrund von Zeitabweichungen von der UTC zu vermeiden, legen Sie das Startdatum auf einen Tag vor dem aktuellen Datum fest. Wählen Sie beispielsweise 05.10.2014 aus, wenn das aktuelle Datum der 6. Oktober 2014 ist.

## <a name="generate-the-sas-url"></a>Generieren des SAS-URI

Der SAS-URI kann auf zwei gängige Arten mithilfe der folgenden Tools generiert werden:

-   Microsoft Azure Storage-Explorer: grafisches Tool für Windows, macOS und Linux
-   Microsoft Azure CLI: empfohlen für Nicht-Windows-Betriebssysteme und automatisierte oder Continuous Integration-Umgebungen


### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Führen Sie zum Generieren eines SAS-URI mit der Azure CLI die folgenden Schritte aus.

1. Laden Sie die [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/) herunter, und installieren Sie sie.  Es stehen Versionen für Windows, macOS und verschiedene Linux-Distributionen zur Verfügung. 
2. Erstellen Sie eine PowerShell-Datei (Dateierweiterung: `.ps1`), kopieren Sie den folgenden Code in die Datei, und speichern Sie sie lokal.

   ``` powershell
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```
    
3. Bearbeiten Sie die Datei, und geben Sie folgende Parameterwerte an.  Datumsangaben sollten im UTC-Datums- und -Uhrzeitformat angegeben werden, z.B. `10-25-2016T00:00:00Z`.
   - `<account-name>`: Der Name Ihres Azure Storage-Kontos.
   - `<account-key>`: Der Schlüssel Ihres Azure Storage-Kontos.
   - `<vhd-name>`: Der Name Ihrer virtuellen Festplatte.
   - `<start-date>`: Das Startdatum der Berechtigung für den Zugriff auf die virtuelle Festplatte. Geben Sie ein Datum an, das einen Tag vor dem aktuellen Datum liegt. 
   - `<expiry-date>`: Das Ablaufdatum der Berechtigung für den Zugriff auf die virtuelle Festplatte.  Geben Sie ein Datum an, das mindestens drei Wochen nach dem aktuellen Datum liegt. 
 
   Das folgende Beispiel zeigt richtige Parameterwerte (zum Zeitpunkt der Erstellung dieses Dokuments).

   ``` powershell
       az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```
 
4. Speichern Sie die Änderungen an diesem PowerShell-Skript.
5. Führen Sie das Skript unter Verwendung von Administratorberechtigungen aus, um eine *SAS-Verbindungszeichenfolge* für den Zugriff auf Containerebene zu generieren.  Sie können zwei grundlegende Vorgehensweisen anwenden:
   - Führen Sie das Skript an der Konsole aus.  Unter Windows klicken Sie mit der rechten Maustaste auf das Skript und wählen **Als Administrator ausführen** aus.
   - Führen Sie das Skript unter Verwendung von Administratorberechtigungen über einen PowerShell-Skript-Editor aus, z.B. die [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). 
     Das folgende Beispiel veranschaulicht das Generieren einer SAS-Verbindungszeichenfolge in diesem Editor. 

     ![Generieren des SAS-URI in der PowerShell ISE](./media/publishvm_032.png)

6. Kopieren Sie die resultierende SAS-Verbindungszeichenfolge, und speichern Sie sie in einer Textdatei in einem sicheren Speicherort.  Bearbeiten Sie diese Zeichenfolge, und fügen Sie die Informationen zum Standort der zugehörigen virtuellen Festplatte hinzu, um den endgültigen SAS-URI zu erstellen. 
7. Navigieren Sie im Azure-Portal zu dem Blobspeicher, der die mit diesem neu generierten URI verknüpfte virtuelle Festplatte enthält.
8. Kopieren Sie den URL-Wert des **Blobdienst-Endpunkts**, wie unten gezeigt.

    ![Blobdienst-Endpunkt im Azure-Portal](./media/publishvm_033.png)

9. Bearbeiten Sie die Textdatei mit der SAS-Verbindungszeichenfolge aus Schritt 6.  Sie erstellen den vollständigen SAS-URI in folgendem Format:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Wenn der Name der virtuellen Festplatte z.B. `TestRGVM2.vhd` lautet, sieht der resultierende SAS-URI folgendermaßen aus:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Wiederholen Sie diese Schritte für jede virtuelle Festplatte in den SKUs, die Sie veröffentlichen möchten.


### <a name="microsoft-storage-explorer"></a>Microsoft Storage-Explorer

Führen Sie die folgenden Schritte aus, um einen SAS-URI mit dem Microsoft Azure Storage-Explorer zu generieren.

1. Laden Sie den [Microsoft Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) herunter, und installieren Sie ihn.
2. Öffnen Sie den Explorer, und klicken Sie in der linken Menüleiste auf das Symbol **Konto hinzufügen**.  Das Dialogfeld **Verbindung mit Azure-Speicher herstellen** wird angezeigt.
3. Wählen Sie **Azure-Konto hinzufügen**, und klicken Sie auf **Anmelden**.  Führen Sie die erforderlichen Schritte aus, um sich bei Ihrem Azure-Konto anzumelden.
4. Navigieren Sie im linken **Explorer**-Bereich zu Ihren **Speicherkonten**, und erweitern Sie diesen Knoten.
5. Klicken Sie mit der rechten Maustaste auf Ihre virtuelle Festplatte, und wählen Sie aus dem Kontextmenü die Option **Shared Access Signature abrufen** aus. 

    ![Menüelement zum Abrufen der SAS im Azure-Explorer](./media/publishvm_034.png)

6. Das **Shared Access Signature**-Dialogfeld wird angezeigt. Geben Sie Werte für die folgenden Felder ein:
   - **Startzeit**: Das Startdatum der Berechtigung für den Zugriff auf die virtuelle Festplatte. Geben Sie ein Datum an, das einen Tag vor dem aktuellen Datum liegt.
   - **Ablaufzeit**: Das Ablaufdatum der Berechtigung für den Zugriff auf die virtuelle Festplatte.  Geben Sie ein Datum an, das mindestens drei Wochen nach dem aktuellen Datum liegt.
   - **Berechtigungen**: Wählen Sie die Berechtigungen `Read` und `List` aus. 

     ![SAS-Dialogfeld im Azure-Explorer](./media/publishvm_035.png)

7. Klicken Sie auf **Erstellen**, um den zugehörigen SAS-URI für diese virtuelle Festplatte zu erstellen.  Das Dialogfeld zeigt Details zu diesem Vorgang an. 
8. Kopieren Sie den **URL**-Wert, und speichern Sie ihn in einer Textdatei in einem sicheren Speicherort. 

    ![Erstellen des SAS-URI im Azure-Explorer](./media/publishvm_036.png)

    Damit wurde der SAS-URI für den Zugriff auf Containerebene generiert.  Um den URI genauer zu bestimmen, muss der Name der zugeordneten virtuellen Festplatte hinzugefügt werden.

9. Bearbeiten Sie die Textdatei. Fügen Sie den Namen Ihrer virtuellen Festplatte nach der Zeichenfolge `vhds` in den SAS-URI ein (einschließlich Schrägstrich).  Der endgültige SAS-URI sollte in folgendem Format vorliegen:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Wenn der Name der virtuellen Festplatte z.B. `TestRGVM2.vhd` lautet, sieht der resultierende SAS-URI folgendermaßen aus:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Wiederholen Sie diese Schritte für jede virtuelle Festplatte in den SKUs, die Sie veröffentlichen möchten.


## <a name="verify-the-sas-uri"></a>Überprüfen des SAS-URI

Überprüfen und verifizieren Sie jeden generierten SAS-URI anhand der folgenden Checkliste.  Verifizieren Sie Folgendes:
- Der URI weist folgendes Format auf: `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
- Der URI enthält den Namen Ihres VHD-Images, einschließlich der Dateierweiterung „.vhd“.
- In der Mitte des URI wird `sp=rl` angezeigt. Diese Zeichenfolge gibt an, dass die Zugriffsberechtigungen `Read` und `List` festgelegt sind.
- Danach wird `sr=c` angezeigt. Diese Zeichenfolge gibt an, dass der Zugriff auf Containerebene festgelegt ist.
- Kopieren Sie den URI, und fügen Sie ihn in einen Browser ein, um mit dem Herunterladen des zugehörigen Blobs zu beginnen.  (Sie können den Vorgang abbrechen, bevor der Download abgeschlossen ist.)


## <a name="next-steps"></a>Nächste Schritte

Wenn beim Generieren des SAS-URI Probleme auftreten, lesen Sie den Artikel [Allgemeine Probleme mit dem SAS-URI](./cpp-common-sas-url-issues.md).  Andernfalls speichern Sie den SAS-URI in einem sicheren Speicherort zur späteren Verwendung. Er wird benötigt, um Ihr [VM-Angebot im Cloud-Partnerportal zu veröffentlichen](./cpp-publish-offer.md).

---
title: 'Schnellstart: Verwalten von Azure-Dateifreigaben mit dem Azure-Portal'
description: Hier erfahren Sie, wie Sie Azure-Dateifreigaben im Azure-Portal erstellen und verwalten. Erstellen Sie ein Speicherkonto und eine Azure-Dateifreigabe, und verwenden Sie die Azure-Dateifreigabe.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6a88124397812f7599ce54b46b23d22e626cf520
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629817"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-the-azure-portal"></a>Schnellstart: Erstellen und Verwalten von Azure-Dateifreigaben mit dem Azure-Portal 
[Azure Files](storage-files-introduction.md) ist das benutzerfreundliche Clouddateisystem von Microsoft. Azure-Dateifreigaben können unter Windows, Linux und macOS bereitgestellt werden. In dieser Anleitung werden die Grundlagen der Arbeit mit Azure-Dateifreigaben mit dem [Azure-Portal](https://portal.azure.com/) Schritt für Schritt beschrieben.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="create-a-storage-account"></a>Erstellen eines Speicherkontos
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-an-azure-file-share"></a>Erstellen einer Azure-Dateifreigabe
So erstellen Sie eine Azure-Dateifreigabe:

1. Wählen Sie im Dashboard das Speicherkonto aus.
2. Wählen Sie auf der Speicherkontoseite im Abschnitt **Dienste** die Option **Dateien**.
    ![Screenshot: Abschnitt „Dienste“ des Speicherkontos, Auswahl des Diensts „Dateien“](media/storage-how-to-use-files-portal/create-file-share-1.png)

3. Klicken Sie im Menü oben auf der Seite **Dateidienst** auf **Dateifreigabe**. Die Seite **Neue Dateifreigabe** wird angezeigt.
4. Geben Sie unter **Name** den Namen *myshare* ein.
5. Klicken Sie auf **OK**, um die Azure-Dateifreigabe zu erstellen.

Freigabenamen dürfen nur Kleinbuchstaben, Zahlen und einzelne Bindestriche enthalten und dürfen nicht mit einem Bindestrich beginnen. Ausführliche Informationen zur Benennung von Dateifreigaben und Dateien finden Sie unter [Benennen und Referenzieren von Freigaben, Verzeichnissen, Dateien und Metadaten](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Verwenden Ihrer Azure-Dateifreigabe
Azure Files bietet drei Methoden für die Arbeit mit Dateien und Ordnern in Ihrer Azure-Dateifreigabe: das [SMB-Protokoll (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) nach Industriestandard, das NFS-Protokoll (Network File System, Vorschau) und das [REST-Protokoll „File“](/rest/api/storageservices/file-service-rest-api). 

Informationen zum Einbinden einer Dateifreigabe mit SMB finden Sie im folgenden Dokument für Ihr jeweiliges Betriebssystem:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-from-the-azure-portal"></a>Verwenden einer Azure-Dateifreigabe über das Azure-Portal
Alle Anforderungen über das Azure-Portal werden mit der Datei-REST-API durchgeführt, sodass Sie Dateien und Verzeichnisse ohne SMB-Zugriff erstellen, ändern und löschen können. Das REST-Protokoll „File“ kann direkt verwendet werden (d.h. Sie können REST-HTTP-Aufrufe selbst erstellen). Neben dem Azure-Portal wird häufig auch das [Azure PowerShell-Modul](storage-how-to-use-files-powershell.md), die [Azure-Befehlszeilenschnittstelle](storage-how-to-use-files-cli.md) oder ein Azure Storage SDK verwendet. Bei allen diesen Methoden steht ein praktischer Wrapper um das REST-Protokoll „File“ in der Skript-/Programmiersprache Ihrer Wahl zur Verfügung. 

Die meisten Benutzer werden ihre Azure-Dateifreigabe in Azure Files wahrscheinlich über das SMB-Protokoll nutzen, da sie so die bereits vorhandenen Anwendungen und Tools verwenden können, die sie erwarten. Es gibt jedoch auch Gründe, die für die Verwendung der REST-API „File“ sprechen:

- Sie müssen unterwegs schnell eine kleine Änderung an Ihrer Azure-Dateifreigabe vornehmen (beispielsweise über einen Laptop ohne SMB-Zugriff, über ein Tablet oder über ein mobiles Gerät).
- Sie müssen ein Skript oder eine Anwendung über einen Client ausführen, der keine SMB-Freigabe einbinden kann (beispielsweise ein lokaler Client, für den die Blockierung von Port 445 nicht aufgehoben wurde).
- Sie nutzen serverlose Ressourcen (beispielsweise [Azure Functions](../../azure-functions/functions-overview.md)). 

Die folgenden Beispiele zeigen, wie Sie über das Azure-Portal Ihre Azure-Dateifreigabe mit dem REST-Protokoll „File“ ändern. 

Nachdem Sie nun eine Azure-Dateifreigabe erstellt haben, können Sie die Dateifreigabe mit SMB unter [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) oder [macOS](storage-how-to-use-files-mac.md) bereitstellen. Alternativ hierzu können Sie mit Ihrer Azure-Dateifreigabe im Azure-Portal arbeiten. 

#### <a name="create-a-directory"></a>Erstellen eines Verzeichnisses
Gehen Sie wie folgt vor, um im Stammverzeichnis Ihrer Azure-Dateifreigabe ein neues Verzeichnis mit dem Namen *myDirectory* zu erstellen:

1. Wählen Sie auf der Seite **Dateidienst** die Dateifreigabe **myshare** aus. Die Seite für Ihre Dateifreigabe wird geöffnet.
2. Wählen Sie im Menü oben auf der Seite die Option **+ Verzeichnis hinzufügen**. Die Seite **Neues Verzeichnis** wird angezeigt.
3. Geben Sie *myDirectory* ein, und klicken Sie anschließend auf **OK**.

#### <a name="upload-a-file"></a>Hochladen einer Datei 
Um das Hochladen einer Datei demonstrieren zu können, müssen Sie zuerst eine Datei erstellen oder auswählen, die hochgeladen werden kann. Hierfür können Sie die von Ihnen bevorzugte Vorgehensweise verwenden. Gehen Sie wie folgt vor, nachdem Sie die gewünschte Datei zum Hochladen ausgewählt haben:

1. Klicken Sie auf das Verzeichnis **myDirectory**. Das Panel **myDirectory** wird geöffnet.
2. Klicken Sie oben im Menü auf **Hochladen**. Das Panel **Dateien hochladen** wird geöffnet.  
    ![Screenshot: Panel zum Hochladen von Dateien](media/storage-how-to-use-files-portal/upload-file-1.png)

3. Klicken Sie auf das Ordnersymbol, um ein Fenster zu öffnen, in dem Sie Ihre lokalen Dateien durchsuchen können. 
4. Wählen Sie eine Datei aus, und klicken Sie anschließend auf **Öffnen**. 
5. Überprüfen Sie auf der Seite **Dateien hochladen** den Dateinamen, und klicken Sie anschließend auf **Hochladen**.
6. Nach Abschluss des Vorgangs sollte die Datei in der Liste auf der Seite **myDirectory** angezeigt werden.

#### <a name="download-a-file"></a>Herunterladen einer Datei
Sie können eine Kopie der hochgeladenen Datei herunterladen, indem Sie mit der rechten Maustaste auf die Datei klicken. Nachdem Sie auf die Schaltfläche zum Herunterladen geklickt haben, richtet sich die genaue Vorgehensweise nach dem verwendeten Betriebssystem und Browser.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Was ist Azure Files?](storage-files-introduction.md)
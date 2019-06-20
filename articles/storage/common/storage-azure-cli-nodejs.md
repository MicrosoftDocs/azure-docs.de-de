---
title: Verwenden der klassischen Azure CLI mit Azure Storage| Microsoft Docs
description: Erfahren Sie, wie Sie die klassische Azure-Befehlszeilenschnittstelle (CLI) mit Azure Storage verwenden, um Speicherkonten zu erstellen und zu verwalten sowie mit Azure-Blobs und -Dateien zu arbeiten.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/30/2017
ms.author: tamram
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 88f713c5695e2453edc58d072899aa417f0514af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147040"
---
# <a name="using-the-azure-classic-cli-with-azure-storage"></a>Verwenden der klassischen Azure CLI mit Azure Storage

## <a name="overview"></a>Übersicht

Die klassische Azure CLI stellt eine Reihe von plattformübergreifenden Open Source-Befehlen für die Arbeit mit der Azure-Plattform bereit. Sie bietet im Wesentlichen die gleiche Funktionalität wie das [Azure-Portal](https://portal.azure.com) sowie umfangreiche Datenzugriffsfunktionen.

Diese Anleitung enthält Informationen zur Verwendung der [klassischen Azure CLI](../../cli-install-nodejs.md) zum Ausführen einer Vielzahl von Entwicklungs- und Verwaltungsaufgaben mit Azure Storage. Sie sollten die neueste klassische Azure CLI herunterladen und installieren bzw. ein Upgrade durchführen, bevor Sie diese Anleitung verwenden.

Diese Anleitung setzt voraus, dass Sie die grundlegenden Konzepte von Azure Storage verstehen. Die Anleitung bietet eine Reihe von Skripts, um die Verwendung der klassischen Azure CLI mit Azure Storage zu veranschaulichen. Sie müssen die Skriptvariablen auf Basis Ihrer Konfiguration aktualisieren, bevor Sie die jeweiligen Skripts ausführen.

> [!NOTE]
> Die Anleitung enthält die Befehle der klassischen Azure CLI und Skriptbeispiele für klassische Speicherkonten. Befehle der klassischen Azure CLI für Resource Manager-Speicherkonten finden Sie unter [Verwenden der klassischen Azure CLI für Mac, Linux und Windows mit der Azure-Ressourcenverwaltung](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects).
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-classic-cli-in-5-minutes"></a>Erste Schritte mit Azure Storage und der klassischen Azure CLI in 5 Minuten
Diese Anleitung verwendet für Beispiele Ubuntu, andere Betriebssystemplattformen funktionieren jedoch auf ähnliche Weise.

**Neu in Azure:** Holen Sie Sich ein Microsoft Azure-Abonnement und ein Microsoft-Konto für dieses Abonnement. Informationen zu Azure-Kaufoptionen finden Sie unter [Kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/), [Kaufoptionen](https://azure.microsoft.com/pricing/purchase-options/) und [Angebote für Mitglieder](https://azure.microsoft.com/pricing/member-offers/) (für Mitglieder von MSDN, Microsoft Partner Network, BizSpark und anderen Microsoft-Programmen).

Unter [Zuweisen von Administratorrollen in Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) finden Sie weitere Informationen zu Azure-Abonnements.

**Nach der Erstellung eines Microsoft Azure-Abonnements und eines Kontos:**

1. Laden Sie die klassische Azure CLI herunterladen und installieren Sie sie gemäß den Anweisungen unter [Installieren der klassischen Azure CLI](../../cli-install-nodejs.md).
2. Sobald die klassische CLI installiert ist, können Sie den Befehl „azure“ in Ihrer Befehlszeilenschnittstelle (Bash, Terminal, Eingabeaufforderung) verwenden, um auf die Befehle der klassischen Azure CLI zuzugreifen. Wenn Sie den Befehl _azure_ eingeben, sollte die folgende Ausgabe angezeigt werden.

    ![Azure-Befehlsausgabe:](./media/storage-azure-cli/azure_command.png)   
3. Geben Sie in der Befehlszeilenschnittstelle `azure storage` ein, um alle Azure Storage-Befehle aufzulisten und einen ersten Eindruck vom Funktionsumfang der klassischen Azure CLI zu erhalten. Sie können Befehlsnamen mit einem **-h**-Parameter (z. B. `azure storage share create -h`) eingeben , um Details der Befehlssyntax anzuzeigen.
4. In einem einfachen Skript werden nun grundlegende Befehle der klassischen Azure CLI für den Zugriff auf Azure Storage dargestellt. In dem Skript werden Sie als erstes dazu aufgefordert, zwei Variablen für Ihr Speicherkonto und Ihren Speicherschlüssel festzulegen. Als Nächstes erstellt das Skript in dem neuen Speicherkonto einen neuen Container und lädt eine vorhandene Image-Datei (Blob) in diesen Container. Nachdem das Skript alle Blobs in diesem Container aufgelistet hat, lädt es die Image-Datei in ein Zielverzeichnis auf dem lokalen Computer herunter.

    ```azurecli
    #!/bin/bash
    # A simple Azure storage example

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export image_to_upload=<image_to_upload>
    export destination_folder=<destination_folder>

    echo "Creating the container..."
    azure storage container create $container_name

    echo "Uploading the image..."
    azure storage blob upload $image_to_upload $container_name $blob_name

    echo "Listing the blobs..."
    azure storage blob list $container_name

    echo "Downloading the image..."
    azure storage blob download $container_name $blob_name $destination_folder

    echo "Done"
    ```

5. Öffnen Sie auf Ihrem lokalen Computer Ihren bevorzugten Texteditor (z. B. Vim). Geben Sie das obige Skript in den Texteditor ein.
6. Nun müssen Sie die Skriptvariablen auf Basis der Konfigurationseinstellungen aktualisieren.

   * **&lt;storage_account_name&gt;** : Verwenden Sie den im Skript angegebenen Namen, oder geben Sie einen neuen Namen für Ihr Speicherkonto ein. **Wichtig:** Der Name des Speicherkontos muss in Azure eindeutig sein. Er darf außerdem nur aus Kleinbuchstaben bestehen!
   * **&lt;storage_account_key&gt;** : Der Zugriffsschlüssel für Ihr Speicherkonto.
   * **&lt;container_name&gt;** : Verwenden Sie den im Skript angegebenen Namen, oder geben Sie einen neuen Namen für Ihren Container ein.
   * **&lt;image_to_upload&gt;** : Geben Sie einen Pfad zu einem Bild auf dem lokalen Computer ein. Beispiel: „~/images/HelloWorld.png“.
   * **&lt;destination_folder&gt;** : Geben Sie den Pfad zu einem lokalen Verzeichnis zum Speichern von Dateien ein, die aus Azure Storage heruntergeladen werden. Beispiel: „~/downloadImages“.
7. Nachdem Sie die erforderlichen Variablen in Vim aktualisiert haben, drücken Sie die Tastenkombinationen `ESC`, `:`, `wq!`, um das Skript zu speichern.
8. Zum Ausführen des Skripts geben Sie einfach den Namen der Skripdatei in der Bash-Konsole ein. Nachdem das Skript ausgeführt wird, sollten Sie über einen lokalen Zielordner verfügen, der die heruntergeladene Image-Datei enthält. Der folgende Screenshot zeigt eine Beispielausgabe:

Nachdem das Skript ausgeführt wird, sollten Sie über einen lokalen Ordner verfügen, der die heruntergeladene Datei enthält.

## <a name="manage-storage-accounts-with-the-azure-classic-cli"></a>Verwalten von Speicherkonten mit der klassischen Azure CLI
### <a name="connect-to-your-azure-subscription"></a>Verbinden mit Ihrem Azure-Abonnement
Obwohl die meisten Speicherbefehle ohne Azure-Abonnement funktionieren, empfehlen wir Ihnen, eine Verbindung zu Ihrem Abonnement über die klassische Azure CLI herzustellen.

### <a name="create-a-new-storage-account"></a>Erstellen eines neuen Speicherkontos
Für die Verwendung von Azure-Speicher benötigen Sie ein Speicherkonto. Nachdem Sie Ihren Computer für die Verbindung mit Ihrem Abonnement konfiguriert haben, können Sie ein neues Azure-Speicherkonto erstellen.

```azurecli
azure storage account create <account_name>
```

Der Name Ihres Speicherkontos muss zwischen 3 und 24 Zeichen lang sein und darf nur Zahlen und Kleinbuchstaben enthalten.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Festlegen eines Azure-Standardspeicherkontos in Umgebungsvariablen
Sie können mehrere Speicherkonten in Ihrem Abonnement verwenden. Sie können ein Speicherkonto auswählen und es in den Umgebungsvariablen als Standardspeicherkonto für alle Speicherbefehle in einer Sitzung festlegen. Dadurch können Sie die Speicherbefehle der klassischen CLI ohne explizite Angabe des Speicherkontos und des Speicherschlüssels ausführen.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Eine weitere Möglichkeit zum Festlegen eines Standardspeicherkontos ist die Verwendung einer Verbindungszeichenfolge. Rufen Sie zuerst die Verbindungszeichenfolge per Befehl ab:

```azurecli
azure storage account connectionstring show <account_name>
```

Kopieren Sie dann die Ausgabeverbindungszeichenfolge und legen Sie sie auf die Umgebungsvariable fest:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>Erstellen und Verwalten von Blobs
Azure Blob Storage ist ein Dienst zur Speicherung großer Mengen unstrukturierter Daten, beispielsweise Text- oder Binärdaten, auf die von überall auf der Welt über HTTP oder HTTPS zugegriffen werden kann. Dieser Abschnitt setzt voraus, dass Sie mit den Konzepten von Azure Blob Storage bereits vertraut sind. Ausführliche Informationen finden Sie unter [Erste Schritte mit Azure Blob Storage mit .NET](../blobs/storage-dotnet-how-to-use-blobs.md) und [Konzepte des Blob-Diensts](https://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Erstellen eines Containers
Jeder Blob im Azure-Speicher muss sich in einem Container befinden. Mit dem Befehl `azure storage container create` können Sie einen privaten Container erstellen:

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> Es gibt drei Ebenen des anonymen Lesezugriffs: **Off**, **Blob** und **Container**. Legen Sie für den Parameter "Permission" den Wert **Off**fest, um den anonymen Zugriff auf Blobs zu verhindern. Der neue Container ist standardmäßig privat, und der Zugriff ist ausschließlich dem Kontobesitzer gestattet. Um den anonymen öffentlichen Lesezugriff auf Blob-Ressourcen, jedoch nicht auf Containermetadaten oder die Liste der im Container enthaltenen Blobs zuzulassen, legen Sie für den Parameter "Permission" den Wert **Blob**fest. Um den vollständigen öffentlichen Lesezugriff auf Blob-Ressourcen, Containermetadaten und die Liste der im Container enthaltenen Blobs zuzulassen, legen Sie für den Parameter "Permission" den Wert **Container**fest. Weitere Informationen finden Sie unter [Verwalten des anonymen Lesezugriffs auf Container und Blobs](../blobs/storage-manage-access-to-resources.md).
>
>

### <a name="upload-a-blob-into-a-container"></a>Hochladen eines Blobs in einen Container
Azure Blob Storage unterstützt Block- und Seitenblobs. Weitere Informationen finden Sie unter [Grundlagen zu Blockblobs, Anfügeblobs und Seitenblobs](https://msdn.microsoft.com/library/azure/ee691964.aspx).

Um Blobs in einen Container hochzuladen, können Sie den Befehl `azure storage blob upload`verwenden. Dieser Befehl lädt standardmäßig die lokalen Dateien in einen Blockblob hoch. Um den Blob-Typ anzugeben, können Sie den Parameter `--blobtype` verwenden.

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>Herunterladen von Blobs aus einem Container
Im folgenden Beispiel wird veranschaulicht, wie Blobs aus einem Container heruntergeladen werden.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>Kopieren von Blobs
Sie können Blobs innerhalb oder zwischen Speicherkonten und Regionen asynchron kopieren.

Im folgenden Beispiel wird veranschaulicht, wie Sie Blobs von einem Speicherkonto in ein anderes kopieren. In diesem Beispiel erstellen wir einen Container, in dem Blobs öffentlich sind und anonym darauf zugegriffen werden kann.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

In diesem Beispiel wird ein asynchroner Kopiervorgang ausgeführt. Sie können den Status jedes Kopiervorgangs überwachen, indem Sie den Vorgang `azure storage blob copy show` ausführen.

Beachten Sie, dass die für den Kopiervorgang bereitgestellte Quell-URL entweder öffentlich zugänglich sein muss oder ein SAS-Token (Token für die freigegebene Zugriffssignatur) enthalten muss.

### <a name="delete-a-blob"></a>Löschen eines BLOBs
Verwenden Sie den nachfolgenden Befehl, um ein Blob zu löschen.

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>Erstellen und Verwalten von Dateifreigaben
Azure Files bietet einen gemeinsam genutzten Speicher für Anwendungen und verwendet das Standardprotokoll SMB. Microsoft Azure Virtual Machines und Clouddienste können wie lokale Anwendungen Dateidaten mithilfe bereitgestellter Freigaben teilen. Dateifreigaben und Dateidaten können über die klassische CLI verwaltet werden. Weitere Informationen zu Azure Files finden Sie unter [Einführung in Azure Files](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Erstellen einer Dateifreigabe
Eine Azure-Dateifreigabe ist eine SMB-Dateifreigabe in Azure. Alle Verzeichnisse und Dateien müssen in einer Dateifreigabe erstellt werden. Ein Konto kann eine unbegrenzte Anzahl von Freigaben enthalten, und eine Freigabe kann eine unbegrenzte Anzahl von Dateien speichern, bis die Kapazitätsgrenze des Speicherkontos erreicht ist. Im folgenden Beispiel wird eine Dateifreigabe namens **myshare**erstellt.

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>Erstellen eines Verzeichnisses
Ein Verzeichnis enthält eine optionale hierarchische Struktur für eine Azure-Dateifreigabe. Im folgenden Beispiel wird ein Verzeichnis namens **myDir** in der Dateifreigabe erstellt.

```azurecli
azure storage directory create myshare myDir
```

Beachten Sie, dass dieser Verzeichnispfad mehrere Ebenen enthalten kann, *z.B.* , **a/b**. Sie müssen allerdings sicherstellen, dass alle übergeordneten Verzeichnisse vorhanden sind. Zum Beispiel müssen Sie für Pfad **a/b**, zuerst Verzeichnis **a** und anschließend Verzeichnis **b** erstellen.

### <a name="upload-a-local-file-to-directory"></a>Hochladen einer lokalen Datei in das Verzeichnis
Im folgenden Beispiel wird eine Datei aus **~/temp/samplefile.txt** in das Verzeichnis **myDir** hochgeladen. Bearbeiten Sie den Dateipfad so, dass er auf eine gültige Datei auf Ihrem lokalen Computer verweist:

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

Beachten Sie, dass eine Datei in der Freigabe bis zu 1 TB groß sein kann.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Auflisten der Dateien im Freigabestamm oder Verzeichnis
Sie können Sie die Dateien und Unterverzeichnisse in einem Freigabestamm oder einem Verzeichnis mit dem folgenden Befehl auflisten:

```azurecli
azure storage file list myshare myDir
```

Beachten Sie, dass der Name des Verzeichnisses für den Auflistungsvorgang optional ist. Wenn kein Namen angegeben wird, listet der Befehl den Inhalt des Stammverzeichnisses der Freigabe auf.

### <a name="copy-files"></a>Kopieren von Dateien
Ab Version 0.9.8 der klassischen CLI können Sie eine Datei in eine andere Datei, eine Datei in ein Blob oder ein Blob in eine Datei kopieren. Im Folgenden wird demonstriert, wie diese Kopiervorgänge mithilfe von CLI-Befehlen ausgeführt werden können. So kopieren Sie eine Datei in das neue Verzeichnis:

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

So kopieren Sie ein Blob in ein Dateiverzeichnis:

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>Nächste Schritte

Sie finden die Befehlsreferenz zur klassischen Azure CLI für die Arbeit mit Speicherressourcen hier:

* [Befehle der klassischen Azure CLI im Resource Manager-Modus](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects)
* [Befehle der klassischen Azure CLI im Modus „Azure-Dienstverwaltung“](../../cli-install-nodejs.md)

Probieren Sie auch die aktuelle Version der [Azure CLI](../storage-azure-cli.md) zur Verwendung mit dem Resource Manager-Bereitstellungsmodell aus.

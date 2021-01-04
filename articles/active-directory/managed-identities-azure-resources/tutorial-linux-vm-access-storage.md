---
title: Tutorial`:` Verwenden einer verwalteten Identität für den Zugriff auf Azure Storage – Linux – Azure AD
description: In diesem Tutorial erfahren Sie, wie Sie eine systemseitig zugewiesene verwaltete Identität eines virtuellen Linux-Computers verwenden, um auf Azure Storage zuzugreifen.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/23/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: c093dcff46676dc5f8a25974c3c38c74ae7666b7
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546686"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage"></a>Tutorial: Verwenden einer systemseitig zugewiesenen verwalteten Identität eines virtuellen Linux-Computers für den Zugriff auf Azure Storage 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

In diesem Tutorial erfahren Sie, wie Sie eine systemseitig zugewiesene verwaltete Identität für einen virtuellen Linux-Computer verwenden, um auf Azure Storage zuzugreifen. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Speicherkonto erstellen
> * Erstellen eines Blobcontainers im Speicherkonto
> * Gewähren Sie der verwalteten Identität der Linux-VM Zugriff auf einen Azure Storage-Container.
> * Erhalten eines Zugriffstokens und seine Verwendung zum Aufrufen von Azure Storage

> [!NOTE]
> Die Azure Active Directory-Authentifizierung für Azure Storage liegt in der Public Preview-Version vor.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

Für die Ausführung der CLI-Beispielskripts in diesem Tutorial stehen Ihnen zwei Möglichkeiten zur Verfügung:

- Verwenden Sie [Azure Cloud Shell](~/articles/cloud-shell/overview.md) entweder vom Azure-Portal aus oder über die Schaltfläche **Ausprobieren** , die sich in der rechten oberen Ecke eines jeden Codeblocks befindet.
- [Installieren Sie die neueste Version von CLI 2.0](/cli/azure/install-azure-cli) (2.0.23 oder höher), wenn Sie lieber eine lokale CLI-Konsole verwenden möchten.

## <a name="create-a-storage-account"></a>Speicherkonto erstellen 

In diesem Abschnitt erstellen Sie ein Speicherkonto. 

1. Klicken Sie in der linken oberen Ecke des Azure-Portals auf die Schaltfläche **+ Ressource erstellen**.
2. Wählen Sie die Option **Speicher** und anschließend **Speicherkonto – Blob, Datei, Tabelle, Warteschlange** aus.
3. Geben Sie unter **Name** einen Namen für das Speicherkonto ein.  
4. **Bereitstellungsmodell** und **Kontoart** sollten jeweils auf **Resource Manager** und **Storage (universell, Version 1)** festgelegt werden. 
5. Stellen Sie sicher, dass **Abonnement** und **Ressourcengruppe** dem entsprechen, was Sie bei der Erstellung Ihrer VM im vorherigen Schritt angegeben haben.
6. Klicken Sie auf **Erstellen**.

    ![Erstellen eines neuen Speicherkontos](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Erstellen eines Blobcontainers und Hochladen einer Datei in das Speicherkonto

Da Dateien Blob Storage erfordern, müssen wir einen Blobcontainer erstellen, in dem die Datei gespeichert wird. Anschließend laden Sie eine Datei in den Blobcontainer im neuen Speicherkonto hoch.

1. Navigieren Sie zurück zum neu erstellten Speicherkonto.
2. Klicken Sie unter **Blob-Dienst** auf **Container**.
3. Klicken Sie oben auf der Seite auf **+ Container**.
4. Geben Sie unter **Neuer Container** einen Namen für den Container ein, und behalten Sie unter **Öffentliche Zugriffsebene** den Standardwert bei.

    ![Erstellen eines Speichercontainers](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Erstellen Sie mit einem Editor Ihrer Wahl eine Datei mit dem Titel *hall_ welt.txt* auf dem lokalen Computer.  Öffnen Sie die Datei, fügen Sie den Text „Hallo Welt! :)“ (ohne Anführungszeichen) hinzu, und speichern Sie ihn. 

6. Laden Sie die Datei in den neu erstellten Container hoch, indem Sie auf den Containernamen und dann auf **Hochladen** klicken.
7. Klicken Sie im Bereich **Blob hochladen** unter **Dateien** auf das Ordnersymbol und navigieren Sie zur Datei **hallo_welt.txt** auf dem lokalen Computer. Wählen Sie dann die Datei aus, und klicken Sie auf **Hochladen**.

    ![Hochladen einer Textdatei](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>Gewähren des Zugriffs auf einen Azure-Speichercontainer für Ihre VM 

Sie können die verwaltete Identität des virtuellen Computers verwenden, um die Daten in Azure Storage Blob abzurufen.

>[!NOTE]
> Weitere Informationen zu den verschiedenen Rollen, die Sie zum Gewähren von Berechtigungen für den Speicher verwenden können, finden Sie unter [Autorisieren des Zugriffs auf Blobs und Warteschlangen mit Azure Active Directory](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights).

1. Navigieren Sie zurück zum neu erstellten Speicherkonto.  
2. Klicken Sie im linken Bereich auf den Link **Zugriffssteuerung (IAM)** .  
3. Klicken Sie oben auf der Seite auf **+ Rollenzuweisung hinzufügen** , um dem virtuellen Computer eine neue Rollenzuweisung hinzuzufügen.
4. Wählen Sie unter **Rolle** in der Dropdownliste **Storage-Blobdatenleser** aus. 
5. Wählen Sie in der nächsten Dropdownliste unter **Zugriff zuweisen zu** die Option **Virtueller Computer** aus.  
6. Stellen Sie im nächsten Schritt sicher, dass das richtige Abonnement in der Dropdownliste **Abonnement** aufgeführt ist, und legen Sie dann für **Ressourcengruppe** die Option **Alle Ressourcengruppen** fest.  
7. Wählen Sie unter **Auswählen** Ihren virtuellen Computer aus, und klicken Sie dann auf **Speichern**.

    ![Zuweisen von Berechtigungen](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Erhalten eines Zugriffstokens und seine Verwendung zum Aufrufen von Azure Storage

Azure Storage unterstützt die Azure AD-Authentifizierung nativ, sodass Zugriffstoken, die mit der MSI abgerufen wurden, direkt angenommen werden können. Dieser Umstand ist Teil der Azure Storage-Integration in Azure AD und unterscheidet sich vom Bereitstellen von Anmeldeinformationen in der Verbindungszeichenfolge.

Um die folgenden Schritte abzuschließen, müssen Sie von der zuvor erstellten VM aus arbeiten. Außerdem benötigen Sie einen SSH-Client, der eine Verbindung damit herstellt. Wenn Sie Windows verwenden, können Sie den SSH-Client im [Windows-Subsystem für Linux](/windows/wsl/about) verwenden. Wenn Sie Hilfe beim Konfigurieren der SSH-Clientschlüssel benötigen, lesen Sie die Informationen unter [Vorgehensweise: Verwenden von SSH-Schlüsseln mit Windows in Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) oder [Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. Navigieren Sie im Azure-Portal zu **Virtuelle Computer** , wechseln Sie zu Ihrem virtuellen Linux-Computer, und klicken Sie dann auf der Seite **Übersicht** auf **Verbinden**. Kopieren Sie die Zeichenfolge, um eine Verbindung mit Ihrem virtuellen Computer herzustellen.
2. **Verbinden** Sie den virtuellen Computer mit dem gewünschten SSH-Client. 
3. Übermitteln Sie im Terminalfenster mit der cURL eine Anforderung an den lokalen Endpunkt der verwalteten Identität zum Abrufen eines Zugriffstokens für Azure Storage.
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com%2F' -H Metadata:true
    ```
4. Greifen Sie nun mithilfe des Zugriffstokens auf Azure Storage zu, z.B. zum Lesen der Inhalte der Beispieldatei, die Sie zuvor in den Container hochgeladen haben. Ersetzen Sie `<STORAGE ACCOUNT>`, `<CONTAINER NAME>` und `<FILE NAME>` durch die Werte, die Sie zuvor angegeben haben, und `<ACCESS TOKEN>` durch den im vorherigen Schritt zurückgegebenen Token.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Die Antwort enthält den Inhalt der Datei:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie eine systemseitig zugewiesene verwaltete Identität eines virtuellen Linux-Computers für den Zugriff auf Azure Storage aktivieren.  Weitere Informationen zu Azure Storage finden Sie hier:

> [!div class="nextstepaction"]
> [Azure Storage (in englischer Sprache)](../../storage/common/storage-introduction.md)
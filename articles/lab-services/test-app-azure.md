---
title: Testen Ihrer App in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Dateifreigabe in einem Lab erstellen und auf Ihrem lokalen Computer und auf einem virtuellen Computer im Lab einbinden und dann Desktop-/Webanwendungen in der Dateifreigabe bereitstellen und testen.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: spelluru
ms.openlocfilehash: f8c57b9e1fabbd04a7d9c92484b0f52f074c2577
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872418"
---
# <a name="test-your-app-in-azure"></a>Testen Ihrer App in Azure 
Dieser Artikel enthält Schritte zum Testen der Anwendung in Azure mithilfe von DevTest Labs. Zunächst richten Sie eine Dateifreigabe in einem Lab ein und binden sie als Laufwerk auf Ihrem lokalen Entwicklungscomputer und einem virtuellen Computer in einem Lab ein. Anschließend stellen Sie die App in Visual Studio 2019 in der Dateifreigabe bereit, damit Sie die App auf dem virtuellen Computer im Lab ausführen können.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen 
1. [Erstellen Sie ein Azure-Abonnement](https://azure.microsoft.com/free/), wenn Sie noch keines besitzen, und melden Sie sich im [Azure-Portal](https://portal.azure.com) an.
2. Befolgen Sie die Anweisungen in [diesem Artikel](devtest-lab-create-lab.md), um mithilfe von Azure DevTest Labs ein Lab zu erstellen. Heften Sie das Lab an Ihr Dashboard an, damit Sie es bei Ihrer nächsten Anmeldung leichter finden. Mit Azure DevTest Labs können Sie Ressourcen in Azure schnell erstellen und dabei den Ressourcenverbrauch minimieren und die Kontrolle über die Kosten behalten. Weitere Informationen zu DevTest Labs finden Sie in der [Übersicht](devtest-lab-overview.md). 
3. Erstellen Sie anhand der Anweisungen im Artikel [Erstellen eines Speicherkontos](../storage/common/storage-create-storage-account.md) ein Azure Storage-Konto in der Ressourcengruppe des Labs. Wählen Sie auf der Seite **Speicherkonto erstellen** die Option **Vorhandene verwenden** für **Ressourcengruppe** und die **Lab-Ressourcengruppe** aus. 
4. Erstellen Sie anhand der Anweisungen im Artikel [Erstellen einer Dateifreigabe in Azure Files](../storage/files/storage-how-to-create-file-share.md) eine Dateifreigabe in Ihrem Azure-Speicher. 

## <a name="mount-the-file-share-on-your-local-machine"></a>Einbinden der Dateifreigabe auf dem lokalen Computer
1. Verwenden Sie auf dem lokalen Computer das Skript aus dem Abschnitt [Beibehalten von Anmeldeinformationen für eine Azure-Dateifreigabe in Windows](../storage/files/storage-how-to-use-files-windows.md#persisting-azure-file-share-credentials-in-windows) des Artikels [Verwenden einer Azure-Dateifreigabe mit Windows](../storage/files/storage-how-to-use-files-windows.md). 
2. Verwenden Sie dann den Befehl `net use`, um die Dateifreigabe auf dem Computer einzubinden. Beispielbefehl: Geben Sie den Namen Ihres Azure-Speichers und der Dateifreigabe an, bevor Sie den Befehl ausführen. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>Erstellen eines virtuellen Computers im Lab
1. Wählen Sie auf der Seite **Dateifreigabe** oben im Breadcrumb-Menü die Option **Ressourcengruppe** aus. Die Seite **Ressourcengruppe** wird angezeigt. 
    
    ![Auswählen der Ressourcengruppe im Breadcrumb-Menü](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. Wählen Sie auf der Seite **Ressourcengruppe** das in DevTest Labs erstellte **Lab** aus.

    ![Auswählen des Labs](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. Wählen Sie auf der Seite **DevTest Lab** für das Lab auf der Symbolleiste die Option **+ Hinzufügen** aus. 

    ![Schaltfläche „Hinzufügen“ für das Lab](media/test-app-in-azure/add-button-in-lab.png)
4. Suchen Sie auf der Seite **Basis auswählen** nach **smalldisk**, und wählen Sie **[smalldisk] Windows Server 2016 Data Center** aus. 

    ![Auswählen eines smalldisk-Windows-Servers](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. Geben Sie auf der Seite **Virtueller Computer** den **Namen des virtuellen Computers**, den **Benutzernamen** und das **Kennwort** an, und wählen Sie **Erstellen** aus.    
    
    ![Seite zum Erstellen des virtuellen Computers](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>Einbinden der Dateifreigabe auf dem virtuellen Computer
1. Nachdem der virtuelle Computer erfolgreich erstellt wurde, wählen Sie den **virtuellen Computer** in der Liste aus.    

    ![Auswählen der Lab-VM](media/test-app-in-azure/select-lab-vm.png)
2. Wählen Sie auf der Symbolleiste **Verbinden** aus, um eine Verbindung mit dem virtuellen Computer herzustellen. 
3. [Installieren Sie Azure PowerShell](/powershell/azure/install-az-ps).
4. Befolgen Sie die Anweisungen im Abschnitt „Einbinden der Dateifreigabe“. 

## <a name="publish-your-app-from-visual-studio"></a>Veröffentlichen der App über Visual Studio
In diesem Abschnitt veröffentlichen Sie Ihre App über Visual Studio auf einem virtuellen Testcomputer in der Cloud.

1. Erstellen Sie eine Desktop-/Webanwendung in Visual Studio 2019.
2. Erstellen Sie die App.
3. Klicken Sie zum Veröffentlichen der App im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus. 
4. Geben Sie im **Veröffentlichungs-Assistenten** das **Laufwerk** ein, das der Dateifreigabe zugeordnet ist.

    **Desktop-App:**

    ![Desktop-App](media/test-app-in-azure/desktop-app.png)

    **Web-App:**

    ![Web-App](media/test-app-in-azure/web-app.png)

1. Wählen Sie **Weiter** aus, um den Veröffentlichungsworkflow abzuschließen, und wählen Sie dann **Fertig stellen** aus. Nachdem Sie die Schritte im Assistenten beendet haben, wird die Anwendung in Visual Studio erstellt und in der Dateifreigabe veröffentlicht. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Testen der App auf dem virtuellen Testcomputer im Lab

1. Navigieren Sie zu der Seite Ihres virtuellen Computers im Lab. 
2. Wählen Sie auf der Symbolleiste **Starten** aus, um den virtuellen Computer zu starten, wenn er sich im Status „Beendet“ befindet. Sie können Richtlinien für das automatische Startes und Herunterfahren für die virtuellen Computer einrichten, um zu verhindern, dass sie jedes Mal gestartet und beendet werden. 
3. Wählen Sie **Verbinden**aus.

    ![Seite für den virtuellen Computer](media/test-app-in-azure/virtual-machine-page.png)
4. Starten Sie **Datei-Explorer** innerhalb des virtuellen Computers, und wählen Sie **Dieser PC** aus, um Ihre Dateifreigabe zu suchen.

    ![Suchen der Freigabe auf dem virtuellen Computer](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > Wenn Sie die Dateifreigabe auf dem virtuellen Computer oder dem lokalen Computer nicht finden, können Sie sie durch Ausführen des Befehls `net use` erneut einbinden. Den Befehl `net use` finden Sie im Assistenten **Verbinden** der **Dateifreigabe** im Azure-Portal.
1. Öffnen Sie die Dateifreigabe, und überprüfen Sie, ob die über Visual Studio bereitgestellte App angezeigt wird. 

    ![Öffnen der Freigabe auf dem virtuellen Computer](media/test-app-in-azure/open-file-share.png)

    Sie können nun auf die App zugreifen und sie innerhalb des virtuellen Testcomputers testen, den Sie in Azure erstellt haben.

## <a name="next-steps"></a>Nächste Schritte
Informationen zum Verwenden von virtuellen Computern in einem Lab finden Sie in den folgenden Artikeln. 

- [Hinzufügen eines virtuellen Computers zu einem Lab](devtest-lab-add-vm.md)
- [Neustarten eines virtuellen Computers in einem Lab](devtest-lab-restart-vm.md)
- [Ändern der Größe eines virtuellen Computers in einem Lab](devtest-lab-resize-vm.md)

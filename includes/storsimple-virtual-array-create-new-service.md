---
title: include file
description: include file
services: storage
author: alkohli
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 1be6a654962b513cfcf755d45e562b86067e7b25
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995007"
---
#### <a name="to-create-a-new-service"></a>So erstellen Sie einen neuen Dienst

1.  Melden Sie sich mit Ihren Microsoft-Kontoanmeldeinformationen unter dieser URL beim Azure-Portal an: <https://portal.azure.com/>. Falls Sie das Gerät im Government-Portal bereitstellen, melden Sie sich unter <https://portal.azure.us/> an.

2.  Klicken Sie im Azure-Portal auf **+ Ressource erstellen** &gt; **Storage** &gt; **Virtuelle StorSimple-Serie**.

    ![Erstellen eines neuen Diensts](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  Führen Sie die nachstehenden Schritte im Blatt **StorSimple-Geräte-Manager** aus, der geöffnet wird:

    1.  Geben Sie einen eindeutigen **Ressourcennamen** für Ihren Dienst an. Der Ressourcenname ist ein Anzeigename, der zum Identifizieren des Diensts verwendet werden kann. Der Name kann zwischen 2 und 50 Zeichen lang sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten. Der Name muss mit einem Buchstaben oder einer Zahl beginnen und enden.

    2.  Wählen Sie ein **Abonnement** aus der Dropdownliste aus. Das Abonnement ist mit Ihrem Abrechnungskonto verknüpft. Dieses Feld wird nicht angezeigt, wenn Sie nur ein Abonnement besitzen.

    3.  Wählen Sie für die **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue. Weitere Informationen finden Sie unter [Azure resource groups (Azure-Ressourcengruppen)](../articles/azure-resource-manager/management/manage-resource-groups-portal.md).

    4.  Geben Sie einen **Standort** für Ihren Dienst an. Unter [Azure-Regionen](https://azure.microsoft.com/regions/#services) finden Sie weitere Informationen dazu, welche Dienste in welcher Region verfügbar sind. Üblicherweise sollten Sie den **Standort** auswählen, welcher der geografischen Region, in der Ihr Gerät bereitgestellt werden soll, am nächsten liegt. Berücksichtigen Sie ggf. auch Folgendes:

        -   Falls Sie über vorhandene Workloads in Azure verfügen, die ebenso über Ihr StorSimple-Gerät bereitgestellt werden sollen, empfiehlt sich die Verwendung dieses Rechenzentrums.

        -   Ihr StorSimple-Geräte-Manager und der Azure-Speicher können sich an zwei verschiedenen Standorten befinden. In einem solchen Fall müssen Sie das StorSimple-Geräte-Manager- und das Azure-Speicherkonto separat erstellen. Navigieren Sie zum Erstellen eines Azure-Speicherkontos im Azure-Portal zu Azure Storage, und führen Sie die unter [Speicherkonto erstellen](../articles/storage/common/storage-account-create.md) beschriebenen Schritte aus. Nachdem Sie dieses Konto erstellt haben, fügen Sie es zu StorSimple-Geräte-Manager hinzu, indem Sie die Schritte unter [Konfigurieren eines neuen Speicherkontos für den Dienst](../articles/storsimple/storsimple-virtual-array-manage-storage-accounts.md#add-a-storage-account-credential) ausführen.

        -   Beim Bereitstellen des virtuellen Geräts im Government-Portal ist der StorSimple-Geräte-Manager-Dienst in den US-Standorten Iowa und Virginia verfügbar.

    5.  Aktivieren Sie **Neues Azure-Speicherkonto erstellen**, um automatisch ein Speicherkonto mit dem Dienst zu erstellen. Geben Sie einen **Speicherkontonamen** an. Wenn Ihre Daten an einem anderen Speicherort gespeichert werden sollen, deaktivieren Sie dieses Kontrollkästchen.

    6.  Überprüfen Sie **An Dashboard anheften**, wenn Sie einen Quicklink zu diesem Dienst auf Ihrem Dashboard erstellen möchten.

    7.  Klicken Sie auf **Erstellen**, um den StorSimple-Geräte-Manager zu erstellen.

        ![Erstellen eines neuen Diensts 2](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

Sie werden auf die Startseite **Dienst** weitergeleitet. Die Diensterstellung dauert einige Minuten. Nachdem der Dienst erfolgreich erstellt wurde, werden Sie entsprechend benachrichtigt, und der Status des Diensts ändert sich in **Aktiv**.
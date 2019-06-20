---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 54a1bc9ce04cf10575b26cda7861c1dd458dc831
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178140"
---
#### <a name="to-create-a-new-service"></a>So erstellen Sie einen neuen Dienst

1. Melden Sie sich mithilfe Ihrer Microsoft-Kontoanmeldeinformationen beim [Azure-Portal](https://portal.azure.com/) an.

2. Klicken Sie im Azure-Portal auf **Ressource erstellen** und dann im Marketplace auf **Alle anzeigen**.

    ![Erstellen eines StorSimple-Geräte-Managers](./media/storsimple-8000-create-new-service/createssdevman1.png)

    Suchen Sie nach _physisches Storsimple_. Wählen Sie **Physische StorSimple-Geräteserie** aus, klicken Sie darauf und dann auf **Erstellen**. Alternativ können Sie im Azure-Portal auf **+** und dann unter **Speicher** auf **Physische StorSimple-Geräteserie** klicken.

    ![Erstellen eines StorSimple-Geräte-Managers](./media/storsimple-8000-create-new-service/createssdevman11.png)

3. Führen Sie auf dem Blatt **StorSimple-Geräte-Manager** die folgenden Schritte aus:
   
   1. Geben Sie einen eindeutigen **Ressourcennamen** für Ihren Dienst an. Dies ist ein Anzeigename, der zum Identifizieren des Diensts verwendet werden kann. Der Name kann zwischen 2 und 50 Zeichen lang sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten. Der Name muss mit einem Buchstaben oder einer Zahl beginnen und enden.

   2. Wählen Sie ein **Abonnement** aus der Dropdownliste aus. Das Abonnement ist mit Ihrem Abrechnungskonto verknüpft. Dieses Feld wird nicht angezeigt, wenn Sie nur ein Abonnement besitzen.

   3. Wählen Sie für **Ressourcengruppe** die Option **Vorhandene verwenden** oder **Neu erstellen** aus. Weitere Informationen finden Sie unter [Azure resource groups (Azure-Ressourcengruppen)](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).
   
   4. Geben Sie einen **Standort** für Ihren Dienst an. Üblicherweise sollten Sie den Standort auswählen, welcher der geografischen Region, in der Ihr Gerät bereitgestellt werden soll, am nächsten liegt. Berücksichtigen Sie ggf. auch Folgendes: 
      
      * Falls Sie vorhandene Workloads in Azure haben, die Sie auch mit Ihrem StorSimple-Gerät bereitstellen möchten, sollten Sie das Datencenter nutzen.
      * Der StorSimple-Geräte-Manager-Dienst und der Azure-Speicher können sich an zwei verschiedenen Standorten befinden. In einem solchen Fall müssen Sie das StorSimple-Geräte-Manager- und das Azure-Speicherkonto separat erstellen. Um ein Azure-Speicherkonto zu erstellen, wechseln Sie zum Azure Storage-Dienst im Azure-Portal, und befolgen Sie die Schritte unter [Erstellen eines Azure-Speicherkontos](../articles/storage/common/storage-quickstart-create-account.md). Nachdem Sie dieses Konto erstellt haben, fügen Sie es zu StorSimple-Geräte-Manager hinzu, indem Sie die Schritte unter [Konfigurieren eines neuen Speicherkontos für den Dienst](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#configure-a-new-storage-account-for-the-service) ausführen.

   5. Aktivieren Sie **Neues Speicherkonto erstellen** , um automatisch ein Speicherkonto mit dem Dienst zu erstellen. Geben Sie einen Namen für dieses Speicherkonto an. Wenn Ihre Daten an einem anderen Speicherort gespeichert werden sollen, deaktivieren Sie dieses Kontrollkästchen.

   6. Überprüfen Sie **An Dashboard anheften**, wenn Sie einen Quicklink zu diesem Dienst auf Ihrem Dashboard erstellen möchten.
      
   7. Klicken Sie auf **Erstellen**, um den StorSimple-Geräte-Manager zu erstellen.

       ![Erstellen eines StorSimple-Geräte-Managers](./media/storsimple-8000-create-new-service/createssdevman2.png)
   
Die Diensterstellung dauert einige Minuten. Nachdem der Dienst erfolgreich erstellt wurde, wird eine Benachrichtigung angezeigt, und das Blatt des neuen Dienstes wird geöffnet.
   
![Erstellen eines StorSimple-Geräte-Managers](./media/storsimple-8000-create-new-service/createssdevman5.png)



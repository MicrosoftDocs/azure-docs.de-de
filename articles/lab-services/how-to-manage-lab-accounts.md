---
title: Verwalten von Lab-Konten in Azure Lab Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie in einem Azure-Abonnement ein Lab-Konto erstellen, alle Lab-Konten anzeigen oder ein Lab-Konto löschen.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 65ff28401b73698e320735613b32c96bf3de58f2
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434089"
---
# <a name="create-and-manage-lab-accounts"></a>Erstellen und Verwalten von Labkonten
In Azure Lab Services ist ein Lab-Konto ein Container für verwaltete Labtypen (beispielsweise Labs). Ein Administrator richtet ein Lab-Konto mit Azure Lab Services ein und gewährt Lab-Besitzern Zugriff, sodass sie Labs unter dem Konto erstellen können. In diesem Artikel erfahren Sie, wie Sie ein Lab-Konto erstellen, alle Lab-Konten anzeigen oder ein Lab-Konto löschen.

## <a name="create-a-lab-account"></a>Erstellen eines Lab-Kontos
Die folgenden Schritte veranschaulichen, wie Sie Azure-Portal verwenden, um ein Lab-Konto in Azure Lab Services zu erstellen. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Menü links **Alle Dienste** aus. Wählen Sie im Abschnitt **DevOps** die Option **Labkonten** aus. Wenn Sie das Sternchen (`*`) neben **Labkonten** auswählen, wird die Option im linken Menü dem Abschnitt **FAVORITEN** hinzugefügt. Beim nächsten Mal wählen Sie **Labkonten** unter **FAVORITEN** aus.

    ![Alle Dienste -> Labkonten](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Wählen Sie auf der Seite **Labkonten** auf der Symbolleiste **Hinzufügen** aus, oder wählen Sie auf der Seite **Labkonto erstellen** aus. 

    ![Auswählen von „Hinzufügen“ auf der Seite „Labkonten“](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Führen Sie auf der Seite **Labkonto erstellen** auf der Registerkarte **Grundlagen** die folgenden Schritte aus: 
    1. Geben Sie einen **Lab-Kontonamen** ein. 
    2. Wählen Sie das **Azure-Abonnement** aus, in dem Sie das Lab-Konto erstellen möchten.
    3. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, und geben Sie einen Namen für die Ressourcengruppe ein.
    4. Wählen Sie als **Standort** einen Standort oder eine Region aus, in dem bzw. der das Lab-Konto erstellt werden soll.
    5. Geben Sie im Feld **Auswahl des Labstandorts** an, ob Sie es Laberstellern ermöglichen möchten, einen Standort für das Lab auszuwählen. Standardmäßig ist die Option deaktiviert. Wenn die Option deaktiviert ist, können Labersteller keinen Standort für das Lab angeben, das sie erstellen. Die Labs werden am nächstgelegenen geografischen Standort (relativ zum Labkonto) erstellt. Wenn die Option aktiviert ist, kann ein Labersteller bei der Laberstellung einen Standort auswählen. Weitere Informationen finden Sie unter [Zulassen der Auswahl des Labstandorts durch den Lab-Ersteller](allow-lab-creator-pick-lab-location.md). 

        ![Labkonto erstellen -> Grundlagen](./media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. Klicken Sie auf **Weiter: Erweitert** unten auf der Seite, um zur Registerkarte **Erweitert** zu navigieren, und führen Sie dann die folgenden Schritte aus: 
    1. Wählen Sie einen vorhandenen **Katalog mit freigegebenen Images** aus, oder erstellen Sie einen solchen. Sie können die Vorlagen-VM im Katalog mit den freigegebenen Images speichern, damit sie von anderen Benutzern wiederverwendet werden kann. Ausführliche Informationen zu Katalogen mit freigegebenen Images finden Sie unter [Verwenden eines Katalogs mit freigegebenen Images in Azure Lab Services](how-to-use-shared-image-gallery.md).
    2. Geben Sie an, ob Sie **virtuelle Windows-Computer automatisch herunterfahren**  möchten, wenn Benutzer ihre Verbindung damit trennen. Geben Sie an, wie lange die virtuellen Computer warten sollen, bis die Verbindung des Benutzers wieder hergestellt wird, bevor sie automatisch heruntergefahren werden. 
    3. Wählen Sie als **Virtuelles Peernetzwerk** ein virtuelles Peernetzwerk (VNET) für das Lab-Netzwerk aus. In diesem Konto erstellte Labs sind mit dem ausgewählten VNET verbunden und haben Zugriff auf die Ressourcen im ausgewählten VNET. Weitere Informationen finden Sie unter [Verbinden des virtuellen Netzwerks Ihres Labs mit einem virtuellen Peernetzwerk](how-to-connect-peer-virtual-network.md).    
    8. Geben Sie einen **Adressbereich** für VMs im Lab an. Der Adressbereich muss in der CIDR-Notation (Classless Inter-Domain Routing) angegeben werden (Beispiel: 10.20.0.0/23). Virtuelle Computer im Lab werden in diesem Adressbereich erstellt. Weitere Informationen finden Sie unter [Angeben eines Adressbereichs für VMs im Lab](how-to-connect-peer-virtual-network.md#specify-an-address-range-for-vms-in-the-lab-account).  

        > [!NOTE]
        > Die Eigenschaft **Adressbereich** ist nur gültig, wenn ein **Virtuelles Peernetzwerk** für das Lab aktiviert ist.

        ![Labkonto erstellen -> Erweitert](./media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. Klicken Sie auf **Weiter: Tags** unten auf der Seite, um zur Registerkarte **Tags** zu wechseln. Wählen Sie alle Tags aus, die Sie mit dem Labkonto verknüpfen möchten. Tags sind Name/Wert-Paare, die Ihnen das Kategorisieren von Ressourcen und die Anzeige einer konsolidierten Abrechnung ermöglichen, indem Sie dasselbe Tag auf mehrere Ressourcen und Ressourcengruppen anwenden. Weitere Informationen finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md).

    ![Screenshot: Seite „Labkonto erstellen“ mit hervorgehobener Registerkarte „Tags“](./media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. Wählen Sie am unteren Rand dieser Seite **Überprüfen + erstellen** aus, um zur Registerkarte **Überprüfen + erstellen** zu wechseln. 
4. Überprüfen Sie die Zusammenfassungsinformationen auf dieser Seite, und wählen Sie **Erstellen** aus. 

    ![Labkonto erstellen -> Tags](./media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. Warten Sie, bis die Bereitstellung abgeschlossen ist, erweitern Sie die Option **Nächste Schritte**, und wählen Sie **Zu Ressource wechseln** aus, wie in der folgenden Abbildung gezeigt: 

    Sie können auch auf der Symbolleiste das **Glockensymbol** (**Benachrichtigungen**) auswählen, überprüfen, ob die Bereitstellung erfolgreich war, und dann **Zu Ressource wechseln** auswählen. 

    Wählen Sie alternativ auf der Seite **Labkonten** die Option **Aktualisieren**, und wählen Sie anschließend das von Ihnen erstellte Labkonto aus. 

    ![Fenster zum Erstellen eines Lab-Kontos](./media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Folgende Seite für das **Lab-Konto** wird angezeigt:

    ![Seite des Lab-Kontos](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Anzeigen von Lab-Konten
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Menü **Alle Ressourcen** aus. 
3. Wählen Sie für **Typ** die Option **Labkonten**. 
    Sie können auch nach Abonnement, Ressourcengruppe, Speicherorten und Tags filtern. 

    ![Alle Ressourcen -> Labkonten](./media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)


## <a name="delete-a-lab-account"></a>Löschen eines Lab-Kontos
Befolgen Sie die Anleitung im vorherigen Abschnitt, um Lab-Konten in einer Liste anzuzeigen. Gehen Sie folgendermaßen vor, um ein Lab-Konto zu löschen: 

1. Wählen Sie das **Lab-Konto** aus, das Sie löschen möchten. 
2. Klicken Sie auf der Symbolleiste auf **Löschen**. 

    ![Labkonten -> Schaltfläche „Löschen“](./media/how-to-manage-lab-accounts/delete-button.png)
1. Geben Sie zur Bestätigung **Ja** ein.
1. Klicken Sie auf **Löschen**. 

    ![Labkonto löschen – Bestätigung](./media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> Sie können auch das PowerShell-Modul Az.LabServices (Vorschauversion) verwenden, um Labkonten zu verwalten. Weitere Informationen finden Sie auf der [Az.LabServices-Startseite auf GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in anderen Artikeln im **Schrittanleitungen** -> **Erstellen und Konfigurieren von Labkonten (Labkontobesitzer)** -Abschnitt des Inhaltsverzeichnisses. 
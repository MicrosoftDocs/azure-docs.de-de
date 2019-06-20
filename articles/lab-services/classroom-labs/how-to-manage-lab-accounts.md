---
title: Verwalten von Lab-Konten in Azure Lab Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie in einem Azure-Abonnement ein Lab-Konto erstellen, alle Lab-Konten anzeigen oder ein Lab-Konto löschen.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 6f283ce007e96547e01a01a3753ddcb60574bfc3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65412802"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Verwalten von Lab-Konten in Azure Lab Services 
In Azure Lab Services ist ein Lab-Konto ein Container für verwaltete Labtypen (beispielsweise Classroom-Labs). Ein Administrator richtet ein Lab-Konto mit Azure Lab Services ein und gewährt Lab-Besitzern Zugriff, sodass sie Labs unter dem Konto erstellen können. In diesem Artikel erfahren Sie, wie Sie ein Lab-Konto erstellen, alle Lab-Konten anzeigen oder ein Lab-Konto löschen.

## <a name="create-a-lab-account"></a>Erstellen eines Lab-Kontos
Die folgenden Schritte veranschaulichen, wie Sie Azure-Portal verwenden, um ein Lab-Konto in Azure Lab Services zu erstellen. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Menü links **Alle Dienste** aus. Wählen Sie im Abschnitt **DEVOPS** die Option **Labkonten**. Wenn Sie das Sternchen (`*`) neben **Labkonten** auswählen, wird die Option im linken Menü dem Abschnitt **FAVORITEN** hinzugefügt. Beim nächsten Mal wählen Sie **Labkonten** unter **FAVORITEN** aus.

    ![Alle Dienste -> Labkonten](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Klicken Sie auf der Seite **Labkonten** auf der Symbolleiste auf **Hinzufügen**. 

    ![Auswählen von „Hinzufügen“ auf der Seite „Labkonten“](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Führen Sie auf der Seite **Labkonto** die folgenden Aktionen aus: 
    1. Geben Sie einen **Lab-Kontonamen** ein. 
    2. Wählen Sie das **Azure-Abonnement** aus, in dem Sie das Lab-Konto erstellen möchten.
    3. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, und geben Sie einen Namen für die Ressourcengruppe ein.
    4. Wählen Sie als **Standort** einen Standort oder eine Region aus, in dem bzw. der das Lab-Konto erstellt werden soll. 
    5. Wählen Sie einen vorhandenen **Katalog mit freigegebenen Images** aus, oder erstellen Sie einen solchen. Sie können die Vorlagen-VM im Katalog mit den freigegebenen Images speichern, damit sie von anderen Benutzern wiederverwendet werden kann. Ausführliche Informationen zu Katalogen mit freigegebenen Images finden Sie unter [Verwenden eines Katalogs mit freigegebenen Images in Azure Lab Services](how-to-use-shared-image-gallery.md).
    6. Wählen Sie als **Virtuelles Peernetzwerk** ein virtuelles Peernetzwerk (VNET) für das Lab-Netzwerk aus. In diesem Konto erstellte Labs sind mit dem ausgewählten VNET verbunden und haben Zugriff auf die Ressourcen im ausgewählten VNET. 
    7. Geben Sie einen **Adressbereich** für VMs im Lab an. Der Adressbereich muss in der CIDR-Notation (Classless Inter-Domain Routing) angegeben werden (Beispiel: 10.20.0.0/23). Virtuelle Computer im Lab werden in diesem Adressbereich erstellt. Weitere Informationen finden Sie unter [Angeben eines Adressbereichs für VMs im Lab](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab).    
    8. Geben Sie im Feld **Auswahl des Labstandorts** an, ob Sie es Laberstellern ermöglichen möchten, einen Standort für das Lab auszuwählen. Standardmäßig ist die Option deaktiviert. Wenn die Option deaktiviert ist, können Labersteller keinen Standort für das Lab angeben, das sie erstellen. Die Labs werden am nächstgelegenen geografischen Standort (relativ zum Labkonto) erstellt. Wenn die Option aktiviert ist, kann ein Labersteller bei der Laberstellung einen Standort auswählen.      
    9. Klicken Sie auf **Erstellen**. 

        ![Fenster zum Erstellen eines Lab-Kontos](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Wählen Sie auf der Symbolleiste das **Glockensymbol** (**Benachrichtigungen**), überprüfen Sie, ob die Bereitstellung erfolgreich war, und wählen Sie dann **Zu Ressource wechseln**. 

    Wählen Sie alternativ auf der Seite **Labkonten** die Option **Aktualisieren**, und wählen Sie anschließend das von Ihnen erstellte Labkonto aus. 

    ![Fenster zum Erstellen eines Lab-Kontos](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Folgende Seite für das **Lab-Konto** wird angezeigt:

    ![Seite des Lab-Kontos](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Anzeigen von Lab-Konten
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Menü **Alle Ressourcen** aus. 
3. Wählen Sie für **Typ** die Option **Labkonten**. 
    Sie können auch nach Abonnement, Ressourcengruppe, Speicherorten und Tags filtern. 

    ![Alle Ressourcen -> Labkonten](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Anzeigen und Verwalten von Labs im Labkonto

1. Wählen Sie auf der Seite **Labkonto** im Menü auf der linken Seite die Option **Labs**.

    ![Labs im Konto](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. Im Konto wird eine **Liste mit Labs** mit den folgenden Informationen angezeigt: 
    1. Name des Labs
    2. Datum, an dem das Lab erstellt wurde 
    3. E-Mail-Adresse des Benutzers, der das Lab erstellt hat 
    4. Maximale Anzahl von Benutzern, die für das Lab zulässig ist 
    5. Status des Labs 

## <a name="delete-a-lab-in-the-lab-account"></a>Löschen eines Labs im Labkonto
Befolgen Sie die Anleitung im vorherigen Abschnitt, um im Labkonto eine Liste mit den Labs anzuzeigen.

1. Wählen Sie **...** (Auslassungszeichen) und dann **Löschen**. 

    ![Lab löschen – Schaltfläche](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. Wählen Sie in der Warnmeldung die Option **Ja**. 

    ![Bestätigen des Löschvorgangs](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Löschen eines Lab-Kontos
Befolgen Sie die Anleitung im vorherigen Abschnitt, um Lab-Konten in einer Liste anzuzeigen. Gehen Sie folgendermaßen vor, um ein Lab-Konto zu löschen: 

1. Wählen Sie das **Lab-Konto** aus, das Sie löschen möchten. 
2. Klicken Sie auf der Symbolleiste auf **Löschen**. 

    ![Labkonten -> Schaltfläche „Löschen“](../media/how-to-manage-lab-accounts/delete-button.png)
1. Geben Sie zur Bestätigung **Ja** ein.
1. Klicken Sie auf **Löschen**. 

    ![Labkonto löschen – Bestätigung](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)


## <a name="next-steps"></a>Nächste Schritte
Lesen Sie den folgenden Artikel: [Konfigurieren von Lab-Konten](how-to-configure-lab-accounts.md).
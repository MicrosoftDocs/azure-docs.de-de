---
title: Konfigurieren von Labkonten in Azure Lab Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein Labkonto konfigurieren, nachdem es erstellt wurde.
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
ms.openlocfilehash: ba469c038f04a31a57e798b97b5120bec573feae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65414043"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Konfigurieren von Labkonten in Azure Lab Services 
In Azure Lab Services ist ein Lab-Konto ein Container für verwaltete Labtypen (beispielsweise Classroom-Labs). Ein Administrator richtet ein Lab-Konto mit Azure Lab Services ein und gewährt Lab-Besitzern Zugriff, sodass sie Labs unter dem Konto erstellen können. In diesem Artikel erfahren Sie, wie Sie ein Lab-Konto erstellen, alle Lab-Konten anzeigen oder ein Lab-Konto löschen.

## <a name="connect-with-a-peer-virtual-network"></a>Herstellen einer Verbindung mit einem virtuellen Peernetzwerk
Um eine Verbindung eines virtuellen Netzwerks als Peernetzwerk mit dem virtuellen Netzwerk des Labs herzustellen, gehen Sie folgendermaßen vor:

1. Wählen Sie auf der Seite **Lab-Konto** im Menü auf der linken Seite die Option **Labs-Konfiguration** aus.

    ![Seite „Lab-Konfiguration“](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. Wählen Sie für das **virtuelle Peernetzwerk** entweder **aktiviert** oder **deaktiviert** aus. Der Standardwert lautet **Deaktiviert**. Führen Sie die folgenden Schritte aus, um das virtuelle Peernetzwerk zu aktivieren: 
    1. Wählen Sie **Aktiviert**.
    2. Wählen Sie das **VNet** in der Dropdownliste aus. 
3. Wählen Sie auf der Symbolleiste **Speichern** aus. 

Mit diesem Konto erstellte Labs werden mit dem ausgewählten virtuellen Netzwerk verbunden. Sie können auf die Ressourcen im ausgewählten virtuellen Netzwerk zugreifen. Weitere Informationen finden Sie unter [Verbinden des Netzwerks Ihres Labs mit einem virtuellen Peernetzwerk in Azure Lab Services](how-to-connect-peer-virtual-network.md).

Wenn Sie ein virtuelles Netzwerk für das Feld **Virtuelles Peernetzwerk** auswählen, ist die Option **Auswahl des Labspeicherorts durch Labersteller zulassen** deaktiviert. Der Grund dafür ist, dass sich die Labs im Labkonto in der gleichen Region befinden müssen wie das Labkonto, damit sie sich mit Ressourcen im virtuellen Peernetzwerk verbinden können. 

## <a name="allow-lab-creator-to-pick-location-for-the-lab"></a>Zulassen der Auswahl des Labspeicherorts durch den Ersteller des Labs
Sie können mit folgenden Schritten zulassen, dass der Ersteller des Labs an einem anderen Speicherort als dem Speicherort des Labkontos Labs erstellt: 

1. Wählen Sie auf der Seite **Lab-Konto** im Menü auf der linken Seite die Option **Labs-Konfiguration** aus.
2. Wählen Sie unter **Auswahl des Lab-Speicherorts durch Lab-Ersteller zulassen** die Option **Aktiviert**, wenn Lab-Ersteller einen Standort für das Lab auswählen dürfen. Wenn diese Option deaktiviert ist, werden die Labs automatisch am gleichen Standort erstellt, in dem das Lab-Konto vorhanden ist. 
    
    Dieses Feld ist deaktiviert, wenn Sie ein virtuelles Netzwerk für das Feld **Virtuelles Peernetzwerk** auswählen. Der Grund dafür ist, dass sich die Labs im Labkonto in der gleichen Region befinden müssen wie das Labkonto, damit sie auf Ressourcen im virtuellen Peernetzwerk zugreifen können. 
1. Wählen Sie auf der Symbolleiste **Speichern** aus. 

    ![Konfigurieren der Labspeicherorteinstellung](../media/how-to-manage-lab-accounts/labs-configuration-page-lab-location.png)


## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Angeben eines Adressbereichs für VMs im Lab
Das folgende Verfahren besteht aus Schritten zum Angeben eines Adressbereichs für VMs im Lab. Wenn Sie den Bereich aktualisieren, den Sie zuvor angegeben haben, gilt der geänderte Adressbereich nur für virtuelle Computer, die nach der Änderung erstellt werden. 

Hier werden einige Einschränkungen zur Angabe des Adressbereichs aufgeführt, die Sie beachten sollten. 

- Das Präfix muss kleiner als oder gleich 23 sein. 
- Wenn ein virtuelles Netzwerk mittels Peering mit dem Labkonto verbunden ist, darf sich der angegebene Adressbereich nicht mit dem Adressbereich des mittels Peering verbundenen virtuellen Netzwerks überschneiden.

1. Wählen Sie auf der Seite **Lab-Konto** im Menü auf der linken Seite die Option **Labs-Konfiguration** aus.
2. Geben Sie für das Feld **Adressbereich** den Adressbereich für virtuelle Computer an, die im Lab erstellt werden. Der Adressbereich muss in der CIDR-Notation (Classless Inter-Domain Routing) angegeben werden (Beispiel: 10.20.0.0/23). Virtuelle Computer im Lab werden in diesem Adressbereich erstellt.
3. Wählen Sie auf der Symbolleiste **Speichern** aus. 

    ![Konfigurieren des Adressenbereichs](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Hinzufügen eines Benutzers zur Rolle „Lab-Ersteller“
Zum Einrichten eines Classroom-Labs in einem Labkonto muss der Benutzer Mitglied der Rolle **Ersteller des Labs** für das Labkonto sein. Das zum Erstellen des Labkontos verwendete Konto wird dieser Rolle automatisch hinzugefügt. Wenn Sie zum Erstellen eines Classroom-Labs das gleiche Benutzerkonto verwenden möchten, können Sie diesen Schritt überspringen. Führen Sie die folgenden Schritte aus, um zum Erstellen eines Classroom-Labs ein anderes Benutzerkonto zu verwenden: 

Um Lehrkräften die Berechtigung zum Erstellen von Labs für ihre Klassen zu erteilen, fügen Sie sie zur Rolle **Lab-Ersteller** hinzu:

1. Wählen Sie auf der Seite **Lab-Konto** die Option **Zugriffssteuerung (IAM)** aus, und klicken Sie auf der Symbolleiste auf **+ Rollenzuweisung hinzufügen**. 

    ![Zugriffssteuerung > Schaltfläche „Rollenzuweisung hinzufügen“](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Wählen Sie auf der Seite **Rollenzuweisung hinzufügen** als **Rolle** die Option **Ersteller des Labs** aus. Wählen Sie den Benutzer aus, den Sie der Rolle „Ersteller des Labs“ hinzufügen möchten, und klicken Sie auf **Speichern**. 

    ![Hinzufügen des Lab-Erstellers](../media/tutorial-setup-lab-account/add-lab-creator.png)

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Angeben von für Lab-Ersteller verfügbare Marketplace-Images
Als Lab-Kontobesitzer können Sie die Marketplace-Images festlegen, die Lab-Ersteller zum Erstellen von Labs im Lab-Konto verwenden können. 

1. Wählen Sie links im Menü die Option **Marketplace-Images** aus. Standardmäßig wird die vollständige Liste der Images (sowohl aktivierte als auch deaktivierte) angezeigt. Sie können die Liste filtern, um nur aktivierte bzw. deaktivierte Images anzuzeigen. Wählen Sie dazu in der Dropdownliste oben die Option **Enabled only**/**Disabled only** (Nur aktivierte/Nur deaktivierte). 
    
    ![Seite mit Marketplace-Images](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    Die Marketplace-Images, die in der Liste angezeigt werden, sind die einzigen, die die folgenden Bedingungen erfüllen:
        
    - Erstellt einen einzelnen virtuellen Computer
    - Verwendet den Azure Resource Manager zum Bereitstellen von virtuellen Computern
    - Erfordert nicht den Erwerb eines zusätzlichen Lizenzplans
2. Um ein aktiviertes Marketplace-Image zu **deaktivieren**, führen Sie eine der folgenden Aktionen aus: 
    1. Klicken Sie in der letzten Spalte auf die Ellipse ( **...** ) und dann auf **Disable image** (Image deaktivieren). 

        ![Deaktivieren eines Images](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Wählen Sie mindestens ein Image in der Liste aus, indem Sie die Kontrollkästchen vor den Imagenamen in der Liste aktivieren, und wählen Sie **Ausgewählte Images deaktivieren** aus. 

        ![Deaktivieren mehrerer Images](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. Ebenso führen Sie zum **Aktivieren** eines Marketplace-Images eine der folgenden Aktionen aus: 
    1. Klicken Sie in der letzten Spalte auf die Ellipse ( **...** ) und dann auf **Enable image** (Image aktivieren). 
    2. Wählen Sie mindestens ein Image in der Liste aus, indem Sie die Kontrollkästchen vor den Imagenamen in der Liste aktivieren, und wählen Sie **Ausgewählte Images aktivieren** aus. 




## <a name="next-steps"></a>Nächste Schritte
Entsprechende Informationen finden Sie in den folgenden Artikeln:

- [Erstellen und Verwalten von Labs als Labbesitzer](how-to-manage-classroom-labs.md)
- [Einrichten und Veröffentlichen von Vorlagen als Labbesitzer](how-to-create-manage-template.md)
- [Konfigurieren und Steuern der Nutzung eines Labs als Labbesitzer](how-to-configure-student-usage.md)
- [Zugreifen auf ein Classroom-Lab in Azure Lab Services](how-to-use-classroom-lab.md) (als Labbenutzer)

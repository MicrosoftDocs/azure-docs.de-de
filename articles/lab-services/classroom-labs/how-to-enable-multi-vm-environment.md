---
title: Aktivieren von Umgebungen mit mehreren VMs in Azure Lab Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Umgebung mit mehreren virtuellen Computern (Virtual Machines, VMs) innerhalb einer Vorlage für virtuelle Computer in einem Classroom-Lab in Azure Lab Services erstellen.
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
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: 6faf32232c42f863bff52fdfb3c0714aee8e9b88
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60702417"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>Erstellen einer Umgebung mit mehreren VMs in einer Vorlage für virtuelle Computer in einem Classroom-Lab
Momentan ermöglicht Azure Lab Services Ihnen, in einem Lab eine Vorlage für virtuelle Computer einzurichten und jedem Ihrer Benutzer jeweils eine Kopie zur Verfügung zu stellen. Wenn Sie aber ein IT-Dozent sind, der eine Klasse in der Einrichtung von Firewalls und Servern unterrichtet, müssen Sie möglicherweise für jeden Ihrer Kursteilnehmer eine Umgebung bereitstellen, in der mehrere virtuelle Computer über ein Netzwerk miteinander kommunizieren können.

Eine geschachtelte Virtualisierung ermöglicht Ihnen, innerhalb einer Vorlage für virtuelle Computer in einem Lab eine Umgebung mit mehreren virtuellen Computern zu erstellen. Wenn diese Vorlage veröffentlicht wird, wird für jeden Benutzer im Lab ein eingerichteter virtueller Computer bereitgestellt, in dem weitere virtuelle Computer eingeschlossen sind.

## <a name="what-is-nested-virtualization"></a>Was ist geschachtelte Virtualisierung?
Die geschachtelte Virtualisierung ermöglicht es Ihnen, virtuelle Computer innerhalb eines virtuellen Computers zu erstellen. Geschachtelte Virtualisierung erfolgt über Hyper-V, und steht nur für Windows-VMs zur Verfügung.

Weitere Informationen zur geschachtelten Virtualisierung finden Sie in den folgenden Artikeln:

- [Nested Virtualization in Azure (Geschachtelte Virtualisierung in Azure)](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Aktivieren der geschachtelten Virtualisierung auf einer Azure-VM](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Verwenden der geschachtelten Virtualisierung in Azure Lab Services
Wichtig sind die folgenden Schritte:

1. Erstellen Sie für das Lab einen **Windows-Vorlagencomputer** mit der Größe **Groß**. 
2. Stellen Sie eine Verbindung zu ihm her und [aktivieren Sie die geschachtelte Virtualisierung](../../virtual-machines/windows/nested-virtualization.md).


Die folgende Prozedur enthält die ausführlichen Schritte: 

1. Erstellen Sie ein Labkonto, falls Sie noch keines besitzen. Anweisungen dazu finden Sie unter: [Tutorial: Einrichten eines Labkontos mit Azure Lab Services](tutorial-setup-lab-account.md).
2. Navigieren Sie zur Website [Azure Lab Services](https://labs.azure.com). 
3. Wählen Sie **Anmelden**, und geben Sie Ihre Anmeldeinformationen ein. Azure Lab Services unterstützt Geschäfts-, Schul- oder Unikonten und Microsoft-Konten. 
4. Führen Sie im Fenster **Neues Lab** die folgenden Aktionen aus: 
    1. Geben Sie unter **Name** einen Namen für Ihr Lab an. 
    2. Geben Sie die maximale **Anzahl von virtuellen Computern** im Lab an. Die Anzahl von virtuellen Computern kann nach der Lab-Erstellung oder in einem bereits vorhandenen Lab erhöht oder verringert werden. Weitere Informationen finden Sie unter [Aktualisieren der Anzahl von virtuellen Computern im Lab](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab).
    6. Wählen Sie **Speichern** aus.

        ![Erstellen eines Classroom-Labs](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Führen Sie auf der Seite **Select virtual machine specifications** (Spezifikationen für virtuellen Computer auswählen) die folgenden Schritte aus:
    1. Wählen Sie als Größe der VMs, die im Lab erstellt werden sollen, **Groß** aus. Momentan unterstützt nur die Größe „Groß“ die geschachtelte Virtualisierung.
    2. Wählen Sie ein VM-Image aus, das ein **Windows-Image** ist. Die geschachtelte Virtualisierung ist nur für Windows-Computer verfügbar. 
    3. Klicken Sie auf **Weiter**.

        ![Angeben von VM-Spezifikationen](../media/how-to-enable-multi-vm-environment/large-windows-vm.png)    
5. Geben Sie auf der Seite **Anmeldeinformationen festlegen** die Standardanmeldeinformationen für alle VMs im Lab an. 
    1. Geben Sie den **Namen des Benutzers** für alle VMs im Lab an.
    2. Geben Sie das **Kennwort** für den Benutzer an. 

        > [!IMPORTANT]
        > Notieren Sie sich den Benutzernamen und das Kennwort. Diese Angaben werden nicht noch einmal angezeigt.
    3. Klicken Sie auf **Erstellen**. 

        ![Festlegen von Anmeldeinformationen](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Auf der Seite **Vorlage konfigurieren** wird der Status für den Prozess der Laberstellung angezeigt. Die Erstellung der Vorlage im Lab dauert bis zu 20 Minuten. 

    ![Konfigurieren der Vorlage](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Nachdem die Konfiguration der Vorlage abgeschlossen ist, wird die folgende Seite angezeigt: 

    ![Seite „Vorlage konfigurieren“ nach Abschluss des Vorgangs](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Wählen Sie auf der Seite **Configure template** (Vorlage konfigurieren) **Verbinden** aus, um eine Verbindung zur Vorlagen-VM herzustellen, um die geschachtelte Virtualisierung zu konfigurieren. Die Konfiguration können Sie auch noch später vornehmen, nachdem Sie die Schritte in diesem Assistenten ausgeführt haben. 
9. Richten Sie innerhalb des virtuellen Vorlagencomputers die geschachtelte Virtualisierung ein, und konfigurieren Sie ein virtuelles Netzwerk mit mehreren virtuellen Computern. Eine detaillierte Schritt-für-Schritt-Anleitung finden Sie unter [Aktivieren der geschachtelten Virtualisierung auf einer Azure-VM](../../virtual-machines/windows/nested-virtualization.md). Kurze Zusammenfassung der Schritte: 
    1. Aktivieren des Hyper-V-Features im virtuellen Vorlagencomputer
    2. Einrichten als internes virtuelles Netzwerk mit Internetverbindung für die geschachtelten virtuellen Computern
    3. Erstellen von virtuellen Computern über den Hyper-V-Manager
    4. Zuweisen einer IP-Adresse zu den virtuellen Computern
10. Wählen Sie auf der Vorlagenseite die Option **Weiter**. 
11. Führen Sie auf der Seite **Vorlage veröffentlichen** die folgenden Aktionen durch. 
    1. Wenn Sie die Vorlage sofort veröffentlichen möchten, wählen Sie **Veröffentlichen** aus.  

        > [!WARNING]
        > Nachdem die Veröffentlichung erfolgt ist, kann sie nicht mehr rückgängig gemacht werden. 
    2. Wählen Sie **Für später speichern**, wenn Sie die Veröffentlichung später durchführen möchten. Sie können die Vorlage für virtuelle Computer veröffentlichen, nachdem der Assistent abgeschlossen wurde. Weitere Informationen zum Konfigurieren und Veröffentlichen nach Abschluss des Assistenten finden Sie im Abschnitt [Veröffentlichen der Vorlage](how-to-create-manage-template.md#publish-the-template-vm) des Artikels [Verwalten von Classroom-Labs in Azure Lab Services](how-to-manage-classroom-labs.md).

        ![Vorlage veröffentlichen](../media/how-to-enable-multi-vm-environment/publish-template-page.png)
11. Für die Vorlage wird der **Status der Veröffentlichung** angezeigt. Dieser Vorgang kann bis zu einer Stunde dauern. 

    ![Veröffentlichen der Vorlage – Status](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Die folgende Seite wird angezeigt, wenn die Veröffentlichung der Vorlage erfolgreich war. Wählen Sie **Fertig**aus.

    ![Veröffentlichen der Vorlage – Erfolg](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Das **Dashboard** für das Lab wird angezeigt. 
    
    ![Dashboard für Classroom-Lab](../media/how-to-enable-multi-vm-environment/dashboard.png)


## <a name="next-steps"></a>Nächste Schritte

Jeder Benutzer erhält nun einen einzelnen virtuellen Computer, der eine Umgebung mit mehreren VMs enthält. Informationen zum Hinzufügen von Benutzern zum Lab und wie diesen der Registrierungslink gesendet wird, finden Sie im folgenden Artikel: [Hinzufügen von Benutzern zum Lab](tutorial-setup-classroom-lab.md#add-users-to-the-lab).
---
title: Festlegen verbindlicher Artefakte für Ihre Azure DevTest Labs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie erforderliche Elemente angeben, die vor der Installation der vom Benutzer ausgewählten Elemente auf virtuellen Computern (VMs) im Lab installiert werden müssen.
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: spelluru
ms.openlocfilehash: 090236ec3647c7c3e38eb862780a615f854e952b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60562210"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Festlegen verbindlicher Artefakte für Ihr Lab in Azure DevTest Labs
Als Besitzer eines Labs können Sie die verbindlichen Elemente festlegen, die auf jeden Computer im Lab angewendet werden. Stellen Sie sich ein Szenario vor, in dem jeder Computer in Ihrem Lab mit dem Unternehmensnetzwerk verbunden werden soll. In diesem Fall müsste jeder Lab-Benutzer beim Erstellen eines virtuellen Computers ein Artefakt für den Domänenbeitritt erstellen, um sicherzustellen, dass ihr Computer mit der Domäne des Unternehmens verbunden ist. Lab-Benutzer müssten also einen Computer neu erstellen, wenn sie vergessen, erforderliche Artefakte auf ihrem Computer anzuwenden. Als Besitzer eines Labs legen Sie das Artefakt für den-Domänenbeitritt als obligatorisches Artefakt in Ihrem Lab fest. So wird sichergestellt, dass jeder Computer mit dem Unternehmensnetzwerk verbunden ist und Ihre Lab-Benutzer Zeit und Aufwand sparen.
 
Weitere erforderliche Artefakte wären beispielsweise ein gängiges Tool, das Ihr Team verwendet, oder ein plattformbezogenes Sicherheitspaket, das standardmäßig auf jedem Computer installiert werden muss. Kurz gesagt, ist jede allgemeine Software, die alle Computer im Lab benötigen, ein verbindliches Artefakt. Wenn Sie ein benutzerdefiniertes Image von einem Computer erstellen, auf dem erforderliche Artefakte angewendet wurden und dann einen neuen Computer aus diesem Image erstellen, werden die verbindlichen Artefakte während der Erstellung erneut auf den Computer angewendet. Auch wenn das benutzerdefinierte Image veraltet ist, werden durch dieses Verhalten bei jedem Erstellen eines Computers aus der aktuellen Version die verbindlichen Artefakte angewendet. 
 
Nur Elemente ohne Parameter können als verbindlich festgelegt werden. Ihr Lab-Benutzer muss bei der Lab-Erstellung keine zusätzlichen Parameter erstellen. Das erleichtert den Prozess der Erstellung des virtuellen Computers wesentlich. 

## <a name="specify-mandatory-artifacts"></a>Angeben verbindlicher Artefakte
Sie können verbindliche Artefakte für Windows- und Linux-Computer separat festlegen. Sie können auch die Reihenfolge ändern, in der diese Artefakte angewendet werden sollen. 

1. Wählen Sie auf der Startseite Ihres Labs **Konfiguration und Richtlinien** unter **EINSTELLUNGEN**. 
3. Wählen Sie **Verbindliche Artefakte** unter **EXTERNE RESSOURCEN**. 
4. Wählen Sie **Bearbeiten** im Abschnitt **Windows** oder **Linux**. In diesem Beispiel wird **Windows** verwendet. 

    ![Seite mit verbindlichen Artefakten – Schaltfläche „Bearbeiten“](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Wählen Sie ein Artefakt. In diesem Beispiel wird die Option **7-Zip** verwendet. 
5. Wählen Sie auf der Seite **Artefakt hinzufügen** die Option **Hinzufügen**. 

    ![Seite mit verbindlichen Artefakten – 7-Zip hinzufügen](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Wählen Sie zum Hinzufügen eines weiteren Artefakts den Artikel aus, und wählen Sie **Hinzufügen**. In diesem Beispiel wird **Chrome** als zweites verbindliches Artefakt hinzugefügt.

    ![Seite mit verbindlichen Artefakten – Chrome hinzufügen](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. Auf der Seite **Verbindliche Artefakte** wird eine Meldung mit der Anzahl der ausgewählten Artefakte angezeigt. Wenn Sie auf die Meldung klicken, sehen Sie die Artefakte, die Sie ausgewählt haben. Wählen Sie **Speichern**. 

    ![Seite mit verbindlichen Artefakten – Artefakte speichern](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Wiederholen Sie die Schritte, um alle verbindlichen Artefakte für virtuelle Linux-Computer anzugeben. 
    
    ![Seite mit verbindlichen Artefakten – Windows- und Linux-Artefakte](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. Wenn Sie ein Artefakt aus der Liste **löschen** möchten, wählen Sie **... (Auslassungspunkte)**  am Ende der Zeile und dann **Löschen**. 
10. Wenn Sie Artefakte in der Liste **neu anordnen** möchten, bewegen Sie die Maus über das Artefakt, wählen Sie **... (Auslassungspunkte)**  am Anfang der Zeile, und ziehen Sie das Element an die neue Position. 
11. Wählen Sie **Speichern**, um verbindliche Artefakte im Lab zu speichern. 

    ![Seite mit verbindlichen Artefakten – Artefakte im Lab speichern](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. Schließen Sie die Seite **Konfiguration und Richtlinien** (wählen Sie **X** in der oberen rechten Ecke), um zur Startseite für Ihr Lab zu zurückzukehren.  

## <a name="delete-a-mandatory-artifact"></a>Löschen eines verbindlichen Artefakts
Wenn Sie ein verbindliches Artefakt aus einem Lab löschen möchten, führen Sie die folgenden Aktionen aus: 

1. Wählen Sie **Konfiguration und Richtlinien** unter **EINSTELLUNGEN** aus. 
2. Wählen Sie **Verbindliche Artefakte** unter **EXTERNE RESSOURCEN**. 
3. Wählen Sie **Bearbeiten** im Abschnitt **Windows** oder **Linux**. In diesem Beispiel wird **Windows** verwendet. 
4. Wählen Sie die Meldung mit der Anzahl der verbindlichen obligatorischen Elemente. 

    ![Seite mit verbindlichen Artefakten – Meldung wählen](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. Wählen Sie auf der Seite **Ausgewählte Artefakte** neben dem zu löschenden Artefakt **... (Auslassungspunkte)** und dann **Entfernen**. 
    
    ![Seite mit verbindlichen Artefakten – Artefakte entfernen](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. Wählen Sie **OK**, um die Seite **Ausgewählte Artefakte** zu schließen. 
7. Wählen Sie **Speichern** auf der Seite mit **Verbindliche Artefakte**.
8. Wiederholen Sie die Schritte für **Linux**-Images (falls notwendig). 
9. Wählen Sie **Speichern**, um die Änderungen am Lab zu speichern. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>Anzeigen verbindlicher Elemente beim Erstellen einer VM
Als Lab-Benutzer können Sie jetzt beim Erstellen einer VM im Lab die Liste der verbindlichen Artefakte anzeigen. Sie können keine verbindlichen Artefakte bearbeiten oder löschen, die Ihr Lab-Besitzer festgelegt hat.

1. Wählen Sie auf der Startseite für Ihr Lab **Übersicht** aus dem Menü.
2. Wählen Sie **+ Hinzufügen**, um dem Lab einen virtuellen Computer hinzuzufügen. 
3. Wählen Sie ein **Basisimage**. Dieses Beispiel verwendet **Windows Server, Version 1709**.
4. Ihnen wird eine Meldung mit der Anzahl der erforderlichen **Artefakte** angezeigt. 
5. Wählen Sie **Artefakte**. 
6. Überprüfen Sie, ob die im Lab festgelegten Konfigurationen und Richtlinien der **verbindlichen Artefakte** angezeigt werden. 

    ![Erstellen eines virtuellen Computers – verbindliche Artefakte](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie, wie Sie einem [ein Git-Artefaktrepository zu einem Lab hinzufügen](devtest-lab-add-artifact-repo.md).


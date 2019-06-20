---
title: Verwalten von Formeln in Azure DevTest Labs zum Erstellen virtueller Computer | Microsoft Docs
description: Erfahren Sie, wie Sie Azure DevTest Labs-Formeln aktualisieren und entfernen.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: d8f2ae64e2f8e694de5a7cf5aa9049e63998dca0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60562530"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Verwalten von Azure DevTest Labs-Formeln

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Dieser Artikel veranschaulicht, wie Sie eine Formel anhand einer Basis (benutzerdefiniertes Image, Marketplace-Image oder eine andere Formel) oder aus einem vorhandenen virtuellen Computer erstellen. In diesem Artikel wird auch gezeigt, wie vorhandene Formeln verwaltet werden.

## <a name="create-a-formula"></a>Erstellen einer Formel
Jeder Benutzer mit der Berechtigung *Benutzer* in DevTest Labs kann virtuelle Computer auf Basis einer Formel erstellen. Es gibt zwei Möglichkeiten, um Formeln zu erstellen: 

* Anhand einer Basis: Entscheiden Sie sich für diese Option, wenn Sie alle Merkmale der Formel definieren möchten.
* Aus einem vorhandenen virtuellen Labcomputer: Entscheiden Sie sich für diese Option, wenn Sie eine Formel basierend auf den Einstellungen eines vorhandenen virtuellen Computers erstellen möchten.

Weitere Informationen zum Hinzufügen von Benutzern und Berechtigungen finden Sie unter [Hinzufügen von Besitzern und Benutzern in Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Erstellen einer Formel anhand einer Basis
In den folgenden Schritten wird beschrieben, wie Sie eine Formel auf der Basis eines benutzerdefinierten Images, eines Marketplace-Images oder auf Grundlage einer anderen Formel erstellen.

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.

2. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.

3. Wählen Sie in der Liste der Labs das gewünschte Lab aus.  

4. Wählen Sie auf der Seite für das Lab die Option **Formeln (wiederverwendbare Basen)** aus.
   
    ![Menü „Formel“](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. Wählen Sie auf der Seite **Formeln** die Option **+ Hinzufügen** aus.
   
    ![Formel hinzufügen](./media/devtest-lab-create-formulas/add-formula.png)

6. Wählen Sie auf der Seite **Basis auswählen** die Grundlage aus, anhand derer Sie die Formel erstellen möchten (also das benutzerdefinierte Image, das Marketplace-Image oder die Formel).
   
    ![Basisliste](./media/devtest-lab-create-formulas/base-list.png)

7. Geben Sie auf der Registerkarte **Grundeinstellungen** der Seite **Formel erstellen** die folgenden Werte an:
   
    * **Formelname**: Geben Sie einen Namen für die Formel ein. Dieser Wert wird in der Liste der Basisimages angezeigt, wenn Sie einen virtuellen Computer erstellen. Der Name wird während der Eingabe überprüft. Falls er nicht gültig ist, werden Sie in einer Meldung über die Anforderungen für einen gültigen Namen informiert.
    * **Benutzername**: Geben Sie einen Benutzernamen ein, dem Administratorrechte erteilt wurden.
    * **Kennwort**: Geben Sie einen Wert ein, der dem geheimen Schlüssel bzw. dem Kennwort zugeordnet ist, das für den angegebenen Benutzer verwendet werden soll, oder wählen Sie diesen Wert aus der Dropdownliste aus. Informationen zum Speichern von Geheimnissen in einem Schlüsseltresor und deren Verwendung beim Erstellen von Lab-Ressourcen finden Sie unter [Speichern von Geheimnissen in Azure DevTest Labs](devtest-lab-store-secrets-in-key-vault.md).
    * **VM-Größe:** Wählen Sie **Größe ändern** aus, um die Größe des virtuellen Computers zu ändern. 
    * **Artefakte:** Wählen Sie die Seite **Artefakte hinzufügen oder entfernen** aus, auf der Sie die Artefakte auswählen und konfigurieren können, die dem Basisimage hinzugefügt werden sollen. Weitere Informationen zu Artefakten finden Sie unter [Erstellen benutzerdefinierter Artefakte für Ihren virtuellen DevTest Labs-Computer](devtest-lab-artifact-author.md).
8. Wechseln Sie zur Registerkarte **Erweiterte Einstellungen**, und geben Sie die folgenden Werte an:
    - **Virtuelles Netzwerk:** Wählen Sie **VNET ändern** aus, um das virtuelle Netzwerk zu ändern. 
    - **Subnetz:** Wählen Sie zum Ändern des Subnetzes die Option **Subnetz ändern** aus. 
    - **IP-Adresskonfiguration**: Geben Sie an, ob Sie öffentliche, private oder freigegebene IP-Adressen verwenden möchten. Weitere Informationen zu freigegebenen IP-Adressen finden Sie unter [Grundlegendes zu freigegebenen IP-Adressen in Azure DevTest Labs](./devtest-lab-shared-ip.md).
    - **Ablaufdatum und -zeit:** Geben Sie Ablaufdatum und -zeit für den virtuellen Computer an, damit dieser automatisch gelöscht wird. 
    - **Diesen Computer als anforderbar festlegen**: Wenn Sie einen Computer als „anforderbar“ festlegen, bedeutet dies, dass dem Computer zum Zeitpunkt der Erstellung kein Besitz zugewiesen wird. Auf der Seite des Labs können Labbenutzer den Besitz des Computers übernehmen („abrufen“).     
    - **Anzahl abrufbarer Instanzen**: Geben Sie an, wie viele abrufbare Instanzen erstellt werden sollen. 
8. Wählen Sie **Senden** aus, um die Formel zu erstellen.

9. Wenn die Formel erstellt wurde, wird sie in der Liste auf der Seite **Formeln** angezeigt.

### <a name="create-a-formula-from-a-vm"></a>Erstellen einer Formel aus einem virtuellen Computer
Die folgenden Schritte führen Sie durch den Prozess der Erstellung einer neuen Formel aus einem vorhandenen virtuellen Labcomputer. 

> [!NOTE]
> Damit Sie anhand eines virtuellen Computers eine Formel erstellen können, muss dieser virtuelle Computer nach dem 30. März 2016 erstellt worden sein. 
> 
> 

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.
2. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
3. Wählen Sie in der Liste der Labs das gewünschte Lab aus.  
4. Wählen Sie auf der Seite **Übersicht** des Labs den virtuellen Computer aus, über den Sie die Formel erstellen möchten.
   
    ![Virtuelle Labs-Computer](./media/devtest-lab-create-formulas/my-vms.png)
5. Wählen Sie auf der Seite für den virtuellen Computer die Option **Formel erstellen (wiederverwendbare Basis)** aus.
   
    ![Formel erstellen](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Geben Sie auf der Seite **Formel erstellen** einen **Namen** und eine **Beschreibung** für die neue Formel ein.
   
    ![Seite „Formel erstellen“](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Wählen Sie **OK** , um die Formel zu erstellen.

## <a name="modify-a-formula"></a>Ändern einer Formel
Um eine Formel zu ändern, gehen Sie folgendermaßen vor:

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.
2. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
3. Wählen Sie in der Liste der Labs das gewünschte Lab aus.  
4. Wählen Sie auf der Seite für das Lab die Option **Formeln (wiederverwendbare Basen)** aus.
   
    ![Menü „Formel“](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Wählen Sie auf der Seite **Labformeln** die Formel aus, die Sie ändern möchten.
6. Nehmen Sie auf der Seite **Formel aktualisieren** die gewünschten Änderungen vor, und wählen Sie **Aktualisieren** aus.

## <a name="delete-a-formula"></a>Löschen einer Formel
Um eine Formel zu löschen, gehen Sie folgendermaßen vor:

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.
2. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
3. Wählen Sie in der Liste der Labs das gewünschte Lab aus.  
4. Wählen Sie auf der Seite **Einstellungen** für das Lab die Option **Formeln** aus.
   
    ![Menü „Formel“](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Wählen Sie auf der Seite **Labformeln** die Auslassungspunkte rechts neben der zu löschenden Formel aus.
   
    ![Menü „Formel“](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. Wählen Sie im Kontextmenü der Formel die Option **Löschen**aus.
   
    ![Kontextmenü „Formel“](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Wählen Sie im Bestätigungsdialogfeld **Ja** .

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Verwandte Blogbeiträge
* [Custom images or formulas? (Benutzerdefinierte Images oder Formeln?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie eine Formel erstellt haben, die zum Erstellen virtueller Computer verwendet werden soll, besteht der nächste Schritt darin, [Ihrem Lab einen virtuellen Computer hinzuzufügen](devtest-lab-add-vm.md).


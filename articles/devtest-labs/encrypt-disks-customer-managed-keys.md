---
title: Verschlüsseln von Betriebssystemdatenträgern mithilfe von kundenseitig verwalteten Schlüsseln in Azure DevTest Labs
description: Erfahren Sie, wie Sie Betriebssystemdatenträger mithilfe von kundenseitig verwalteten Schlüsseln in Azure DevTest Labs verschlüsseln.
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: 26ef4ff1529483da9956c6dcc43807af0ffd6463
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341206"
---
# <a name="encrypt-operating-system-os-disks-using-customer-managed-keys-in-azure-devtest-labs"></a>Verschlüsseln von Betriebssystem-Datenträgern mithilfe von kundenseitig verwalteten Schlüsseln in Azure DevTest Labs
Die serverseitige Verschlüsselung (Server-side Encryption, SSE) schützt Ihre Daten und unterstützt Sie beim Einhalten der Sicherheits- und Complianceanforderungen Ihrer Organisation. Durch SSE werden Ihre auf verwalteten Datenträgern (Betriebssystemdatenträger und reguläre Datenträger) in Azure gespeicherten ruhenden Daten standardmäßig automatisch verschlüsselt, wenn sie in der Cloud gespeichert werden. Erfahren Sie mehr über [Datenträgerverschlüsselung](../virtual-machines/disk-encryption.md) in Azure. 

In DevTest Labs werden alle Betriebssystemdatenträger und Datenträger, die als Teil eines Labs erstellt werden, mit von der Plattform verwalteten Schlüsseln verschlüsselt. Als Lab-Besitzer können Sie jedoch Lab-VM-Betriebssystemdatenträger mit ihren eigenen Schlüsseln verschlüsseln. Wenn Sie die Verschlüsselung mit eigenen Schlüsseln verwalten möchten, können Sie einen **kundenseitig verwalteten Schlüssel** angeben, der zum Verschlüsseln von Daten auf Lab-Betriebssystemdatenträgern verwendet werden soll. Weitere Informationen zur serverseitigen Verschlüsselung (Server Side Encryption, SSE) mit kundenseitig verwalteten Schlüsseln und anderen Verschlüsselungstypen für verwaltete Datenträger finden Sie unter [Kundenseitig verwaltete Schlüssel](../virtual-machines/disk-encryption.md#customer-managed-keys). Lesen Sie auch [Einschränkungen bei der Verwendung von kundenseitig verwalteten Schlüsseln](../virtual-machines/disks-enable-customer-managed-keys-portal.md#restrictions).

> [!NOTE]
> - Derzeit wird die Datenträgerverschlüsselung mit einem kundenseitig verwalteten Schlüssel nur für Betriebssystemdatenträger in DevTest Labs unterstützt. 
> 
> - Diese Einstellung gilt für neu erstellte Betriebssystemdatenträger im Lab. Wenn Sie sich entscheiden, den Datenträgerverschlüsselungssatz irgendwann zu ändern, bleiben ältere Datenträger im Lab weiterhin mit dem vorherigen Datenträgerverschlüsselungssatz verschlüsselt. 

Der folgende Abschnitt zeigt, wie ein Lab-Besitzer die Verschlüsselung mit einem kundenseitig verwalteten Schlüssel einrichten kann.

## <a name="pre-requisites"></a>Voraussetzungen

1. Wenn Sie über keinen Datenträgerverschlüsselungssatz verfügen, vollziehen Sie die Anleitungen in diesem Artikel nach, um [einen Key Vault und einen Datenträgerverschlüsselungssatz einzurichten](../virtual-machines/disks-enable-customer-managed-keys-portal.md). Beachten Sie die folgenden Anforderungen für den Datenträgerverschlüsselungssatz: 

    - Der Datenträgerverschlüsselungssatz muss sich **in derselben Region und im gleichen Abonnement wie ihr Lab befinden**. 
    - Stellen Sie sicher, dass Sie (der Lab-Besitzer) mindestens über **Zugriff auf Leserebene** für den Datenträgerverschlüsselungssatz verfügen, der zum Verschlüsseln von Lab-Betriebssystemdatenträgern verwendet wird. 
1. Bei Labs, die vor dem 1.8.2020 erstellt wurden, muss der Labbesitzer sicherstellen, dass die zugewiesene Identität des Labsystems aktiviert ist. Hierzu kann der Labbesitzer zum Lab wechseln, auf **Konfiguration und Richtlinien** und dann auf das Blatt **Identität (Vorschau)** klicken, die vom System zugewiesene Identität **Status** in **Ein** ändern und auf **Speichern** klicken. Bei neuen Labs, die nach dem 1.8.2020 erstellt wurden, ist die vom System zugewiesene Labidentität standardmäßig aktiviert. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/managed-keys.png" alt-text="Verwaltete Schlüssel":::
1. Damit das Lab die Verschlüsselung für alle Lab-Betriebssystemdatenträger verarbeitet, muss der Besitzer des Labs dem Lab explizit die Leserolle der **vom System zugewiesenen Identität** für den Datenträgerverschlüsselungssatz und die Rolle „Mitwirkender für virtuelle Computer“ für das zugrunde liegende Azure-Abonnement gewähren. Der Lab-Besitzer kann zu diesem Zweck die folgenden Schritte ausführen:

   
    1. Stellen Sie sicher, dass Sie Mitglied der [Rolle „Benutzerzugriffsadministrator“](../role-based-access-control/built-in-roles.md#user-access-administrator) auf Azure-Abonnementebene sind, damit Sie den Benutzerzugriff auf Azure-Ressourcen verwalten können. 
    1. Wählen Sie im linken Menü der Seite **Datenträgerverschlüsselungssatz** die Option **Zugriffssteuerung (IAM)** aus. 
    1. Klicken Sie auf der Symbolleiste auf **+ Hinzufügen**, und wählen Sie **Rollenzuweisung hinzufügen** aus.  

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/add-role-management-menu.png" alt-text="Rollenverwaltung hinzufügen: Menü":::
    1. Wählen Sie auf der Seite **Rollenzuweisung hinzufügen** die Rolle **Leser** oder eine Rolle aus, die umfangreicheren Zugriff zulässt. 
    1. Geben Sie den Lab-Namen ein, für den der Datenträgerverschlüsselungssatz verwendet werden soll, und wählen Sie in der Dropdownliste den Lab-Namen (vom System zugewiesene Identität für das Lab) aus. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/select-lab.png" alt-text="Auswählen der vom System verwalteten Identität des Labs":::        
    1. Wählen Sie auf der Symbolleiste **Speichern** aus. 

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/save-role-assignment.png" alt-text="Speichern der Rollenzuweisung":::
3. Fügen Sie die **vom System zugewiesene Identität** des Labs mithilfe der Seite **Abonnement** -> **Zugriffssteuerung (IAM)** der Rolle **Mitwirkender für virtuelle Computer** hinzu. Die Schritte den vorherigen Schritten. 

    
    1. Navigieren Sie im Azure-Portal zur Seite **Abonnement**. 
    1. Wählen Sie die Option **Zugriffssteuerung (IAM)** aus. 
    1. Klicken Sie auf der Symbolleiste auf **+ Hinzufügen**, und wählen Sie **Rollenzuweisung hinzufügen** aus. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/subscription-access-control-page.png" alt-text="Seite „Abonnement > Zugriffssteuerung (IAM)“":::
    1. Wählen Sie auf der Seite **Rollenzuweisung hinzufügen** die Option **Mitwirkender für virtuelle Computer** als Rolle aus.
    1. Geben Sie den Lab-Namen ein, und wählen Sie in der Dropdownliste den **Lab-Namen** (vom System zugewiesene Identität für das Lab) aus. 
    1. Wählen Sie auf der Symbolleiste **Speichern** aus. 

## <a name="encrypt-lab-os-disks-with-a-customer-managed-key"></a>Verschlüsseln von Lab-Betriebssystemdatenträgern mit einem kundenseitig verwalteten Schlüssel 

1. Wählen Sie auf der Homepage Ihres Labs im Azure-Portal **Konfiguration und Richtlinien** im linken Menü aus. 
1. Wählen Sie auf der Seite **Konfiguration und Richtlinien** im Abschnitt **Verschlüsselung** die Option **Datenträger (Vorschau)** aus. Standardmäßig ist **Verschlüsselungstyp** auf **Verschlüsselung ruhender Daten mit einem plattformseitig verwalteten Schlüssel** festgelegt.

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disks-page.png" alt-text="Registerkarte „Datenträger“ der Seite „Konfiguration und Richtlinien“":::
1. Wählen Sie las **Verschlüsselungstyp** die Option **Verschlüsselung ruhender Daten mit einem kundenseitig verwalteten Schlüssel** aus der Dropdownliste aus. 
1. Wählen Sie als **Datenträgerverschlüsselungssatz** den zuvor erstellten Datenträgerverschlüsselungssatz aus. Dabei handelt es sich um den gleichen Datenträgerverschlüsselungssatz, auf den die vom System zugewiesene Identität des Labs zugreifen kann.
1. Wählen Sie auf der Symbolleiste **Speichern** aus. 

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disk-encryption-set.png" alt-text="Aktivieren der Verschlüsselung mit kundenseitig verwaltetem Schlüssel":::
1. Wählen Sie um Textfeld mit dem folgenden Text: *Diese Einstellung gilt für neu erstellte Computer im Lab. Der alte Betriebssystemdatenträger bleibt mit dem alten Datenträgerverschlüsselungssatz verschlüsselt* die Option **OK** aus. 

    Nach der Konfiguration werden die Betriebssystemdatenträger mit dem kundenseitig verwalteten Schlüssel verschlüsselt, der mithilfe des Datenträgerverschlüsselungssatzes bereitgestellt wird. 
   
## <a name="how-to-validate-if-disks-are-being-encrypted"></a>Überprüfen, ob Datenträger verschlüsselt werden

1. Navigieren Sie zu einem virtuellen Labcomputer, der nach der Aktivierung der Datenträgerverschlüsselung mit einem kundenseitig verwalteten Schlüssel im Lab erstellt wurde.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/enabled-encryption-vm.png" alt-text="Virtueller Computer mit aktivierter Datenträgerverschlüsselung":::
1. Klicken Sie auf die Ressourcengruppe des virtuellen Computers und anschließend auf den Betriebssystemdatenträger.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/vm-resource-group.png" alt-text="Ressourcengruppe des virtuellen Computers":::
1. Navigieren Sie zu „Verschlüsselung“, und überprüfen Sie, ob die Verschlüsselung auf den von Ihnen ausgewählten Datenträgerverschlüsselungssatz mit kundenseitig verwaltetem Schlüssel festgelegt ist.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/validate-encryption.png" alt-text="Überprüfen der Verschlüsselung":::
  
## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in folgenden Artikeln: 

- [Azure Disk Encryption](../virtual-machines/disk-encryption.md). 
- [Kundenseitig verwaltete Schlüssel](../virtual-machines/disk-encryption.md#customer-managed-keys)
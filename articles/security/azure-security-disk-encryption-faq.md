---
title: FAQ – Azure Disk Encryption für virtuelle Linux-IaaS-Computer | Microsoft-Dokumentation
description: Dieser Artikel bietet Antworten auf häufig gestellte Fragen zu Microsoft Azure Disk Encryption für virtuelle Windows- und Linux-IaaS-Computer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 149452bd7d43ce46f320b9bae63a6f9cd48d98d4
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730703"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>Azure Disk Encryption für virtuelle IaaS-Computer – FAQ

Dieser Artikel bietet Antworten auf häufig gestellte Fragen zu Azure Disk Encryption für virtuelle Windows- und Linux-IaaS-Computer. Weitere Informationen zu diesem Dienst finden Sie unter [Azure Disk Encryption für virtuelle Windows- und Linux-IaaS-Computer](azure-security-disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Wo ist Azure Disk Encryption allgemein verfügbar?

Azure Disk Encryption für virtuelle Windows- und Linux-IaaS-Computer ist in allen öffentlichen Azure-Regionen allgemein verfügbar.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Welche Benutzeroberflächen sind für Azure Disk Encryption verfügbar?

Azure Disk Encryption mit allgemeiner Verfügbarkeit unterstützt Azure Resource Manager-Vorlagen, Azure PowerShell und die Azure CLI. Dank der unterschiedlichen Benutzeroberflächen können Sie flexibel vorgehen. Ihnen stehen drei Optionen zum Aktivieren der Datenträgerverschlüsselung für Ihre IaaS-VMs zur Verfügung. Weitere Informationen zur Benutzeroberfläche und eine Schritt-für-Schritt-Anleitung für Azure Disk Encryption finden Sie unter [Aktivieren von Azure Disk Encryption für Windows](azure-security-disk-encryption-windows.md) und [Aktivieren von Azure Disk Encryption für Linux](azure-security-disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Was kostet Azure Disk Encryption?

Es fallen keine Gebühren für die Verschlüsselung von VM-Datenträgern mit Azure Disk Encryption an, es werden jedoch Gebühren für die Verwendung von Azure Key Vault berechnet. Weitere Informationen zu den Azure Key Vault-Kosten finden Sie auf der Seite [Key Vault – Preise](https://azure.microsoft.com/pricing/details/key-vault/).


## <a name="which-virtual-machine-tiers-does-azure-disk-encryption-support"></a>Welche VM-Tarife unterstützt Azure Disk Encryption?

Azure Disk Encryption ist in VMs mit Standardtarifen einschließlich IaaS-VMs der Serien [A, D, DS, G, GS und F](https://azure.microsoft.com/pricing/details/virtual-machines/) verfügbar. Sie ist auch für virtuelle Computer mit Storage Premium verfügbar. Für virtuelle Computer im Tarif „Basic“ steht sie nicht zur Verfügung.

## <a name="bkmk_LinuxOSSupport"></a> Welche Linux-Distributionen werden von Azure Disk Encryption unterstützt?

Azure Disk Encryption wird von einer Teilmenge der [Azure zugelassenen Linux-Distributionen](../virtual-machines/linux/endorsed-distros.md) unterstützt, die selbst eine Teilmenge aller möglichen Linux-Serverdistributionen ist.

 ![Venn-Diagramm zu den Linux-Serverdistributionen, die Azure Disk Encryption unterstützen.](./media/azure-security-disk-encryption-faq/ade-supported-distros.png)

Nicht von Azure zugelassene Linux-Serverdistributionen unterstützen Azure Disk Encryption nicht. Von den zugelassenen Distributionen unterstützen nur die folgenden Distributionen und Versionen Azure Disk Encryption:

| Linux-Distribution | Version | Für die Verschlüsselung unterstützter Volumetyp|
| --- | --- |--- |
| Ubuntu | 18,04| Betriebssystem- und andere Datenträger |
| Ubuntu | 16.04| Betriebssystem- und andere Datenträger |
| Ubuntu | 14.04.5</br>[für Azure optimierter Kernel aktualisiert auf 4.15 oder eine höhere Version](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | Betriebssystem- und andere Datenträger |
| RHEL | 7.6 | Betriebssystem- und andere Datenträger (siehe der Hinweis unten) |
| RHEL | 7,5 | Betriebssystem- und andere Datenträger (siehe der Hinweis unten) |
| RHEL | 7.4 | Betriebssystem- und andere Datenträger (siehe der Hinweis unten) |
| RHEL | 7.3 | Betriebssystem- und andere Datenträger (siehe der Hinweis unten) |
| RHEL | 7.2 | Betriebssystem- und andere Datenträger (siehe der Hinweis unten) |
| RHEL | 6,8 | Datenträger für Daten (siehe der Hinweis unten) |
| RHEL | 6.7 | Datenträger für Daten (siehe der Hinweis unten) |
| CentOS | 7,5 | Betriebssystem- und andere Datenträger |
| CentOS | 7.4 | Betriebssystem- und andere Datenträger |
| CentOS | 7.3 | Betriebssystem- und andere Datenträger |
| CentOS | 7.2n | Betriebssystem- und andere Datenträger |
| CentOS | 6,8 | Datenträger |
| openSUSE | 42.3 | Datenträger |
| SLES | 12-SP4 | Datenträger |
| SLES | 12-SP3 | Datenträger |

> [!NOTE]
> Die neue ADE-Implementierung wird für Betriebssystem- und andere Datenträger für RHEL7-Images mit nutzungsbasierter Bezahlung unterstützt. ADE wird derzeit nicht für RHEL-BYOS-Images (Bring-Your-Own-Subscription) unterstützt. Weitere Informationen finden Sie unter [Azure Disk Encryption für Linux](azure-security-disk-encryption-linux.md).

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Wie sehen die ersten Schritte mit Azure Disk Encryption aus?

Informationen zu den ersten Schritten finden Sie unter [Azure Disk Encryption Overview](azure-security-disk-encryption-overview.md) (Azure Disk Encryption – Übersicht).

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Kann ich sowohl Start- als auch Datenvolumes mit Azure Disk Encryption verschlüsseln?

Ja. Sie können Start- und Datenvolumes für virtuelle Windows- und Linux-IaaS-Computer verschlüsseln. Bei virtuellen Windows-Computern können die Daten ohne vorherige Verschlüsselung des Betriebssystemvolumes nicht verschlüsselt werden. Bei virtuellen Linux-Computern kann das Datenvolume ohne vorherige Verschlüsselung des Betriebssystemvolumes verschlüsselt werden. Wenn Sie das Betriebssystemvolume für Linux verschlüsselt haben, wird die Deaktivierung der Verschlüsselung für ein Betriebssystemvolume für virtuelle Linux-IaaS-Computer nicht unterstützt. Bei virtuellen Linux-Computern in einem Skalierungsgruppe kann nur das Datenvolumen verschlüsselt werden.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Kann ich ein nicht bereitgestelltes Volume mit Azure Disk Encryption verschlüsseln?

Nein, Azure Disk Encryption verschlüsselt nur bereitgestellte Volumes.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Wie rotiere ich geheime oder Verschlüsselungsschlüssel?

Zum Rotieren geheimer Schlüssel rufen Sie einfach den gleichen Befehl auf, den Sie ursprünglich zur Aktivierung der Datenträgerverschlüsselung verwendet haben, und geben Sie einen anderen Key Vault an. Zum Rotieren des Schlüssels für die Verschlüsselung anderer Schlüssel (Key Encryption Key, KEK) rufen Sie den gleichen Befehl auf, den Sie ursprünglich zum Aktivieren der Datenträgerverschlüsselung verwendet haben, und geben die neue Schlüsselverschlüsselung an. 

>[!WARNING]
> - Wenn Sie zuvor [Azure Disk Encryption mit Azure AD-App](azure-security-disk-encryption-prerequisites-aad.md) durch Angabe von Azure AD-Anmeldeinformationen zum Verschlüsseln dieses virtuellen Computers verwendet haben, müssen Sie diese Verschlüsselungsoption auch weiterhin für Ihren virtuellen Computer verwenden. Sie können [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) auf dieser verschlüsselten VM nicht verwenden, da dies kein unterstütztes Szenario ist. Das bedeutet, das Verlassen der AAD-Anwendung für diese verschlüsselte VM wird noch nicht unterstützt.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Wie füge ich einen Schlüssels für die Verschlüsselung anderer Schlüssel (Key Encryption Key, KEK) hinzu oder entferne ihn, wenn ich ursprünglich keinen verwendet habe?

Um einen KEK hinzuzufügen, rufen Sie den Aktivierungsbefehl erneut auf, und übergeben den KEK-Parameter. Um einen KEK zu entfernen, rufen Sie den Aktivierungsbefehl erneut ohne den KEK-Parameter auf.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Ermöglicht Azure Disk Encryption die Verwendung von BYOK (Bring Your Own Key)?

Ja. Sie können Ihre eigenen Schlüssel für die Verschlüsselung anderer Schlüssel (Key Encryption Keys, KEKs) angeben. Diese Schlüssel werden in Azure Key Vault, dem Schlüsselspeicher für Azure Disk Encryption, aufbewahrt. Weitere Informationen zu den einzelnen KEK-Szenarien finden Sie unter [Azure Disk Encryption Prerequisites](azure-security-disk-encryption-prerequisites.md) (Azure Disk Encryption – Voraussetzungen).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Kann ich einen von Azure erstellten KEK verwenden?

Ja. Sie können Azure Key Vault verwenden, um einen KEK für Azure Disk Encryption zu erstellen. Diese Schlüssel werden in Azure Key Vault, dem Schlüsselspeicher für Azure Disk Encryption, aufbewahrt. Weitere Informationen zum Schlüssel für die Schlüsselverschlüsselung (KEK) finden Sie unter [Azure Disk Encryption Prerequisites](azure-security-disk-encryption-prerequisites.md) (Azure Disk Encryption – Voraussetzungen).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Kann ich den lokalen Schlüsselverwaltungsdienst oder HSM verwenden, um die Verschlüsselungsschlüssel zu schützen?

Sie können den lokalen Schlüsselverwaltungsdienst oder HSM nicht verwenden, um die Verschlüsselungsschlüssel mit Azure Disk Encryption zu schützen. Sie können hierzu nur den Azure Key Vault-Dienst verwenden. Weitere Informationen zu den einzelnen KEK-Szenarien finden Sie unter [Azure Disk Encryption Prerequisites](azure-security-disk-encryption-prerequisites.md) (Azure Disk Encryption – Voraussetzungen).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Was sind die Voraussetzungen für die Konfiguration von Azure Disk Encryption?

Für Azure Disk Encryption müssen einige Voraussetzungen erfüllt werden. Lesen Sie den Artikel [Azure Disk Encryption – Voraussetzungen](azure-security-disk-encryption-prerequisites.md), um einen neuen Schlüsseltresor zu erstellen oder einen vorhandenen Schlüsseltresor für den verschlüsselten Datenträgerzugriff einzurichten, um die Verschlüsselung zu aktivieren und Geheimnisse und Schlüssel zu schützen. Weitere Informationen zu den einzelnen KEK-Szenarien finden Sie unter [Azure Disk Encryption Overview](azure-security-disk-encryption-overview.md) (Azure Disk Encryption – Übersicht).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Welche Voraussetzungen müssen für die Konfiguration von Azure Disk Encryption mit einer Azure AD-App (Vorgängerversion) erfüllt sein?

Für Azure Disk Encryption müssen einige Voraussetzungen erfüllt werden. Lesen Sie den Artikel [Azure Disk Encryption Prerequisites](azure-security-disk-encryption-prerequisites-aad.md) (Azure Disk Encryption – Voraussetzungen), um eine Azure Active Directory-Anwendung zu erstellen, einen neuen Schlüsseltresor zu erstellen oder einen vorhandenen Schlüsseltresor für den verschlüsselten Datenträgerzugriff einzurichten, um die Verschlüsselung zu aktivieren und Geheimnisse und Schlüssel zu schützen. Weitere Informationen zu den einzelnen KEK-Szenarien finden Sie unter [Azure Disk Encryption Overview](azure-security-disk-encryption-overview.md) (Azure Disk Encryption – Übersicht).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Wird Azure Disk Encryption mit einer Azure AD-App (Vorgängerversion) weiterhin unterstützt?
Ja. Die Datenträgerverschlüsselung mit einer Azure AD-App wird weiterhin unterstützt. Für die Verschlüsselung neuer virtueller Computer empfiehlt sich jedoch die Verwendung der neuen Methode, anstatt eine Azure AD-App für die Verschlüsselung verwenden. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Kann ich virtuelle Computer, die mit einer Azure AD-App verschlüsselt wurden, zur Verschlüsselung ohne Azure AD-App migrieren?
  Derzeit gibt es für Computer, die mit einer Azure AD-App verschlüsselt wurden, keinen direkten Migrationspfad zur Verschlüsselung ohne Azure AD-App. Es gibt außerdem keinen direkten Pfad von der Verschlüsselung ohne Azure AD-App zur Verschlüsselung mit AD-App. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Welche Version von Azure PowerShell wird von Azure Disk Encryption unterstützt?

Verwenden Sie die neueste Version des Azure PowerShell SDK, um Azure Disk Encryption zu konfigurieren. Laden Sie die neueste Version von [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) herunter. Azure Disk Encryption wird *nicht* vom Azure SDK Version 1.1.0 unterstützt.

> [!NOTE]
> Die Vorschauerweiterung von Azure Disk Encryption auf Linux ist veraltet. Weitere Informationen finden Sie unter [Deprecating Azure disk encryption preview extension for Linux IaaS VMs](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/) (Die Vorschauerweiterung von Azure Disk Encryption unter Linux ist veraltet).

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Kann ich Azure Disk Encryption auf mein benutzerdefiniertes Linux-Image anwenden?

Sie können Azure Disk Encryption nicht auf Ihr benutzerdefiniertes Linux-Image anwenden. Nur die Linux-Images im Katalog für die zuvor genannten unterstützten Distributionen werden unterstützt. Benutzerdefinierte Linux-Images werden derzeit nicht unterstützt.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Kann ich eine Linux Red Hat-VM mit dem „yum“-Update aktualisieren?

Ja. Sie können auf eine Linux Red Hat-VM ein „yum“-Update anwenden.  Weitere Informationen finden Sie unter [Linux-Paketverwaltung hinter einer Firewall](azure-security-disk-encryption-tsg.md#linux-package-management-behind-a-firewall).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Welcher Azure Disk Encryption-Workflow wird für Linux empfohlen?

Der folgende Workflow wird empfohlen, um unter Linux die besten Ergebnisse zu erzielen:
* Beginnen Sie mit dem unveränderten Katalogimage für die gewünschte Distribution und Version des Betriebssystems.
* Sichern Sie alle eingebundenen Laufwerke, die verschlüsselt werden.  Diese Sicherung ermöglicht die Wiederherstellung bei einem Ausfall – etwa, wenn der virtuelle Computer vor Abschluss der Verschlüsselung neu gestartet wird.
* Führen Sie die Verschlüsselung durch (dies kann je nach VM-Merkmalen und der Größe etwaiger angefügten Datenträger mehrere Stunden oder sogar Tage dauern).
* Passen Sie das Image an, und fügen Sie bei Bedarf Software hinzu.

Ist dieser Workflow nicht möglich, stellt die [Storage Service Encryption](../storage/common/storage-service-encryption.md) (SSE, Speicherdienstverschlüsselung) auf der Ebene des Plattformspeicherkontos möglicherweise eine Alternative zur vollständigen Datenträgerverschlüsselung mit „dm-crypt“ dar.

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>Um was handelt es sich beim Datenträger „Bek Volume“ bzw. „/mnt/azure_bek_disk“?

„Bek volume“ für Windows oder „/mnt/azure_bek_disk“ für Linux ist ein lokales Datenvolume, auf dem die Verschlüsselungsschlüssel für verschlüsselte Azure-IaaS-VMs sicher gespeichert werden.
> [!NOTE]
> Löschen oder bearbeiten Sie keine Inhalte auf diesem Datenträger. Die Bereitstellung des Datenträgers kann nicht aufgehoben werden, da für Verschlüsselungsvorgänge auf der IaaS-VM der Verschlüsselungsschlüssel vorhanden sein muss.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Welche Verschlüsselungsmethode verwendet Azure Disk Encryption?

Unter Windows verwendet ADE das BitLocker-AES256-Verschlüsselungsverfahren (AES256WithDiffuser für Versionen vor Windows Server 2012). Unter Linux verwendet ADE den Entschlüsselungsstandard von aes-xts-plain64 mit einem 256-Bit-Volumehauptschlüssel.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Werden die Daten auf den bereits verschlüsselten Datenlaufwerken gelöscht, wenn ich EncryptFormatAll verwende und alle Volumetypen angebe?
Nein, Daten werden nicht von Datenträgern für Daten gelöscht, die bereits mit Azure Disk Encryption verschlüsselt wurden. Ebenso wie EncryptFormatAll das Betriebssystemlaufwerk nicht erneut verschlüsselte, werden auch bereits verschlüsselte Laufwerke für Daten nicht erneut verschlüsselt. Weitere Informationen finden Sie unter den [Kriterien für EncryptFormatAll](azure-security-disk-encryption-linux.md#bkmk_EFACriteria).        

## <a name="is-xfs-filesystem-supported"></a>Wird das XFS-Dateisystem unterstützt?
XFS-Volumes werden für die Datenträgerverschlüsselung nur mit „EncryptFormatAll“ unterstützt. Das Volume wird hierbei neu formatiert, und alle zuvor vorhandenen Daten werden gelöscht. Weitere Informationen finden Sie unter den [Kriterien für EncryptFormatAll](azure-security-disk-encryption-linux.md#bkmk_EFACriteria).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Kann ich einen verschlüsselten virtuellen Computer sichern und wiederherstellen? 

Azure Backup bietet einen Mechanismus zum Sichern und Wiederherstellen verschlüsselter virtueller Computer innerhalb desselben Abonnements und derselben Region.  Anleitungen finden Sie unter [Sichern und Wiederherstellen verschlüsselter virtueller Computer mit Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).  Das Wiederherstellen eines verschlüsselten virtuellen Computers in einer anderen Region wird derzeit nicht unterstützt.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Wo kann ich Fragen stellen oder Feedback geben?

Sie können im [Azure Disk Encryption-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption) Fragen stellen und Feedback geben.

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie Antworten auf die am häufigsten gestellten Fragen im Zusammenhang mit Azure Disk Encryption erhalten. Weitere Informationen zu diesem Dienst finden Sie in den folgenden Artikeln:

- [Übersicht über Azure Disk Encryption](azure-security-disk-encryption-overview.md)
- [Anwenden der Datenträgerverschlüsselung in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Datenverschlüsselung ruhender Azure-Daten](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)

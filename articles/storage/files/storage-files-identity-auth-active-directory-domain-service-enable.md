---
title: Verwenden von Azure AD Domain Services zum Autorisieren des Zugriffs auf Dateidaten über SMB
description: Erfahren Sie, wie Sie eine identitätsbasierte Authentifizierung über SMB (Server Message Block) für Azure Files über Azure Active Directory Domain Services aktivieren. Ihre in die Domäne eingebundenen virtuellen Windows-Computer (VMs) können dann mit Azure AD-Anmeldeinformationen auf Azure-Dateifreigaben zugreifen.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: rogarana
ms.subservice: files
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: deed7c3dce2d7da1940a8a4871efac7453e2cf60
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033679"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Aktivieren der Azure Active Directory Domain Services-Authentifizierung über Azure Files

[Azure Files](storage-files-introduction.md) unterstützt die identitätsbasierte Authentifizierung über SMB (Server Message Block) über zwei Arten von Domänendiensten: lokale Active Directory Domain Services (AD DS) und Azure Active Directory Domain Services (Azure AD DS). Es wird dringend empfohlen, den Abschnitt [Funktionsweise](./storage-files-active-directory-overview.md#how-it-works) zu überprüfen, um den richtigen Domänendienst für die Authentifizierung auszuwählen. Die Einrichtung unterscheidet sich abhängig von dem von Ihnen gewählten Domänendienst. In diesem Artikel wird das Aktivieren und Konfigurieren von Azure AD DS für die Authentifizierung mit Azure-Dateifreigaben erläutert.

Wenn Sie noch nicht mit Azure-Dateifreigaben vertraut sind, empfiehlt es sich, unsere [Planhinweisliste](storage-files-planning.md) zu lesen, bevor Sie die folgende Artikelreihe lesen.

> [!NOTE]
> Azure Files unterstützt Kerberos-Authentifizierung mit Azure AD DS mit RC4-HMAC-Verschlüsselung. AES-Kerberos-Verschlüsselung wird noch nicht unterstützt.
> Azure Files unterstützt die Authentifizierung für Azure AD DS mit vollständiger Synchronisierung mit Azure AD. Wenn Sie die bereichsbezogene Synchronisierung in Azure AD DS aktiviert haben, bei der nur eine begrenzte Gruppe von Identitäten von Azure AD synchronisiert wird, wird die Authentifizierung und Autorisierung nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Azure AD über SMB für Azure-Dateifreigaben aktivieren, sollten Sie sicherstellen, dass Sie die folgenden Voraussetzungen erfüllt haben:

1.  **Wählen Sie einen Azure AD-Mandanten aus oder erstellen Sie einen.**

    Sie können einen neuen oder vorhandenen Mandanten für die Azure AD-Authentifizierung über SMB verwenden. Der Mandant und die Dateifreigabe, auf die Sie zugreifen möchten, müssen dem gleichen Abonnement zugeordnet sein.

    Zum Erstellen eines neuen Azure AD-Mandanten können Sie [einen Azure AD-Mandanten und ein Azure AD-Abonnement hinzufügen](/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Wenn Sie über einen vorhandenen Azure AD-Mandanten verfügen, aber einen neuen Mandanten für die Verwendung mit Azure-Dateifreigaben erstellen möchten, finden Sie weitere Informationen unter [Erstellen eines Azure Active Directory-Mandanten](/rest/api/datacatalog/create-an-azure-active-directory-tenant).

1.  **Aktivieren Sie Azure AD Domain Services auf dem Azure AD-Mandanten.**

    Zur Unterstützung der Authentifizierung mit Azure AD-Anmeldeinformationen müssen Sie Azure AD Domain Services für Ihren Azure AD-Mandanten aktivieren. Wenn Sie nicht der Administrator des Azure AD-Mandanten sind, wenden Sie sich an den Administrator und folgen Sie der schrittweisen Anleitung zum [Aktivieren von Azure Active Directory Domain Services über das Azure-Portal](../../active-directory-domain-services/tutorial-create-instance.md).

    Es dauert in der Regel etwa 15 Minuten, bis eine Bereitstellung von Azure AD DS abgeschlossen ist. Vergewissern Sie sich, dass der Integritätsstatus von Azure AD DS bei aktivierter Kennworthashsynchronisierung **Wird ausgeführt** anzeigt, bevor Sie mit dem nächsten Schritt fortfahren.

1.  **Domänenbeitritt einer Azure-VM mit Azure AD DS.**

    Damit Sie mit Azure AD-Anmeldeinformationen von einem virtuellen Computer auf eine Dateifreigabe zugreifen können, muss Ihr virtueller Computer in Azure AD DS in eine Domäne eingebunden sein. Weitere Informationen zum Einbinden in die Domäne eines virtuellen Computers finden Sie unter [Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Domäne](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Die Azure AD DS-Authentifizierung über SMB mit Azure-Dateifreigaben wird nur auf virtuellen Azure-Computern unterstützt, die auf Betriebssystemversionen über Windows 7 oder Windows Server 2008 R2 ausgeführt werden.

1.  **Wählen oder erstellen Sie eine Azure-Dateifreigabe.**

    Wählen Sie eine neue oder vorhandene Dateifreigabe aus, die mit demselben Abonnement wie Ihr Azure AD-Mandant verbunden ist. Weitere Informationen zum Erstellen einer neuen Dateifreigabe finden Sie unter [Erstellen einer Dateifreigabe in Azure Files](storage-how-to-create-file-share.md).
    Für eine optimale Leistung wird empfohlen, dass sich Ihre Dateifreigabe in derselben Region befindet wie der virtuelle Computer, von dem aus Sie auf die Freigabe zugreifen möchten.

1.  **Überprüfen Sie die Konnektivität von Azure Files, indem Sie Azure-Dateifreigaben mit Ihrem Speicherkontenschlüssel einbinden.**

    Um sicherzustellen, dass Ihr virtueller Computer und die Dateifreigabe richtig konfiguriert sind, versuchen Sie, die Dateifreigabe mit Ihrem Speicherkontenschlüssel einzubinden. Weitere Informationen finden Sie unter [Einbinden einer Azure-Dateifreigabe und Zugreifen auf die Freigabe unter Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Regionale Verfügbarkeit

Die Azure Files-Authentifizierung bei Azure AD DS ist in [allen öffentlichen Azure-Regionen und Azure Government-Regionen](https://azure.microsoft.com/global-infrastructure/locations/) verfügbar.

## <a name="overview-of-the-workflow"></a>Übersicht über den Workflow

Bevor Sie Azure AD DS-Authentifizierung über SMB für Azure-Dateifreigaben aktivieren, vergewissern Sie sich, dass Ihre Azure AD- und Azure Storage-Umgebungen ordnungsgemäß konfiguriert sind. Es wird empfohlen, dass Sie die [Voraussetzungen](#prerequisites) durchgehen, um sicherzustellen, dass Sie alle erforderlichen Schritte ausgeführt haben.

Als Nächstes gewähren Sie Zugriff auf Azure Files-Ressourcen mit Azure AD-Anmeldeinformationen, indem Sie die folgenden Schritte ausführen:

1. Aktivieren Sie die Azure AD DS-Authentifizierung über SMB für Ihr Speicherkonto, um das Speicherkonto bei der zugehörigen Bereitstellung von Azure AD DS zu registrieren.
2. Weisen Sie einer Azure AD-Identität (Benutzer, Gruppe oder Dienstprinzipal) Zugriffsberechtigungen für eine Freigabe zu.
3. Konfigurieren Sie NTFS-Berechtigungen über SMB für Verzeichnisse und Dateien.
4. Stellen Sie eine Azure-Dateifreigabe von einem domänengebundenen virtuellen Computer bereit.

Das folgende Diagramm zeigt den vollständigen Workflow zur Aktivierung der Azure AD DS-Authentifizierung über SMB für Azure Files.

![Diagramm mit Azure AD über SMB für Azure Files-Workflow](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Aktivieren der Azure AD DS-Authentifizierung für Ihr Konto

Zur Aktivierung der Azure AD DS-Authentifizierung über SMB für Azure Files können Sie mit dem Azure-Portal, Azure PowerShell oder der Azure-Befehlszeilenschnittstelle eine Eigenschaft für Speicherkonten festlegen. Wenn Sie diese Eigenschaft festlegen, wird für das Speicherkonto bei der zugehörigen Bereitstellung von Azure AD DS implizit ein Domänenbeitritt durchgeführt. Die Azure AD DS-Authentifizierung über SMB ist dann für alle neuen und vorhandenen Dateifreigaben im Speicherkonto aktiviert.

Beachten Sie, dass Sie die Azure AD DS-Authentifizierung über SMB erst dann aktivieren können, wenn Sie Azure AD DS erfolgreich in Ihrem Azure AD-Mandanten bereitgestellt haben. Weitere Informationen finden Sie unter [Voraussetzungen](#prerequisites).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um die Azure AD DS-Authentifizierung über SMB über das [Azure-Portal](https://portal.azure.com) zu aktivieren:

1. Navigieren Sie im Azure-Portal zu Ihrem vorhandenen Speicherkonto, oder [erstellen Sie ein Speicherkonto](../common/storage-account-create.md).
1. Wählen Sie im Abschnitt **Einstellungen** die Option **Konfiguration**.
1. Schalten Sie unter **Identitätsbasierter Zugriff auf Dateifreigaben** die Option **Azure Active Directory Domain Service (AAD DS)** auf **Aktiviert** um.
1. Wählen Sie **Speichern** aus.

In der folgenden Abbildung ist dargestellt, wie Sie die Azure AD DS-Authentifizierung über SMB für Ihr Speicherkonto aktivieren.

![Aktivieren der Azure AD DS-Authentifizierung über SMB im Azure-Portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um die Azure AD DS-Authentifizierung über SMB mit Azure PowerShell zu aktivieren, installieren Sie das neueste Az-Modul (2.4 oder höher) oder das Az.Storage-Modul (1.5 oder höher). Weitere Informationen zum Installieren von PowerShell finden Sie unter [Installieren von Azure PowerShell unter Windows mit PowerShellGet](/powershell/azure/install-Az-ps).

Rufen Sie zum Erstellen eines neuen Speicherkontos [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0) auf, und legen Sie dann den Parameter **EnableAzureActiveDirectoryDomainServicesForFile** auf **true** fest. Denken Sie im folgenden Beispiel daran, die Platzhalterwerte durch Ihre eigenen Werte zu ersetzen. (Wenn Sie das vorherige Vorschaumodul verwendet haben, lautet der Parameter für die Funktionsaktivierung **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Verwenden Sie den folgenden Befehl, um diese Funktion für vorhandene Speicherkonten zu aktivieren:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Wenn Sie die Azure AD-Authentifizierung über SMB mithilfe der Azure-Befehlszeilenschnittstelle aktivieren möchten, installieren Sie die neueste Version der Befehlszeilenschnittstelle (Version 2.0.70 oder höher). Weitere Informationen zum Installieren der Azure-Befehlszeilenschnittstelle finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Rufen Sie zum Erstellen eines neuen Speicherkontos [az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) auf, und legen Sie die Eigenschaft `--enable-files-aadds` auf **true** fest. Denken Sie im folgenden Beispiel daran, die Platzhalterwerte durch Ihre eigenen Werte zu ersetzen. (Wenn Sie das vorherige Vorschaumodul verwendet haben, lautet der Parameter für die Featureaktivierung **file-aad**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Verwenden Sie den folgenden Befehl, um diese Funktion für vorhandene Speicherkonten zu aktivieren:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```
---

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Sie haben jetzt erfolgreich die Azure AD DS-Authentifizierung über SMB aktiviert und eine benutzerdefinierte Rolle zugewiesen, die den Zugriff auf eine Azure-Dateifreigabe mit einer Azure AD-Identität ermöglicht. Befolgen Sie die Anweisungen in den Abschnitten [Zuweisen von Zugriffsberechtigungen zum Verwenden einer Identität](#assign-access-permissions-to-an-identity) und [Konfigurieren von NTFS-Berechtigungen über SMB](#configure-ntfs-permissions-over-smb), um weiteren Benutzern den Zugriff auf Ihre Dateifreigabe zu gewähren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Files und zur Verwendung von Azure AD über SMB finden Sie in den folgenden Ressourcen:

- [ Übersicht über die Unterstützung der identitätsbasierten Authentifizierung mit Azure Files für SMB-Zugriff](storage-files-active-directory-overview.md)
- [Häufig gestellte Fragen](storage-files-faq.md)
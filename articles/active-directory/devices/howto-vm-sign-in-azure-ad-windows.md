---
title: Anmelden bei einem virtuellen Windows-Computer in Azure mithilfe von Azure Active Directory (Vorschau)
description: Azure AD-Anmeldung bei einem virtuellen Azure-Computer unter Windows
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.custom: references_regions, devx-track-azurecli
ms.collection: M365-identity-device-management
ms.openlocfilehash: 134148fa3ea73212d85393cc433d60f7ddeecd17
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837123"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Anmelden bei einem virtuellen Windows-Computer in Azure mit der Azure Active Directory-Authentifizierung (Vorschau)

Organisationen können nun die Azure Active Directory-Authentifizierung (Azure AD) für ihre virtuellen Azure-Computer verwenden, auf denen **Windows Server 2019 Datacenter Edition** oder **Windows 10 1809** und höher ausgeführt wird. Durch Verwendung von Azure AD für die Authentifizierung bei virtuellen Computern haben Sie die Möglichkeit, Richtlinien zentral zu steuern und zu erzwingen. Mit Tools wie der rollenbasierten Zugriffssteuerung (RBAC) in Azure und dem bedingten Azure AD-Zugriff können Sie steuern, wer auf eine VM zugreifen kann. In diesem Artikel wird beschrieben, wie Sie einen virtuellen Windows Server 2019-Computer zur Verwendung der Azure AD-Authentifizierung erstellen und konfigurieren.

> [!NOTE]
> Die Azure AD-Anmeldung für virtuelle Azure Windows-Computer ist eine öffentliche Previewfunktion für Azure Active Directory. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Die Verwendung der Azure AD-Authentifizierung für die Anmeldung bei virtuellen Windows-Computern in Azure bietet viele Vorteile, z. B.:

- Verwenden der gleichen verbundbasierten oder verwalteten Azure AD-Anmeldeinformationen, die Sie normalerweise verwenden
- Keine Verwaltung von lokalen Administratorkonten mehr erforderlich
- Mit Azure RBAC können Sie den entsprechenden Zugriff auf virtuelle Computer nach Bedarf gewähren und entfernen, wenn er nicht mehr benötigt wird.
- Bevor der Zugriff auf einen virtuellen Computer zugelassen wird, können mit dem bedingten Azure AD-Zugriff zusätzliche Anforderungen erzwungen werden, z. B.: 
   - Multi-Factor Authentication
   - Überprüfung des Anmelderisikos
- Automatisieren und Skalieren der Azure AD-Einbindung von virtuellen Azure Windows-Computern, die Teil Ihrer VDI-Bereitstellungen sind

> [!NOTE]
> Nachdem Sie diese Funktion aktiviert haben, werden Ihre virtuellen Windows-Computer in Azure mit Azure AD verknüpft. Es ist nicht möglich, sie mit einer anderen Domäne wie dem lokalen Active Directory oder Azure AD DS zu verknüpfen. Wenn dies erforderlich ist, müssen Sie die VM von Ihrem Azure AD-Mandanten trennen, indem Sie die Erweiterung deinstallieren.

## <a name="requirements"></a>Requirements (Anforderungen)

### <a name="supported-azure-regions-and-windows-distributions"></a>Unterstützte Azure-Regionen und Windows-Distributionen

Während der Vorschauphase dieser Funktion werden derzeit die folgenden Windows-Distributionen unterstützt:

- Windows Server 2019 Datacenter
- Windows 10 1809 und höher

> [!IMPORTANT]
> Eine Remoteverbindung mit in Azure AD eingebundenen virtuellen Computern ist nur von Windows 10-PCs zulässig, die entweder in Azure AD registriert (ab Windows 10 20H1) oder über Azure AD normal oder hybrid im **selben** Verzeichnis wie der virtuelle Computer eingebunden sind. 

Während der Vorschauphase dieses Features werden derzeit die folgenden Azure-Regionen unterstützt:

- Alle globalen Azure-Regionen

> [!IMPORTANT]
> Zur Verwendung dieser Previewfunktion kann die Bereitstellung nur in einer unterstützten Windows-Distribution und in einer unterstützten Azure-Region erfolgen. Die Funktion wird aktuell in einer Azure Government Cloud oder Sovereign Cloud nicht unterstützt.

### <a name="network-requirements"></a>Netzwerkanforderungen

Zum Aktivieren der Azure AD-Authentifizierung für Ihre virtuellen Windows-Computer in Azure müssen Sie sicherstellen, dass die Netzwerkkonfiguration der virtuellen Computer den ausgehenden Zugriff auf die folgenden Endpunkte über TCP-Port 443 zulässt:

- https:\//enterpriseregistration.windows.net
- https:\//login.microsoftonline.com
- https:\//device.login.microsoftonline.com
- https:\//pas.windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Aktivieren der Azure AD-Anmeldung für einen virtuellen Windows-Computer in Azure

Zur Verwendung der Azure AD-Anmeldung für einen virtuellen Windows-Computer in Azure müssen Sie zunächst die Azure AD-Anmeldeoption für den virtuellen Windows-Computer aktivieren. Anschließend müssen Sie Azure-Rollenzuweisungen für Benutzer konfigurieren, die berechtigt sind, sich bei dem virtuellen Computer anzumelden.
Es gibt mehrere Möglichkeiten, wie Sie die Azure AD-Anmeldung für den virtuellen Windows-Computer aktivieren können:

- Über das Azure-Portal beim Erstellen eines virtuellen Windows-Computers
- Unter Verwendung von Azure Cloud Shell beim Erstellen eines virtuellen Windows-Computers **oder für einen vorhandenen virtuellen Windows-Computer**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Aktivieren der Azure AD-Anmeldung über das Azure-Portal beim Erstellen eines virtuellen Computers

Sie können die Azure AD-Anmeldung für VM-Images unter Windows Server 2019 Datacenter oder Windows 10 1809 und höher aktivieren. 

So erstellen Sie einen virtuellen Windows Server 2019 Datacenter-Computer in Azure mit Azure AD-Anmeldung 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Konto an, das Zugriff zum Erstellen von virtuellen Computern hat, und wählen Sie **+ Ressource erstellen** aus.
1. Geben Sie **Windows Server** auf der Suchleiste „Marketplace durchsuchen“ ein.
   1. Klicken Sie auf **Windows Server**, und wählen Sie in der Dropdownliste „Softwareplan auswählen“ den Eintrag **Windows Server 2019 Datacenter** aus.
   1. Klicken Sie auf **Erstellen**.
1. Ändern Sie auf der Registerkarte „Verwaltung“ im Bereich „Azure Active Directory“ die Option für **Mit AAD-Anmeldeinformationen anmelden (Vorschau)** von „Aus“ in **Ein**.
1. Stellen Sie sicher, dass **Systemseitig zugewiesene verwaltete Identität** im Bereich „Identität“ auf **Ein** festgelegt ist. Dies sollte automatisch erfolgen, nachdem Sie „Mit AAD-Anmeldeinformationen anmelden“ aktiviert haben.
1. Führen Sie die weiteren Schritte zum Erstellen eines virtuellen Computers aus. Während dieser Vorschauphase müssen Sie einen Administratorbenutzernamen und ein Administratorkennwort für den virtuellen Computer erstellen.

![Anmelden mit Azure AD-Anmeldeinformationen zum Erstellen eines virtuellen Computers](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Damit Sie sich bei dem virtuellen Computer mit Ihren Azure AD-Anmeldeinformationen anmelden können, müssen Sie zunächst wie in einem der folgenden Abschnitte beschrieben Rollenzuweisungen für den virtuellen Computer konfigurieren.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Aktivieren der Azure AD-Anmeldung mithilfe von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Allgemeine Tools sind in Cloud Shell vorinstalliert und für die Verwendung mit Ihrem Konto konfiguriert. Wählen Sie einfach die Schaltfläche „Kopieren“ aus, um den Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen. Cloud Shell kann auf mehrere Arten geöffnet werden:

Wählen Sie rechts oben in einem Codeblock die Option „Ausprobieren“ aus.
Öffnen Sie Cloud Shell in Ihrem Browser.
Wählen Sie im Menü rechts oben im [Azure-Portal](https://portal.azure.com) die Schaltfläche „Cloud Shell“ aus.

Wenn Sie die Befehlszeilenschnittstelle (CLI) lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 2.0.31 der Azure-Befehlszeilenschnittstelle ausführen. Führen Sie „az --version“ aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie im Artikel [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

1. Erstellen Sie mit [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe. 
1. Erstellen Sie mit [az vm create](/cli/azure/vm#az-vm-create) einen virtuellen Computer unter Verwendung einer unterstützten Distribution in einer unterstützten Region. 
1. Installieren Sie die VM-Erweiterung für die Azure AD-Anmeldung. 

Im folgenden Beispiel wird der virtuelle Computer „myVM“, auf dem Win2019Datacenter ausgeführt wird, in der Ressourcengruppe „myResourceGroup“ in der Region „southcentralus“ bereitgestellt. In den folgenden Beispielen können Sie den Namen Ihrer Ressourcengruppe und Ihres virtuellen Computers nach Bedarf angeben.

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> Es ist erforderlich, dass Sie die systemseitig zugewiesene verwaltete Identität auf dem virtuellen Computer aktivieren, bevor Sie die VM-Erweiterung für die Azure AD-Anmeldung installieren.

Das Erstellen des virtuellen Computers und der unterstützenden Ressourcen dauert einige Minuten.

Installieren Sie schließlich die VM-Erweiterung für die Azure AD-Anmeldung, um die Azure AD-Anmeldung für den virtuellen Windows-Computer zu aktivieren. VM-Erweiterungen sind kleine Anwendungen, die Konfigurations- und Automatisierungsaufgaben auf virtuellen Azure-Computern nach der Bereitstellung ermöglichen. Verwenden Sie [az vm extension set](/cli/azure/vm/extension#az-vm-extension-set), um die Erweiterung AADLoginForWindows auf dem virtuellen Computer „myVM“ in der Ressourcengruppe „myResourceGroup“ zu installieren:

> [!NOTE]
> Sie können die Erweiterung AADLoginForWindows auf einem vorhandenen virtuellen Computer unter Windows Server 2019 oder Windows 10 1809 und höher installieren, um ihn für die Azure AD-Authentifizierung zu aktivieren. Ein Beispiel für die Azure-Befehlszeilenschnittstelle ist nachstehend dargestellt.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

Nachdem die Erweiterung auf dem virtuellen Computer installiert wurde, wird der Wert `Succeeded` für `provisioningState` angezeigt.

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurieren der Rollenzuweisungen für den virtuellen Computer

Nach dem Erstellen des virtuellen Computers müssen Sie eine Azure-RBAC-Richtlinie konfigurieren, um festzulegen, wer sich bei dem virtuellen Computer anmelden kann. Zur Autorisierung der VM-Anmeldung werden zwei Azure-Rollen verwendet:

- **VM-Administratoranmeldung:** Benutzer, denen diese Rolle zugewiesen ist, können sich mit Administratorberechtigungen bei einem virtuellen Azure-Computer anmelden.
- **VM-Benutzeranmeldung:** Benutzer, denen diese Rolle zugewiesen ist, können sich mit normalen Benutzerberechtigungen bei einem virtuellen Azure-Computer anmelden.

> [!NOTE]
> Damit sich ein Benutzer über RDP bei dem virtuellen Computer anmelden kann, müssen Sie ihm die Rolle „VM-Administratoranmeldung“ oder „VM-Benutzeranmeldung“ zuweisen. Ein Azure-Benutzer, dem eine der Rollen „Besitzer“ oder „Mitwirkender“ für einen virtuellen Computer zugewiesen ist, verfügt nicht automatisch über die Berechtigungen für die Anmeldung bei dem virtuellen Computer über RDP. Dadurch wird eine überprüfte Trennung zwischen den Personen, die virtuelle Computer steuern können, und den Personen, die auf virtuelle Computer zugreifen können, ermöglicht.

Es gibt mehrere Möglichkeiten, wie Sie Rollenzuweisungen für den virtuellen Computer konfigurieren können:

- Verwenden des Azure AD-Portals
- Verwenden von Azure Cloud Shell

### <a name="using-azure-ad-portal-experience"></a>Verwenden des Azure AD-Portals

So konfigurieren Sie Rollenzuweisungen für Azure AD-fähige virtuelle Computer unter Windows Server 2019 Datacenter

1. Navigieren Sie zur Übersichtsseite des jeweiligen virtuellen Computers.
1. Wählen Sie **Zugriffssteuerung (IAM)** in den Menüoptionen aus.
1. Wählen Sie **Hinzufügen** und dann **Rollenzuweisung hinzufügen** aus, um den Bereich „Rollenzuweisung hinzufügen“ zu öffnen.
1. Wählen Sie in der Dropdownliste **Rolle** eine Rolle aus, z. B. **VM-Administratoranmeldung** oder **VM-Benutzeranmeldung**.
1. Wählen Sie im Feld **Auswählen** einen Benutzer, eine Gruppe, einen Dienstprinzipal oder eine verwaltete Identität aus. Wird der Sicherheitsprinzipal in der Liste nicht angezeigt, können Sie im Feld **Auswählen** einen Begriff eingeben, um das Verzeichnis nach Anzeigenamen, E-Mail-Adressen und Objektbezeichner zu durchsuchen.
1. Wählen Sie **Speichern** aus, um die Rolle zuzuweisen.

Nach einigen Augenblicken wird dem Sicherheitsprinzipal die Rolle für den Bereich zugewiesen.

![Zuweisen von Rollen zu Benutzern, die auf den virtuellen Computer zugreifen](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Verwenden von Azure Cloud Shell

Im folgenden Beispiel wird [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) verwendet, um dem aktuellen Azure-Benutzer die Rolle „VM-Administratoranmeldung“ für den virtuellen Computer zuzuweisen. Der Benutzername des aktiven Azure-Kontos wird mit [az account show](/cli/azure/account#az-account-show) abgerufen. Der Bereich wird mit [az vm show](/cli/azure/vm#az-vm-show) auf den in einem vorherigen Schritt erstellten virtuellen Computer festgelegt. Der Bereich kann auch auf Ebene einer Ressourcengruppe oder eines Abonnements zugewiesen werden. Dann gelten normale Azure RBAC-Vererbungsberechtigungen. Weitere Informationen finden Sie unter [Anmelden bei einem virtuellen Linux-Computer in Azure mit der Azure Active Directory-Authentifizierung](../../virtual-machines/linux/login-using-aad.md).

```   AzureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Wenn die AAD-Domäne und die Domäne des Benutzeranmeldenamens nicht übereinstimmen, müssen Sie die Objekt-ID des Benutzerkontos mit `--assignee-object-id` angeben. Die Angabe des Benutzernamens für `--assignee` genügt nicht. Sie können die Objekt-ID für Ihr Benutzerkonto mithilfe der [Azure Active Directory-Benutzerliste (az as user list)](/cli/azure/ad/user#az-ad-user-list) erhalten.

Weitere Informationen zur Verwendung der rollenbasierten Zugriffssteuerung (Azure RBAC) zum Verwalten des Zugriffs auf Ihre Azure-Abonnementressourcen finden Sie in folgenden Artikeln:

- [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure-Befehlszeilenschnittstelle](../../role-based-access-control/role-assignments-cli.md)
- [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md)
- [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe von Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

## <a name="using-conditional-access"></a>Verwenden von bedingtem Zugriff

Sie können Richtlinien für bedingten Zugriff erzwingen, z. B. die mehrstufige Authentifizierung oder Überprüfung des Anmelderisikos für Benutzer, bevor der Zugriff auf virtuelle Windows-Computer in Azure gewährt wird, für die die Azure AD-Anmeldung aktiviert ist. Zum Anwenden einer Richtlinie für bedingten Zugriff müssen Sie die App Azure Windows VM Sign-In über die Zuweisungsoption für Cloud-Apps oder Aktionen auswählen und dann „Anmelderisiko“ als Bedingung und/oder „Mehrstufige Authentifizierung erforderlich“ als Zugriffssteuerung verwenden. 

> [!NOTE]
> Wenn Sie „Mehrstufige Authentifizierung erforderlich“ als Zugriffssteuerung für das Anfordern des Zugriffs auf die App Azure Windows VM Sign-In verwenden, müssen Sie den Anspruch der mehrstufigen Authentifizierung als Teil des Clients angeben, der die RDP-Sitzung für den virtuellen Windows-Zielcomputer in Azure initiiert. Dies kann auf einem Windows 10-Client nur durch Verwendung der Windows Hello for Business-PIN oder der biometrischen Authentifizierung mit dem RDP-Client erreicht werden. Die Unterstützung für die biometrische Authentifizierung wurde dem RDP-Client in Windows 10 Version 1809 hinzugefügt. Remotedesktop unter Verwendung der Windows Hello for Business-Authentifizierung ist nur für Bereitstellungen verfügbar, die das Modell der Zertifikatvertrauensstellung verwenden und derzeit nicht für das Modell der schlüsselbasierten Vertrauensstellung verfügbar sind.

> [!WARNING]
> Eine aktivierte/erzwungene Authentifizierung über Azure AD Multi-Factor Authentication pro Benutzer wird für VM-Anmeldungen nicht unterstützt.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Anmelden bei einem virtuellen Windows-Computer mithilfe von Azure AD-Anmeldeinformationen

> [!IMPORTANT]
> Eine Remoteverbindung mit in Azure AD eingebundenen VMs ist nur auf Windows 10-PCs zulässig, die entweder über Azure AD (mindestens Build 20H1 erforderlich) registriert oder über Azure AD im **selben** Verzeichnis wie die VM regulär oder hybrid eingebunden sind. Zusätzlich muss dem Benutzer für eine RDP-Verbindung unter Verwendung von Azure AD-Anmeldeinformationen eine der beiden Azure-Rollen „VM-Administratoranmeldung“ oder „VM-Benutzeranmeldung“ zugewiesen sein. Bei Verwendung eines für Azure AD registrierten Windows 10-Computers müssen Sie Anmeldeinformationen im Format Azure AD\UPN eingeben (z. B. AzureAD\john@contoso.com). Derzeit kann Azure Bastion nicht für die Anmeldung mithilfe der Azure Active Directory-Authentifizierung und der Erweiterung „AADLoginForWindows“ verwendet werden. Nur direktes RDP wird unterstützt.

So melden Sie sich mithilfe von Azure AD bei Ihrer Windows Server 2019-VM an: 

1. Navigieren Sie zur Übersichtsseite des virtuellen Computers, der für die Azure AD-Anmeldung aktiviert wurde.
1. Wählen Sie **Verbinden** aus, um das Blatt „Verbindung mit virtuellem Computer herstellen“ zu öffnen.
1. Wählen Sie **RDP-Datei herunterladen** aus.
1. Wählen Sie **Öffnen** aus, um den Remotedesktopverbindungs-Client zu starten.
1. Wählen Sie **Verbinden** aus, um das Dialogfeld „Windows-Anmeldung“ zu öffnen.
1. Melden Sie sich mit Ihren Azure AD-Anmeldeinformationen an.

Sie sind nun mit den entsprechend zugewiesenen Rollenberechtigungen (z. B. „VM-Benutzer“ oder „VM-Administrator“) bei dem virtuellen Azure Windows Server 2019-Computer angemeldet. 

> [!NOTE]
> Sie können die RDP-Datei lokal auf dem Computer speichern, um damit zukünftige Remotedesktopverbindungen mit dem virtuellen Computer zu starten, anstatt zur Übersichtsseite des virtuellen Computers im Azure-Portal navigieren und die Option „Verbinden“ verwenden zu müssen.

## <a name="troubleshoot"></a>Problembehandlung

### <a name="troubleshoot-deployment-issues"></a>Problembehandlung bei Bereitstellungsproblemen

Die Erweiterung AADLoginForWindows muss installiert sein, damit der Azure AD-Einbindungsprozess auf dem virtuellen Computer abgeschlossen werden kann. Führen Sie die folgenden Schritte aus, wenn die VM-Erweiterung nicht ordnungsgemäß installiert wird.

1. Stellen Sie über das lokale Administratorkonto eine RDP-Verbindung mit dem virtuellen Computer her, und überprüfen Sie die Datei „CommandExecution.log“ unter  
   
   C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > Wenn die Erweiterung nach dem anfänglichen Fehler neu gestartet wird, wird das Protokoll mit dem Bereitstellungsfehler als „CommandExecution_JJJJMMTTHHMMSSSSS.log“ gespeichert. "
1. Öffnen Sie eine PowerShell-Eingabeaufforderung auf dem virtuellen Computer, und überprüfen Sie, ob diese Abfragen für den auf dem Azure-Host ausgeführten Instance Metadata Service (IMDS)-Endpunkt Folgendes zurückgeben:

   | Auszuführender Befehl | Erwartete Ausgabe |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Richtige Informationen zum virtuellen Azure-Computer |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Gültige dem Azure-Abonnement zugeordnete Mandanten-ID |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Gültiges Zugriffstoken, das von Azure Active Directory für die verwaltete Identität, die dem virtuellen Computer zugewiesen ist, ausgestellt wird |

   > [!NOTE]
   > Das Zugriffstoken kann mithilfe eines Tools wie [http://calebb.net/](http://calebb.net/) decodiert werden. Vergewissern Sie sich, dass die „appid“ im Zugriffstoken mit der verwalteten Identität übereinstimmt, die dem virtuellen Computer zugewiesen ist.

1. Überprüfen Sie mithilfe der Befehlszeile, ob über den virtuellen Computer auf die erforderlichen Endpunkte zugegriffen werden kann:
   
   - curl https:\//login.microsoftonline.com/ -D –
   - curl https:\//login.microsoftonline.com/`<TenantID>`/ -D –

   > [!NOTE]
   > Ersetzen Sie `<TenantID>` durch die ID des Azure AD-Mandanten, die dem Azure-Abonnement zugeordnet ist.

   - curl https:\//enterpriseregistration.windows.net/ -D -
   - curl https:\//device.login.microsoftonline.com/ -D -
   - curl https:\//pas.windows.net/ -D -

1. Der Gerätestatus kann durch Ausführen von `dsregcmd /status` angezeigt werden. Ziel ist, dass der Gerätestatus `AzureAdJoined : YES` angezeigt wird.

   > [!NOTE]
   > Die Azure AD-Einbindungsaktivität wird in der Ereignisanzeige unter dem Protokoll „User Device Registration\Admin“ erfasst.

Wenn beim Ausführen der Erweiterung AADLoginForWindows ein bestimmter Fehlercode ausgegeben wird, können Sie die folgenden Schritte ausführen:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Problem 1: Erweiterung AADLoginForWindows kann mit dem Terminalfehlercode „1007“ und dem Exitcode „-2145648574“ nicht installiert werden

Dieser Exitcode ergibt DSREG_E_MSI_TENANTID_UNAVAILABLE, da die Erweiterung die Informationen des Azure AD-Mandanten nicht abfragen kann.

1. Vergewissern Sie sich, dass der virtuelle Azure-Computer die Mandanten-ID von Instance Metadata Service abrufen kann.

   - Stellen Sie als lokaler Administrator eine RDP-Verbindung mit dem virtuellen Computer her, und überprüfen Sie, ob der Endpunkt eine gültige Mandanten-ID zurückgibt. Führen Sie dazu den folgenden Befehl über eine Befehlszeile mit erhöhten Rechten auf dem virtuellen Computer aus:
      
      - curl -H Metadata:true http://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. Der VM-Administrator versucht, die Erweiterung AADLoginForWindows zu installieren, aber eine systemseitig zugewiesene verwaltete Identität hat den virtuellen Computer zuvor nicht aktiviert. Navigieren Sie zum Blatt „Identität“ des virtuellen Computers. Überprüfen Sie auf der Registerkarte „Vom System zugewiesen“, ob der Status auf „Ein“ festgelegt ist.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Problem 2: Erweiterung AADLoginForWindows kann mit dem Exitcode „-2145648607“ nicht installiert werden

Dieser Exitcode ergibt DSREG_AUTOJOIN_DISC_FAILED, da die Erweiterung den Endpunkt `https://enterpriseregistration.windows.net` nicht erreichen kann.

1. Überprüfen Sie über die Befehlszeile, ob über den virtuellen Computer auf die erforderlichen Endpunkte zugegriffen werden kann:

   - curl https:\//login.microsoftonline.com/ -D –
   - curl https:\//login.microsoftonline.com/`<TenantID>`/ -D –
   
   > [!NOTE]
   > Ersetzen Sie `<TenantID>` durch die ID des Azure AD-Mandanten, die dem Azure-Abonnement zugeordnet ist. Wenn Sie die Mandanten-ID suchen möchten, können Sie auf den Namen Ihres Kontos zeigen, um die Verzeichnis-ID oder Mandanten-ID abzurufen, oder im Azure-Portal die Optionen „Azure Active Directory“ > „Eigenschaften“ > „Verzeichnis-ID“ auswählen.

   - curl https:\//enterpriseregistration.windows.net/ -D -
   - curl https:\//device.login.microsoftonline.com/ -D -
   - curl https:\//pas.windows.net/ -D -

1. Wenn bei einem der Befehle der Fehler „Der Hostname `<URL>` konnte nicht aufgelöst werden“ auftritt, führen Sie den folgenden Befehl aus, um den DNS-Server zu ermitteln, der von dem virtuellen Computer verwendet wird.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Ersetzen Sie `<URL>` durch die von den Endpunkten verwendeten vollqualifizierten Domänennamen, z. B. „login.microsoftonline.com“.

1. Überprüfen Sie dann, ob der Befehl durch die Angabe eines öffentlichen DNS-Servers ausgeführt werden kann:

   `nslookup <URL> 208.67.222.222`

1. Ändern Sie gegebenenfalls den DNS-Server, der der Netzwerksicherheitsgruppe, zu der der virtuelle Azure-Computer gehört, zugewiesen ist.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Problem 3: Erweiterung AADLoginForWindows kann mit folgendem Exitcode nicht installiert werden: 51

Der Exitcode „51“ ergibt „Diese Erweiterung wird vom Betriebssystem der VM nicht unterstützt“.

In der öffentlichen Vorschauphase ist die Erweiterung AADLoginForWindows nur für die Installation unter Windows Server 2019 oder Windows 10 (Build 1809 oder höher) vorgesehen. Vergewissern Sie sich, dass die Windows-Version unterstützt wird. Wenn der Windows-Build nicht unterstützt wird, deinstallieren Sie die VM-Erweiterung.

### <a name="troubleshoot-sign-in-issues"></a>Beheben von Problemen bei der Anmeldung

Zu den häufig auftretenden Fehlern beim Herstellen einer RDP-Verbindung mithilfe von Azure AD-Anmeldeinformationen gehören nicht zugewiesene Azure-Rollen, ein nicht autorisierter Client und eine erforderliche 2FA-Anmeldemethode. Im Folgenden finden Sie Informationen zum Beheben dieser Probleme.

Der Gerätestatus und der SSO-Status können durch Ausführen von `dsregcmd /status` angezeigt werden. Ziel ist, dass der Gerätestatus `AzureAdJoined : YES` und für `SSO State` der Wert `AzureAdPrt : YES` angezeigt werden.

Außerdem wird die RDP-Anmeldung über Azure AD-Konten in der Ereignisanzeige in den Protokollen unter „AAD\Operational event“ erfasst.

#### <a name="azure-role-not-assigned"></a>Azure-Rolle nicht zugewiesen

Beim Initiieren einer Remotedesktopverbindung mit dem virtuellen Computer wird die folgende Fehlermeldung angezeigt: 

- Die Konfiguration Ihres Kontos lässt die Verwendung dieses Geräts nicht zu. Wenden Sie sich an den Systemadministrator, um weitere Informationen zu erhalten.

![Die Konfiguration Ihres Kontos lässt die Verwendung dieses Geräts nicht zu.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Überprüfen Sie, ob Sie für den virtuellen Computer [Azure RBAC-Richtlinien konfiguriert](../../virtual-machines/linux/login-using-aad.md) haben, mit denen dem Benutzer die Rolle „VM-Administratoranmeldung“ oder „VM-Benutzeranmeldung“ zugewiesen wird:
 
#### <a name="unauthorized-client"></a>Nicht autorisierter Client

Beim Initiieren einer Remotedesktopverbindung mit dem virtuellen Computer wird die folgende Fehlermeldung angezeigt: 

- Mit den Anmeldeinformationen konnte keine Verbindung hergestellt werden

![Mit den Anmeldeinformationen konnte keine Verbindung hergestellt werden](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Vergewissern Sie sich, dass der Windows 10-PC, den Sie zum Initiieren der Remotedesktopverbindung verwenden, über Azure AD regulär oder hybrid im selben Azure AD-Verzeichnis eingebunden ist, in dem auch der virtuelle Computer eingebunden ist. Weitere Informationen zur Geräteidentität finden Sie im Artikel [Was ist eine Geräteidentität](./overview.md).

> [!NOTE]
> Im Build 20H1 für Windows 10 wurde Unterstützung für einen für Azure AD registrierten Computer hinzugefügt, um RDP-Verbindungen zu Ihrer VM zu initiieren. Bei Verwendung eines für Azure AD registrierten (nicht über Azure AD regulär oder hybrid eingebundenen) Computers als RDP-Client zum Initiieren von Verbindungen zu Ihrer VM müssen Sie Anmeldeinformationen im Format AzureAD\UPn (z. B. AzureAD\john@contoso.com) eingeben.

Vergewissern Sie sich außerdem, dass die Erweiterung AADLoginForWindows nach Abschluss der Azure AD-Einbindung nicht deinstalliert wurde.
 
#### <a name="mfa-sign-in-method-required"></a>MFA-Anmeldemethode erforderlich

Beim Initiieren einer Remotedesktopverbindung mit dem virtuellen Computer wird die folgende Fehlermeldung angezeigt: 

- „The sign-in method you're trying to use isn't allowed.“ (Die Anmeldemethode, die Sie verwenden möchten, ist nicht zulässig.) Try a different sign-in method or contact your system administrator.“ (Die Anmeldemethode, die Sie verwenden möchten, ist nicht zulässig. Verwenden Sie eine andere Anmeldemethode, oder wenden Sie sich an Ihren Systemadministrator.)

![„The sign-in method you're trying to use isn't allowed.“ (Die Anmeldemethode, die Sie verwenden möchten, ist nicht zulässig.)](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Wenn Sie eine Richtlinie für bedingten Zugriff konfiguriert haben, die die mehrstufige Authentifizierung (MFA) erforderlich macht, damit Sie auf die Ressource zugreifen können, müssen Sie sicherstellen, dass die Anmeldung auf dem Windows 10-PC, über den die Remotedesktopverbindung mit dem virtuellen Computer initiiert wird, mithilfe einer starken Authentifizierungsmethode erfolgt, z. B. mit Windows Hello. Wenn Sie keine starke Authentifizierungsmethode für die Remotedesktopverbindung verwenden, wird dieser Fehler angezeigt.

Wenn Sie Windows Hello for Business nicht bereitgestellt haben und dies derzeit keine Option ist, können Sie die MFA-Anforderung ausschließen, indem Sie eine Richtlinie für bedingten Zugriff konfigurieren, die die App Azure Windows VM Sign-In aus der Liste der Cloud-Apps ausschließt, für die die mehrstufige Authentifizierung erforderlich ist. Weitere Informationen zu Windows Hello for Business finden Sie in der [Übersicht zu Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

> [!NOTE]
> Die Authentifizierung über die Windows Hello for Business-PIN mit RDP wird unter Windows 10 in mehreren Versionen unterstützt. Die Unterstützung für die biometrische Authentifizierung mit RDP wurde dagegen in Windows 10 Version 1809 hinzugefügt. Die Verwendung der Windows Hello for Business-Authentifizierung bei der RDP-Verbindung ist nur für Bereitstellungen verfügbar, die das Modell der Zertifikatvertrauensstellung verwenden und derzeit nicht für das Modell der schlüsselbasierten Vertrauensstellung verfügbar sind.
 
## <a name="preview-feedback"></a>Feedback zur Vorschauversion

Geben Sie Feedback zu dieser Previewfunktion, oder melden Sie Probleme bei der Verwendung der Funktion im [Azure AD-Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Active Directory finden Sie unter [Was ist Azure Active Directory?](../fundamentals/active-directory-whatis.md).

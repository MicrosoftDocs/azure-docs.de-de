---
title: Anmelden bei einem virtuellen Linux-Computer mit Azure Active Directory-Anmeldeinformationen | Microsoft-Dokumentation
description: In dieser Anleitung wird beschrieben, wie Sie einen virtuellen Linux-Computer zur Verwendung der Azure Active Directory-Authentifizierung für Benutzeranmeldungen erstellen und konfigurieren.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/17/2018
ms.author: cynthn
ms.openlocfilehash: d1db228f4c73cc00cd32ca6ae5b86056db68f05b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66155955"
---
# <a name="log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Anmelden bei einem virtuellen Linux-Computer in Azure mit der Azure Active Directory-Authentifizierung (Vorschau)

Zur Verbesserung der Sicherheit von virtuellen Linux-Computern in Azure können Sie die Integration in die Azure Active Directory-Authentifizierung (AD) durchführen. Bei Verwendung der Azure AD-Authentifizierung für virtuelle Linux-Computer werden Richtlinien, mit denen der Zugriff auf die virtuellen Computer zugelassen oder verweigert wird, zentral gesteuert und erzwungen. In diesem Artikel wird beschrieben, wie Sie einen virtuellen Linux-Computer zur Verwendung der Azure AD-Authentifizierung erstellen und konfigurieren.

> [!NOTE]
> Dieses Feature befindet sich in der Vorschau, die Verwendung für virtuelle Computer oder Workloads in einer Produktionsumgebung wird daher nicht empfohlen. Verwenden Sie dieses Feature auf einem virtuellen Testcomputer, von dem Sie davon ausgehen, dass er nach dem Testen verworfen wird.

Die Verwendung der Azure AD-Authentifizierung für die Anmeldung bei virtuellen Linux-Computern in Azure bietet viele Vorteile, z.B.:

- **Verbesserte Sicherheit:**
  - Sie können sich mit Ihren Unternehmens-AD-Anmeldeinformationen bei virtuellen Linux-Computern in Azure anmelden. Es ist nicht erforderlich, lokale Administratorkonten zu erstellen und die Gültigkeitsdauer von Anmeldeinformationen zu verwalten.
  - Durch die Verringerung der Abhängigkeit von lokalen Administratorkonten müssen Sie sich nicht damit auseinandersetzen, dass Anmeldeinformationen verloren gehen oder gestohlen werden, dass Benutzer unsichere Anmeldeinformationen konfigurieren usw.
  - Auch die für Ihr Azure AD-Verzeichnis konfigurierten Richtlinien zur Kennwortkomplexität und Kennwortgültigkeitsdauer tragen zur Sicherheit von virtuellen Linux-Computern bei.
  - Zum weiteren Schutz der Anmeldung bei virtuellen Azure-Computern können Sie die mehrstufige Authentifizierung konfigurieren.
  - Die Möglichkeit zur Anmeldung bei virtuellen Linux-Computern mit Azure Active Directory besteht auch für Kunden, die [Verbunddienste](../../active-directory/hybrid/how-to-connect-fed-whatis.md) verwenden.

- **Nahtlose Zusammenarbeit:** Mithilfe der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) können Sie angeben, wer sich bei einem bestimmten virtuellen Computer als normaler Benutzer oder als Benutzer mit Administratorrechten anmelden kann. Wenn Benutzer Ihrem Team beitreten oder es verlassen, können Sie die RBAC-Richtlinie für den virtuellen Computer aktualisieren, um den Zugriff entsprechend zuzuweisen. Diese Vorgehensweise ist wesentlich einfacher, als virtuelle Computer bereinigen zu müssen, um unnötige öffentliche SSH-Schlüssel zu entfernen. Wenn Mitarbeiter Ihre Organisation verlassen und ihre Benutzerkonten in Azure AD deaktiviert oder entfernt werden, haben sie keinen Zugriff mehr auf Ihre Ressourcen.

## <a name="supported-azure-regions-and-linux-distributions"></a>Unterstützte Azure-Regionen und Linux-Distributionen

Während der Vorschauphase dieses Features werden derzeit die folgenden Linux-Distributionen unterstützt:

| Distribution | Version |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Leap 42.3 |
| Red Hat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 und Ubuntu Server 18.04 |


Während der Vorschauphase dieses Features werden derzeit die folgenden Azure-Regionen unterstützt:

- Alle globalen Azure-Regionen

>[!IMPORTANT]
> Zur Verwendung dieses Vorschaufeatures kann die Bereitstellung nur in einer unterstützten Linux-Distribution und in einer unterstützten Azure-Region durchgeführt werden. Das Feature wird in einer Azure Government Cloud oder Sovereign Cloud nicht unterstützt.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial die Azure CLI-Version 2.0.31 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-linux-virtual-machine"></a>Erstellen einer virtuellen Linux-Maschine

Erstellen Sie mit [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe und dann mit [az vm create](/cli/azure/vm#az-vm-create) einen virtuellen Computer mit einer unterstützten Distribution und in einer unterstützten Region. Im folgenden Beispiel wird der virtuelle Computer *myVM*, der *Ubuntu 16.04 LTS* verwendet, in der Ressourcengruppe *myResourceGroup* in der Region *southcentralus*  bereitgestellt. In den folgenden Beispielen können Sie den Namen Ihrer Ressourcengruppe und Ihres virtuellen Computers nach Bedarf angeben.

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Das Erstellen des virtuellen Computers und der unterstützenden Ressourcen dauert einige Minuten.

## <a name="install-the-azure-ad-login-vm-extension"></a>Installieren der VM-Erweiterung für die Azure AD-Anmeldung

Für die Anmeldung bei einem virtuellen Linux-Computer mit Azure Active Directory-Anmeldeinformationen müssen Sie die VM-Erweiterung für die Azure Active Directory-Anmeldung installieren. VM-Erweiterungen sind kleine Anwendungen, die Konfigurations- und Automatisierungsaufgaben auf virtuellen Azure-Computern nach der Bereitstellung ermöglichen. Verwenden Sie [az vm extension set](/cli/azure/vm/extension#az-vm-extension-set), um die Erweiterung *AADLoginForLinux* auf dem virtuellen Computer *myVM* in der Ressourcengruppe *myResourceGroup* zu installieren:

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

Nachdem die Erweiterung auf dem virtuellen Computer installiert wurde, wird für *provisioningState* der Wert *Succeeded* angezeigt.

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurieren der Rollenzuweisungen für den virtuellen Computer

Mit der Azure RBAC-Richtlinie wird festgelegt, wer sich bei dem virtuellen Computer anmelden kann. Zur Autorisierung der Anmeldung bei virtuellen Computern werden zwei RBAC-Rollen verwendet:

- **VM-Administratoranmeldung:** Benutzer, denen diese Rolle zugewiesen ist, können sich mit den Berechtigungen eines Windows-Administrators oder Linux-Root-Benutzers bei einem virtuellen Azure-Computer anmelden.
- **VM-Benutzeranmeldung:** Benutzer, denen diese Rolle zugewiesen ist, können sich mit normalen Benutzerberechtigungen bei einem virtuellen Azure-Computer anmelden.

> [!NOTE]
> Damit sich ein Benutzer bei dem virtuellen Computer über SSH anmelden kann, müssen Sie ihm einer der Rollen *VM-Administratoranmeldung* oder *VM-Benutzeranmeldung* zuweisen. Ein Azure-Benutzer, dem eine der Rollen *Besitzer* oder *Mitwirkender* für einen virtuellen Computer zugewiesen ist, verfügt nicht automatisch über die Berechtigungen für die Anmeldung bei dem virtuellen Computer über SSH.

Im folgenden Beispiel wird [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) verwendet, um dem aktuellen Azure-Benutzer die Rolle *VM-Administratoranmeldung* für den virtuellen Computer zuzuweisen. Der Benutzername des aktiven Azure-Kontos wird mit [az account show](/cli/azure/account#az-account-show) abgerufen, und der *Bereich* wird mit [az vm show](/cli/azure/vm#az-vm-show) auf den in einem vorherigen Schritt erstellten virtuellen Computer festgelegt. Der Bereich kann auch auf Ebene einer Ressourcengruppe oder eines Abonnements zugewiesen werden. Dann gelten normale RBAC-Vererbungsberechtigungen. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung](../../role-based-access-control/overview.md).

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Wenn die AAD-Domäne und die Domäne des Benutzeranmeldenamens nicht übereinstimmen, müssen Sie die Objekt-ID Ihres Benutzerkontos mit *----assignee-object-id* angeben. Die Angabe des Benutzernamens für *--assignee* reicht nicht aus. Sie können die Objekt-ID für Ihr Benutzerkonto mithilfe der [Azure Active Directory-Benutzerliste (az as user list)](/cli/azure/ad/user#az-ad-user-list) erhalten.

Weitere Informationen zur Verwendung der rollenbasierten Zugriffssteuerung zum Verwalten des Zugriffs auf Ihre Azure-Abonnementressourcen finden Sie in den Artikeln zur Verwaltung der rollenbasierten Zugriffssteuerung mit [Azure CLI](../../role-based-access-control/role-assignments-cli.md), über das [Azure-Portal](../../role-based-access-control/role-assignments-portal.md) oder mit [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Sie können Azure AD zudem so konfigurieren, dass für die Anmeldung eines bestimmten Benutzers bei dem virtuellen Linux-Computer eine mehrstufige Authentifizierung erforderlich ist. Weitere Informationen finden Sie unter [Erste Schritte mit Azure Multi-Factor Authentication in der Cloud](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="log-in-to-the-linux-virtual-machine"></a>Anmelden bei dem virtuellen Linux-Computer

Zeigen Sie zunächst die öffentliche IP-Adresse des virtuellen Computers mit dem Befehl [az vm show](/cli/azure/vm#az-vm-show) an:

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Melden Sie sich mit Ihren Azure AD-Anmeldeinformationen bei dem virtuellen Azure Linux-Computer an. Mit dem Parameter `-l` können Sie die Adresse Ihres Azure AD-Kontos angeben. Die Kontoadressen müssen ausschließlich in Kleinbuchstaben eingegeben werden. Geben Sie die öffentliche IP-Adresse Ihrer VM aus dem vorherigen Befehl an:

```azurecli-interactive
ssh -l azureuser@contoso.onmicrosoft.com publicIps
```

Sie werden aufgefordert, sich unter [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) mit einem einmalig zu verwendenden Code bei Azure AD anzumelden. Kopieren Sie wie im folgenden Beispiel gezeigt den einmalig zu verwendenden Code, und fügen Sie ihn auf der Seite für die Geräteanmeldung ein:

```bash
~$ ssh -l azureuser@contoso.onmicrosoft.com 13.65.237.247
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJS3K6X4D to authenticate. Press ENTER when ready.
```

Geben Sie bei der entsprechenden Aufforderung Ihre Azure AD-Anmeldeinformationen auf der Anmeldeseite ein. Nach Ihrer erfolgreichen Authentifizierung wird im Webbrowser die folgende Meldung angezeigt:

    You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.

Schließen Sie das Browserfenster, kehren Sie zur SSH-Eingabeaufforderung zurück, und drücken Sie die **Eingabetaste**. Sie sind nun mit den entsprechend zugewiesenen Rollenberechtigungen (z.B. *VM-Benutzer* oder *VM-Administrator*) bei dem virtuellen Azure Linux-Computer angemeldet. Wenn Ihrem Benutzerkonto die Rolle *VM-Administratoranmeldung* zugewiesen ist, können Sie `sudo` verwenden, um Befehle auszuführen, für die Root-Berechtigungen erforderlich sind.

## <a name="sudo-and-aad-login"></a>Sudo- und AAD-Anmeldung

Wenn Sie sudo das erste Mal ausführen, werden Sie aufgefordert, sich ein zweites Mal zu authentifizieren. Wenn Sie sich zur Ausführung von Sudo nicht erneut authentifizieren möchten, können Sie Ihre sudo-Datei `/etc/sudoers.d/aad_admins` bearbeiten und diese Zeile ersetzen:

```bash
%aad_admins ALL=(ALL) ALL
```
Durch diese Zeile:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Beheben von Problemen bei der Anmeldung

Zu den häufig auftretenden Fehlern beim Herstellen einer SSH-Verbindung mit Azure AD-Anmeldeinformationen gehören nicht zugewiesene RBAC-Rollen und wiederholte Aufforderungen zur Anmeldung. In den folgenden Abschnitten finden Sie Informationen zum Beheben dieser Probleme.

### <a name="access-denied-rbac-role-not-assigned"></a>Zugriff verweigert: RBAC-Rolle nicht zugewiesen

Wenn an der SSH-Eingabeaufforderung die folgende Fehlermeldung angezeigt wird, überprüfen Sie, ob Sie für den virtuellen Computer *RBAC-Richtlinien konfiguriert* haben, mit denen dem Benutzer eine der Rollen *VM-Administratoranmeldung* oder VM-Benutzeranmeldung zugewiesen wird:

```bash
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Wiederholte Aufforderungen zur SSH-Anmeldung

Nachdem Sie die Authentifizierung in einem Webbrowser erfolgreich abgeschlossen haben, werden Sie möglicherweise sofort aufgefordert, sich erneut mit einem neuen Code anzumelden. Dieser Fehler wird normalerweise dadurch verursacht, dass der Anmeldename, den Sie an der SSH-Eingabeaufforderung angegeben haben, nicht mit dem Konto übereinstimmt, mit dem Sie sich bei Azure AD angemeldet haben. So beheben Sie dieses Problem

- Überprüfen Sie, ob der an der SSH-Eingabeaufforderung angegebene Anmeldename richtig ist. Ein Tippfehler im Anmeldenamen kann dazu führen, dass der an der SSH-Eingabeaufforderung angegebene Anmeldename nicht mit dem Konto übereinstimmt, mit dem Sie sich bei Azure AD angemeldet haben. Beispiel: Sie geben *azuresuer\@contoso.onmicrosoft.com* anstelle von *azureuser\@contoso.onmicrosoft.com* ein.
- Wenn Sie über mehrere Benutzerkonten verfügen, achten Sie darauf, dass Sie im Browserfenster kein anderes Benutzerkonto als bei der Anmeldung bei Azure AD angeben.
- Linux ist ein Betriebssystem, bei dem die Groß-/Kleinschreibung berücksichtigt wird. Es besteht also ein Unterschied zwischen „Azureuser@contoso.onmicrosoft.com“ und „azureuser@contoso.onmicrosoft.com“. Dies kann zu einer Nichtübereinstimmung führen. Stellen Sie sicher, dass Sie den Benutzerprinzipalnamen (UPN) an der SSH-Eingabeaufforderung mit der richtigen Groß- und Kleinschreibung angeben.

## <a name="preview-feedback"></a>Feedback zur Vorschauversion

Geben Sie Feedback zu diesem Vorschaufeature oder melden Sie Probleme bei der Verwendung davon im [Azure AD-Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Active Directory finden Sie unter [Was ist Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md).

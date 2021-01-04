---
title: Bereitstellen des Azure AD-Anwendungsproxys für Azure AD Domain Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Remotebenutzern sicheren Zugriff auf interne Anwendungen ermöglichen, indem Sie den Azure Active Directory-Anwendungsproxy in einer über Azure Active Directory Domain Services verwalteten Domäne bereitstellen und konfigurieren.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 8305b2190cf3b157973f5844c3237ffe73adba66
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619915"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-active-directory-domain-services-managed-domain"></a>Bereitstellen des Azure AD-Anwendungsproxys für sicheren Zugriff auf interne Anwendungen in einer verwalteten Azure Active Directory Domain Services-Domäne

Mit Azure AD Domain Services (Azure AD DS) können Sie lokal ausgeführte Legacyanwendungen per Lift & Shift zu Azure migrieren. Mithilfe des Azure Active Directory-Anwendungsproxy können Sie dann Remotebenutzer unterstützen, indem Sie diese internen Anwendungen, die Teil einer über Azure AD DS verwalteten Domäne sind, sicher veröffentlichen, sodass über das Internet darauf zugegriffen werden kann.

Wenn Sie noch nicht mit dem Azure AD-Anwendungsproxy vertraut sind und mehr darüber erfahren möchten, lesen Sie den Artikel [Remotezugriff auf lokale Anwendungen über den Azure Active Directory-Anwendungsproxy](../active-directory/manage-apps/application-proxy.md).

In diesem Artikel wird beschrieben, wie Sie einen Azure AD-Anwendungsproxyconnector erstellen und konfigurieren, um sicheren Zugriff auf Anwendungen in einer verwalteten Domäne zu ermöglichen.

## <a name="before-you-begin"></a>Voraussetzungen

Für diesen Artikel benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der entweder mit einem lokalen Verzeichnis synchronisiert oder ein reines Cloudverzeichnis ist.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
    * Für die Verwendung des Azure AD-Anwendungsproxys ist eine **Azure AD Premium-Lizenz** erforderlich.
* Eine verwaltete Azure Active Directory Domain Services-Domäne, die in Ihrem Azure AD-Mandanten aktiviert und konfiguriert ist.
    * [Erstellen und konfigurieren Sie eine verwaltete Azure Active Directory Domain Services-Domäne][create-azure-ad-ds-instance], sofern erforderlich.

## <a name="create-a-domain-joined-windows-vm"></a>Erstellen einer in die Domäne eingebundenen Windows-VM

Um den Datenverkehr an in Ihrer Umgebung ausgeführte Anwendungen weiterzuleiten, installieren Sie die Connectorkomponente des Azure AD-Anwendungsproxys. Dieser Azure AD-Anwendungsproxyconnector muss auf einem virtuellen Windows Server-Computer (Virtual Machine, VM) installiert werden, der in die verwaltete Domäne eingebunden ist. Bei einigen Anwendungen können Sie mehrere Server bereitstellen, auf denen der Connector installiert ist. Diese Bereitstellungsoption bietet Ihnen eine größere Verfügbarkeit und hilft bei der Verarbeitung größerer Authentifizierungslasten.

Die VM, auf der der Azure AD-Anwendungsproxyconnector ausgeführt wird, muss sich im gleichen virtuellen Netzwerk wie Ihre verwaltete Domäne oder in einem per Peering verbundenen virtuellen Netzwerk befinden. Die VMs, auf denen die von Ihnen über den Anwendungsproxy veröffentlichten Anwendungen dann gehostet werden, müssen ebenfalls im gleichen virtuellen Azure-Netzwerk bereitgestellt sein.

Um eine VM für den Azure AD-Anwendungsproxyconnector zu erstellen, führen Sie die folgenden Schritte aus:

1. [Erstellen Sie eine benutzerdefinierte Organisationseinheit](create-ou.md). Sie können Berechtigungen zum Verwalten dieser benutzerdefinierten Organisationseinheit an Benutzer innerhalb der verwalteten Domäne delegieren. Die VMs für den Azure AD-Anwendungsproxy, auf denen Ihre Anwendungen ausgeführt werden, müssen zur benutzerdefinierten OE gehören, nicht zur Standard-OE *AAD DC Computers*.
1. [Binden Sie die virtuellen Computer][create-join-windows-vm] (den Computer, auf dem der Azure AD-Anwendungsproxyconnector ausgeführt wird, und die Computer, auf denen Ihre Anwendungen ausgeführt werden), in die verwaltete Domäne ein. Erstellen Sie diese Computerkonten in der benutzerdefinierten Organisationseinheit (OE) aus dem vorherigen Schritt.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Herunterladen des Azure AD-Anwendungsproxyconnectors

Führen Sie die folgenden Schritte aus, um den Azure AD-Anwendungsproxyconnector herunterzuladen. Die Setupdatei, die Sie herunterladen, wird im nächsten Abschnitt auf Ihre VM mit dem Anwendungsproxy kopiert.

1. Melden Sie sich mit einem Benutzerkonto beim [Azure-Portal](https://portal.azure.com) an, das über Berechtigungen als *Unternehmensadministrator* in Azure AD verfügt.
1. Suchen Sie im oberen Bereich des Portals nach dem **Azure Active Directory**, wählen Sie es aus, und klicken Sie dann auf **Unternehmensanwendungen**.
1. Wählen Sie im Menü auf der linken Seite **Anwendungsproxy** aus. Um den ersten Connector zu erstellen und den Anwendungsproxy zu aktivieren, wählen Sie den Link zum **Herunterladen eines Connectors** aus.
1. Akzeptieren Sie auf der Downloadseite die Lizenzbedingungen und die Datenschutzerklärung, und klicken Sie dann auf **Bedingungen akzeptieren und herunterladen**.

    ![Herunterladen des Azure AD-Anwendungsproxyconnectors](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Installieren und Registrieren des Azure AD-Anwendungsproxyconnectors

Nachdem Sie über eine VM verfügen, die als Azure AD-Anwendungsproxyconnector verwendet werden kann, kopieren Sie die aus dem Azure-Portal heruntergeladene Setupdatei, und führen Sie sie aus.

1. Kopieren Sie die Setupdatei für den Azure AD-Anwendungsproxyconnector auf Ihre VM.
1. Führen Sie die Setupdatei – z. B. *AADApplicationProxyConnectorInstaller.exe* – aus. Akzeptieren Sie die Softwarelizenzbedingungen.
1. Während der Installation werden Sie aufgefordert, den Connector mit dem Anwendungsproxy in Ihrer Azure AD-Instanz zu registrieren.
   * Geben Sie die Anmeldeinformationen eines globalen Administrators für Ihre Azure AD-Instanz an. Die Anmeldeinformationen für den globalen Azure AD-Administrator können sich von den Azure-Anmeldeinformationen im Portal unterscheiden.

        > [!NOTE]
        > Das zum Registrieren des Connectors verwendete globale Administratorkonto muss demselben Verzeichnis angehören, in dem Sie den Anwendungsproxydienst aktivieren.
        >
        > Wenn die Azure AD-Domäne beispielsweise *contoso.com* lautet, muss sich der globale Administrator als `admin@contoso.com` oder mit einem anderen gültigen Aliasnamen in dieser Domäne anmelden.

   * Falls auf der VM, auf der Sie den Connector installieren, die Option „Verstärkte Sicherheitskonfiguration für Internet Explorer“ aktiviert ist, ist der Registrierungsbildschirm möglicherweise blockiert. Zum Zulassen des Zugriffs befolgen Sie die Anweisungen in der Fehlermeldung, um „Verstärkte Sicherheitskonfiguration für Internet Explorer“ während des Installationsvorgangs zu deaktivieren.
   * Falls bei der Connectorregistrierung ein Fehler auftritt, helfen Ihnen die Informationen unter [Problembehandlung von Anwendungsproxys](../active-directory/manage-apps/application-proxy-troubleshoot.md) weiter.
1. Am Ende des Setupprozesses wird ein Hinweis zu Umgebungen mit einem ausgehenden Proxy angezeigt. Um den Azure AD-Anwendungsproxyconnector so zu konfigurieren, dass er mit dem ausgehenden Proxy funktioniert, führen Sie das bereitgestellte Skript aus, z. B. `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`.
1. Auf der Azure-Portalseite mit Anwendungsproxys wird der neue Connector mit dem Status *Aktiv* aufgeführt, wie im folgenden Beispiel gezeigt:

    ![Der neue Azure AD-Anwendungsproxyconnector mit dem Status „Aktiv“ im Azure-Portal](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Um Hochverfügbarkeit für Anwendungen bereitzustellen, die sich über den Azure AD-Anwendungsproxy authentifizieren, können Sie Connectors auf mehreren VMs installieren. Wiederholen Sie die im vorherigen Abschnitt aufgeführten Schritte, um den Connector auf weiteren Servern zu installieren, die in die verwaltete Domäne eingebunden sind.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>Aktivieren der ressourcenbasierten eingeschränkten Kerberos-Delegierung

Wenn Sie das einmalige Anmelden bei Ihren Anwendungen über die integrierte Windows-Authentifizierung (IWA) verwenden möchten, erteilen Sie den Azure AD-Anwendungsproxyconnectors die Berechtigung, die Identität von Benutzern anzunehmen und in deren Namen Token zu senden und zu empfangen. Zum Erteilen dieser Berechtigungen konfigurieren Sie die eingeschränkte Kerberos-Delegierung (Kerberos Constrained Delegation, KCD) für den Connector so, dass er auf Ressourcen in der verwalteten Domäne zugreifen kann. Da Sie in einer verwalteten Domäne nicht über Domänenadministratorberechtigungen verfügen, kann die herkömmliche KCD auf Kontoebene für eine verwaltete Domäne nicht konfiguriert werden. Verwenden Sie stattdessen eine ressourcenbasierte KCD.

Weitere Informationen finden Sie unter [Konfigurieren der eingeschränkten Kerberos-Delegierung (KCD) in Azure Active Directory Domain Services](deploy-kcd.md).

> [!NOTE]
> Sie müssen bei einem Benutzerkonto angemeldet sein, das Mitglied der Gruppe *Azure AD DC-Administratoren* in Ihrem Azure AD-Mandanten ist, um die folgenden PowerShell-Befehle auszuführen.
>
> Die Computerkonten für Ihre Anwendungsproxyconnector-VM und Anwendungs-VMs müssen sich in einer benutzerdefinierten OE befinden, in der Sie zum Erstellen der ressourcenbasierten KCD berechtigt sind. Im integrierten Container *AAD DC Computers* können Sie keine ressourcenbasierte KCD für ein Computerkonto konfigurieren.

Verwenden Sie das Cmdlet [Get-ADComputer][Get-ADComputer], um die Einstellungen für den Computer abzurufen, auf dem der Azure AD-Anwendungsproxyconnector installiert ist. Melden Sie sich mit einem Benutzerkonto an, das Mitglied der Gruppe *Azure AD DC-Administratoren* ist, und führen Sie auf Ihrem in die Domäne eingebundenen virtuellen Verwaltungscomputer die folgenden Cmdlets aus.

Das folgende Beispiel ruft Informationen über das Computerkonto namens *appproxy.aaddscontoso.com* ab. Geben Sie Ihren eigenen Computernamen für die in den vorherigen Schritten konfigurierte Azure AD-Anwendungsproxy-VM an.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

Verwenden Sie für jeden Anwendungsserver, auf dem die Apps hinter dem Azure AD-Anwendungsproxy ausgeführt werden, das PowerShell-Cmdlet [Set-ADComputer][Set-ADComputer], um die ressourcenbasierte KCD zu konfigurieren. Im folgenden Beispiel werden dem Azure AD-Anwendungsproxyconnector Berechtigungen zur Verwendung des Computers *appserver.aaddscontoso.com* erteilt:

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

Wenn Sie mehrere Azure AD-Anwendungsproxyconnectors bereitstellen, müssen Sie die ressourcenbasierte KCD für jede Connectorinstanz konfigurieren.

## <a name="next-steps"></a>Nächste Schritte

Wenn der Azure AD-Anwendungsproxy in Azure AD DS integriert ist, veröffentlichen Sie die Anwendungen für den Zugriff durch Benutzer. Weitere Informationen finden Sie unter [Veröffentlichen von Anwendungen mit dem Azure AD-Anwendungsproxy](../active-directory/manage-apps/application-proxy-add-on-premises-application.md).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
---
title: Eingeschränkte Kerberos-Delegierung für Azure AD Domain Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die eingeschränkte Kerberos-Delegierung (Kerberos constrained delegation, KCD) in verwalteten Azure Active Directory Domain Services-Domänen aktivieren.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 138b90a33ff1dbc4b014f17fa0098112e1da66e4
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619775"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Konfigurieren der eingeschränkten Kerberos-Delegierung (KCD) in Azure Active Directory Domain Services

Beim Ausführen von Anwendungen müssen diese Anwendungen möglicherweise im Kontext eines anderen Benutzers auf Ressourcen zugreifen. Active Directory Domain Services (AD DS) unterstützt einen Mechanismus namens *Kerberos-Delegierung*, die diesen Anwendungsfall ermöglicht. Die *eingeschränkte* Kerberos-Delegierung (KCD) baut auf diesem Mechanismus auf und definiert bestimmte Ressourcen, auf die im Kontext des Benutzers zugegriffen werden kann.

Verwaltete Azure AD DS-Domänen (Azure Active Directory Domain Services) sind zuverlässiger geschützt als herkömmliche lokale AD DS-Umgebungen. Verwenden Sie daher die sicherere *ressourcenbasierte* KCD.

Dieser Artikel veranschaulicht die Konfiguration der ressourcenbasierten eingeschränkten Kerberos-Delegierung in einer verwalteten Azure AD DS-Domäne.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie folgende Ressourcen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der entweder mit einem lokalen Verzeichnis synchronisiert oder ein reines Cloudverzeichnis ist.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
* Eine verwaltete Azure Active Directory Domain Services-Domäne, die in Ihrem Azure AD-Mandanten aktiviert und konfiguriert ist.
    * [Erstellen und konfigurieren Sie eine verwaltete Azure Active Directory Domain Services-Domäne][create-azure-ad-ds-instance], sofern erforderlich.
* Eine Windows Server-Verwaltungs-VM, die in die verwaltete Azure AD DS-Domäne eingebunden ist.
    * Führen Sie bei Bedarf das Tutorial zum [Erstellen eines virtuellen Windows Server-Computers und Einbinden des virtuellen Computers in eine verwaltete Domäne][create-join-windows-vm] aus, und [installieren Sie dann die AD DS-Verwaltungstools][tutorial-create-management-vm].
* Ein Benutzerkonto, das Mitglied der *Administratorengruppe für Azure AD-Domänencontroller* (AAD-DC-Administratoren) in Ihrem Azure AD-Mandanten ist.

## <a name="kerberos-constrained-delegation-overview"></a>Übersicht über die eingeschränkte Kerberos-Delegierung

Die Kerberos-Delegierung ermöglicht einem Konto, für den Zugriff auf Ressourcen die Identität eines anderen Kontos anzunehmen. Beispielsweise kann eine Webanwendung, die auf eine Back-End-Webkomponente zugreift, beim Herstellen der Back-End-Verbindung die Identität eines anderen Benutzerkontos annehmen. Die Kerberos-Delegierung ist unsicher, da sie die Ressourcen, auf die das Konto für den Identitätswechsel zugreifen kann, nicht einschränkt.

Die *eingeschränkte* Kerberos-Delegierung (Kerberos Constrained Delegation, KCD) schränkt die Dienste oder Ressourcen ein, mit denen ein bestimmter Server oder eine Anwendung bei einem Identitätswechsel eine Verbindung herstellen kann. Bei der herkömmlichen eingeschränkten Kerberos-Delegierung sind zum Konfigurieren eines Domänenkontos für einen Dienst Domänenadministratorrechte erforderlich. Außerdem muss das Konto in einer einzelnen Domäne ausgeführt werden.

Mit der herkömmlichen KCD sind auch einige Probleme verbunden. Bei früheren Betriebssystemversionen beispielsweise hatte der Dienstadministrator keine praktische Möglichkeit herauszufinden, welche Front-End-Dienste an die Ressourcendienste delegierten, die sich in ihrem Besitz befanden. Jeder Front-End-Dienst, der an einen Ressourcendienst delegieren konnte, bot einen potenziellen Angriffspunkt. Wenn ein Server kompromittiert wurde, der einen Front-End-Dienst hostete, für den die Delegierung an Ressourcendienste konfiguriert war, wurden möglicherweise auch die Ressourcendienste kompromittiert.

In einer verwalteten Domäne verfügen Sie nicht über Domänenadministratorberechtigungen. Demzufolge kann die herkömmliche kontobasierte KCD in einer verwalteten Domäne nicht konfiguriert werden. Stattdessen kann eine ressourcenbasierte KCD verwendet werden, die außerdem sicherer ist.

### <a name="resource-based-kcd"></a>Ressourcenbasierte KCD

Seit Windows Server 2012 haben Dienstadministratoren die Möglichkeit, eine eingeschränkte Delegierung für ihren Dienst zu konfigurieren. Dieses Modell wird als ressourcenbasierte KCD bezeichnet. Mit diesem Ansatz kann der Back-End-Dienstadministrator bestimmten Front-End-Diensten die Verwendung von KCD erlauben oder verweigern.

Die ressourcenbasierte KCD wird mithilfe von PowerShell konfiguriert. Sie verwenden das Cmdlet [Set-ADComputer][Set-ADComputer] oder [Set-ADUser][Set-ADUser], je nachdem, ob es sich beim Konto für den Identitätswechsel um ein Computerkonto oder ein Benutzerkonto/Dienstkonto handelt.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>Konfigurieren der ressourcenbasierten KCD für ein Computerkonto

In diesem Szenario gehen wir davon aus, dass Sie über eine Web-App verfügen, die auf einem Computer mit dem Namen *contoso-webapp.aaddscontoso.com* ausgeführt wird.

Die Web-App muss im Kontext von Domänenbenutzern auf eine Web-API zugreifen können, die auf einem Computer mit dem Namen *contoso-api.aaddscontoso.com* ausgeführt wird.

Führen Sie die folgenden Schritte aus, um dieses Szenario zu konfigurieren:

1. [Erstellen Sie eine benutzerdefinierte Organisationseinheit](create-ou.md). Sie können Berechtigungen zum Verwalten dieser benutzerdefinierten Organisationseinheit an Benutzer innerhalb der verwalteten Domäne delegieren.
1. [Binden Sie die virtuellen Computer][create-join-windows-vm] (den Computer, auf dem die Web-App ausgeführt wird, und den Computer, auf dem die Web-API ausgeführt wird) in die verwaltete Domäne ein. Erstellen Sie diese Computerkonten in der benutzerdefinierten Organisationseinheit (OE) aus dem vorherigen Schritt.

    > [!NOTE]
    > Die Computerkonten für die Web-App und die Web-API müssen sich in einer benutzerdefinierten OE befinden, in der Sie zum Erstellen der ressourcenbasierten KCD berechtigt sind. Im integrierten Container *AAD DC Computers* können Sie keine ressourcenbasierte KCD für ein Computerkonto konfigurieren.

1. Konfigurieren Sie abschließend die ressourcenbasierte KCD mit dem PowerShell-Cmdlet [Set-ADComputer][Set-ADComputer].

    Melden Sie sich mit einem Benutzerkonto an, das Mitglied der Gruppe *Azure AD DC-Administratoren* ist, und führen Sie auf Ihrem in die Domäne eingebundenen virtuellen Verwaltungscomputer die folgenden Cmdlets aus. Geben Sie Ihre eigenen Computernamen nach Bedarf an:
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>Konfigurieren der ressourcenbasierten KCD für ein Benutzerkonto

In diesem Szenario gehen wir davon aus, dass Sie über eine Web-App verfügen, die als Dienstkonto namens *appsvc* ausgeführt wird. Die Web-App muss im Kontext von Domänenbenutzern auf eine Web-API zugreifen können, die als Dienstkonto namens *backendsvc* ausgeführt wird. Führen Sie die folgenden Schritte aus, um dieses Szenario zu konfigurieren:

1. [Erstellen Sie eine benutzerdefinierte Organisationseinheit](create-ou.md). Sie können Berechtigungen zum Verwalten dieser benutzerdefinierten Organisationseinheit an Benutzer innerhalb der verwalteten Domäne delegieren.
1. [Binden Sie die virtuellen Computer][create-join-windows-vm], auf denen die Back-End-Web-API/-Ressource ausgeführt werden, in die verwaltete Domäne ein. Erstellen Sie das zugehörige Computerkonto innerhalb der benutzerdefinierten OE.
1. Erstellen Sie das Dienstkonto (z. B. *appsvc*) zum Ausführen der Web-App in der benutzerdefinierten Organisationseinheit.

    > [!NOTE]
    > Zur Erinnerung: Sowohl das Computerkonto für den virtuellen Web-API-Computer als auch das Dienstkonto für die Web-App müssen sich in einer benutzerdefinierten OE befinden, in der Sie zum Erstellen der ressourcenbasierten KCD berechtigt sind. Im integrierten Container *AAD DC Computers* bzw. *AAD DC Users* können Sie keine ressourcenbasierte KCD für Konten konfigurieren. Das bedeutet auch, dass Sie keine von Azure AD synchronisierten Benutzerkonten zum Einrichten einer ressourcenbasierten KCD verwenden können. Sie müssen speziell in Azure AD DS erstellte Dienstkonten verwenden.

1. Konfigurieren Sie abschließend die ressourcenbasierte KCD mit dem PowerShell-Cmdlet [Set-ADUser][Set-ADUser].

    Melden Sie sich mit einem Benutzerkonto an, das Mitglied der Gruppe *Azure AD DC-Administratoren* ist, und führen Sie auf Ihrem in die Domäne eingebundenen virtuellen Verwaltungscomputer die folgenden Cmdlets aus. Geben Sie Ihre eigenen Computernamen nach Bedarf an:

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Funktionsweise der Delegierung in Active Directory Domain Services finden Sie unter [Übersicht über die eingeschränkte Kerberos-Delegierung][kcd-technet].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)
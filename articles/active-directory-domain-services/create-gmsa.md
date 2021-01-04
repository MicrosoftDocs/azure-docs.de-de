---
title: Gruppenverwaltete Dienstkonten für Azure AD Domain Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein gruppenverwaltetes Dienstkonto (Group Managed Service Account, gMSA) für verwaltete Azure Active Directory Domain Services-Domänen erstellen.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: f975d3e0e605b7c24b9fd31dc8fc78f0f37bb6b9
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619983"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-active-directory-domain-services"></a>Erstellen eines gruppenverwalteten Dienstkontos (gMSA) Azure Active Directory Domain Services

Anwendungen und Dienste benötigen häufig eine Identität, um sich bei anderen Ressourcen zu authentifizieren. Beispielsweise muss sich ein Webdienst unter Umständen bei einem Datenbankdienst authentifizieren. Wenn eine Anwendung oder ein Dienst über mehrere Instanzen verfügt (z.B. eine Webserverfarm), wird das manuelle Erstellen und Konfigurieren der Identitäten für diese Ressourcen zeitaufwändig.

Stattdessen können Sie in der verwalteten Azure AD DS-Domäne (Azure Active Directory Domain Services) ein gruppenverwaltetes Dienstkonto (gMSA) erstellen. Das Windows-Betriebssystem verwaltet die Anmeldeinformationen für ein gMSA automatisch, was die Verwaltung großer Ressourcengruppen vereinfacht.

In diesem Artikel wird gezeigt, wie Sie in einer verwalteten Domäne mithilfe von Azure PowerShell ein gruppenverwaltetes Dienstkonto (gMSA) erstellen.

## <a name="before-you-begin"></a>Voraussetzungen

Für diesen Artikel benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der entweder mit einem lokalen Verzeichnis synchronisiert oder ein reines Cloudverzeichnis ist.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
* Eine verwaltete Azure Active Directory Domain Services-Domäne, die in Ihrem Azure AD-Mandanten aktiviert und konfiguriert ist.
    * Führen Sie bei Bedarf das Tutorial zum [Erstellen und Konfigurieren einer verwalteten Azure Active Directory Domain Services-Domäne][create-azure-ad-ds-instance] aus.
* Eine Windows Server-Verwaltungs-VM, die in die verwaltete Azure AD DS-Domäne eingebunden ist.
    * Führen Sie bei Bedarf das Tutorial zum [Erstellen eines virtuellen Verwaltungscomputers][tutorial-create-management-vm] aus.

## <a name="managed-service-accounts-overview"></a>Übersicht über gruppenverwaltete Dienstkonten

Ein eigenständiges verwaltetes Dienstkonto (standalone-Managed Service Account, sMSA) ist ein Domänenkonto mit automatisch verwaltetem Kennwort. Dieser Ansatz vereinfacht die Verwaltung des Dienstprinzipalnamens und ermöglicht die Delegierung der Verwaltung an andere Administratoren. Sie müssen die Anmeldeinformationen für das Konto nicht manuell erstellen und ändern.

Ein gruppenverwaltetes Dienstkonto (gMSA) bietet die gleiche Vereinfachung der Verwaltung, jedoch für mehrere Server in der Domäne. Mit einem gMSA können alle Instanzen eines in einer Serverfarm gehosteten Diensts den gleichen Dienstprinzipal verwenden, damit Protokolle für die gegenseitige Authentifizierung funktionieren. Wird ein gMSA als Dienstprinzipal verwendet, wird das Kontokennwort nicht vom Administrator, sondern wieder vom Windows-Betriebssystem verwaltet.

Weitere Informationen finden Sie unter [Übersicht über gruppenverwaltete Dienstkonten (gMSA)][gmsa-overview].

## <a name="using-service-accounts-in-azure-ad-ds"></a>Verwenden von Dienstkonten in Azure AD DS

Da verwaltete Azure Domänen von Microsoft gesperrt und verwaltet werden, sind bei der Verwendung von Dienstkonten einige Überlegungen erforderlich:

* Erstellen Sie Dienstkonten in benutzerdefinierten Organisationseinheiten (OEs) in der verwalteten Domäne.
    * In den integrierten Organisationseinheiten *AADDC Users* und *AADDC Computers* können Sie keine Dienstkonten erstellen.
    * [Erstellen Sie stattdessen eine benutzerdefinierte Organisationseinheit][create-custom-ou] in der verwalteten Domäne, und erstellen Sie anschließend die Dienstkonten in dieser benutzerdefinierten Organisationseinheit.
* Der Stammschlüssel der Schlüsselverteilungsdienste (Key Distribution Services, KDS) wird vorab erstellt.
    * Der KDS-Stammschlüssel wird zum Generieren und Abrufen von Kennwörtern für gMSAs verwendet. In Azure AD DS wird der KDS-Stammschlüssel für Sie erstellt.
    * Sie haben keine Berechtigungen zum Erstellen eines anderen KDS-Stammschlüssels und können auch den Standardschlüssel nicht anzeigen.

## <a name="create-a-gmsa"></a>Erstellen eines gruppenverwalteten Dienstkontos (gMSA)

Erstellen Sie zunächst mithilfe des Cmdlets [New-ADOrganizationalUnit][New-AdOrganizationalUnit] eine benutzerdefinierte Organisationseinheit. Weitere Informationen zum Erstellen und Verwalten von benutzerdefinierten Organisationseinheiten finden Sie unter [Benutzerdefinierte Organisationseinheiten in Azure AD DS][create-custom-ou].

> [!TIP]
> Führen Sie diese Schritte zum Erstellen eines gMSA [auf Ihrer Verwaltungs-VM][tutorial-create-management-vm] aus. Diese Verwaltungs-VM muss bereits über die erforderlichen AD PowerShell-Cmdlets und eine Verbindung mit der verwalteten Domäne verfügen.

Im folgenden Beispiel wird eine benutzerdefinierte Organisationseinheit namens *myNewOU* in der verwalteten Domäne namens *aaddscontoso.com* erstellt. Verwenden Sie Ihre eigene Organisationseinheit und den Namen der verwalteten Domäne:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

Erstellen Sie nun mit dem Cmdlet [New-ADServiceAccount][New-ADServiceAccount] ein gMSA. Als Beispiel werden die folgenden Parameter definiert:

* **-Name** = *WebFarmSvc*
* **-Path**: Dieser Parameter gibt die benutzerdefinierte Organisationseinheit für das gMSA an, das im vorherigen Schritt erstellt wurde.
* DNS-Einträge und Dienstprinzipalnamen werden für *WebFarmSvc.aaddscontoso.com* festgelegt.
* Prinzipale in *AADDSCONTOSO-SERVER$* dürfen das Kennwort abrufen und die Identität verwenden.

Geben Sie Ihre eigenen Namen und Domänennamen an.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.aaddscontoso.com `
    -Path "OU=MYNEWOU,DC=aaddscontoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.aaddscontoso.com/aaddscontoso.com, `
        http/WebFarmSvc.aaddscontoso.com/aaddscontoso, `
        http/WebFarmSvc/aaddscontoso.com, `
        http/WebFarmSvc/aaddscontoso `
    -PrincipalsAllowedToRetrieveManagedPassword AADDSCONTOSO-SERVER$
```

Anwendungen und Dienste können jetzt so konfiguriert werden, dass Sie bei Bedarf das gMSA verwenden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu gMSAs finden Sie unter [Erste Schritte mit gruppenverwalteten Dienstkonten][gmsa-start].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts

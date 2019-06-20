---
title: 'Azure Active Directory Domain Services: Erstellen eines gruppenverwalteten Dienstkontos | Microsoft-Dokumentation'
description: Verwalten von durch die Azure Active Directory-Domänendienste verwalteten Domänen
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mstephen
ms.openlocfilehash: 0f4e57359797e9c28ea62397e5c92c5fca05c69e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245357"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>Erstellen eines gruppenverwalteten Dienstkontos (group managed service account, gMSA) in einer verwalteten Azure AD Domain Services-Domäne
In diesem Artikel erfahren Sie, wie Sie verwaltete Dienstkonten in einer verwalteten Azure AD Domain Services-Domäne erstellen.

## <a name="managed-service-accounts"></a>Verwaltete Dienstkonten
Ein eigenständiges verwaltetes Dienstkonto (standalone-Managed Service Account, sMSA) ist ein verwaltetes Domänenkonto mit automatisch verwaltetem Kennwort. Es vereinfacht die SPN-Verwaltung (Service Principal Name, Dienstprinzipalname) und ermöglicht die Delegierung der Verwaltung an andere Administratoren. Diese Art von verwaltetem Dienstkonto (Managed Service Account, MSA) wurde in Windows Server 2008 R2 und Windows 7 eingeführt.

Das gruppenverwaltete Dienstkonto (group-Managed Service Account, gMSA) bietet die gleichen Vorteile für viele Server in der Domäne. Alle Instanzen eines in einer Serverfarm gehosteten Diensts müssen den gleichen Dienstprinzipal verwenden, damit Protokolle für die gegenseitige Authentifizierung funktionieren. Wenn ein gSMA als Dienstprinzipal verwendet wird, wird das Kontokennwort nicht vom Administrator, sondern vom Windows-Betriebssystem verwaltet.

**Weitere Informationen:**
- [Group Managed Service Accounts Overview](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) (Übersicht über gruppenverwaltete Dienstkonten)
- [Getting Started with Group Managed Service Accounts](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts) (Erste Schritte mit gruppenverwalteten Dienstkonten)


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>Verwenden von Dienstkonten in Azure AD Domain Services
Verwaltete Azure AD Domain Services-Domänen sind gesperrt und werden von Microsoft verwaltet. Bei der Verwendung von Dienstkonten mit Azure AD Domain Services sind einige wichtige Punkte zu berücksichtigen.

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>Erstellen von Dienstkonten in benutzerdefinierten Organisationseinheiten (OEs) in der verwalteten Domäne
In den integrierten Organisationseinheiten „AADDC Users“ und „AADDC Computers“ können keine Dienstkonten erstellt werden. [Erstellen Sie eine benutzerdefinierte Organisationseinheit](create-ou.md) in Ihrer verwalteten Domäne, und erstellen Sie anschließend Dienstkonten in dieser benutzerdefinierten Organisationseinheit.

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>Vorab erstellter Stammschlüssel der Schlüsselverteilungsdienste (Key Distribution Services, KDS)
Der Stammschlüssel der Schlüsselverteilungsdienste (Key Distribution Services, KDS) wird vorab für eine verwaltete Azure AD Domain Services-Domäne erstellt. Sie müssen keinen KDS-Stammschlüssel erstellen (und sind auch nicht dazu berechtigt). Darüber hinaus können Sie den KDS-Stammschlüssel für die verwaltete Domäne nicht anzeigen.

## <a name="sample---create-a-gmsa-using-powershell"></a>Beispiel: Erstellen eines gMSA mithilfe von PowerShell
Im folgende Beispiel wird gezeigt, wie Sie mithilfe von PowerShell eine benutzerdefinierte Organisationseinheit erstellen. Diese Organisationseinheit können Sie anschließend mithilfe des Parameters ```-Path``` angeben, um ein gMSA innerhalb dieser Organisationseinheit zu erstellen.

```powershell
# Create a new custom OU on the managed domain
New-ADOrganizationalUnit -Name "MyNewOU" -Path "DC=CONTOSO100,DC=COM"

# Create a service account 'WebFarmSvc' within the custom OU.
New-ADServiceAccount -Name WebFarmSvc  `
-DNSHostName ` WebFarmSvc.contoso100.com  `
-Path "OU=MYNEWOU,DC=CONTOSO100,DC=com"  `
-KerberosEncryptionType AES128, AES256  ` -ManagedPasswordIntervalInDays 30  `
-ServicePrincipalNames http/WebFarmSvc.contoso100.com/contoso100.com, `
http/WebFarmSvc.contoso100.com/contoso100,  `
http/WebFarmSvc/contoso100.com, http/WebFarmSvc/contoso100  `
-PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

**PowerShell-Cmdlet-Dokumentation:**
- [Cmdlet „New-ADOrganizationalUnit“](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit)
- [Cmdlet „New-ADServiceAccount“](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount)


## <a name="next-steps"></a>Nächste Schritte
- [Erstellen einer Organisationseinheit für eine verwaltete Domäne](create-ou.md)
- [Group Managed Service Accounts Overview](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) (Übersicht über gruppenverwaltete Dienstkonten)
- [Getting Started with Group Managed Service Accounts](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts) (Erste Schritte mit gruppenverwalteten Dienstkonten)

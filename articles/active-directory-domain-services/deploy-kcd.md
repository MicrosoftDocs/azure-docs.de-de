---
title: 'Azure Active Directory Domain Services: Aktivieren der eingeschränkten Kerberos-Delegierung | Microsoft-Dokumentation'
description: Aktivieren der eingeschränkten Kerberos-Delegierung in verwalteten Azure Active Directory Domain Services-Domänen
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mstephen
ms.openlocfilehash: 287aea990664cf01caf83e1871acf69e48210226
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245333"
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Konfigurieren der eingeschränkten Kerberos-Delegierung (KCD) in einer verwalteten Domäne
Viele Anwendungen müssen im Kontext des Benutzers auf Ressourcen zugreifen. Active Directory unterstützt einen Mechanismus, die so genannte Kerberos-Delegierung, die diesen Anwendungsfall ermöglicht. Darüber hinaus können Sie die Delegierung einschränken, sodass im Kontext des Benutzers nur auf bestimmte Ressourcen zugegriffen werden kann. In Azure AD Domain Services verwaltete Domänen unterscheiden sich insofern von herkömmlichen Active Directory-Domänen, als dass sie sicherer gesperrt sind.

Dieser Artikel veranschaulicht die Konfiguration der eingeschränkten Kerberos-Delegierung in einer verwalteten Azure AD Domain Services-Domäne.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="kerberos-constrained-delegation-kcd"></a>Eingeschränkte Kerberos-Delegierung (KCD)
Die Kerberos-Delegierung ermöglicht einem Konto den Identitätswechsel zu einem anderen Sicherheitsprinzipal (z.B. einem Benutzer) für den Zugriff auf Ressourcen. Stellen Sie sich eine Webanwendung vor, die im Kontext eines Benutzers auf eine Back-End-Web-API zugreift. In diesem Beispiel nimmt die Webanwendung (die im Kontext eines Dienstkontos oder eines Computerkontos ausgeführt wird) beim Zugriff auf die Ressource (Back-End-Web-API) die Identität des Benutzers an. Die Kerberos-Delegierung ist unsicher, da sie nicht die Ressourcen einschränkt, auf die das Konto mit dem Identitätswechsel im Kontext des Benutzers zugreifen kann.

Die eingeschränkte Kerberos-Delegierung (KCD) schränkt die Dienste/Ressourcen ein, an denen der angegebene Server im Namen eines Benutzers Aktionen durchführen kann. Die herkömmliche KCD erfordert Domänenadministratorrechte zum Konfigurieren eines Domänenkontos für einen Dienst und beschränkt das Konto auf eine einzelne Domäne.

Mit der herkömmlichen KCD sind auch einige Probleme verbunden. Wenn der Domänenadministrator kontobasierte KCD für den Dienst konfiguriert hat, hatte der Dienstadministrator in früheren Betriebssystemversionen keine praktische Möglichkeit herauszufinden, welche Front-End-Dienste an die Ressourcendienste delegierten, die sich in ihrem Besitz befanden. Jeder Front-End-Dienst, der an einen Ressourcendienst delegieren konnte, bot einen potenziellen Angriffspunkt. Wenn ein Server kompromittiert wurde, der einen Front-End-Dienst hostete, und wenn dieser Server für die Delegierung an Ressourcendienste konfiguriert war, wurden möglicherweise auch die Ressourcendienste kompromittiert.

> [!NOTE]
> In einer verwalteten Azure AD Domain Services-Domäne besitzen Sie keine Domänenadministratorberechtigungen. Aus diesem Grund **kann die herkömmliche kontobasierte KCD in einer verwalteten Domäne nicht konfiguriert werden**. Verwenden Sie die ressourcenbasierte KCD entsprechend der Beschreibung in diesem Artikel. Dieser Mechanismus ist zudem sicherer.
>
>

## <a name="resource-based-kcd"></a>Ressourcenbasierte KCD
Seit Windows Server 2012 haben Dienstadministratoren die Möglichkeit, eingeschränkte Delegierung für den Dienst zu konfigurieren. In diesem Modell kann der Back-End-Dienstadministrator bestimmten Front-End-Diensten die Verwendung von KCD erlauben oder verweigern. Dieses Modell wird als **ressourcenbasierte KCD** bezeichnet.

Die ressourcenbasierte KCD wird mithilfe von PowerShell konfiguriert. Sie verwenden die Cmdlets `Set-ADComputer` oder `Set-ADUser` abhängig davon, ob es sich beim Konto für den Identitätswechsel um ein Computerkonto oder ein Benutzerkonto/Dienstkonto handelt.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Konfigurieren der ressourcenbasierten KCD für ein Computerkonto in einer verwalteten Domäne
Angenommen, Sie haben eine Web-App, die auf dem Computer „contoso100-Webapp.contoso100.com“ ausgeführt wird. Sie muss im Kontext von Domänenbenutzern auf die Ressource zugreifen (eine Web-API, die auf „contoso100-api.contoso100.com“ ausgeführt wird). Hier wird gezeigt, wie Sie die ressourcenbasierte KCD für dieses Szenario einrichten würden:

1. [Erstellen Sie eine benutzerdefinierte Organisationseinheit](create-ou.md). Sie können Berechtigungen zum Verwalten dieser benutzerdefinierten Organisationseinheit an Benutzer innerhalb der verwalteten Domäne delegieren.
2. Verknüpfen Sie beide virtuellen Computer (den mit der Web-App und den mit der Web-API) mit der verwalteten Domäne. Erstellen Sie diese Computerkonten innerhalb der benutzerdefinierten OE.
3. Jetzt konfigurieren Sie ressourcenbasierte KCD mit dem folgenden PowerShell-Befehl:

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Die Computerkonten für die Web-App und die Web-API müssen sich in einer benutzerdefinierten OE befinden, in der Sie zum Erstellen ressourcenbasierter KCD berechtigt sind. Im integrierten Container „AAD DC Computers“ können Sie keine ressourcenbasierte KCD für ein Benutzerkonto konfigurieren.
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Konfigurieren der ressourcenbasierten KCD für ein Benutzerkonto in einer verwalteten Domäne
Angenommen, Sie haben eine Web-App, die als Dienstkonto „appsvc“ ausgeführt wird, und diese muss im Kontext von Domänenbenutzern auf die Ressource zugreifen (eine Web-API, die als Dienstkonto „backendsvc“ ausgeführt wird). Hier wird gezeigt, wie Sie die ressourcenbasierte KCD für dieses Szenario einrichten würden.

1. [Erstellen Sie eine benutzerdefinierte Organisationseinheit](create-ou.md). Sie können Berechtigungen zum Verwalten dieser benutzerdefinierten Organisationseinheit an Benutzer innerhalb der verwalteten Domäne delegieren.
2. Verknüpfen Sie den virtuellen Computer, auf dem die Back-End-Web-API/-Ressource ausgeführt wird, mit der verwalteten Domäne. Erstellen Sie das zugehörige Computerkonto innerhalb der benutzerdefinierten OE.
3. Erstellen Sie das Dienstkonto (z.B. „appsvc“) zum Ausführen der Web-App innerhalb der benutzerdefinierten OE.
4. Jetzt konfigurieren Sie ressourcenbasierte KCD mit dem folgenden PowerShell-Befehl:

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Sowohl das Computerkonto für die Back-End-Web-API als auch das Dienstkonto müssen sich in einer benutzerdefinierten OE befinden, in der Sie zum Erstellen ressourcenbasierter KCD berechtigt sind. Im integrierten Container „AAD DC Computers“ oder für Benutzerkonten im integrierten Container „AAD DC Users“ können Sie keine ressourcenbasierte KCD für ein Benutzerkonto konfigurieren. Daher können Sie keine aus Azure AD synchronisierten Benutzerkonten zum Einrichten ressourcenbasierter KCD verwenden.
>

## <a name="related-content"></a>Verwandte Inhalte
* [Azure AD-Domänendienste – Leitfaden zu den ersten Schritten](create-instance.md)
* [Übersicht über die eingeschränkte Kerberos-Delegierung](https://technet.microsoft.com/library/jj553400.aspx)

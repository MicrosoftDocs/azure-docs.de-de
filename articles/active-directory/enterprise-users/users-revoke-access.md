---
title: Widerrufen des Benutzerzugriffs im Notfall in Azure Active Directory | Microsoft-Dokumentation
description: Widerrufen des gesamten Zugriffs für einen Benutzer in Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
author: curtand
ms.author: curtand
manager: daveba
ms.reviewer: krbain
ms.date: 12/02/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 826ca9fc20d8bbcf9a5f90ccc895b9f9867a6be1
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860574"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>Widerrufen des Benutzerzugriffs in Azure Active Directory

Zu den Szenarien, die einen Administrator dazu zwingen könnten, einem Benutzer jeglichen Zugriff zu entziehen, gehören kompromittierte Konten, die Kündigung des Beschäftigungsverhältnisses und andere innerbetriebliche Bedrohungen. Je nach Komplexität der Umgebung können Administratoren mehrere Schritte unternehmen, um sicherzustellen, dass der Zugriff widerrufen wird. In einigen Szenarien kann zwischen der Einleitung des Widerrufs des Zugriffs und dem Zeitpunkt, an dem der Zugriff tatsächlich widerrufen wird, eine gewisse Zeitspanne liegen.

Um die Risiken zu entschärfen, müssen Sie verstehen, wie Token funktionieren. Es gibt viele Arten von Token, die unter eines der in den folgenden Abschnitten erwähnten Muster fallen.

## <a name="access-tokens-and-refresh-tokens"></a>Zugriffs- und Aktualisierungstoken

Zugriffs- und Aktualisierungstoken werden häufig bei umfangreichen Clientanwendungen verwendet und auch in browserbasierten Anwendungen wie Single-Page-Webanwendungen eingesetzt.

- Wenn Benutzer sich bei Azure AD authentifizieren, werden Autorisierungsrichtlinien ausgewertet, um festzustellen, ob dem Benutzer Zugriff auf eine bestimmte Ressource gewährt werden darf.  

- Bei Autorisierung stellt Azure AD ein Zugriffs- und ein Aktualisierungstoken für die Ressource aus.  

- Von Azure AD ausgestellte Zugriffstoken sind standardmäßig 1 Stunde gültig. Wenn das Authentifizierungsprotokoll es zulässt, kann die App den Benutzer automatisch erneut authentifizieren, indem sie das Aktualisierungstoken an Azure AD übergibt, sobald das Zugriffstoken abläuft.

Azure AD wertet dann die Autorisierungsrichtlinien erneut aus. Wenn der Benutzer weiterhin autorisiert ist, stellt Azure AD ein neues Zugriffs- und Aktualisierungstoken aus.

Zugriffstoken können ein Sicherheitsproblem darstellen, wenn der Zugriff innerhalb einer Zeitspanne widerrufen werden muss, die kürzer als die Gültigkeitsdauer des Token ist, die normalerweise etwa eine Stunde beträgt. Aus diesem Grund arbeitet Microsoft daran, eine [kontinuierliche Zugriffsauswertung](../conditional-access/concept-continuous-access-evaluation.md) für Microsoft 365-Anwendungen bereitzustellen, sodass Zugriffstoken nahezu in Echtzeit ungültig gemacht werden können.  

## <a name="session-tokens-cookies"></a>Sitzungstoken (Cookies)

Die meisten browserbasierten Anwendungen verwenden Sitzungstoken anstelle von Zugriffs- und Aktualisierungstoken.  

- Wenn ein Benutzer einen Browser öffnet und sich über Azure AD bei einer Anwendung authentifiziert, empfängt der Benutzer zwei Sitzungstoken: eines von Azure AD und ein zweites von der Anwendung.  

- Sobald eine Anwendung ein eigenes Sitzungstoken ausstellt, wird der Zugriff auf die Anwendung durch die Sitzung der Anwendung geregelt. Zu diesem Zeitpunkt unterliegt der Benutzer nur den Autorisierungsrichtlinien, die der Anwendung bekannt sind.

- Die Autorisierungsrichtlinien von Azure AD werden so oft neu ausgewertet, wie die Anwendung den Benutzer zu Azure AD zurückleitet. Die erneute Auswertung erfolgt in der Regel automatisch, obwohl die Häufigkeit von der Konfiguration der Anwendung abhängt. Es ist möglich, dass die App den Benutzer keinesfalls zu Azure AD zurückleitet, solange das Sitzungstoken gültig ist.

- Damit ein Sitzungstoken widerrufen werden kann, muss die Anwendung den Zugriff auf der Grundlage ihrer eigenen Autorisierungsrichtlinien widerrufen. Azure AD kann ein von einer Anwendung ausgestelltes Sitzungstoken nicht direkt widerrufen.  

## <a name="revoke-access-for-a-user-in-the-hybrid-environment"></a>Widerrufen des Zugriffs eines Benutzers in der Hybridumgebung

Für eine Hybridumgebung mit lokalem Active Directory, das mit Azure Active Directory synchronisiert ist, empfiehlt Microsoft IT-Administratoren, die folgenden Maßnahmen zu ergreifen.  

### <a name="on-premises-active-directory-environment"></a>Lokale Active Directory-Umgebung

Stellen Sie als Active Directory-Administrator eine Verbindung mit Ihrem lokalen Netzwerk her, öffnen Sie PowerShell, und führen Sie die folgenden Aktionen durch:

1. Deaktivieren Sie den Benutzer in Active Directory. Weitere Informationen finden Sie unter [Disable-ADAccount](/powershell/module/addsadministration/disable-adaccount).

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

1. Setzen Sie das Kennwort des Benutzers in Active Directory zweimal zurück. Weitere Informationen finden Sie unter [Set-ADAccountPassword](/powershell/module/addsadministration/set-adaccountpassword).

    > [!NOTE]
    > Der Grund für das zweimalige Ändern des Kennworts eines Benutzers besteht darin, das Pass-the-Hash-Risiko zu verringern, insbesondere wenn es zu Verzögerungen bei der Replikation des lokalen Kennworts kommt. Wenn Sie sicher davon ausgehen können, dass dieses Konto nicht kompromittiert ist, genügt es, das Kennwort nur einmal zurückzusetzen.

    > [!IMPORTANT] 
    > Verwenden Sie die Beispielkennwörter nicht in den folgenden Cmdlets. Stellen Sie sicher, dass Sie die Kennwörter in eine Zufallszeichenfolge ändern.

    ```PowerShell
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd1" -Force)
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd2" -Force)
    ```

### <a name="azure-active-directory-environment"></a>Azure Active Directory-Umgebung

Öffnen Sie als Azure Active Directory-Administrator PowerShell. Führen Sie dann ``Connect-AzureAD`` und die folgenden Aktionen aus:

1. Deaktivieren Sie den Benutzer in Azure AD. Weitere Informationen finden Sie unter [Set-AzureADUser](/powershell/module/azuread/Set-AzureADUser).

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```
1. Widerrufen Sie die Azure AD-Aktualisierungstoken des Benutzers. Weitere Informationen finden Sie unter [Revoke-AzureADUserAllRefreshToken](/powershell/module/azuread/revoke-azureaduserallrefreshtoken).

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

1. Deaktivieren Sie die Geräte des Benutzers. Weitere Informationen finden Sie unter [Get-AzureADUserRegisteredDevice](/powershell/module/azuread/get-azureaduserregistereddevice).

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```

## <a name="optional-steps"></a>Optionale Schritte

- [Zurücksetzen von Unternehmensdaten in von in Intune verwalteten Anwendungen](/mem/intune/apps/apps-selective-wipe).

- [Zurücksetzen unternehmenseigener Geräte durch Zurücksetzen des Geräts auf die Werkseinstellungen](/mem/intune/remote-actions/devices-wipe).

> [!NOTE]
> Daten auf dem Gerät können nach dem Zurücksetzen nicht wiederhergestellt werden.

## <a name="when-access-is-revoked"></a>Wann der Zugriff widerrufen wird

Sobald Administratoren die oben genannten Schritte ausgeführt haben, kann der Benutzer keine neuen Token für Anwendungen erhalten, die an Azure Active Directory gebunden sind. Die Zeit, die zwischen dem Widerruf und dem Entzug des Zugriffs durch den Benutzer vergeht, hängt davon ab, wie die Anwendung Zugriff gewährt:

- Bei **Anwendungen, die Zugriffstoken verwenden**, verliert der Benutzer den Zugriff, wenn das Zugriffstoken abläuft.

- Bei **Anwendungen, in denen Sitzungstoken verwendet werden**, enden die bestehenden Sitzungen, sobald das Token abläuft. Wenn der deaktivierte Zustand des Benutzers mit der Anwendung synchronisiert ist, kann die Anwendung die bestehenden Sitzungen des Benutzers automatisch widerrufen, sofern sie entsprechend konfiguriert ist.  Die Zeitspanne hängt von der Häufigkeit der Synchronisierung zwischen Anwendung und Azure AD ab.

## <a name="next-steps"></a>Nächste Schritte

- [Sichere Zugriffsmethoden für Azure AD-Administratoren](../roles/security-planning.md)
- [Hinzufügen oder Aktualisieren von Benutzerprofilinformationen](../fundamentals/active-directory-users-profile-azure-portal.md)

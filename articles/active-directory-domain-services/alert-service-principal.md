---
title: Problembehandlung von Warnungen für Dienstprinzipale in Azure AD Domain Services | Microsoft-Dokumentation
description: Lernen Sie die Problembehandlung von Warnungen im Zusammenhang mit der Konfiguration von Dienstprinzipalen in Azure Active Directory Domain Services kennen.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 00ab5c85a477c9c4080acf252cbbde9d4ce816a9
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620238"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Bekannte Probleme: Warnungen für Dienstprinzipale in Azure Active Directory Domain Services

[Dienstprinzipale](../active-directory/develop/app-objects-and-service-principals.md) sind Anwendungen, die die Azure-Plattform zum Verwalten, Aktualisieren und Pflegen einer verwalteten Azure AD DS-Domäne (Azure Active Directory Domain Services) verwendet. Wenn ein Dienstprinzipal gelöscht wird, wirkt sich dies auf die Funktionalität in der verwalteten Domäne aus.

Dieser Artikel hilft Ihnen bei der Problembehandlung und dem Auflösen von Warnungen im Zusammenhang mit der Konfiguration des Dienstprinzipals.

## <a name="alert-aadds102-service-principal-not-found"></a>Warnung AADDS102: Dienstprinzipal nicht gefunden

### <a name="alert-message"></a>Warnmeldung

*Ein für den ordnungsgemäßen Betrieb von Azure AD Domain Services erforderlicher Dienstprinzipal wurde aus Ihrem Azure AD-Verzeichnis gelöscht. Diese Konfiguration wirkt sich darauf aus, wie Microsoft Ihre verwaltete Domäne überwachen, verwalten, patchen und synchronisieren kann.*

Wenn ein erforderlicher Dienstprinzipal gelöscht wird, kann die Azure-Plattform keine automatischen Verwaltungsaufgaben mehr ausführen. Die verwaltete Domäne kann möglicherweise Updates nicht ordnungsgemäß anwenden oder Sicherungen durchführen.

### <a name="check-for-missing-service-principals"></a>Überprüfen fehlender Dienstprinzipale

Führen Sie die folgenden Schritte aus, um zu überprüfen, welcher Dienstprinzipal fehlt und neu erstellt werden muss:

1. Wählen Sie im Azure-Portal im linken Navigationsmenü die Option **Azure Active Directory** aus.
1. Wählen Sie **Unternehmensanwendungen**. Wählen Sie im Dropdownmenü **Anwendungstyp** die Option *Alle Anwendungen* und dann **Anwenden** aus.
1. Suchen Sie nach den folgenden Anwendungs-IDs. Wenn keine vorhandene Anwendung gefunden wird, führen Sie die *Lösungsschritte* aus, um den Dienstprinzipal zu erstellen oder den Namespace erneut zu registrieren.

    | Anwendungs-ID | Lösung |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Fehlenden Dienstprinzipal neu erstellen](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Namespace „Microsoft.AAD“ erneut registrieren](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Namespace „Microsoft.AAD“ erneut registrieren](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Namespace „Microsoft.AAD“ erneut registrieren](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Neuerstellen eines fehlenden Dienstprinzipals

Wenn die Anwendungs-ID *2565bd9d-da50-47d4-8b85-4c97f669dc36* in Ihrem Azure AD-Verzeichnis fehlt, verwenden Sie Azure AD PowerShell, um die folgenden Schritte auszuführen. Weitere Informationen finden Sie unter [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. Installieren Sie ggf. das Azure AD PowerShell-Modul, und importieren Sie es wie folgt:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. Erstellen Sie nun mit dem Cmdlet [New-AzureAdServicePrincipal][New-AzureAdServicePrincipal] den Dienstprinzipal neu:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

Die Integrität der verwalteten Domäne wird innerhalb von zwei Stunden automatisch aktualisiert, und die Warnung wird entfernt.

### <a name="re-register-the-microsoft-aad-namespace"></a>Erneutes Registrieren des Namespace „Microsoft.AAD“

Wenn die Anwendungs-ID *443155a6-77f3-45e3-882b-22b3a8d431fb*, *abba844e-bc0e-44b0-947a-dc74e5d09022* oder *d87dcbc6-a371-462e-88e3-28ad15ec4e64* in Ihrem Azure AD-Verzeichnis fehlt, führen Sie die folgenden Schritte aus, um den Ressourcenanbieter *Microsoft.AAD* erneut zu registrieren:

1. Suchen Sie im Azure-Portal nach dem Eintrag **Abonnements**, und wählen Sie ihn aus.
1. Wählen Sie das Abonnement aus, das Ihrer verwalteten Domäne zugeordnet ist.
1. Wählen Sie im linken Navigationsbereich den Eintrag **Ressourcenanbieter** aus.
1. Suchen Sie nach *Microsoft.AAD*, und wählen Sie dann **Erneut registrieren** aus.

Die Integrität der verwalteten Domäne wird innerhalb von zwei Stunden automatisch aktualisiert, und die Warnung wird entfernt.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Warnung AADDS105: Kennwortsynchronisierungsanwendung ist veraltet

### <a name="alert-message"></a>Warnmeldung

*Der Dienstprinzipal mit der Anwendungs-ID „d87dcbc6-a371-462e-88e3-28ad15ec4e64“ wurde gelöscht und dann neu erstellt. Die Wiederherstellung hinterlässt inkonsistente Berechtigungen für Azure AD Domain Services-Ressourcen, die für Ihre verwaltete Domäne benötigt werden. Die Synchronisierung von Kennwörtern in Ihrer verwalteten Domäne kann beeinträchtigt werden.*

Azure AD DS synchronisiert automatisch Benutzerkonten und Anmeldeinformationen von Azure AD. Wenn es bei der für diesen Prozess verwendeten Azure AD-Anwendung ein Problem gibt, tritt bei der Synchronisierung der Anmeldeinformationen zwischen Azure AD DS und Azure AD ein Fehler auf.

### <a name="resolution"></a>Lösung

Zum erneuten Erstellen der Azure AD-Anwendung, die für die Synchronisierung von Anmeldeinformationen verwendet wird, führen Sie mithilfe von Azure AD PowerShell die folgenden Schritte aus. Weitere Informationen finden Sie unter [Installieren von Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. Installieren Sie ggf. das Azure AD PowerShell-Modul, und importieren Sie es wie folgt:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Löschen Sie jetzt die alte Anwendung und das Objekt mit den folgenden PowerShell-Cmdlets:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $spObject
    ```

Nachdem Sie beide Anwendungen gelöscht haben, erstellt die Azure-Plattform diese automatisch neu und versucht, die Kennwortsynchronisierung fortzusetzen. Die Integrität der verwalteten Domäne wird innerhalb von zwei Stunden automatisch aktualisiert, und die Warnung wird entfernt.

## <a name="next-steps"></a>Nächste Schritte

Falls weiterhin Probleme auftreten, [öffnen Sie eine Azure-Supportanfrage][azure-support], um weitere Hilfe bei der Problembehandlung zu erhalten.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal

---
title: 'Azure Active Directory Domain Services: Beheben von Problemen bei der Konfiguration des Dienstprinzipals | Microsoft-Dokumentation'
description: Beheben von Problemen bei der Konfiguration des Dienstprinzipals für Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: ''
editor: ''
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: mstephen
ms.openlocfilehash: a8c21e3b27a4868837d3e381c7c1868c89858bea
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245377"
---
# <a name="troubleshoot-invalid-service-principal-configuration-for-your-managed-domain"></a>Beheben von Problemen bei einer ungültigen Konfiguration des Dienstprinzipals für verwaltete Domänen

Dieser Artikel hilft Ihnen bei der Problembehandlung und Lösung von Konfigurationsfehlern im Zusammenhang mit dem Dienstprinzipal, die zu der folgenden Fehlermeldung führen:

## <a name="alert-aadds102-service-principal-not-found"></a>Warnung AADDS102: Dienstprinzipal nicht gefunden

**Warnmeldung:** *Ein für den ordnungsgemäßen Betrieb von Azure AD Domain Services erforderlicher Dienstprinzipal wurde aus Ihrem Azure AD-Verzeichnis gelöscht. Diese Konfiguration wirkt sich darauf aus, wie Microsoft Ihre verwaltete Domäne überwachen, verwalten, patchen und synchronisieren kann.*

[Dienstprinzipale](../active-directory/develop/app-objects-and-service-principals.md) sind Anwendungen, die Microsoft zum Verwalten und Aktualisieren Ihrer verwalteten Domäne verwendet. Wenn diese gelöscht werden, kann Microsoft Ihre Domäne nicht mehr im Betrieb halten.


## <a name="check-for-missing-service-principals"></a>Überprüfen fehlender Dienstprinzipale
Ermitteln Sie anhand der folgenden Schritte, welche Dienstprinzipale neu erstellt werden müssen:

1. Navigieren Sie im Azure-Portal zur Seite [Unternehmensanwendungen – Alle Anwendungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps).
2. Wählen Sie in der Dropdownliste **Anzeigen** die Option **Alle Anwendungen** aus, und klicken Sie auf **Anwenden**.
3. Nutzen Sie die folgende Tabelle für die Suche der einzelnen Anwendungs-IDs, indem Sie die ID in das Suchfeld einfügen und die EINGABETASTE drücken. Wenn keine Suchergebnisse angezeigt werden, müssen Sie den Dienstprinzipal mithilfe der Schritte in der Spalte „Lösung“ neu erstellen.

| Anwendungs-ID | Lösung |
| :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Erneutes Erstellen eines fehlenden Dienstprinzipals mit PowerShell](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Erneutes Registrieren beim Namespace „Microsoft.AAD“](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Erneutes Registrieren beim Namespace „Microsoft.AAD“](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Erneutes Registrieren beim Namespace „Microsoft.AAD“](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>Erneutes Erstellen eines Dienstprinzipals mit PowerShell
Gehen Sie folgendermaßen vor, wenn ein Dienstprinzipal mit der ID ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` in Ihrem Azure AD-Verzeichnis fehlt.

**Lösung:** Sie benötigen Azure AD PowerShell, um diese Schritte durchzuführen. Weitere Informationen zum Installieren von Azure AD PowerShell finden Sie [in diesem Artikel](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Um dieses Problem zu beheben, geben Sie die folgenden Befehle in einem PowerShell-Fenster ein:
1. Installieren Sie das Azure AD PowerShell-Modul, und importieren Sie es.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Überprüfen Sie, ob der für Azure AD Domain Services erforderliche Dienstprinzipal in Ihrem Verzeichnis fehlt, indem Sie den folgenden PowerShell-Befehl ausführen:

    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```

3. Erstellen Sie den Dienstprinzipal, indem Sie den folgenden PowerShell-Befehl eingeben:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

4. Nachdem Sie den fehlenden Dienstprinzipal erstellt haben, warten Sie zwei Stunden, und überprüfen Sie dann die Integrität Ihrer verwalteten Domäne.


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Erneutes Registrieren beim Namespace „Microsoft.AAD“ über das Azure-Portal
Gehen Sie folgendermaßen vor, wenn ein Dienstprinzipal mit einer der IDs ```443155a6-77f3-45e3-882b-22b3a8d431fb```, ```abba844e-bc0e-44b0-947a-dc74e5d09022``` oder ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` in Ihrem Azure AD-Verzeichnis fehlt.

**Lösung:** Stellen Sie Domain Services anhand der folgenden Schritte in Ihrem Verzeichnis wieder her:

1. Navigieren Sie im Azure-Portal zur Seite [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Wählen Sie das Abonnement in der Tabelle, die Ihrer verwalteten Domäne zugeordnet ist, aus.
3. Wählen Sie links auf der Navigationsleiste **Ressourcenanbieter** aus.
4. Suchen Sie in der Tabelle nach „Microsoft.AAD“, und klicken Sie auf **Erneut registrieren**.
5. Um sicherzustellen, dass die Warnung aufgelöst wurde, zeigen Sie die Integritätsseite für Ihre verwaltete Domäne nach zwei Stunden an.


## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Warnung AADDS105: Kennwortsynchronisierungsanwendung ist veraltet

**Warnmeldung:** Der Dienstprinzipal mit der Anwendungs-ID „d87dcbc6-a371-462e-88e3-28ad15ec4e64“ wurde gelöscht und dann neu erstellt. Die Wiederherstellung hinterlässt inkonsistente Berechtigungen für Azure AD Domain Services-Ressourcen, die für Ihre verwaltete Domäne benötigt werden. Die Synchronisierung von Kennwörtern in Ihrer verwalteten Domäne kann beeinträchtigt werden.


**Lösung:** Sie benötigen Azure AD PowerShell, um diese Schritte durchzuführen. Weitere Informationen zum Installieren von Azure AD PowerShell finden Sie [in diesem Artikel](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Um dieses Problem zu beheben, geben Sie die folgenden Befehle in einem PowerShell-Fenster ein:
1. Installieren Sie das Azure AD PowerShell-Modul, und importieren Sie es.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```
2. Löschen der alten Anwendung und des Objekts mit den folgenden PowerShell-Befehlen

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```
3. Nachdem Sie beide Elemente gelöscht haben, wird ein Lösungsprozess durchgeführt, und die für die Kennwortsynchronisierung erforderlichen Anwendungen werden neu erstellt. Warten Sie zwei Stunden, und überprüfen Sie die Integrität Ihrer Domäne, um sicherzustellen, dass die Gründe für die Warnung beseitigt wurden.


## <a name="contact-us"></a>So erreichen Sie uns
Wenden Sie sich an das Produktteam der Azure Active Directory Domain Services, um [Feedback zu geben oder Support zu erhalten](contact-us.md).

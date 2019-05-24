---
title: Reaktivieren deaktivierter Azure Access Control Service (ACS)-Namespaces
description: Erfahren Sie, wie Sie Ihre Azure Access Control Service (ACS)-Namespaces finden und aktivieren können, und fordern Sie eine Erweiterung an, um die Aktivierung bis zum 4. Februar 2019 beizubehalten.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fe1517cce8a1f9b1024085340ffb927b282f0fb
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540279"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Gewusst wie: Reaktivieren deaktivierter Access Control Service-Namespaces

Im November 2017 haben wir bekanntgegeben, dass Microsoft Azure Access Control Service (ACS), ein Dienst von Azure Active Directory (Azure AD), wird am 7. November 2018 eingestellt wird.

Wir haben 12 Monate, 9 Monate, 6 Monate, 3 Monate, 1 Monat, 2 Wochen, 1 Woche und 1 Tag vor der Einstellung am 7. November 2018 E-Mails bezüglich der Einstellung von ACS an die Verwaltungs-E-Mail der ACS-Abonnements gesendet.

Am 3. Oktober 2018 gaben wir (per E-Mail und [einem Blogbeitrag](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) ein Verlängerungsangebot für Kunden bekannt, die ihre Migration nicht vor dem 7. November 2018 abschließen können. Die Ankündigung enthielt auch Anweisungen zum Anfordern der Verlängerung.

## <a name="why-your-namespace-is-disabled"></a>Darum wird Ihr Namespace deaktiviert

Wenn Sie sich nicht für die Verlängerung entschieden haben, werden wir ab dem 7. November 2018 ACS-Namespaces deaktivieren. Sie müssen die Verlängerung bis zum 4. Februar 2019 angefordert haben. Andernfalls können Sie die Namespaces nicht mithilfe von PowerShell aktivieren.

> [!NOTE]
> Sie müssen ein Dienstadministrator oder Co-Admin des Abonnements sein, um die PowerShell-Befehle auszuführen und eine Verlängerung anzufordern.

## <a name="find-and-enable-your-acs-namespaces"></a>Suchen und Aktivieren Ihrer ACS-Namespaces

Sie können ACS PowerShell verwenden, um alle Ihre ACS-Namespaces aufzulisten und deaktivierte zu reaktivieren.

1. Herunterladen und Installieren von ACS PowerShell:
    1. Wechseln Sie zum PowerShell-Katalog, und laden Sie [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2) herunter.
    1. Installieren des Moduls:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Rufen Sie eine Liste mit den möglichen Befehlen ab:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Führen Sie Folgendes aus, um Hilfe zu einem bestimmten Befehl zu erhalten:

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        Hierbei steht `[Command-Name]` für den Namen des ACS-Befehls.
1. Stellen Sie eine Verbindung mit ACS her, indem Sie das Cmdlet **Connect-AcsAccount** verwenden. 

    Möglicherweise müssen Sie Ihre Ausführungsrichtlinie ändern, indem Sie **Set-ExecutionPolicy** ausführen, bevor Sie den Befehl ausführen können.
1. Listen Sie Ihre verfügbaren Azure-Abonnements auf, indem Sie das Cmdlet **Get-AcsSubscription** verwenden.
1. Listen Sie Ihre ACS-Namespaces auf, indem Sie das Cmdlet **Get-AcsNamespace** verwenden.
1. Bestätigen Sie, dass die Namespaces deaktiviert sind, indem Sie bestätigen, dass `State` `Disabled` ist.

    [![Bestätigen, dass die Namespaces deaktiviert sind](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Sie können auch `nslookup {your-namespace}.accesscontrol.windows.net` verwenden, um zu überprüfen, ob die Domäne noch aktiv ist.

1. Aktivieren Sie Ihre ACS-Namespaces, indem Sie das Cmdlet **Enable-AcsNamespace** verwenden.

    Nachdem Sie Ihre Namespaces aktiviert haben, können Sie eine Verlängerung beantragen, damit die Namespaces nicht vor dem 4. Februar 2019 wieder deaktiviert werden. Nach diesem Datum tritt bei allen Anforderungen an ACS ein Fehler auf.

## <a name="request-an-extension"></a>Anfordern einer Verlängerung

Wir nehmen ab dem 21. Januar 2019 neue Verlängerungsanforderungen an.

Die Namespaces von Kunden, die eine Verlängerung angefordert haben, werden ab dem 4. Februar 2019 deaktiviert. Sie können Namespaces auch weiterhin mithilfe von PowerShell aktivieren, die Namespaces werden jedoch nach 48 Stunden erneut deaktiviert.

Nach dem 4. März 2019 können die Kunden keine Namespaces mehr mithilfe von PowerShell aktivieren.

Zusätzliche Verlängerungen werden dann nicht mehr automatisch genehmigt. Wenn Sie zusätzliche Zeit für die Migration benötigen, wenden Sie sich an den [Azure-Support](https://portal.azure.com/#create/Microsoft.Support), und stellen Sie einen detaillierten Migrationszeitplan bereit.

### <a name="to-request-an-extension"></a>So fordern Sie eine Verlängerung an

1. Melden Sie sich beim Azure-Portal an, und erstellen Sie eine [neue Supportanfrage](https://portal.azure.com/#create/Microsoft.Support).
1. Füllen Sie das Formular für die neue Supportanfrage wie im folgenden Beispiel gezeigt aus.

    | Supportanfragefeld | Wert |
    |-----------------------|--------------------|
    | **Problemtyp** | `Technical` |
    | **Abonnement** | Ihr Abonnement |
    | **Service** | `All services` |
    | **Ressource** | `General question/Resource not available` |
    | **Problemtyp** | `ACS to SAS Migration` |
    | **Betreff** | Beschreibung des Problems |

   ![Neue Anfrage an den technischen Support](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

<!--

1. Navigate to your ACS namespace's management portal by going to `https://{your-namespace}.accesscontrol.windows.net`.
1. Select the **Read Terms** button to read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/), which will direct you to a page with the updated Terms of Use.

    [![Select the Read Terms button](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Select **Request Extension** on the banner at the top of the page. The button will only be enabled after you read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/).

    [![Select the Request Extension button](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. After the extension request is registered, the page will refresh with a new banner at the top of the page.

    [![Updated page with refreshed banner](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)
-->

## <a name="help-and-support"></a>Hilfe und Support

- Wenn Sie nach dem Befolgen dieser Anleitung auf Probleme stoßen, wenden Sie sich an den [Azure-Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Wenn Sie Fragen oder Feedback zur Einstellung von ACS haben, kontaktieren Sie uns unter acsfeedback@microsoft.com.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie die Informationen zur ACS-Deaktivierung unter [ Migrieren aus dem Azure Access Control Service](active-directory-acs-migration.md).

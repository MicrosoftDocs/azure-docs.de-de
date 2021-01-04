---
title: Konfigurieren der Art der Endbenutzereinwilligung für Anwendungen in Azure AD
description: Erfahren Sie, wie Sie verwalten können, wie und wann Benutzer Anwendungen zustimmen können, die Zugriff auf die Daten Ihrer Organisation haben.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: c279b7f4ec2d70e7eb6a97abf02cfbf4f2ef87f0
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033611"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Konfigurieren der Art und Weise, wie Endbenutzer Anwendungen zustimmen können

Sie können Ihre Anwendung in Microsoft Identity Platform integrieren, um Benutzern die Anmeldung mit ihrem Geschäfts-, Schul- oder Unikonto und Zugriff auf die Daten Ihrer Organisation zu ermöglichen, um umfassende datengesteuerte Erfahrungen zu bieten.

Bevor eine Anwendung auf die Daten Ihrer Organisation zugreifen kann, muss ein Benutzer der Anwendung eine entsprechende Berechtigungen erteilen. Unterschiedliche Berechtigungen erlauben verschiedene Zugriffsebenen. Standardmäßig sind alle Benutzer berechtigt, in Berechtigungen für Anwendungen einzuwilligen, die keine Administratoreinwilligung erfordern. Standardmäßig kann ein Benutzer beispielsweise einwilligen, dass eine App auf sein Postfach zugreifen kann, er kann aber nicht einwilligen, dass eine App uneingeschränkten Lese- und Schreibzugriff auf alle Dateien in Ihrer Organisation erhält.

Wenn Sie es Benutzern ermöglichen, Apps Zugriff auf Daten zu gewähren, können Benutzer problemlos nützliche Anwendungen abrufen und produktiv arbeiten. In einigen Situationen kann diese Konfiguration jedoch ein Risiko darstellen, wenn Sie nicht sorgfältig überwacht und gesteuert wird.

> [!IMPORTANT]
> Um das Risiko zu verringern, dass böswillige Anwendungen versuchen, Benutzern den Zugriff auf Daten Ihrer Organisation zu gewähren, empfiehlt es sich, die Einwilligung des Benutzers nur für Anwendungen zuzulassen, die von einem [verifizierten Herausgeber](../develop/publisher-verification-overview.md) veröffentlicht wurden.

## <a name="user-consent-settings"></a>Einstellungen für die Benutzereinwilligung

Richtlinien zur Einwilligung für die App beschreiben Bedingungen, die erfüllt werden müssen, bevor bei einer App eingewilligt werden kann. Diese Richtlinien enthalten möglicherweise Bedingungen für die App, die Zugriff anfordert, sowie die von der App angeforderten Berechtigungen.

Indem Sie auswählen, welche Richtlinien zur Einwilligung für die App für alle Benutzer gelten sollen, können Sie Grenzwerte im Hinblick darauf festlegen, wann Endbenutzer Einwilligung für Apps erteilen dürfen und wann sie zum Anfordern einer Überprüfung und Genehmigung durch den Administrator aufgefordert werden sollen:

* **Benutzereinwilligung deaktivieren**: Benutzer können Anwendungen keine Berechtigungen erteilen. Benutzer können sich weiterhin bei Apps anmelden, in die sie zuvor einwilligt haben oder in die Administratoren in Ihrem Namen einwilligt haben. Sie sind jedoch nicht berechtigt, in neue Berechtigungen oder neue Apps eigenständig einzuwilligen. Nur Benutzer, denen eine Verzeichnisrolle mit der Berechtigung zum Erteilen von Einwilligung erteilt wurde, können in neue Apps einwilligen.

* **Benutzer können in Apps von verifizierten Herausgebern oder Ihrer Organisation einwilligen, aber nur für Berechtigungen, die Sie auswählen** – Alle Benutzer können nur in Apps einwilligen, die von einem [verifizierten Herausgeber](../develop/publisher-verification-overview.md) veröffentlicht wurden, und in Apps, die in Ihrem Mandanten registriert sind. Benutzer können nur in die Berechtigungen einwilligen, die Sie als „geringe Auswirkung“ klassifiziert haben. Sie müssen [Berechtigungen klassifizieren](configure-permission-classifications.md), um auszuwählen, in welche Berechtigungen Benutzer einwilligen dürfen.

* **Benutzer können in alle Apps einwilligen** – Diese Option ermöglicht es allen Benutzern, für beliebige Anwendungen in jede beliebige Berechtigung einzuwilligen, die keine Administratoreinwilligung erfordert.

* **Benutzerdefinierte Richtlinie zur Einwilligung für die App**: Bei sogar mehr Optionen für die Bedingungen zur Steuerung der Benutzereinwilligung können Sie die [benutzerdefinierte Richtlinie zur Einwilligung für die App erstellen](manage-app-consent-policies.md#create-a-custom-app-consent-policy) und für Benutzereinwilligung konfigurieren.

# <a name="portal"></a>[Portal](#tab/azure-portal)

So konfigurieren Sie die Einstellungen für die Benutzereinwilligung über das Azure-Portal:

1. Melden Sie sich als [globaler Administrator](../roles/permissions-reference.md#global-administrator--company-administrator) beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie **Azure Active Directory** > **Unternehmensanwendungen** > **Einwilligung und Berechtigungen** > **Einstellungen für Benutzereinwilligung** aus.
1. Wählen Sie unter **Benutzereinwilligung für Anwendungen** aus, welche Einwilligungseinstellung für alle Benutzer konfiguriert werden soll.
1. Klicken Sie auf **Save** (Speichern), um Ihre Einstellungen zu speichern.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Einstellungen für die Benutzereinwilligung":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Sie können mithilfe des neuesten Azure AD PowerShell-Vorschaumoduls, [AzureADPreview](/powershell/azure/active-directory/install-adv2?preserve-view=true&view=azureadps-2.0-preview), auswählen, welche Richtlinie zur Einwilligung für die App die Benutzereinwilligung für Anwendungen regeln soll.

#### <a name="disable-user-consent"></a>Benutzereinwilligung deaktivieren

Legen Sie zum Deaktivieren der Benutzereinwilligung die Einwilligungsrichtlinien zur Steuerung dieser Einwilligung als „leer“ fest:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

#### <a name="allow-user-consent-subject-to-an-app-consent-policy"></a>Benutzereinwilligung zulassen, die einer Richtlinie zur Einwilligung für die App unterliegt

Wählen Sie zum Zulassen der Benutzereinwilligung aus, welche Richtlinie zur Einwilligung für die App die Autorisierung von Benutzern zum Erteilen einer Einwilligung für Apps steuern soll:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.{consent-policy-id}")
  ```

Ersetzen Sie `{consent-policy-id}` durch die ID der Richtlinie, die Sie anwenden möchten. Sie können eine von Ihnen erstellte [benutzerdefinierte Richtlinie zur Einwilligung für die App](manage-app-consent-policies.md#create-a-custom-app-consent-policy) wählen oder aus den folgenden integrierten Richtlinien auswählen:

| id | Beschreibung |
|:---|:------------|
| microsoft-user-default-low | **Für ausgewählte Berechtigungen die Benutzereinwilligung für Apps von verifizierten Herausgebern zulassen**<br /> Erlauben Sie eingeschränkte Benutzereinwilligung nur für Apps von verifizierten Herausgebern und für in Ihrem Mandanten registrierte Apps, sowie nur für Berechtigungen, die Sie als „geringe Auswirkung“ klassifizieren. (Vergessen Sie nicht, [Berechtigungen zu klassifizieren](configure-permission-classifications.md), um auszuwählen, in welche Berechtigungen Benutzer einwilligen dürfen.) |
| microsoft-user-default-legacy | **Benutzereinwilligung für Apps zulassen**<br /> Diese Option ermöglicht es allen Benutzern, für beliebige Anwendungen in jede beliebige Berechtigung einzuwilligen, die keine Administratoreinwilligung erfordert. |
  
So aktivieren Sie beispielsweise eine Benutzereinwilligung, die der integrierten Richtlinie `microsoft-user-default-low` unterliegt:

```powershell
Set-AzureADMSAuthorizationPolicy `
   -Id "authorizationPolicy" `
   -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.microsoft-user-default-low")
```

---

> [!TIP]
> [Aktivieren Sie den Workflow für Administratoreinwilligung](configure-admin-consent-workflow.md), wenn Sie Benutzern das Anfordern einer Überprüfung und Genehmigung für eine Anwendung durch einen Administrator ermöglichen möchten, bei der ein Benutzer nicht einwilligen darf – z. B. wenn die Benutzereinwilligung deaktiviert wurde oder eine Anwendung Berechtigungen anfordert, die der Benutzer nicht erteilen darf.

## <a name="risk-based-step-up-consent"></a>Risikobasierte hochgestufte Einwilligung

Die risikobasierte hochgestufte Einwilligung trägt zur Reduzierung der Benutzergefährdung durch böswillige Apps bei, die [unrechtmäßige Einwilligungsanforderungen](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) ausgeben. Wenn Microsoft eine risikobehaftete Anforderung der Endbenutzereinwilligung erkennt, wird die Anforderung stattdessen auf Administratoreinwilligung „hochgestuft“. Diese Funktion ist standardmäßig aktiviert, führt jedoch nur zu einer Änderung des Verhaltens, wenn die Endbenutzereinwilligung aktiviert ist.

Wird eine risikobehaftete Einwilligungsanforderung erkannt, wird in der Einwilligungsaufforderung eine Meldung angezeigt, die besagt, dass eine Administratorgenehmigung erforderlich ist. Wenn der [Workflow zum Anfordern der Administratoreinwilligung](configure-admin-consent-workflow.md) aktiviert ist, kann der Benutzer die Anforderung zur weiteren Überprüfung direkt von der Einwilligungsaufforderung an einen Administrator senden. Ist diese Funktion nicht aktiviert, wird die folgende Meldung angezeigt:

* **AADSTS90094:** &lt;Anzeigename der Client-App&gt; benötigt eine Berechtigung zum Zugriff auf Ressourcen in Ihrer Organisation, die nur ein Administrator erteilen kann. Bitten Sie einen Administrator, dieser App Berechtigungen zu erteilen, bevor Sie sie verwenden können.

In diesem Fall wird auch ein Überwachungsereignis der Kategorie „ApplicationManagement“ mit dem Aktivitätstyp „Einwilligung in Anwendung“ und dem Statusgrund „Riskante Anwendung erkannt“ protokolliert.

> [!IMPORTANT]
> Administratoren sollten vor der Genehmigung einer Anforderung [alle Einwilligungsanforderungen sorgfältig auswerten](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent), besonders wenn Microsoft ein Risiko erkannt hat.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Deaktivieren oder erneutes Aktivieren der risikobasierten hochgestuften Einwilligung mit PowerShell

Mit dem Modul „Azure AD PowerShell Preview“ ([AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)) können Sie die Hochstufung auf Administratoreinwilligung deaktivieren (wenn Microsoft Risiken erkennt) oder erneut aktivieren, wenn sie zuvor deaktiviert wurde.

1. Stellen Sie sicher, dass Sie das Modul [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) verwenden. Dieser Schritt ist wichtig, wenn auf Ihrem Computer sowohl das Modul [AzureAD](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) als auch das Modul [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) installiert ist.

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Stellen Sie eine Verbindung mit Azure AD PowerShell her.

   ```powershell
   Connect-AzureAD
   ```

1. Rufen Sie den aktuellen Wert für die Verzeichniseinstellungen der **Zustimmungsrichtlinieneinstellungen** in Ihrem Mandanten ab. Dazu muss überprüft werden, ob die Verzeichniseinstellungen für dieses Feature erstellt wurden, und wenn dies nicht der Fall ist, müssen die Werte aus der entsprechenden Vorlage für die Verzeichniseinstellungen verwendet werden.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```

1. Grundlegendes zum Einstellungswert:

    | Einstellung       | type         | BESCHREIBUNG  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Boolean |  Flag, das angibt, ob die Benutzereinwilligung beim Erkennen einer risikobehafteten Anforderung blockiert wird. |

1. Aktualisieren Sie den Einstellungswert für die gewünschte Konfiguration:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

1. Speichern Sie die Einstellungen.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen:

* [Konfigurieren von Einstellungen für die Benutzereinwilligung](configure-user-consent.md)
* [Verwalten von Richtlinien zur Einwilligung für die App](manage-app-consent-policies.md)
* [Konfigurieren des Workflows für die Administratoreinwilligung (Vorschau)](configure-admin-consent-workflow.md)
* Weitere Informationen zum [Verwalten der Einwilligung zu Anwendungen und Auswerten von Einwilligungsanforderungen](manage-consent-requests.md)
* [Erteilen einer mandantenweiten Administratoreinwilligung für eine Anwendung](grant-admin-consent.md)
* [Berechtigungen und Zustimmung im Microsoft Identity Platform-Endpunkt](../develop/v2-permissions-and-consent.md)

So erhalten Sie Hilfe oder finden Antworten auf Ihre Fragen:
* [Azure AD bei StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
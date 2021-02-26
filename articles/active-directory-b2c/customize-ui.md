---
title: Anpassen der Benutzeroberfläche
titleSuffix: Azure AD B2C
description: In diesem Artikel erfahren Sie, wie Sie die Benutzeroberfläche Ihrer Anwendungen anpassen, die Azure Active Directory B2C verwenden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ecece3a00a788b67f6c831804bf5b00372fef93d
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056792"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Anpassen der Benutzeroberfläche in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Das Branding und Anpassen der Benutzeroberfläche, die Azure Active Directory B2C (Azure AD B2C) für Ihre Kunden anzeigt, trägt zu einer nahtlosen Benutzererfahrung in Ihrer Anwendung bei. Diese Benutzererfahrung umfasst Registrieren, Anmelden, Bearbeiten von Profilen und Zurücksetzen von Kennwörtern. In diesem Artikel passen Sie Ihre Azure AD B2C-Seiten mithilfe einer Seitenvorlage und des Unternehmensbrandings an. 

> [!TIP]
> Informationen zum Anpassen anderer Aspekte Ihrer Benutzerflowseiten über die Seitenvorlage, das Bannerlogo, das Hintergrundbild oder die Hintergrundfarbe hinaus finden Sie unter [Anpassen der Benutzeroberfläche mit einer HTML-Vorlage](customize-ui-with-html.md).

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="overview"></a>Übersicht

Azure AD B2C bietet eine Reihe von integrierten Vorlagen, unter denen Sie auswählen können, um Ihren Seiten mit Benutzererfahrung einen professionellen Look zu verleihen. Diese Seitenvorlagen können auch als Ausgangspunkt für eigene Anpassungsschritte mit dem [Unternehmensbranding](#company-branding) dienen.

### <a name="ocean-blue"></a>Ozeanblau

Beispiel für die gerenderte Vorlage „Ozeanblau“ auf der Seite für die Registrierung/Anmeldung:

![Screenshot der Vorlage „Ozeanblau“](media/customize-ui/template-ocean-blue.png)

### <a name="slate-gray"></a>Schiefergrau

Beispiel für die gerenderte Vorlage „Schiefergrau“ auf der Seite für die Registrierung/Anmeldung:

![Screenshot der Vorlage „Schiefergrau“](media/customize-ui/template-slate-gray.png)

### <a name="classic"></a>Klassisch

Beispiel für die gerenderte Vorlage „Klassisch“ auf der Seite für die Registrierung/Anmeldung:

![Screenshot der Vorlage „Klassisch“](media/customize-ui/template-classic.png)

### <a name="company-branding"></a>Unternehmensbranding

In Azure Active Directory können Sie mithilfe des Features [Unternehmensbranding](../active-directory/fundamentals/customize-branding.md) Ihre Azure AD B2C-Seiten mit einem Bannerlogo, einem Hintergrundbild und einer Hintergrundfarbe anpassen. Dieses Unternehmensbranding umfasst die Funktionen zum Registrieren, Anmelden und Bearbeiten von Profilen und zum Zurücksetzen von Kennwörtern. 

Das folgende Beispiel zeigt eine Seite für die *Registrierung und Anmeldung* mit einem benutzerdefinierten Logo und einem Hintergrundbild mit der Vorlage „Ozeanblau“:

![Von Azure AD B2C bereitgestellte Registrierungs-/Anmeldeseite mit Branding](media/customize-ui/template-ocean-blue-branded.png)


## <a name="select-a-page-template"></a>Auswählen einer Seitenvorlage

::: zone pivot="b2c-user-flow"

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie **Benutzerflows** aus.
1. Wählen Sie einen Benutzerflow aus, den Sie anpassen möchten.
1. Wählen Sie im linken Menü unter **Anpassen** die Option **Seitenlayouts** und anschließend eine **Vorlage** aus.
    ![Dropdownmenü zur Vorlagenauswahl auf der Seite des Benutzerflows im Azure-Portal](./media/customize-ui/select-page-template.png)

Wenn Sie eine Vorlage auswählen, wird diese auf alle Seiten im Benutzerflow angewandt. Der URI für jede Seite wird im Feld **Benutzerdefinierter Seiten-URI** angezeigt.

::: zone-end

::: zone pivot="b2c-custom-policy"

Wenn Sie eine Seitenvorlage auswählen möchten, legen Sie das `LoadUri`-Element der [Inhaltsdefinitionen](contentdefinitions.md) fest. Im folgenden Beispiel werden die Inhaltsdefinitions-IDs mit dem zugehörigen `LoadUri` gezeigt. 

Ozeanblau:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

Schiefergrau:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/MSA/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/MSA/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/MSA/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

Klassisch: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/default/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/default/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end


## <a name="configure-company-branding"></a>Konfigurieren des Unternehmensbrandings

Zum Anpassen Ihrer Benutzerflowseiten konfigurieren Sie zuerst das Unternehmensbranding in Azure Active Directory, und aktivieren es dann in Ihren Benutzerflows in Azure AD B2C.

Legen Sie zuerst das Bannerlogo, das Hintergrundbild und die Hintergrundfarbe in **Unternehmensbranding** fest.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie unter **Verwalten** die Option **Unternehmensbranding** aus.
1. Führen Sie die unter [Hinzufügen von Branding zur Azure Active Directory-Anmeldeseite Ihrer Organisation](../active-directory/fundamentals/customize-branding.md) aufgeführten Schritte aus.

Beachten Sie beim Konfigurieren des Unternehmensbrandings in Azure AD B2C die folgenden Punkte:

* Das Unternehmensbranding in Azure AD B2C ist derzeit auf die Anpassung des **Hintergrundbilds**, des **Bannerlogos** und der **Hintergrundfarbe** beschränkt. Die anderen Eigenschaften im Bereich „Unternehmensbranding“ (z. B. die unter **Erweiterte Einstellungen**) werden *nicht unterstützt*.
* Auf Ihren Benutzerflowseiten wird die Hintergrundfarbe angezeigt, bevor das Hintergrundbild geladen wird. Es wird empfohlen, eine Hintergrundfarbe auszuwählen, die weitestgehend den Farben in Ihrem Hintergrundbild entspricht, um ein reibungsloseres Ladeerlebnis zu ermöglichen.
* Das Bannerlogo wird in den Überprüfungs-E-Mails angezeigt, die an Ihre Benutzer gesendet werden, wenn sie einen Benutzerflow für die Registrierung initiieren.



## <a name="enable-company-branding-in-user-flow-pages"></a>Aktivieren des Unternehmensbrandings auf Benutzerflowseiten

::: zone pivot="b2c-user-flow"

Nachdem Sie das Unternehmensbranding konfiguriert haben, können Sie es in Ihren Benutzerflows aktivieren.

1. Wählen Sie im Azure-Portal im linken Menü die Option **Azure AD B2C** aus.
1. Wählen Sie unter **Richtlinien** die Option **Benutzerflows (Richtlinien)** aus.
1. Wählen Sie den Benutzerflow aus, für den Sie das Unternehmensbranding aktivieren möchten. Für die Standard-Benutzerflowtypen *Anmeldung* und *Profilbearbeitung* wird das Unternehmensbranding **nicht unterstützt**.
1. Wählen Sie unter **Anpassen** die Option **Seitenlayouts** und dann die Seite aus, die Sie mit Branding versehen möchten. Wählen Sie z. B. **Einheitliche Seite für Registrierung oder Anmeldung** aus.
1. Wählen Sie für **Seitenlayoutversion (Vorschau)** die Version **1.2.0** oder eine höhere Version aus.
1. Wählen Sie **Speichern** aus.

Wenn Sie alle Seiten im Benutzerflow mit Branding versehen möchten, legen Sie die Seitenlayoutversion für jedes Seitenlayout im Benutzerflow fest.

![Seitenlayoutauswahl in Azure AD B2C im Azure-Portal](media/customize-ui/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Nachdem Sie das Unternehmensbranding konfiguriert haben, können Sie es in Ihrer benutzerdefinierten Richtlinie aktivieren. Konfigurieren Sie die [Seitenlayoutversion](contentdefinitions.md#migrating-to-page-layout) mit der `contract`-Version der Seite für *alle* Inhaltsdefinitionen in Ihrer benutzerdefinierten Richtlinie. Das Format des Werts muss das Wort `contract` enthalten: _urn:com:microsoft:aad:b2c:elements:**contract**:page-name:version_. Sie können das Seitenlayout in Ihren benutzerdefinierten Richtlinien angeben, die einen alten **DataUri**-Wert verwenden. Erfahren Sie, wie Sie mit der Seitenversion eine [Migration zum Seitenlayout](contentdefinitions.md#migrating-to-page-layout) durchführen.

Das folgende Beispiel zeigt die Inhaltsdefinitionen mit den entsprechenden Seitenverträgen und der Seitenvorlage *Ozeanblau*: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen dazu, wie Sie die Benutzeroberfläche Ihrer Anwendungen anpassen können, finden Sie unter [Anpassen der Benutzeroberfläche einer Anwendung in Azure Active Directory B2C](customize-ui-with-html.md).

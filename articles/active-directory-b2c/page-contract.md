---
title: Auswählen eines Seitenvertrags – Azure Active Directory B2C | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie in Azure Active Directory B2C einen Seitenvertrag auswählen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7aab43695f0b11590d8bd2aa011073ba04d95250
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66512998"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>Auswählen eines Seitenvertrags in Azure Active Directory B2C mit benutzerdefinierten Richtlinien

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Sie können den clientseitigen JavaScript-Code in Ihren Azure Active Directory B2C- Richtlinien (Azure AD) aktivieren, und zwar unabhängig davon, ob Sie Benutzerflows oder benutzerdefinierte Richtlinien verwenden. Um JavaScript für Ihre Anwendungen zu aktivieren, müssen Sie ein Element zu Ihrer [benutzerdefinierten Richtlinie](active-directory-b2c-overview-custom.md) hinzufügen, einen Seitenvertrag auswählen und [b2clogin.com](b2clogin.md) in Ihren Anforderungen verwenden. Ein Seitenvertrag ist eine Zusammenstellung der von Azure AD B2C bereitgestellten Elemente und des von Ihnen bereitgestellten Inhalts. In diesem Artikel wird das Auswählen eines Seitenvertrags in Azure AD B2C beschrieben, indem dieser in einer benutzerdefinierten Richtlinie konfiguriert wird.

> [!NOTE]
> Wenn Sie JavaScript für Benutzerflows aktivieren möchten, finden Sie Informationen unter [JavaScript und Seitenvertragsversionen in Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="replace-datauri-values"></a>Ersetzen von DataUri-Werten

In Ihren benutzerdefinierten Richtlinien verfügen Sie ggf. über [ContentDefinitions](contentdefinitions.md), mit denen die HTML-Vorlagen für die User Journey definiert werden. Das **ContentDefinition**-Element enthält einen **DataUri**, mit dem auf die von Azure AD B2C bereitgestellten Seitenelemente verwiesen wird. Der **LoadUri** ist der relative Pfad zum HTML- und CSS-Inhalt, den Sie bereitstellen.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

Zum Auswählen eines Seitenvertrags ändern Sie die **DataUri**-Werte in den [ContentDefinitions](contentdefinitions.md)-Elementen Ihrer Richtlinien. Indem Sie von den alten **DataUri**-Werten zu den neuen Werten wechseln, wählen Sie ein unveränderliches Paket aus. Der Vorteil der Verwendung dieses Pakets besteht darin, dass Sie sicher sein können, dass es nicht zu Änderungen kommt und kein unerwartetes Verhalten für Ihre Seite auftritt. 

Verwenden Sie zum Festlegen eines Seitenvertrags die folgende Tabelle, um nach **DataUri**-Werten zu suchen. 

| Alter DataUri-Wert | Neuer DataUri-Wert |
| ----------------- | ----------------- |
| urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0 | 
| urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0 |
| urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0 | 
| urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0 |
| urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0 |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen dazu, wie Sie die Benutzeroberfläche Ihrer Anwendungen anpassen können, finden Sie unter [Anpassen der Benutzeroberfläche einer Anwendung mithilfe einer benutzerdefinierten Richtlinie in Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).




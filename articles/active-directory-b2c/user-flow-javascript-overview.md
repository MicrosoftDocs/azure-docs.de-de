---
title: JavaScript und Seitenvertragsversionen – Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie in Azure Active Directory B2C JavaScript aktivieren und Seitenvertragsversionen verwenden.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ef474bec71a9015209b5748b6947816002bd4a5d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511978"
---
# <a name="javascript-and-page-contract-versions-in-azure-active-directory-b2c"></a>JavaScript und Seitenvertragsversionen in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C stellt eine Reihe von gepackten Inhalten bereit, die HTML, CSS und JavaScript für die Benutzeroberflächenelemente in Ihren Benutzerflows und benutzerdefinierten Richtlinien enthalten. Um JavaScript für Ihre Anwendungen zu aktivieren, müssen Sie ein Element zu Ihrer [benutzerdefinierten Richtlinie](active-directory-b2c-overview-custom.md) hinzufügen oder im Portal für Benutzerflows aktivieren, einen Seitenvertrag auswählen und [b2clogin.com](b2clogin.md) in Ihren Anforderungen verwenden.

Wenn Sie beabsichtigen, clientseitigen [JavaScript](javascript-samples.md)-Code zu aktivieren, sollten Sie sicherstellen, dass die Elemente, auf denen der JavaScript-Code basiert, unveränderlich sind. Andernfalls könnten Änderungen zu einem unerwarteten Verhalten auf Ihren Benutzerseiten führen. Um diese Probleme zu vermeiden, können Sie die Verwendung eines Seitenvertrags erzwingen und eine Seitenvertragsversion angeben. Dadurch wird sichergestellt, dass alle Inhaltsdefinitionen, auf denen Ihr JavaScript-Code basiert, unveränderlich sind. Auch wenn Sie nicht beabsichtigen, JavaScript zu aktivieren, können Sie eine Seitenvertragsversion für Ihre Seiten angeben.

## <a name="user-flows"></a>Benutzerabläufe

In den Benutzerfloweigenschaften können Sie JavaScript aktivieren, wodurch auch die Verwendung eines Seitenvertrags erzwungen wird. Anschließend können Sie die Seitenvertragsversion festlegen, wie im nächsten Abschnitt beschrieben.

![Einstellung „JavaScript aktivieren“](media/user-flow-javascript-overview/javascript-settings.png)

Sie können eine Seitenvertragsversion für Ihre Benutzerflowseiten angeben, und zwar unabhängig davon, ob Sie JavaScript in den Benutzerfloweigenschaften aktivieren oder nicht. Öffnen Sie den Benutzerflow, und wählen Sie **Seitenlayouts** aus. Wählen Sie unter **Layoutname** eine Benutzerflowseite und dann die Option **Seitenvertragsversion** aus.

![Einstellung „JavaScript aktivieren“](media/user-flow-javascript-overview/page-contract-version.png)

## <a name="custom-policies"></a>Benutzerdefinierte Richtlinien

Um JavaScript in benutzerdefinierten Richtlinien zu aktivieren, fügen Sie in der Datei für die benutzerdefinierte Richtlinie dem Element **RelyingParty** das Element **ScriptExecution** hinzu. Weitere Informationen finden Sie unter [JavaScript-Beispiele für die Verwendung in Azure Active Directory B2C](javascript-samples.md).

Unabhängig davon, ob Sie JavaScript in den benutzerdefinierten Richtlinien aktivieren oder nicht, können Sie eine Seitenvertragsversion für Ihre Seiten angeben. Weitere Informationen zum Angeben eines Seitenvertrags finden Sie unter [Auswählen eines Seitenvertrags in Azure Active Directory B2C mit benutzerdefinierten Richtlinien](page-contract.md).

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie [JavaScript-Beispiele für die Verwendung in Azure Active Directory B2C](javascript-samples.md).

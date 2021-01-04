---
title: Synchronisieren von Attributen mit Azure AD zu Zuordnungszwecken
description: Erfahren Sie, wie Sie Attribute aus Ihren lokalen Active Directory Domain Services (AD) mit Azure Active Directory (Azure AD) synchronisieren können. Verwenden Sie beim Konfigurieren der Benutzerbereitstellungen in SaaS-Apps (Software-as-a-Service) die Verzeichniserweiterungsfunktion, um Quellattribute hinzuzufügen, die nicht standardmäßig synchronisiert sind.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/13/2019
ms.author: kenwith
ms.openlocfilehash: cecdda27139aa1dd0733b50898db9db70aa34dc3
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040991"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Synchronisieren eines Attributs aus lokalen Active Directory Domain Services mit Azure AD für die Bereitstellung einer Anwendung

Beim Anpassen von Attributzuordnungen für die Benutzerbereitstellung wird das Attribut, das Sie zuordnen möchten, möglicherweise nicht in der **Quellattribut** -Liste angezeigt. In diesem Artikel wird veranschaulicht, wie Sie das fehlende Attribut hinzufügen, indem Sie es aus Ihren lokalen Active Directory Domain Services mit Azure Active Directory synchronisieren.

Azure AD muss bei der Bereitstellung eines Benutzerkontos aus Azure AD in eine SaaS-App alle erforderlichen Daten zum Erstellen eines Benutzerprofils enthalten. In einigen Fällen müssen Sie möglicherweise Attribute aus Ihren lokalen AD-Instanzen mit Azure AD synchronisieren, um die Daten verfügbar zu machen. Azure AD Connect synchronisiert zwar automatisch bestimmte Attribute mit Azure AD, allerdings nicht alle. Darüber hinaus werden einige standardmäßig synchronisierte Attribute (z. B. SAMAccountName) nicht über die Microsoft Graph-API verfügbar gemacht. In diesen Fällen können Sie die Verzeichniserweiterungsfunktion von Azure AD Connect verwenden, um das Attribut mit Azure AD zu synchronisieren. Auf diese Weise wird das Attribut über die Microsoft Graph-API und den Azure AD-Bereitstellungsdienst angezeigt.

Führen Sie die folgenden Schritte aus, wenn sich die für die Bereitstellung benötigten Daten in Active Directory Domain Services befinden, aus den zuvor genannten Gründen aber nicht für die Bereitstellung zur Verfügung stehen.
 
## <a name="sync-an-attribute"></a>Synchronisieren eines Attributs 

1. Öffnen Sie den Azure AD Connect-Assistenten, wählen Sie „Tasks“ aus, und klicken Sie auf **Synchronisierungsoptionen anpassen**.

   ![Seite „Weitere Aufgaben“ des Azure Active Directory Connect-Assistenten](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Anmelden als globaler Azure AD-Administrator 

3. Wählen Sie auf der Seite **Optionale Features** **Verzeichniserweiterungen-Attributsynchronisierung** aus.
 
   ![Seite „Optionale Features“ des Azure Active Directory Connect-Assistenten](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Wählen Sie das Attribut bzw. die Attribute aus, die Sie mit Azure AD erweitern möchten.
   > [!NOTE]
   > Bei der Suche im Feld **Verfügbare Attribute** wird die Groß-/Kleinschreibung berücksichtigt.

   ![Screenshot der Auswahlseite „Verzeichniserweiterungen“](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Beenden Sie den Azure AD Connect-Assistenten, und führen Sie einen vollständigen Synchronisierungszyklus aus. Nach Abschluss des Synchronisierungszyklus wird das Schema erweitert, und die neuen Werte werden zwischen Ihrem lokalen AD und Azure AD synchronisiert.
 
6. Während Sie im Azure-Portal [Benutzerattributzuordnungen bearbeiten](customize-application-attributes.md), enthält die Liste der **Quellattribute** jetzt die hinzugefügten Attribute im `<attributename> (extension_<appID>_<attributename>)`-Format. Wählen Sie das Attribut aus, und ordnen Sie es der Zielanwendung für die Bereitstellung zu.

   ![Seite „Directory extensions selection“ (Verzeichniserweiterungsauswahl) des Azure Active Directory Connect-Assistenten](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> Die Möglichkeit des Bereitstellens von Verweisattributen aus lokalen AD-Instanzen (z. B. **managedBy** oder **DN/DistinguishedName** ) wird derzeit nicht unterstützt. Sie können dieses Feature über [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory) anfordern. 

## <a name="next-steps"></a>Nächste Schritte

* [Attribute-based application provisioning with scoping filters (Definieren des Geltungsbereichs für die Bereitstellung)](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

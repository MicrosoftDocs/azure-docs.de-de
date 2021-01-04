---
title: Aktivieren der systemseitig zugewiesenen verwalteten Identität für die Azure Spring Cloud-Anwendung
description: Hier erfahren Sie, wie Sie eine systemseitig zugewiesene verwaltete Identität für Ihre Anwendung aktivieren.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/13/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 2fbdd46b872c4c70999355d457045f4aac8aa34c
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92742801"
---
# <a name="how-to-enable-system-assigned-managed-identity-for-azure-spring-cloud-application"></a>Aktivieren einer systemseitig zugewiesenen verwalteten Identität für eine Azure Spring Cloud-Anwendung

**Dieser Artikel gilt für:** ✔️ Java ✔️ C#

Verwaltete Identitäten stellen für Azure-Ressourcen wie Ihre Azure Spring Cloud-Anwendung eine automatisch verwaltete Identität in Azure Active Directory bereit. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

In diesem Artikel erfahren Sie, wie Sie systemseitig zugewiesene verwaltete Identitäten für eine Azure Spring Cloud-App aktivieren und deaktivieren können. Dazu verwenden Sie das Azure-Portal und die CLI (verfügbar in Version 0.2.4).

## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie nicht mit verwalteten Identitäten für Azure-Ressourcen vertraut sind, sehen Sie sich die [Übersicht](../active-directory/managed-identities-azure-resources/overview.md) an.
Sie benötigen eine bereitgestellte Azure Spring Cloud-Instanz. Befolgen Sie dazu die Schritte im Artikel [Schnellstart: Starten einer Java Spring-Anwendung mit der Azure CLI](spring-cloud-quickstart.md).

## <a name="add-a-system-assigned-identity"></a>Hinzufügen einer systemseitig zugewiesenen Identität
Für die Erstellung einer App mit einer systemseitig zugewiesenen Identität muss eine zusätzliche Eigenschaft für die Anwendung festgelegt werden.

### <a name="using-azure-portal"></a>Verwenden des Azure-Portals
Erstellen Sie zuerst eine Anwendung, und aktivieren Sie dann das Feature, um eine verwaltete Entität im [Azure-Portal](https://portal.azure.com/) einzurichten.

1. Erstellen Sie wie gewohnt eine App im Portal. Navigieren Sie im Portal zu dieser App.
2. Scrollen Sie im linken Navigationsbereich nach unten zur Gruppe **Einstellungen** .
3. Wählen Sie **Identität** aus.
4. Ändern Sie auf der Registerkarte **Systemseitig zugewiesen** den **Status** in *Ein* . Klicken Sie auf **Speichern** .

 ![Verwaltete Identität im Portal](./media/spring-cloud-managed-identity/identity-1.png)

### <a name="using-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle
Sie können eine vom System zugewiesene verwaltete Identität beim Erstellen einer App oder für eine vorhandene App aktivieren.

**Aktivieren einer vom System zugewiesenen verwalteten Identität beim Erstellen einer App**

Im folgenden Beispiel wird eine App namens *app_name* mit einer vom System zugewiesenen verwalteten Identität erstellt. Dies wird vom Parameter `--assign-identity` angefordert.

```azurecli
az spring-cloud app create -n app_name -s service_name -g resource_group_name --assign-identity
```

**Aktivieren einer vom System zugewiesenen verwalteten Identität für eine vorhandene App** Verwenden Sie den `az spring-cloud app identity assign`-Befehl, um eine vom System zugewiesene Identität für eine vorhandene App zu aktivieren.

```azurecli
az spring-cloud app identity assign -n app_name -s service_name -g resource_group_name
```

## <a name="obtain-tokens-for-azure-resources"></a>Abrufen von Tokens für Azure-Ressourcen
Eine App kann mithilfe ihrer verwalteten Identität Token für den Zugriff auf andere durch Azure Active Directory geschützte Ressourcen wie z. B. Azure Key Vault abrufen. Diese Tokens stellen die Anwendung dar, die auf die Ressource zugreift, keinen bestimmten Benutzer der Anwendung.

Sie müssen die [Zielressource möglicherweise für den Zugriff über die Anwendung konfigurieren](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Wenn Sie beispielsweise ein Token für den Zugriff auf Key Vault anfordern, müssen Sie sicherstellen, dass Sie eine Zugriffsrichtlinie hinzugefügt haben, die die Identität Ihrer Anwendung enthält. Andernfalls werden Ihre Aufrufe von Key Vault abgelehnt, auch wenn diese das Token enthalten. Informationen zu den Ressourcen, die Azure Active Directory-Token unterstützen, finden Sie unter [Azure-Dienste, die die Azure AD-Authentifizierung unterstützen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Azure Spring Cloud und die Azure-VM nutzen den Endpunkt für die Tokenbeschaffung gemeinsam. Es wird empfohlen, Token mithilfe des das Java SDK oder von Spring Boot Startern abzurufen.  Im Artikel [Verwenden von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer zum Abrufen eines Zugriffstokens](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) finden Sie verschiedene Code- und Skriptbeispiele und Anleitungen zu wichtigen Themen, z. B. zum Umgang mit Tokenablauf und HTTP-Fehlern.

Empfohlen: Verwenden Sie das Java SDK oder Spring Boot Starter, um Tokens abzurufen.  Beispiele finden Sie unter [Nächste Schritte](#next-steps).

## <a name="disable-system-assigned-identity-from-an-app"></a>Deaktivieren einer vom System zugewiesenen Identität in einer App
Wenn Sie eine systemseitig zugewiesene Identität entfernen, wird sie auch aus Azure AD gelöscht. Systemseitig zugewiesene Identitäten werden automatisch aus Azure AD entfernt, wenn die App-Ressource gelöscht wird.

### <a name="using-azure-portal"></a>Verwenden des Azure-Portals
Bei einer App, für die eine vom System zugewiesene verwaltete Identität nicht mehr benötigt wird, gehen Sie wie folgt vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit einem Konto an, das dem Azure-Abonnement zugeordnet ist, das die Azure Spring Cloud-Instanz enthält.
1. Navigieren Sie zum gewünschten virtuellen Computer, und wählen Sie **Identität** aus.
1. Wählen Sie unter **Vom System zugewiesen**/**Status** die Option **Aus** aus, und klicken Sie dann auf **Speichern** :

 ![Verwaltete Identität](./media/spring-cloud-managed-identity/remove-identity.png)

### <a name="using-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle
Für eine App, für die eine vom System zugewiesene verwaltete Identität nicht mehr benötigt wird, können Sie den folgenden Befehl zum Entfernen der Identität verwenden:
```azurecli
az spring-cloud app identity remove -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>Nächste Schritte

* [Zugreifen auf Azure Key Vault mit verwalteten Identitäten in Spring Boot Starter](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets/README.md#use-msi--managed-identities)
* [Weitere Informationen zu verwalteten Identitäten für Azure-Ressourcen](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Verwenden verwalteter Identitäten mit dem Java SDK](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)

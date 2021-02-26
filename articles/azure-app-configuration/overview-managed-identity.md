---
title: Konfigurieren verwalteter Identitäten mit Azure App Configuration
description: Erfahren Sie, wie verwaltete Identitäten in Azure App Configuration funktionieren und wie Sie eine verwaltete Identität konfigurieren.
author: barbkess
ms.topic: article
ms.date: 02/25/2020
ms.author: barbkess
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: e6ced30e8c8c5eb60eea3806d9c8080df7c2a12d
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979588"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Verwenden von verwalteten Identitäten für Azure App Configuration

In diesem Thema erfahren Sie, wie Sie eine verwaltete Identität für Azure App Configuration erstellen. Über eine verwaltete Identität aus Azure Active Directory (AAD) kann Azure App Configuration problemlos auf andere durch AAD geschützte Ressourcen wie Azure Key Vault zugreifen. Die Identität wird von der Azure-Plattform verwaltet. Sie müssen keine Geheimnisse bereitstellen oder rotieren. Weitere Informationen zu verwalteten Identitäten in AAD finden Sie unter [Verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md).

Ihrer Anwendung können zwei Arten von Identitäten zugewiesen werden:

- Eine **vom System zugewiesene Identität** ist an Ihren Konfigurationsspeicher gebunden. Sie wird gelöscht, wenn Ihr Konfigurationsspeicher gelöscht wird. Ein Konfigurationsspeicher kann nur eine systemseitig zugewiesene Identität aufweisen.
- Eine **benutzerseitig zugewiesene Identität** ist eine eigenständige Azure-Ressource, die Ihrem Konfigurationsspeicher zugewiesen werden kann. Ein Konfigurationsspeicher kann mehrere benutzerseitig zugewiesene Identitäten aufweisen.

## <a name="adding-a-system-assigned-identity"></a>Hinzufügen einer systemseitig zugewiesenen Identität

Für die Erstellung eines App Configuration-Speichers mit einer systemseitig zugewiesenen Identität muss eine zusätzliche Eigenschaft im Speicher festgelegt werden.

### <a name="using-the-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Um eine verwaltete Identität mithilfe der Azure-Befehlszeilenschnittstelle einzurichten, wenden Sie den Befehl [az appconfig identity assign] auf einen vorhandenen Konfigurationsspeicher an. Für die Ausführung der Beispiele in diesem Abschnitt gibt es drei Optionen:

- Verwenden Sie [Azure Cloud Shell](../cloud-shell/overview.md) über das Azure-Portal.
- Verwenden Sie die eingebettete Azure Cloud Shell über die Schaltfläche „Ausprobieren“ in der rechten oberen Ecke der unten aufgeführten Codeblöcke.
- [Installieren Sie die aktuelle Version der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) (2.1 oder höher), wenn Sie lieber eine lokale CLI-Konsole verwenden möchten.

In den nachstehenden Schritten werden Sie durch das Erstellen eines App Configuration-Speichers und das Zuweisen einer Identität mithilfe der Befehlszeilenschnittstelle geleitet:

1. Melden Sie sich bei Verwendung der Azure CLI in einer lokalen Konsole zunächst mit [az login] bei Azure an. Verwenden Sie ein Konto, das mit Ihrem Azure-Abonnement verknüpft ist:

    ```azurecli-interactive
    az login
    ```

1. Erstellen Sie über die Befehlszeilenschnittstelle einen App Configuration-Speicher. Weitere Beispiele für die Verwendung der Befehlszeilenschnittstelle mit Azure App Configuration finden Sie unter [CLI-Beispiele für App Configuration](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Führen Sie den Befehl [az appconfig identity assign] aus, um die vom System zugewiesene Identität für diesen Konfigurationsspeicher zu erstellen:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Hinzufügen einer benutzerseitig zugewiesenen Identität

Für das Erstellen eines App Configuration-Speichers mit einer benutzerseitig zugewiesenen Identität müssen Sie die Identität erstellen und dann Ihrem Speicher den Ressourcenbezeichner der Identität zuweisen.

### <a name="using-the-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Um eine verwaltete Identität mithilfe der Azure-Befehlszeilenschnittstelle einzurichten, wenden Sie den Befehl [az appconfig identity assign] auf einen vorhandenen Konfigurationsspeicher an. Für die Ausführung der Beispiele in diesem Abschnitt gibt es drei Optionen:

- Verwenden Sie [Azure Cloud Shell](../cloud-shell/overview.md) über das Azure-Portal.
- Verwenden Sie die eingebettete Azure Cloud Shell über die Schaltfläche „Ausprobieren“ in der rechten oberen Ecke der unten aufgeführten Codeblöcke.
- [Installieren Sie die neueste Version der Azure CLI](/cli/azure/install-azure-cli) (2.0.31 oder höher), wenn Sie lieber eine lokale CLI-Konsole verwenden möchten.

Die folgenden Schritte führen Sie durch das Erstellen einer vom Benutzer zugewiesenen Identität und eines App Configuration-Speichers und das anschließende Zuweisen der Identität zum Speicher mithilfe der Befehlszeilenschnittstelle:

1. Melden Sie sich bei Verwendung der Azure CLI in einer lokalen Konsole zunächst mit [az login] bei Azure an. Verwenden Sie ein Konto, das mit Ihrem Azure-Abonnement verknüpft ist:

    ```azurecli-interactive
    az login
    ```

1. Erstellen Sie über die Befehlszeilenschnittstelle einen App Configuration-Speicher. Weitere Beispiele für die Verwendung der Befehlszeilenschnittstelle mit Azure App Configuration finden Sie unter [CLI-Beispiele für App Configuration](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Erstellen Sie mit der Befehlszeilenschnittstelle eine vom Benutzer zugewiesene Identität mit dem Namen `myUserAssignedIdentity`.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    Beachten Sie in der Ausgabe dieses Befehls den Wert der `id`-Eigenschaft.

1. Führen Sie den Befehl [az appconfig identity assign] aus, um diesem Konfigurationsspeicher die neue vom Benutzer zugewiesene Identität zuzuweisen. Verwenden Sie den Wert der `id`-Eigenschaft, den Sie im vorherigen Schritt notiert haben.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Entfernen einer Identität

Eine vom System zugewiesene Identität kann entfernt werden, indem das Feature mithilfe des Befehls [az appconfig identity remove](/cli/azure/appconfig/identity#az-appconfig-identity-remove) in der Azure-Befehlszeilenschnittstelle deaktiviert wird. Benutzerseitig zugewiesene Identitäten können einzeln entfernt werden. Bei dieser Methode zum Entfernen einer systemseitig zugewiesenen Identität wird diese auch aus AAD gelöscht. Systemseitig zugewiesene Identitäten werden automatisch aus AAD entfernt, wenn die App-Ressource gelöscht wird.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer ASP.NET Core-App mit Azure App Configuration](quickstart-aspnet-core-app.md)

[az appconfig identity assign]: /cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-assign
[az login]: /cli/azure/reference-index#az-login

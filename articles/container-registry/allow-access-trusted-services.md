---
title: Zugreifen auf eine Registrierung mit Netzwerkeinschränkungen mit einem vertrauenswürdigen Azure-Dienst
description: Aktivieren des sicheren Zugriffs einer vertrauenswürdigen Azure-Dienstinstanz auf eine Containerregistrierung mit Netzwerkeinschränkungen zum Pullen oder Pushen von Images
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 2e6b6ee3736f98f53ebb0aa43d707d42ba4cc058
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527097"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>Zulassen des sicheren Zugriffs vertrauenswürdiger Dienste auf eine Containerregistrierung mit Netzwerkeinschränkungen (Vorschau)

Mit Azure Container Registry können Sie ausgewählten vertrauenswürdigen Azure-Diensten den Zugriff auf eine Registrierung gewähren, die mit Netzwerkzugriffsregeln konfiguriert ist. Wenn vertrauenswürdige Dienste zulässig sind, kann eine vertrauenswürdige Dienstinstanz die Netzwerkregeln der Registrierung sicher umgehen und Vorgänge ausführen wie Pull oder Push von Images. Die verwaltete Identität der Dienstinstanz wird für den Zugriff verwendet. Ihr muss einer Azure-Rolle zugewiesen sein, und sie muss sich bei der Registrierung authentifizieren.

Verwenden Sie Azure Cloud Shell oder eine lokale Installation der Azure CLI, um die Beispielbefehle in diesem Artikel auszuführen. Wenn Sie sie lokal verwenden möchten, ist die Version 2.18 oder höher erforderlich. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

Das Zulassen des Registrierungszugriffs durch vertrauenswürdige Azure-Dienste ist eine **Preview** funktion.

## <a name="limitations"></a>Einschränkungen

* Sie müssen eine systemseitig zugewiesene verwaltete Identität verwenden, die in einem [vertrauenswürdigen Dienst](#trusted-services) aktiviert ist, um auf eine Containerregistrierung mit Netzwerkeinschränkungen zuzugreifen. Benutzerseitig zugewiesene verwaltete Identitäten werden zurzeit nicht unterstützt.
* Das Zulassen von vertrauenswürdigen Diensten gilt nicht für eine Containerregistrierung, die mit einem [Dienstendpunkt](container-registry-vnet.md) konfiguriert ist. Die Funktion wirkt sich nur auf Registrierungen aus, die durch einen [privaten Endpunkt](container-registry-private-link.md) eingeschränkt sind, oder auf die [Zugriffsregeln für öffentliche IP-Adressen](container-registry-access-selected-networks.md) angewendet sind. 

## <a name="about-trusted-services"></a>Informationen zu vertrauenswürdigen Diensten

Azure Container Registry verfügt über ein mehrstufiges Sicherheitsmodell, das mehrere Netzwerkkonfigurationen unterstützt, die den Zugriff auf eine Registrierung einschränken, einschließlich:

* [Privater Endpunkt mit Azure Private Link](container-registry-private-link.md). Wenn ein privater Endpunkt einer Registrierung konfiguriert ist, können nur Ressourcen innerhalb des virtuellen Netzwerks unter Verwendung privater IP-Adressen darauf zugreifen.  
* [Firewallregeln für die Registrierung](container-registry-access-selected-networks.md), die den Zugriff auf den öffentlichen Endpunkt der Registrierung nur über bestimmte öffentliche IP-Adressen oder -Adressbereiche zulassen. Sie können die Firewall auch zum Blockieren des gesamten Zugriffs auf den öffentlichen Endpunkt konfigurieren, wenn private Endpunkte verwendet werden.

Wenn eine Registrierung in einem virtuellen Netzwerk bereitgestellt oder mit Firewallregeln konfiguriert wird, verweigert sie Benutzern oder Diensten von außerhalb dieser Quellen standardmäßig den Zugriff. 

Mehrere mehrinstanzenfähige Azure-Dienste werden aus Netzwerken betrieben, die nicht in diese Netzwerkeinstellungen für die Registrierung eingeschlossen werden können, sodass sie am Übertragen von Images per Pull oder Push gehindert werden. Wenn Sie bestimmte Dienstinstanzen als „vertrauenswürdig“ festlegen, kann ein Registrierungsbesitzer ausgewählten Azure-Ressourcen gestatten, die Netzwerkeinstellungen der Registrierung für das Übertragen von Images per Pull oder Push sicher zu umgehen. 

### <a name="trusted-services"></a>Vertrauenswürdige Dienste

Instanzen der folgenden Dienste können auf eine Containerregistrierung mit Netzwerkeinschränkungen zugreifen, wenn die Einstellung **Vertrauenswürdige Dienste zulassen** der Registrierung aktiviert ist (Standardeinstellung). Im Laufe der Zeit werden weitere Dienste hinzugefügt.

|Vertrauenswürdiger Dienst  |Unterstützte Verwendungsszenarien  |
|---------|---------|
|ACR-Aufgaben     | [Zugreifen auf eine andere Registrierung aus einem ACR Task](container-registry-tasks-cross-registry-authentication.md)       |
|Machine Learning | [Bereitstellen](../machine-learning/how-to-deploy-custom-docker-image.md) oder [Trainieren](../machine-learning/how-to-train-with-custom-image.md) eines Modells in einem Machine Learning-Arbeitsbereich mithilfe eines benutzerdefinierten Docker-Containers |
|Azure Container Registry | [Importieren von Images aus einer anderen Azure Container Registry-Instanz](container-registry-import-images.md#import-from-an-azure-container-registry-in-the-same-ad-tenant) | 

> [!NOTE]
> Wenn Sie die Einstellung „Vertrauenswürdige Dienste zulassen“ aktivieren, dürfen Instanzen anderer verwalteter Azure-Dienste, einschließlich App Service, Azure Container Instances und Azure Security Center und Azure Security Center, zurzeit nicht auf eine Containerregistrierung mit Netzwerkeinschränkungen zugreifen.

## <a name="allow-trusted-services---cli"></a>Vertrauenswürdige Dienste zulassen: CLI

Standardmäßig ist die Einstellung „Vertrauenswürdige Dienste zulassen“ in einer neuen Azure Container Registry-Instanz aktiviert. Deaktivieren oder aktivieren Sie die Einstellung, indem Sie den Befehl [az acr update](/cli/azure/acr#az-acr-update) ausführen.

So deaktivieren Sie

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

So aktivieren Sie die Einstellung in einer vorhandenen Registrierung oder in einer Registrierung, in der sie bereits deaktiviert ist

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>Vertrauenswürdige Dienste zulassen: Portal

Standardmäßig ist die Einstellung „Vertrauenswürdige Dienste zulassen“ in einer neuen Azure Container Registry-Instanz aktiviert. 

So deaktivieren oder aktivieren Sie die Einstellung im Portal erneut

1. Navigieren Sie im Azure-Portal zu Ihrer Containerregistrierung.
1. Wählen Sie unter **Einstellungen** die Option **Netzwerk** aus. 
1. Wählen Sie unter **Öffentlichen Netzwerkzugriff zulassen** die Option **Ausgewählte Netzwerke** oder **Deaktiviert** aus.
1. Führen Sie eines der folgenden Verfahren aus:
    * Um den Zugriff von vertrauenswürdigen Diensten zu deaktivieren, deaktivieren Sie unter **Firewallausnahme** die Option **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf diese Containerregistrierung gestatten**. 
    * Um vertrauenswürdige Dienste zuzulassen, aktivieren Sie unter **Firewallausnahme** die Option **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf diese Containerregistrierung gestatten**.
1. Wählen Sie **Speichern** aus.

## <a name="trusted-services-workflow"></a>Workflow für vertrauenswürdige Dienste

Im Folgenden finden Sie einen typischen Workflow zum Aktivieren einer Instanz eines vertrauenswürdigen Diensts für den Zugriff auf eine Containerregistrierung mit Netzwerkeinschränkungen.

1. Aktivieren Sie eine systemseitig zugewiesene [verwaltete Identität für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md) in einer Instanz eines der [vertrauenswürdigen Dienste](#trusted-services) für Azure Container Registry.
1. Weisen Sie der Identität eine [Azure-Rolle](container-registry-roles.md) für Ihre Registrierung zu. Weisen Sie z. B. die Rolle „ACRPull“ zu, um Containerimages zu pullen.
1. Konfigurieren Sie in der Registrierung mit Netzwerkeinschränkungen die Einstellung so, dass der Zugriff von vertrauenswürdigen Diensten zugelassen wird.
1. Verwenden Sie die Anmeldeinformationen der Identität für die Authentifizierung bei der Registrierung mit Netzwerkeinschränkungen. 
1. Pullen Sie Images aus der Registrierung, oder führen Sie andere für die Rolle zulässige Vorgänge aus.

### <a name="example-acr-tasks"></a>Beispiel: ACR-Aufgaben

Das folgende Beispiel veranschaulicht die Verwendung von ACR Tasks als vertrauenswürdiger Dienst. Ausführliche Informationen zu den Tasks finden Sie unter [Registrierungsübergreifende Authentifizierung in einem ACR Task unter Verwendung einer in Azure verwalteten Identität](container-registry-tasks-cross-registry-authentication.md).

1. Erstellen oder aktualisieren Sie eine Azure Container Registry-Instanz, und [übertragen Sie per Push ein Beispielbasisimage](container-registry-tasks-cross-registry-authentication.md#prepare-base-registry) in die Registrierung. Diese Registrierung ist die *Basisregistrierung* für das Szenario.
1. In einer zweiten Azure Container Registry-Instanz [definieren](container-registry-tasks-cross-registry-authentication.md#define-task-steps-in-yaml-file) und [erstellen](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity) Sie einen ACR Task, um ein Image per Pull aus der Basisregistrierung zu übertragen. Aktivieren Sie eine systemseitig zugewiesene verwaltete Identität, wenn Sie den Task erstellen.
1. Weisen Sie der Task-Identität [eine Azure-Rolle für den Zugriff auf die Basisregistrierung](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources) zu. Weisen Sie z. B. die Rolle „AcrPull“ zu, die über Berechtigungen zum Pullen von Images verfügt.
1. [Fügen Sie dem Task Anmeldeinformationen der verwalteten Identität hinzu](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task).
1. Um zu bestätigen, dass der Task Netzwerkeinschränkungen umgeht, [deaktivieren Sie den öffentlichen Zugriff](container-registry-access-selected-networks.md#disable-public-network-access) in der Basisregistrierung.
1. Führen Sie den Task aus. Wenn die Basisregistrierung und der Task ordnungsgemäß konfiguriert sind, wird der Task erfolgreich ausgeführt, weil die Basisregistrierung den Zugriff zulässt.

So testen Sie das Deaktivieren des Zugriffs von vertrauenswürdigen Diensten

1. Deaktivieren Sie in der Basisregistrierung die Einstellung, mit der der Zugriff von vertrauenswürdigen Diensten zugelassen wird.
1. Führen Sie den Task erneut aus. In diesem Fall schlägt die Ausführung des Tasks fehl, weil die Basisregistrierung den Zugriff durch den Task nicht mehr zulässt.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Einschränken des Zugriffs auf eine Registrierung mithilfe eines privaten Endpunkts in einem virtuellen Netzwerk finden Sie unter [Konfigurieren von Azure Private Link für eine Azure-Container Containerregistrierung](container-registry-private-link.md).
* Informationen zum Einrichten von Firewallregeln für die Registrierung finden Sie unter [Konfigurieren von Netzwerkregeln für öffentliche IP-Adressen](container-registry-access-selected-networks.md).

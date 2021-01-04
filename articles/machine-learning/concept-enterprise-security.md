---
title: Unternehmenssicherheit und -governance
titleSuffix: Azure Machine Learning
description: 'Verwenden Sie Azure Machine Learning sicher: Authentifizierung, Autorisierung, Netzwerksicherheit, Datenverschlüsselung und Überwachung.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/20/2020
ms.openlocfilehash: a079504872eaf3840416a99e784c4d33a6828b0c
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992028"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Sicherheit und Governance in Unternehmen für Azure Machine Learning

In diesem Artikel lernen Sie Sicherheits- und Governancefeatures kennen, die für Azure Machine Learning zur Verfügung stehen. Diese Features sind nützlich für Administratoren, DevOps und MLOps, die eine sichere, mit ihren Unternehmensrichtlinien kompatible Konfiguration erstellen möchten. Mit Azure Machine Learning und der Azure-Plattform haben Sie folgende Möglichkeiten:

* Einschränken des Zugriffs auf Ressourcen und Vorgänge durch Benutzerkonten oder Gruppen
* Einschränken der eingehenden und ausgehenden Netzwerkkommunikation
* Verschlüsseln von Daten während der Übertragung und im Ruhezustand
* Überprüfen auf Sicherheitsrisiken
* Anwenden und Überwachen von Konfigurationsrichtlinien

## <a name="restrict-access-to-resources-and-operations"></a>Einschränken des Zugriffs auf Ressourcen und Vorgänge

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) ist der Identitätsdienstanbieter für Azure Machine Learning. Damit können Sie die Sicherheitsobjekte (Benutzer, Gruppe, Dienstprinzipal und verwaltete Identität), die zum _Authentifizieren_ bei Azure-Ressourcen verwendet werden, erstellen und verwalten. Die mehrstufige Authentifizierung wird unterstützt, wenn Azure AD dafür konfiguriert ist.

Dies ist der Authentifizierungsprozess für Azure Machine Learning mit Multi-Factor Authentication in Azure AD:

1. Der Client meldet sich bei Azure AD an und ruft das Azure Resource Manager-Token ab.
1. Der Client übergibt das Token an Azure Resource Manager und an Azure Machine Learning.
1. Azure Machine Learning stellt ein Machine Learning Service-Token für das Benutzercomputeziel bereit (z. B. Azure Machine Learning-Computecluster). Dieses Token wird nach Abschluss der Ausführung vom Benutzercomputeziel für einen erneuten Aufruf von Machine Learning Service verwendet. Der Bereich ist auf den Arbeitsbereich beschränkt.

[![Authentifizierung in Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Jedem Arbeitsbereich ist eine vom System zugewiesene [verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md) zugeordnet, die den gleichen Namen hat wie der Arbeitsbereich. Diese verwaltete Identität wird für den sicheren Zugriff auf Ressourcen verwendet, die vom Arbeitsbereich verwendet werden. Sie verfügt über die folgenden Azure RBAC-Berechtigungen für angefügte Ressourcen:

| Resource | Berechtigungen |
| ----- | ----- |
| Arbeitsbereich | Mitwirkender |
| Speicherkonto | Mitwirkender an Storage-Blobdaten |
| Schlüsseltresor | Zugriff auf alle Schlüssel, Geheimnisse und Zertifikate |
| Azure Container Registry | Mitwirkender |
| Ressourcengruppe, die den Arbeitsbereich enthält | Mitwirkender |
| Ressourcengruppe, die den Schlüsseltresor enthält (wenn sie nicht mit der Ressourcengruppe identisch ist, die den Arbeitsbereich enthält) | Mitwirkender |

Administratoren sollten den Zugriff der verwalteten Identität auf die in der vorhergehenden Tabelle genannten Ressourcen nicht widerrufen. Sie können den Zugriff mithilfe des [Vorgangs zum erneuten Synchronisieren von Schlüsseln](how-to-change-storage-access-key.md) wiederherstellen.

Azure Machine Learning erstellt auch eine weitere Anwendung (deren Name mit `aml-` oder `Microsoft-AzureML-Support-App-` beginnt) mit Zugriff auf Mitwirkendenebene in Ihrem Abonnement für jede Region des Arbeitsbereichs. Wenn Sie z. B. im selben Abonnement über einen Arbeitsbereich in „USA, Osten“ und einen anderen in „Europa, Norden“ verfügen, sehen Sie zwei dieser Anwendungen. Azure Machine Learning kann Sie mithilfe dieser Anwendungen beim Verwalten der Computeressourcen unterstützen.

Sie können auch Ihre eigenen verwalteten Identitäten für die Verwendung mit Azure Virtual Machines und Azure Machine Learning-Computecluster konfigurieren. Bei einer VM kann die verwaltete Identität verwendet werden, um über das SDK anstatt das Azure AD-Konto eines einzelnen Benutzers auf Ihren Arbeitsbereich zuzugreifen. Bei einem Computecluster wird die verwaltete Identität verwendet, um auf Ressourcen wie gesicherte Datenspeicher zuzugreifen, auf die der Benutzer, der den Trainingsauftrag durchführt, möglicherweise keinen Zugriff hat. Weitere Informationen finden Sie unter [Authentifizierung für einen Azure Machine Learning-Arbeitsbereich](how-to-setup-authentication.md).

> [!TIP]
> Es gibt einige Ausnahmen bei der Verwendung von Azure AD und Azure RBAC in Azure Machine Learning:
> * Sie können optional den __SSH__-Zugriff auf Computeressourcen wie die Azure Machine Learning-Computeinstanz und Computecluster aktivieren. Der SSH-Zugriff basiert auf öffentlichen/privaten Schlüsselpaaren, nicht auf Azure AD. Der SSH-Zugriff wird nicht durch Azure RBAC geregelt.
> * Mit Authentifizierung auf Basis von __Schlüssel__ oder __Token__ können Sie sich bei Modellen authentifizieren, die als Webdienste (Rückschlussendpunkte) bereitgestellt werden. Schlüssel sind statische Zeichenfolgen, während Token mithilfe eines Azure AD-Sicherheitsobjekts abgerufen werden. Weitere Informationen finden Sie unter [Konfigurieren der Authentifizierung für Modelle, die als Webdienst bereitgestellt werden](how-to-authenticate-web-service.md).

Weitere Informationen finden Sie in den folgenden Artikeln:
* [Einrichten der Authentifizierung für Azure Machine Learning-Ressourcen und -Workflows](how-to-setup-authentication.md)
* [Verwalten des Zugriffs auf einen Azure Machine Learning-Arbeitsbereich](how-to-assign-roles.md)
* [Herstellen einer Verbindung mit Speicherdiensten in Azure](how-to-access-data.md)
* [Verwenden von Geheimnissen als Anmeldeinformationen für die Authentifizierung in Azure Machine Learning-Trainingsausführungen](how-to-use-secrets-in-runs.md)
* [Verwenden von verwalteten Identitäten mit Azure Machine Learning (Vorschau)](how-to-use-managed-identities.md)
* [Verwenden der Azure AD-Identität mit Ihrem Machine Learning-Webdienst in Azure Kubernetes Service](how-to-use-azure-ad-identity.md)

## <a name="network-security-and-isolation"></a>Netzwerksicherheit und -isolation

Um den Netzwerkzugriff auf Azure Machine Learning-Ressourcen zu beschränken, können Sie [Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md) verwenden. Mit VNETs können Sie Netzwerkumgebungen erstellen, die teilweise oder vollständig vom öffentlichen Internet isoliert sind. Dadurch werden die Angriffsfläche für die Lösung und die Möglichkeit einer Datenexfiltration reduziert.

Sie können ein VPN-Gateway (Virtual Private Network, virtuelles privates Netzwerk) verwenden, um einzelne Clients oder Ihr eigenes Netzwerk mit dem VNET zu verbinden.

Der Azure Machine Learning-Arbeitsbereich kann mit [Azure Private Link](../private-link/private-link-overview.md) einen privaten Endpunkt hinter dem VNET erstellen. Dadurch wird eine Gruppe privater IP-Adressen bereitgestellt, die für den Zugriff auf den Arbeitsbereich innerhalb des VNET verwendet werden können. Einige der Dienste, auf denen Azure Machine Learning basiert, können auch Azure Private Link verwenden, aber einige basieren auf Netzwerksicherheitsgruppen oder benutzerdefiniertem Routing.

Weitere Informationen finden Sie in den folgenden Dokumenten:

* [Übersicht zu Isolation und Datenschutz bei virtuellen Netzwerken](how-to-network-security-overview.md)
* [Schützen von Arbeitsbereichsressourcen](how-to-secure-workspace-vnet.md)
* [Schützen der Trainingsumgebung](how-to-secure-training-vnet.md)
* [Schützen der Rückschlussumgebung](how-to-secure-inferencing-vnet.md)
* [Verwenden von Azure Machine Learning Studio in einem virtuellen Netzwerk](how-to-enable-studio-virtual-network.md)
* [Verwenden von benutzerdefiniertem DNS](how-to-custom-dns.md)
* [Konfigurieren der Firewall](how-to-access-azureml-behind-firewall.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Datenverschlüsselung

Azure Machine Learning verwendet verschiedene Computeressourcen und Datenspeicher auf der Azure-Plattform. Weitere Informationen zu deren Unterstützung der Verschlüsselung von Daten im Ruhezustand und während der Übertragung finden Sie unter [Datenverschlüsselung mit Azure Machine Learning](concept-data-encryption.md).

Beim Bereitstellen von Modellen als Webdienste können Sie TLS (Transport Layer Security) aktivieren, um Daten während der Übertragung zu verschlüsseln. Weitere Informationen finden Sie unter [Verwenden von TLS zum Absichern eines Webdiensts mit Azure Machine Learning](how-to-secure-web-service.md).

## <a name="vulnerability-scanning"></a>Überprüfung auf Sicherheitsrisiken

[Azure Security Center](../security-center/security-center-introduction.md) bietet einheitliche Funktionen für die Sicherheitsverwaltung und den erweiterten Schutz vor Bedrohungen für Hybrid Cloud-Workloads. Für Azure Machine Learning sollten Sie die Überprüfung der [Azure Container Registry](../container-registry/container-registry-intro.md)-Ressourcen sowie der Azure Kubernetes Service-Ressourcen aktivieren. Weitere Informationen finden Sie unter [Azure Container Registry-Imagescans durch Security Center](../security-center/defender-for-container-registries-introduction.md) und [Azure Kubernetes Service-Integration in Security Center](../security-center/defender-for-kubernetes-introduction.md).

## <a name="audit-and-manage-compliance"></a>Überwachen und Verwalten der Compliance

[Azure Policy](../governance/policy/index.yml) ist ein Governancetool, mit dem Sie sicherstellen können, dass Azure-Ressourcen mit Ihren Richtlinien konform sind. Sie können Richtlinien festlegen, um bestimmte Konfigurationen zuzulassen oder zu erzwingen, z. B. ob Ihr Azure Machine Learning Arbeitsbereich einen privaten Endpunkt verwendet. Weitere Informationen zu Azure Policy finden Sie in der [Dokumentation zu Azure Policy](../governance/policy/overview.md). Weitere Informationen zu den Richtlinien, die speziell für Azure Machine Learning gelten, finden Sie unter [Überwachen und Verwalten der Compliance mit Azure Policy](how-to-integrate-azure-policy.md).

## <a name="next-steps"></a>Nächste Schritte

* [Sichere Azure Machine Learning-Webdienste mit TLS](how-to-secure-web-service.md)
* [Nutzen eines als Webdienst bereitgestellten Machine Learning-Modells](how-to-consume-web-service.md)
* [Verwenden von Azure Machine Learning hinter Azure Firewall](how-to-access-azureml-behind-firewall.md)
* [Verwenden von Azure Machine Learning mit einem virtuellen Azure-Netzwerk](how-to-network-security-overview.md)
* [Datenverschlüsselung von ruhenden Daten und während der Übertragung](concept-data-encryption.md)
* [Erstellen einer Echtzeitempfehlungs-API in Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)

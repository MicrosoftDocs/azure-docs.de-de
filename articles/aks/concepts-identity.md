---
title: Konzepte – Zugriff und Identität in Azure Kubernetes Service (AKS)
description: Erfahren Sie mehr über Zugriff und Identität in Azure Kubernetes Service (AKS), einschließlich Integration von Azure Active Directory, rollenbasierte Kubernetes-Zugriffssteuerung (RBAC) sowie Rollen und Bindungen.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: 3432ba671431c25b7cd9ee58decc638861e884c3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60467044"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Zugriffs- und Identitätsoptionen für Azure Kubernetes Service (AKS)

Es gibt verschiedene Möglichkeiten, sich bei Kubernetes-Clustern zu authentifizieren und Kubernetes-Cluster zu schützen. Mit der rollenbasierten Zugriffssteuerung (RBAC) können Sie den Zugriff von Benutzern oder Gruppen auf die Ressourcen beschränken, die diese benötigen. Mit Azure Kubernetes Service (AKS) können Sie die Sicherheits- und Berechtigungsstruktur weiter verbessern, indem Sie Azure Active Directory verwenden. Diese Ansätze helfen Ihnen, die Anwendungsworkloads und Kundendaten zu schützen.

In diesem Artikel werden die wichtigsten Konzepte für die Authentifizierung und das Zuweisen von Berechtigungen in AKS vorgestellt:

- [Kubernetes-Dienstkonten](#kubernetes-service-accounts)
- [Azure Active Directory-Integration](#azure-active-directory-integration)
- [Rollenbasierte Zugriffssteuerung (RBAC)](#role-based-access-controls-rbac)
- [Rollen und Clusterrollen](#roles-and-clusterroles)
- [Rollenbindungen und Clusterrollenbindungen](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Kubernetes-Dienstkonten

Einer der Hauptbenutzertypen in Kubernetes ist ein *Dienstkonto*. Ein Dienstkonto ist in der Kubernetes-API vorhanden und wird von dieser verwaltet. Die Anmeldeinformationen für Dienstkonten werden als Kubernetes-Geheimnisse gespeichert. Dadurch können sie von autorisierten Pods für die Kommunikation mit dem API-Server verwendet werden. Die meisten API-Anforderungen stellen einen Authentifizierungstoken für ein Dienstkonto oder ein normales Benutzerkonto bereit.

Normale Benutzerkonten ermöglichen den eher herkömmlichen Zugriff für Personen wie Administratoren oder Entwickler und nicht nur für Dienste und Prozesse. Kubernetes selbst bietet keine Identitätsverwaltungslösung, in der reguläre Benutzerkonten und Kennwörter gespeichert werden. Stattdessen können externe Identitätsverwaltungslösungen in Kubernetes integriert werden. Bei AKS-Clustern fungiert Azure Active Directory als integrierte Identitätsverwaltungslösung.

Weitere Informationen zu den Identitätsoptionen in Kubernetes finden Sie unter [Authentifizierung in Kubernetes][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Azure Active Directory-Integration

Die Sicherheit der AKS-Cluster kann durch die Integration von Azure Active Directory (AD) erhöht werden. Azure Active Directory (Azure AD) ist ein mehrinstanzenfähiger, cloudbasierter Verzeichnis- und Identitätsverwaltungsdienst, der Kernverzeichnisdienste, Anwendungszugriffsverwaltung und Identitätsschutz in sich vereint und auf jahrzehntelange Erfahrung mit der Identitätsverwaltung in Unternehmen aufbaut. Mit Azure AD können Sie lokale Identitäten in AKS-Cluster integrieren und so eine zentrale Quelle für die Kontoverwaltung und Sicherheit bereitstellen.

![Azure Active Directory-Integration mit AKS-Clustern](media/concepts-identity/aad-integration.png)

Mit in Azure AD integrierten AKS-Clustern können Sie Benutzern oder Gruppen Zugriff auf Kubernetes-Ressourcen in einem Namespace oder im ganzen Cluster gewähren. Um einen `kubectl`-Konfigurationskontext abzurufen, kann ein Benutzer den Befehl [az aks get-credentials][az-aks-get-credentials] ausführen. Wenn ein Benutzer dann mit dem AKS-Cluster mit `kubectl`interagiert, wird er aufgefordert, sich mit seinen Azure AD-Anmeldeinformationen anzumelden. Dieser Ansatz stellt eine zentrale Quelle für die Verwaltung von Benutzerkonten und Anmeldekennwörtern bereit. Der Benutzer kann nur auf die Ressourcen zugreifen, die der Clusteradministrator definiert hat.

Für die Azure AD-Authentifizierung in AKS-Clustern wird OpenID Connect verwendet, eine Identitätsebene, die auf dem OAuth 2.0-Protokoll aufbaut. OAuth 2.0 definiert Mechanismen zum Abrufen und Verwenden von Zugriffstoken für den Zugriff auf geschützte Ressourcen, und OpenID Connect implementiert die Authentifizierung als Erweiterung des OAuth 2.0-Autorisierungsprozesses. Weitere Informationen zu OpenID Connect finden Sie in der [OpenID Connect-Dokumentation][openid-connect]. Um die von Azure AD über OpenID Connect abgerufenen Authentifizierungstoken zu prüfen, verwenden AKS-Cluster die Webhooktokenauthentifizierung von Kubernetes. Weitere Informationen finden Sie in der [Dokumentation zur Webhookauthentifizierung][webhook-token-docs].

## <a name="role-based-access-controls-rbac"></a>Rollenbasierte Zugriffssteuerung (RBAC)

Um eine präzise Filterung der Aktionen bereitzustellen, die Benutzer ausführen können, verwendet Kubernetes die rollenbasierte Zugriffssteuerung (RBAC). Mit diesem Steuerungsmechanismus können Sie Benutzern oder Benutzergruppen die Berechtigung für bestimmte Aktionen (z. B. Ressourcen erstellen bzw. ändern oder Protokolle zur Workload ausgeführter Anwendungen anzeigen) zuweisen. Diese Berechtigungen können auf einen einzelnen Namespace begrenzt oder für den gesamten AKS-Cluster erteilt werden. Mit der rollenbasierten Zugriffssteuerung (RBAC) von Kubernetes können Sie *Rollen* erstellen, um Berechtigungen zu definieren, und anschließend diese Rollen mit *Rollenbindungen* Benutzern zuweisen.

Weitere Informationen finden Sie unter [Verwenden der RBAC-Autorisierung][kubernetes-rbac].

### <a name="azure-role-based-access-controls-rbac"></a>Rollenbasierte Zugriffssteuerung von Azure (RBAC)
Ein zusätzlicher Mechanismus zur Steuerung des Zugriffs auf Ressourcen ist die rollenbasierte Zugriffssteuerung (RBAC) von Azure. Die rollenbasierte Zugriffssteuerung von Kubernetes ist für die Ressourcen in Ihrem AKS-Cluster, und die rollenbasierte Zugriffssteuerung von Azure ist für Ressourcen innerhalb Ihres Azure-Abonnements bestimmt. Mit der rollenbasierten Zugriffssteuerung von Azure erstellen Sie eine *Rollendefinition*, welche die anzuwendenden Berechtigungen erläutert. Dieser Rollendefinition wird dann ein Benutzer oder eine Gruppe für einen bestimmten *Bereich* zugewiesen. Dieser Bereich kann eine einzelne Ressource oder eine Ressourcengruppe sein oder das ganze Abonnement beinhalten.

Weitere Informationen finden Sie unter [Was ist die rollenbasierte Azure-Zugriffssteuerung?][azure-rbac]

## <a name="roles-and-clusterroles"></a>Rollen und Clusterrollen

Bevor Sie Benutzern mit der rollenbasierten Zugriffssteuerung von Kubernetes Berechtigungen zuweisen, müssen Sie diese Berechtigungen als *Rolle* definieren. Kubernetes-Rollen *gewähren* Berechtigungen. In diesem Konzept ist eine Berechtigung des Typs *Verweigern* nicht vorgesehen.

Rollen werden zum Gewähren von Berechtigungen innerhalb eines Namespace verwendet. Wenn Sie Berechtigungen für den gesamten Cluster oder für Clusterressourcen außerhalb eines bestimmten Namespace erteilen müssen, können Sie stattdessen *Clusterrollen* verwenden.

Auch mit einer Clusterrolle werden Berechtigungen für Ressourcen erteilt. Allerdings kann sie auf Ressourcen im gesamten Cluster angewendet werden und ist nicht auf einen bestimmten Namespace beschränkt.

## <a name="rolebindings-and-clusterrolebindings"></a>Rollenbindungen und Clusterrollenbindungen

Sobald Rollen definiert sind, um Berechtigungen für Ressourcen zu gewähren, weisen Sie diese Berechtigungen der rollenbasierten Zugriffssteuerung von Kubernetes mit einer *Rollenbindung* zu. Wenn Azure Active Directory in Ihr AKS-Cluster integriert ist, wird mit Bindungen definiert, wie Azure AD-Benutzern Berechtigungen zum Ausführen von Aktionen innerhalb des Clusters erteilt werden.

Rollenbindungen werden zum Zuweisen von Rollen für einen bestimmten Namespace verwendet. Mit diesem Ansatz können Sie einen einzelnen AKS-Cluster logisch aufteilen und den Benutzern nur den Zugriff auf die Anwendungsressourcen im jeweils zugewiesenen Namespace erlauben. Wenn Sie Rollen über den gesamten Cluster oder an Clusterressourcen außerhalb eines bestimmten Namespace binden müssen, können Sie stattdessen *Clusterrollenbindungen* verwenden.

Eine Clusterrollenbindung funktioniert auf dieselbe Weise wie die Rollenbindung für Benutzer, allerdings kann sie auf Ressourcen im gesamten Cluster angewendet werden und ist nicht auf einen bestimmten Namespace beschränkt. Mit diesem Ansatz können Sie Ihren Administratoren oder Supporttechnikern Zugriff auf alle Ressourcen im AKS-Cluster gewähren.

## <a name="next-steps"></a>Nächste Schritte

Die ersten Schritte mit Azure AD und der rollenbasierten Zugriffssteuerung von Kubernetes sind unter [Integrieren von Azure Active Directory in AKS][aks-aad] beschrieben.

Zugehörige bewährte Methoden finden Sie unter [Best Practices für die Authentifizierung und Autorisierung in Azure Kubernetes Service (AKS)][operator-best-practices-identity].

Weitere Informationen zu den wesentlichen Konzepten von Kubernetes und AKS finden Sie in den folgenden Artikeln:

- [Kubernetes/AKS: Cluster und Workloads][aks-concepts-clusters-workloads]
- [Kubernetes/AKS: Sicherheit][aks-concepts-security]
- [Kubernetes/AKS: virtuelle Netzwerke][aks-concepts-network]
- [Kubernetes/AKS: Speicher][aks-concepts-storage]
- [Kubernetes/AKS: Skalierung][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v1-protocols-openid-connect-code.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: azure-ad-integration-cli.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md

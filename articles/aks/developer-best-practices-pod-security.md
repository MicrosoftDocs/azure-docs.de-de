---
title: Best Practices für Entwickler – Podsicherheit in Azure Kubernetes Service (AKS)
description: Hier finden Sie bewährte Methode für Entwickler zum Absichern von Pods in Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: zarhoads
ms.openlocfilehash: 1c7143b6d3479cf3083cfc730301c68dcf4eb705
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900827"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Best Practices für Podsicherheit in Azure Kubernetes Service (AKS)

Beim Entwickeln und Ausführen von Anwendungen in Azure Kubernetes Service (AKS) ist die Sicherheit Ihrer Pods ein wichtiger Aspekt. Ihre Anwendungen sollten auf das Prinzip der geringsten Anzahl von erforderlichen Berechtigungen ausgelegt sein. Die Sicherheit privater Daten ist für Kunden oberste Priorität. Sie möchten nicht zulassen, dass Anmeldeinformationen wie Datenbankverbindungszeichenfolgen, Schlüssel oder Geheimnisse und Zertifikate offengelegt werden, die Angreifer für bösartige Zwecke nutzen könnten. Fügen Sie sie nicht Ihrem Code hinzu, und betten Sie sie nicht in Containerimages ein. Dieser Ansatz würde ein Risiko der Offenlegung darstellen und die Möglichkeit einschränken, diese Anmeldeinformationen wechseln, wenn die Containerimages neu erstellt werden müssen.

Dieser Artikel mit bewährten Methoden konzentriert sich darauf, wie Sie Pods in AKS absichern. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Verwenden des Podsicherheitskontexts, um den Zugriff auf Prozesse und Dienste oder die Eskalation von Berechtigungen einzuschränken
> * Authentifizieren bei anderen Azure-Ressourcen unter Verwendung von verwalteten Podidentitäten
> * Anfordern und Abrufen von Anmeldeinformationen aus einem digitalen Tresor wie Azure Key Vault

Weitere Informationen finden Sie in den bewährten Methoden für [Clustersicherheit][best-practices-cluster-security] und [Verwaltung von Containerimages][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Absichern des Podzugriffs auf Ressourcen

**Best Practices-Anleitung** : Für die Ausführung als ein anderer Benutzer oder eine andere Gruppe und um den Zugriff auf die zugrunde liegenden Knotenprozesse und -dienste zu beschränken, definieren Sie Einstellungen für den Podsicherheitskontext. Weisen Sie die geringste Anzahl von erforderlichen Berechtigungen zu.

Damit Ihre Anwendungen korrekt ausgeführt werden, sollten Pods als definierter Benutzer oder definierte Gruppe und nicht als *root* ausgeführt werden. Mit dem `securityContext` für einen Pod oder Container können Sie Einstellungen wie *runAsUser* oder *fsGroup* definieren, um die entsprechenden Berechtigungen zu übernehmen. Vergeben Sie nur die erforderlichen Benutzer- oder Gruppenberechtigungen und verwenden Sie den Sicherheitskontext nicht als Mittel, um zusätzliche Berechtigungen zu erhalten. *RunAsUser* , die Berechtigungsausweitung und andere Linux-Funktionseinstellungen sind nur auf Linux-Knoten und -Pods verfügbar.

Wenn Sie als Nicht-Root-Benutzer ausgeführt werden, können sich Container nicht an die privilegierten Ports unter 1024 binden. In diesem Szenario können Kubernetes-Dienste verwendet werden, um die Tatsache zu verschleiern, dass eine App auf einem bestimmten Port läuft.

Ein Podsicherheitskontext kann auch zusätzliche Funktionen oder Berechtigungen für den Zugriff auf Prozesse und Dienste definieren. Die folgenden allgemeinen Sicherheitsskontextdefinitionen können festgelegt werden:

* **allowPrivilegeEscalation** definiert, ob der Pod *root* -Berechtigungen übernehmen kann. Gestalten Sie Ihre Anwendungen so, dass diese Einstellung immer auf *false* festgelegt ist.
* Mithilfe von **Linux-Funktionen** kann der Pod auf zugrunde liegende Knotenprozesse zugreifen. Gehen Sie mit der Zuweisung dieser Funktionen umsichtig um. Weisen Sie die geringste Anzahl von benötigten Berechtigungen zu. Weitere Informationen finden Sie unter [Linux-Funktionen][linux-capabilities].
* Mit dem Linux-Kernelsicherheitsmodul **SELinux-Bezeichnungen** können Sie Zugriffsrichtlinien für Dienste, Prozesse und den Dateisystemzugriff definieren. Auch hier gilt: Weisen Sie die geringste Anzahl von benötigten Berechtigungen zu. Weitere Informationen finden Sie unter [SELinux-Optionen in Kubernetes][selinux-labels].

Das folgende Beispielpod-YAML-Manifest legt die Sicherheitskontexteinstellungen folgendermaßen fest:

* Der Pod wird als Benutzer-ID *1000* und Teil der Gruppen-ID *2000* ausgeführt.
* Es können keine Berechtigungen für die Verwendung von `root` eskaliert werden.
* Erlaubt Linux-Funktionen den Zugriff auf Netzwerkschnittstellen und die Echtzeituhr (Hardwareuhr) des Hosts.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    fsGroup: 2000
  containers:
    - name: security-context-demo
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        runAsUser: 1000
        allowPrivilegeEscalation: false
        capabilities:
          add: ["NET_ADMIN", "SYS_TIME"]
```

Arbeiten Sie mit Ihrem Clusteroperator zusammen, um zu ermitteln, welche Sicherheitskontexteinstellungen Sie benötigen. Versuchen Sie, Ihre Anwendungen so zu gestalten, dass die vom Pod benötigten zusätzlichen Berechtigungen und Zugriffsrechte minimiert werden. Es gibt zusätzliche Sicherheitsfeatures, um den Zugriff mit AppArmor und seccomp (sicheres Computing) zu beschränken, die von Clusteroperatoren implementiert werden können. Weitere Informationen finden Sie unter [Sicherer Containerzugriff auf Ressourcen][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Beschränken der Offenlegung von Anmeldeinformationen

**Best Practices-Anleitung** : Definieren Sie keine Anmeldeinformationen in Ihrem Anwendungscode. Verwenden Sie verwaltete Identitäten für Azure-Ressourcen, damit Ihr Pod Zugriff auf andere Ressourcen anfordern kann. Außerdem sollte ein digitaler Tresor, wie z.B. Azure Key Vault, zum Speichern und Abrufen digitaler Schlüssel und Anmeldeinformationen verwendet werden. Pod-verwaltete Identitäten sind nur für die Verwendung mit Linux-Pods und -Containerimages vorgesehen.

Um das Risiko zu begrenzen, dass Anmeldeinformationen in Ihrem Anwendungscode offengelegt werden, vermeiden Sie die Verwendung von festen oder gemeinsamen Anmeldeinformationen. Anmeldeinformationen oder Schlüssel sollten nicht direkt in Ihrem Code enthalten sein. Wenn diese Anmeldeinformationen offengelegt werden, muss die Anwendung aktualisiert und neu bereitgestellt werden. Ein besserer Ansatz ist es, den Pods eine eigene Identität und eine Möglichkeit zu geben, sich selbst zu authentifizieren oder automatisch Anmeldeinformationen aus einem digitalen Tresor abzurufen.

### <a name="use-azure-container-compute-upstream-projects"></a>Verwenden von Azure Container Compute Upstream-Projekten

> [!IMPORTANT]
> Zugehörige AKS-Open-Source-Projekte werden vom technischen Support von Azure nicht unterstützt. Sie werden den Benutzern zur Verfügung gestellt, damit sie diese selbst in Clustern installieren und Feedback von unserer Community erhalten können.

Mit den folgenden [zugehörigen AKS-Open-Source-Projekten][aks-associated-projects] können Sie Pods automatisch authentifizieren oder Anmeldeinformationen und Schlüssel von einem digitalen Tresor anfordern. Diese Projekte werden vom Azure Container Compute Upstream-Team betreut und sind Teil einer [umfassenderen Liste von verfügbaren Projekten](https://github.com/Azure/container-compute-upstream/blob/master/README.md#support).

 * [Azure Active Directory-Podidentität][aad-pod-identity]
 * [Azure Key Vault-Anbieter für den Secrets Store CSI-Treiber](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage)

#### <a name="use-pod-managed-identities"></a>Verwenden verwalteter Podidentitäten

Eine verwaltete Identität für Azure-Ressourcen ermöglicht es einem Pod, sich bei jedem Azure-Dienst zu authentifizieren, der dies unterstützt, z. B. Storage oder SQL. Dem Pod wird eine Azure-Identität zugewiesen, mit der er sich bei Azure Active Directory authentifizieren und ein digitales Token abrufen kann. Dieses digitale Token kann anderen Azure-Diensten vorgelegt werden, die überprüfen, ob der Pod berechtigt ist, auf den Dienst zuzugreifen und die erforderlichen Aktionen auszuführen. Dieser Ansatz bedeutet z. B., dass keine Geheimnisse für Datenbankverbindungszeichenketten erforderlich sind. Der vereinfachte Workflow für eine verwaltete Podidentität ist im folgenden Diagramm dargestellt:

:::image type="content" source="media/developer-best-practices-pod-security/basic-pod-identity.svg" alt-text="Vereinfachter Workflow für verwaltete Podidentität in Azure":::

Bei einer verwalteten Identität muss Ihr Anwendungscode keine Anmeldeinformationen für den Zugriff auf einen Dienst, wie beispielsweise Azure Storage, enthalten. Da sich jeder Pod mit seiner eigenen Identität authentifiziert, können Sie den Zugriff überwachen und überprüfen. Wenn sich Ihre Anwendung mit anderen Azure-Diensten verbindet, verwenden Sie verwaltete Identitäten, um die Wiederverwendung von Berechtigungen und das Risiko einer Offenlegung zu begrenzen.

Weitere Informationen zu Podidentitäten finden Sie unter [Konfigurieren eines AKS-Clusters für die Verwendung von verwalteten Podidentitäten und mit Ihren Anwendungen][aad-pod-identity].

#### <a name="use-azure-key-vault-with-secrets-store-csi-driver"></a>Verwenden von Azure Key Vault mit dem Secrets Store CSI-Treiber

Die Verwendung des Identitätsprojekts für Pods ermöglicht die Authentifizierung bei unterstützenden Azure-Diensten. Bei Ihren eigenen Diensten oder Anwendungen ohne verwaltete Identitäten für Azure-Ressourcen können Sie sich weiterhin mit Anmeldeinformationen oder Schlüsseln authentifizieren. Ein digitaler Tresor kann verwendet werden, um diese Geheimnisinhalte zu speichern.

Wenn Anwendungen Anmeldeinformationen benötigen, kommunizieren sie mit dem digitalen Tresor, rufen die neuesten Geheimnisinhalte ab und stellen dann die Verbindung mit dem gewünschten Dienst her. Azure Key Vault kann dieser digitale Tresor sein. Der vereinfachte Workflow zum Abrufen von Anmeldeinformationen aus Azure Key Vault unter Verwendung von verwalteten Podidentitäten ist im folgenden Diagramm dargestellt:

:::image type="content" source="media/developer-best-practices-pod-security/basic-key-vault.svg" alt-text="Vereinfachter Workflow für verwaltete Podidentität in Azure":::

Mit Key Vault werden Geheimnisse wie Anmeldeinformationen, Speicherkontenschlüssel oder Zertifikate gespeichert und regelmäßig rotiert. Sie können Azure Key Vault mit einem AKS-Cluster unter Verwendung des [Azure Key Vault-Anbieters für den Secrets Store CSI-Treiber](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage) integrieren. Mit dem Secrets Store CSI-Treiber kann der AKS-Cluster nativ Geheimnisinhalte aus Azure Key Vault abrufen und diese ausschließlich dem anfordernden Pod sicher zur Verfügung stellen. Arbeiten Sie mit Ihrem Clusteroperator zusammen, um den Secrets Store CSI-Treiber auf AKS-Workerknoten bereitzustellen. Sie können eine vom Pod verwaltete Identität verwenden, um Zugriff auf Key Vault anzufordern und die erforderlichen Geheimnisinhalte über den Secrets Store CSI-Treiber abzurufen.

Azure Key Vault mit Secrets Store CSI-Treiber kann für Linux-Knoten und -Pods verwendet werden, die mindestens die Kubernetes-Version 1.16 benötigen. Für Windows-Knoten und -Pods ist mindestens die Kubernetes-Version 1.18 erforderlich.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde erläutert, wie Pods gesichert werden. Wenn Sie einige dieser Best Practices implementieren möchten, lesen Sie folgende Artikel:

* [Verwenden von verwalteten Identitäten für Azure-Ressourcen mit AKS][aad-pod-identity]
* [Integrieren von Azure Key Vault in AKS][aks-keyvault-csi-driver]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo
[aks-keyvault-csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#selinuxoptions-v1-core
[aks-associated-projects]: https://awesomeopensource.com/projects/aks?categoryPage=11

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources

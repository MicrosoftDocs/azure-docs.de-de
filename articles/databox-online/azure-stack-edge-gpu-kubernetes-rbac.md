---
title: Grundlegendes zur rollenbasierten Zugriffssteuerung von Kubernetes auf einem Azure Stack Edge Pro-Gerät | Microsoft-Dokumentation
description: Erfahren Sie, wie die rollenbasierte Zugriffssteuerung von Kubernetes auf einem Azure Stack Edge Pro-Gerät erfolgt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 9a9625dcf40ae7d11e1154fc89b7f04652c8ca16
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2020
ms.locfileid: "94635839"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-pro-gpu-device"></a>Rollenbasierte Zugriffssteuerung von Kubernetes auf einem Azure Stack Edge Pro-GPU-Gerät


Wenn Sie auf Ihrem Azure Stack Edge Pro-Gerät die Computerolle konfigurieren, wird ein Kubernetes-Cluster erstellt. Sie können die rollenbasierte Zugriffssteuerung von Kubernetes (Kubernetes RBAC) verwenden, um den Zugriff auf die Clusterressourcen auf Ihrem Gerät einzuschränken.

Dieser Artikel bietet eine Übersicht über das von Kubernetes bereitgestellte RBAC-System und erläutert, wie es auf Ihrem Azure Stack Edge Pro-Gerät implementiert wird. 

## <a name="kubernetes-rbac"></a>RBAC in Kubernetes

Mithilfe von RBAC in Kubernetes können Sie Benutzern oder Benutzergruppen die Berechtigung für bestimmte Aktionen (z. B. Ressourcen erstellen bzw. ändern oder Protokolle zur Workload ausgeführter Anwendungen anzeigen) zuweisen. Diese Berechtigungen können auf einen einzelnen Namespace begrenzt oder für den gesamten Cluster erteilt werden. 

Wenn Sie den Kubernetes-Cluster einrichten, wird ein einzelner Benutzer entsprechend diesem Cluster angelegt, der als Clusteradministratorbenutzer bezeichnet wird.  Dem Clusteradministratorbenutzer ist eine `kubeconfig`-Datei zugeordnet. Die `kubeconfig`-Datei ist eine Textdatei, die alle Konfigurationsinformationen enthält, die für die Verbindung mit dem Cluster zum Authentifizieren des Benutzers erforderlich sind.

## <a name="namespaces-types"></a>Typen von Namespaces

Kubernetes-Ressourcen wie Pods und Bereitstellungen werden logisch in einen Namespace gruppiert. Diese Gruppierungen ermöglichen es, einen Kubernetes-Cluster logisch zu unterteilen und den Zugriff zum Erstellen, Anzeigen oder Verwalten von Ressourcen einzuschränken. Benutzer können nur mit den Ressourcen innerhalb der ihnen zugewiesenen Namespaces interagieren.

Namespaces sind für den Einsatz in Umgebungen mit vielen Benutzern vorgesehen, die auf mehrere Teams oder Projekte verteilt sind. Weitere Informationen finden Sie unter [Kubernetes-Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).

Ihr Azure Stack Edge Pro-Gerät verfügt über die folgenden Namespaces:

- **Systemnamespace**: In diesem Namespace befinden sich grundlegende Ressourcen, beispielsweise Netzwerkfeatures wie DNS und Proxy oder das Kubernetes-Dashboard. In diesem Namespace stellen Sie in der Regel keine eigenen Anwendungen bereit. Verwenden Sie diesen Namespace zum Debuggen von Problemen mit dem Kubernetes-Cluster. 

    Es gibt mehrere Systemnamespaces auf Ihrem Gerät. Die diesen Systemnamespaces entsprechenden Namen sind reserviert. Es folgt eine Liste der reservierten Systemnamespaces: 
    - kube-system
    - metallb-system
    - dbe-namespace
    - default
    - kubernetes-dashboard
    - kube-node-lease
    - kube-public


    Stellen Sie sicher, dass Sie keine reservierten Namen für von Ihnen angelegte Benutzernamespaces verwenden. 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **Benutzernamespace**: Dies sind die Namespaces, die Sie über **kubectl** oder die PowerShell-Schnittstelle des Geräts erstellen können, um Anwendungen lokal bereitzustellen.
 
- **IoT Edge-Namespace**: Sie stellen eine Verbindung mit diesem `iotedge`-Namespace her, um die über IoT Edge bereitgestellten Anwendungen zu verwalten.

- **Azure Arc-Namespace**: Sie stellen eine Verbindung mit diesem `azure-arc`-Namespace her, um die über Azure Arc bereitgestellten Anwendungen zu verwalten. Mit Azure Arc können Sie auch Anwendungen in anderen Benutzernamespaces bereitstellen. 

## <a name="namespaces-and-users"></a>Namespaces und Benutzer

In der Praxis ist es wichtig, den Cluster in mehrere Namespaces aufzuteilen. 

- **Mehrere Benutzer**: Wenn Sie mehrere Benutzer haben, ermöglichen mehrere Namespaces diesen Benutzern, ihre Anwendungen und Dienste jeweils in ihren spezifischen Namespaces isoliert voneinander bereitzustellen. 
- **Einzelbenutzer**: Auch wenn es nur einen Benutzer gibt, würden mehrere Namespaces es ihm ermöglichen, mehrere Versionen der Anwendungen im selben Kubernetes-Cluster auszuführen.

### <a name="roles-and-rolebindings"></a>Rollen und Rollenbindungen

Kubernetes arbeitet mit dem Konzept von Rollen und Rollenbindung, mit dem Sie Benutzern oder Ressourcen Berechtigungen auf Namespace- und Clusterebene erteilen können. 

- **Rollen**: Sie können Berechtigungen für Benutzer als **Rolle** definieren und dann mithilfe von **Rollen** Berechtigungen innerhalb eines Namespaces erteilen. 
- **Rollenbindungen**: Nachdem Sie die Rollen definiert haben, können Sie mit **Rollenbindungen** Rollen für einen bestimmten Namespace zuweisen. 

Bei diesem Ansatz können Sie einen einzelnen Kubernetes-Cluster logisch aufteilen und den Benutzern nur Zugriff auf die Anwendungsressourcen im jeweils zugewiesenen Namespace erlauben. 

## <a name="kubernetes-rbac-on-azure-stack-edge-pro"></a>Kubernetes RBAC auf Azure Stack Edge Pro-Geräten

In der aktuellen Implementierung von Kubernetes RBAC können Sie mit Azure Stack Edge Pro in einem eingeschränkten PowerShell-Runspace die folgenden Aktionen ausführen:

- Erstellen von Namespaces.  
- Erstellen weiterer Benutzer.
- Gewähren von Administratorzugriff auf die von Ihnen erstellten Namespaces. Denken Sie daran, dass Sie keinen Zugriff auf die Rolle des Clusteradministrators oder eine Sicht auf die clusterweiten Ressourcen haben.
- Abrufen der `kubeconfig`-Datei mit Informationen für den Zugriff auf den Kubernetes-Cluster.


Das Azure Stack Edge Pro-Gerät verfügt über mehrere Systemnamespaces, und Sie können Benutzernamespaces mit `kubeconfig`-Dateien erstellen, um auf diese Namespaces zuzugreifen. Die Benutzer haben die volle Kontrolle über diese Namespaces und können Benutzer erstellen, ändern oder ihnen Zugriff gewähren. Nur der Clusteradministrator hat Vollzugriff auf Systemnamespaces und clusterweite Ressourcen. Ein `aseuser` hat schreibgeschützten Zugriff auf Systemnamespaces.

Nachfolgend sehen Sie ein Diagramm, das die Implementierung von Kubernetes RBAC auf einem Azure Stack Edge Pro-Gerät zeigt.

![Kubernetes RBAC auf einem Azure Stack Edge Pro-Gerät](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

In diesem Diagramm haben Alice, Bob und Chuck nur Zugriff auf zugewiesene Benutzernamespaces, in diesem Fall `ns1`, `ns2` und `ns3`. Innerhalb dieser Namespaces haben sie Administratorzugriff. Der Clusteradministrator hat hingegen Administratorzugriff auf Systemnamespaces und clusterweite Ressourcen.

Als Benutzer können Sie Namespaces und Benutzer erstellen, Benutzer zu Namespaces zuweisen oder `kubeconfig`-Dateien herunterladen. Detaillierte Anweisungen finden Sie unter [Zugreifen auf Kubernetes-Cluster über kubectl auf einem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-create-kubernetes-cluster.md).


Wenn Sie mit Namespaces und Benutzern auf Ihren Azure Stack Edge Pro-Geräten arbeiten, gelten die folgenden Vorbehalte:

- Sie dürfen in den Systemnamespaces keine Vorgänge wie Erstellen von Benutzern oder Gewähren oder Entziehen des Zugriffs auf Namespaces für Benutzer durchführen. Beispiele von Systemnamespaces sind `kube-system`, `metallb-system`, `kubernetes-dashboard`, `default`, `kube-node-lease`, `kube-public`. Zu den Systemnamespaces gehören auch die Namespaces, die für Bereitstellungstypen reserviert sind, wie z. B. `iotedge` (IoT-Edge-Namespace) und `azure-arc` (Azure Arc-Namespace).
- Sie können Benutzernamespaces anlegen und innerhalb dieser Namespaces zusätzliche Benutzer erstellen und diesen Benutzern Zugriff auf Namespaces gewähren oder entziehen.
- Sie dürfen keine Namespaces mit Namen einrichten, die mit denen für einen beliebigen Systemnamespace identisch sind. Die Namen für Systemnamespaces sind reserviert.  
- Sie dürfen keine Benutzernamespaces mit Namen anlegen, die bereits von anderen Benutzernamespaces verwendet werden. Wenn Sie z. B. einen `test-ns` haben, den Sie erstellt haben, können Sie keinen weiteren `test-ns`-Namespace erstellen.
- Sie dürfen keine Benutzer mit Namen anlegen, die bereits reserviert sind. `aseuser` ist z. B. ein reservierter Benutzer und kann nicht verwendet werden.


## <a name="next-steps"></a>Nächste Schritte

Informationen dazu, wie Sie einen Benutzer anlegen, einen Namespace erstellen und dem Benutzer Zugriff auf den Namespace gewähren können, finden Sie unter [Zugreifen auf einen Kubernetes-Cluster über kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).


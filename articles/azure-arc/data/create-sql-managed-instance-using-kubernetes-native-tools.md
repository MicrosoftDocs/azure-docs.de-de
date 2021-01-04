---
title: Erstellen einer verwalteten SQL-Instanz mithilfe von Kubernetes-Tools
description: Erstellen einer verwalteten SQL-Instanz mithilfe von Kubernetes-Tools
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2902274cfff11ac256459abd4fe0378146ee067b
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280182"
---
# <a name="create-azure-sql-managed-instance-using-kubernetes-tools"></a>Erstellen einer verwalteten Azure SQL-Instanz mithilfe von Kubernetes-Tools

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Voraussetzungen

Einen [Azure Arc-Datencontroller](./create-data-controller.md) sollten Sie bereits erstellt haben.

Zum Erstellen einer verwalteten SQL-Instanz mithilfe von Kubernetes-Tools müssen die Kubernetes-Tools installiert sein.  In den Beispielen in diesem Artikel wird `kubectl` verwendet, allerdings können auch ähnliche Ansätze mit anderen Kubernetes-Tools verfolgt werden, z. B. dem Kubernetes-Dashboard, `oc` oder `helm`, wenn Sie mit diesen Tools und Kubernetes-YAML/JSON-Dateien vertraut sind.

[Installieren des kubectl-Tools](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>Übersicht

Zum Erstellen einer verwalteten SQL-Instanz müssen Sie ein Kubernetes-Geheimnis erstellen, um den Anmeldenamen und das Kennwort Ihres Systemadministrators sicher zu speichern, sowie eine benutzerdefinierte Ressource einer verwalteten SQL-Instanz auf Grundlage der Definition der benutzerdefinierten Ressource „sqlmanagedinstance“.

## <a name="create-a-yaml-file"></a>Erstellen einer YAML-Datei

Sie können die [YAML-Vorlage](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/sqlmi.yaml)datei als Ausgangspunkt verwenden, um Ihre eigene benutzerdefinierte YAML-Datei für die verwaltete SQL-Instanz zu erstellen.  Laden Sie diese Datei auf Ihren Computer herunter, und öffnen Sie sie in einem Text-Editor.  Es ist hilfreich, einen Text-Editor wie [VS Code](https://code.visualstudio.com/download) zu verwenden, der Syntaxhervorhebung und Linten für YAML-Dateien unterstützen.

Dies ist eine YAML-Beispieldatei:

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
  username: <your base64 encoded user name. 'sa' is not allowed>
kind: Secret
metadata:
  name: example-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
  name: example
spec:
  limits:
    memory: 4Gi
    vcores: "4"
  requests:
    memory: 2Gi
    vcores: "1"
  service:
    type: LoadBalancer
  storage:
    backups:
      className: default
      size: 5Gi
    data:
      className: default
      size: 5Gi
    datalogs:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>Anpassen des Anmeldenamens und des Kennworts

Ein Kubernetes-Geheimnis wird als Base64-codierte Zeichenfolge gespeichert – eine für den Benutzernamen und eine für das Kennwort.  Sie müssen den Anmeldenamen und das Kennwort eines Systemadministrators base64-codieren und sie am Platzhalterort unter `data.password` und `data.username` ablegen.  Nehmen Sie nicht die Symbole `<` und `>` mit auf, die in der Vorlage vorhanden sind.

> [!NOTE]
> Um optimale Sicherheit zu garantieren, ist die Verwendung des Werts „sa“ als Anmeldename nicht zulässig.
> Befolgen Sie die [Kennwortkomplexitätsrichtlinie](/sql/relational-databases/security/password-policy#password-complexity).

Sie können ein Onlinetool verwenden, um den gewünschten Benutzernamen und das zugehörige Kennwort mit einer Base64-Codierung zu versehen, oder Sie können abhängig von Ihrer Plattform integrierte CLI-Tools verwenden.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('example'))

```

Linux/macOS

```console
echo '<your string to encode here>' | base64

#Example
# echo 'example' | base64
```

### <a name="customizing-the-name"></a>Anpassen des Namens

Die Vorlage hat für das Attribut „Name“ den Wert „example“ (Beispiel).  Sie können diesen ändern, hierbei muss es sich aber um Zeichen handeln, die die DNS-Benennungsstandards einhalten.  Sie müssen außerdem den Namen des Geheimnisses entsprechend ändern.  Wenn Sie z. B. den Namen der verwalteten SQL-Instanz in „sql1“ ändern, müssen Sie den Namen des Geheimnisses von „example-login-secret“ in „sql1-login-secret“ ändern.

### <a name="customizing-the-resource-requirements"></a>Anpassen der Ressourcenanforderungen

Sie können die Ressourcenanforderungen (Limits und Anforderungen für RAM und Kerne) je nach Bedarf ändern.  

> [!NOTE]
> Weitere Informationen zur [Kubernetes-Ressourcengovernance](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

Anforderungen für Ressourcenlimits und -anforderungen:
- Der Grenzwert für die Anzahl von Kernen ist aus Abrechnungsgründen **obligatorisch**.
- Der Rest der Ressourcenanforderungen und -limits ist optional.
- Das Limit und die Anforderung für Kerne müssen ein positiver ganzzahliger Wert sein, falls angegeben.
- Für die Kerneanforderung sind mindestens 2 Kerne erforderlich, falls angegeben.
- Das Format des Arbeitsspeicherwerts folgt der Kubernetes-Notation.  
- Für die Arbeitsspeicheranforderung sind mindestens 2 GB erforderlich, falls angegeben.
- Als generelle Richtlinie sollten Sie 4 GB RAM für jeden Kern bei Produktionsanwendungsfällen haben.

### <a name="customizing-service-type"></a>Anpassen des Diensttyps

Der Diensttyp kann bei Bedarf in „NodePort“ geändert werden.  Es wird eine zufällige Portnummer zugewiesen.

### <a name="customizing-storage"></a>Anpassen von Speicher

Sie können die Speicherklassen für Speicher so anpassen, dass sie Ihrer Umgebung entsprechen.  Wenn Sie nicht sicher sind, welche Speicherklassen verfügbar sind, können Sie den Befehl `kubectl get storageclass` ausführen, um sie anzuzeigen.  Die Vorlage enthält den Standardwert „default“ (Standard).  Dies bedeutet, dass es eine Speicherklasse _namens_ „default“ gibt, nicht dass es eine Speicherklasse gibt, die der Standard _ist_.  Sie können die Größe Ihres Speichers auch optional ändern.  Weitere Informationen zur [Speicherkonfiguration](./storage-configuration.md).

## <a name="creating-the-sql-managed-instance"></a>Erstellen der verwalteten SQL-Instanz

Nachdem Sie nun die YAML-Datei der verwalteten SQL-Instanz angepasst haben, können Sie die verwaltete SQL-Instanz erstellen, indem Sie den folgenden Befehl ausführen:

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\sqlmi.yaml
```


## <a name="monitoring-the-creation-status"></a>Überwachen des Erstellungsstatus

Das Erstellen der verwalteten SQL-Instanz dauert einige Minuten. Mithilfe der folgenden Befehle können Sie den Status in einem anderen Terminalfenster überwachen:

> [!NOTE]
>  Bei den folgenden Beispielbefehlen wird davon ausgegangen, dass Sie eine verwaltete SQL-Instanz namens „sql1“ sowie einen Kubernetes-Namespace namens „arc“ erstellt haben.  Wenn Sie andere Namen für den Namespace/die verwaltete SQL-Instanz verwendet haben, können Sie „arc“ und „sqlmi“ durch Ihre Namen ersetzen.

```console
kubectl get sqlmi/sql1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

Sie können auch den Erstellungsstatus eines bestimmten Pods überprüfen, indem Sie einen Befehl wie den folgenden ausführen.  Dies ist besonders bei der Problembehandlung hilfreich.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/sql1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Beheben von Problemen bei der Erstellung

Wenn Probleme bei der Erstellung auftreten, finden Sie weitere Informationen im [Handbuch zur Problembehandlung](troubleshoot-guide.md).

## <a name="next-steps"></a>Nächste Schritte

[Herstellen einer Verbindung zu Azure Arc-fähigen SQL Managed Instance-Instanzen](connect-managed-instance.md)

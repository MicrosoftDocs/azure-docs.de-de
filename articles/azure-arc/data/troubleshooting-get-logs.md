---
title: Abrufen von Protokollen für die Problembehandlung Azure Arc-fähiger Datendienste
description: Erfahren Sie, wie Sie Protokolldateien von einem Datencontroller für die Problembehandlung Azure Arc-fähiger Datendienste abrufen.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0c4cff7583f08fe27649cee464fcef802cddd88f
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234041"
---
# <a name="get-logs-to-troubleshoot-azure-arc-enabled-data-services"></a>Abrufen von Protokollen für die Problembehandlung Azure Arc-fähiger Datendienste

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie fortfahren:

* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]. Weitere Informationen finden Sie unter [Installieren von Clienttools zum Bereitstellen und Verwalten von Azure Arc-fähigen Datendiensten](./install-client-tools.md).
* Administratorkonto zum Anmelden beim Azure Arc-fähigen Datencontroller.

## <a name="get-log-files"></a>Abrufen von Protokolldateien

Zur Problembehandlung können Sie die Protokolle zu allen oder nur zu bestimmten Pods abrufen. Eine Möglichkeit besteht in der Verwendung von Kubernetes-Standardtools wie dem Befehl `kubectl logs`. In diesem Artikel verwenden Sie das [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]-Tool, das das gleichzeitige Abrufen aller Protokolle erleichtert.

1. Melden Sie sich mit einem Administratorkonto beim Datencontroller an.

   ```console
   azdata login
   ```

2. Führen Sie den folgenden Befehl aus, um die Protokolle zu sichern:

   ```console
   azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress
   ```

   Zum Beispiel:

   ```console
   #azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
   ```

Der Datencontroller erstellt die Protokolldateien im aktuellen Arbeitsverzeichnis in ein Unterverzeichnis namens `logs`. 

## <a name="options"></a>Optionen

Der Befehl `azdata arc dc debug copy-logs` bietet die folgenden Optionen zum Verwalten der Ausgabe:

* Mithilfe des Parameters `--target-folder` geben Sie die Protokolldateien in ein anderes Verzeichnis aus.
* Sie komprimieren die Dateien, indem Sie den Parameter `--skip-compress` weglassen.
* Sie lösen Speicherabbilder aus und schließen diese ein, indem Sie `--exclude-dumps` weglassen. Diese Methode wird nur empfohlen, wenn der Microsoft-Support die Speicherabbilder angefordert hat. Zum Abrufen eines Speicherabbilds muss die Einstellung `allowDumps` des Datencontrollers zum Zeitpunkt der Erstellung des Datencontrollers auf `true` festgelegt sein.
* Durch Filtern nach Namen erfassen Sie nur Protokolle für einen bestimmten Pod (`--pod`) oder Container (`--container`).
* Durch Filtern erfassen Sie Protokolle für eine bestimmte benutzerdefinierte Ressource, indem Sie die Parameter `--resource-kind` und `--resource-name` übergeben. Der Parameterwert `resource-kind` muss einem der Namen der benutzerdefinierten Ressourcendefinitionen entsprechen. Sie können diese Namen mit dem Befehl `kubectl get customresourcedefinition` abrufen.

Mit diesen Parametern können Sie die `<parameters>` im folgenden Beispiel ersetzen: 

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>
```

Beispiel:

```console
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

Die folgende Ordnerhierarchie ist ein Beispiel. Sie ist nach Podnamen, dann nach Containern und dann nach der Verzeichnishierarchie innerhalb des Containers organisiert.

```output
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```

## <a name="next-steps"></a>Nächste Schritte

[azdata arc dc debug copy-logs](/sql/azdata/reference/reference-azdata-arc-dc-debug#azdata-arc-dc-debug-copy-logs?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json)

---
title: Erstellen einer Wiederherstellung einer Azure Red Hat OpenShift 4-Clusteranwendung mithilfe von Velero
description: Hier erfahren Sie, wie Sie mithilfe von Velero eine Wiederherstellung Ihrer Azure Red Hat OpenShift-Clusteranwendungen erstellen.
ms.service: container-service
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc
ms.openlocfilehash: a4b22036612aac3d2b67e299881ba11b896979e5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492402"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-restore"></a>Erstellen einer Wiederherstellung einer Azure Red Hat OpenShift 4-Clusteranwendung

In diesem Artikel erfahren Sie, wie Sie Ihre Umgebung für das Erstellen einer Wiederherstellung einer Azure Red Hat OpenShift 4-Clusteranwendung vorbereiten. Sie lernen Folgendes:

> [!div class="checklist"]
> * Einrichten der erforderlichen Komponenten und Installieren der erforderlichen Tools
> * Erstellen einer Azure Red Hat OpenShift 4-Anwendungswiederherstellung

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens Version 2.6.0 der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Voraussetzungen

### <a name="create-an-azure-red-hat-openshift-4-application-backup"></a>Erstellen einer Azure Red Hat OpenShift 4-Anwendungssicherung

Informationen zum Erstellen einer Azure Red Hat OpenShift 4-Anwendungssicherung finden Sie unter [Erstellen einer Sicherung einer Azure Red Hat OpenShift 4-Clusteranwendung](./howto-create-a-backup.md).

## <a name="restore-an-azure-red-hat-openshift-4-application"></a>Wiederherstellen einer Azure Red Hat OpenShift 4-Anwendung

Mit diesen Schritten können Sie eine Anwendung wiederherstellen, die zuvor mit Velero gesichert wurde.
Sie können die Liste der Sicherungen, die zurzeit vom Cluster erkannt werden, überprüfen, um die für die Wiederherstellung verfügbaren Sicherungen zu ermitteln.  Für diesen Schritt müssen Sie den folgenden Befehl ausführen:

_(Bei diesem Schritt wird davon ausgegangen, dass Sie Velero in einem Projekt namens „velero“ installiert haben.)_

```bash
oc get backups -n velero
```

Sobald Sie über die Sicherung verfügen, die Sie wiederherstellen möchten, müssen Sie die Wiederherstellung mit dem folgenden Befehl ausführen:

```bash
velero restore create <name of restore> --from-backup <name of backup from above output list>
```

In diesem Schritt werden die Kubernetes-Objekte erstellt, die zuvor im Rahmen der Sicherungserstellung gesichert wurden.

Führen Sie den folgenden Schritt aus, um den Status der Wiederherstellung anzuzeigen:

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
Wenn die Phase `Completed` lautet, müsste Ihre Azure Red Hat 4-Anwendung wiederhergestellt worden sein.

## <a name="restore-an-azure-red-hat-openshift-4-application-with-included-snapshots"></a>Wiederherstellen einer Azure Red Hat OpenShift 4-Anwendung mit enthaltenen Momentaufnahmen


Wenn Sie eine Wiederherstellung einer Azure Red Hat OpenShift 4-Anwendung mit persistenten Volumes mithilfe von Velero erstellen möchten, müssen Sie die Wiederherstellung mit dem folgenden Befehl ausführen:

```bash
velero restore create <name of the restore> --from-backup <name of backup from above output list> --exclude-resources="nodes,events,events.events.k8s.io,backups.ark.heptio.com,backups.velero.io,restores.ark.heptio.com,restores.velero.io"
```
In diesem Schritt werden die Kubernetes-Objekte erstellt, die zuvor im Rahmen der Sicherungserstellung gesichert wurden.

Führen Sie den folgenden Schritt aus, um den Status der Wiederherstellung anzuzeigen:

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
Wenn die Phase `Completed` lautet, müsste Ihre Azure Red Hat 4-Anwendung wiederhergestellt worden sein.

Weitere Informationen zum Erstellen von Sicherungen und Wiederherstellungen mithilfe von Velero finden Sie in dem Artikel zum [Sichern von OpenShift-Ressourcen auf native Weise](https://www.openshift.com/blog/backup-openshift-resources-the-native-way).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde eine Azure Red Hat OpenShift 4-Clusteranwendung wiederhergestellt. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Erstellen einer Wiederherstellung einer OpenShift v4-Clusteranwendung mithilfe von Velero
> * Erstellen einer Wiederherstellung einer OpenShift v4-Clusteranwendung mit Momentaufnahmen mithilfe von Velero


Im nächsten Artikel erfahren Sie mehr über die von Azure Red Hat OpenShift 4 unterstützten Ressourcen:

* [Azure Red Hat OpenShift-Ressourcen](supported-resources.md)
---
title: Hinzufügen oder Aktualisieren Ihres Red Hat-Pullgeheimnisses für einen Azure Red Hat OpenShift 4-Cluster
description: Hinzufügen oder Aktualisieren Ihres Red Hat-Pullgeheimnisses für vorhandene 4.x-ARO-Cluster
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 05/21/2020
keywords: Pullgeheimnis, ARO, OpenShift, Red Hat
ms.openlocfilehash: b1a8e45a2eab1b7b4c8a5936049f404d5bd3441c
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071085"
---
# <a name="add-or-update-your-red-hat-pull-secret-on-an-azure-red-hat-openshift-4-cluster"></a>Hinzufügen oder Aktualisieren Ihres Red Hat-Pullgeheimnisses für einen Azure Red Hat OpenShift 4-Cluster

In diesem Leitfaden wird beschrieben, wie Sie Ihr Red Hat-Pullgeheimnis für einen vorhandenen Azure Red Hat OpenShift (ARO) 4.x-Cluster hinzufügen oder aktualisieren.

Beim erstmaligen Erstellen eines Clusters können Sie Ihr Pullgeheimnis hinzufügen. Weitere Informationen zum Erstellen eines ARO-Clusters mit einem Red Hat-Pullgeheimnis finden Sie unter [Erstellen eines Azure Red Hat OpenShift 4-Clusters](tutorial-create-cluster.md#get-a-red-hat-pull-secret-optional).

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Leitfaden wird davon ausgegangen, dass Sie über einen Azure Red Hat OpenShift 4-Cluster verfügen. Vergewissern Sie sich, dass Sie Administratorzugriff auf den Cluster haben.

## <a name="prepare-your-pull-secret"></a>Vorbereiten des Pullgeheimnisses
Wenn Sie einen ARO-Cluster erstellen, ohne ein Red Hat-Pullgeheimnis hinzuzufügen, wird trotzdem automatisch ein Pullgeheimnis für Ihren Cluster erstellt. Dieses Pullgeheimnis ist jedoch nicht vollständig aufgefüllt.

In diesem Abschnitt wird Schritt für Schritt erläutert, wie Sie dieses Pullgeheimnis mit zusätzlichen Werten aus Ihrem Red Hat-Pullgeheimnis aktualisieren.

1. Rufen Sie das Geheimnis mit dem Namen `pull-secret` im Namespace `openshift-config` ab, und speichern Sie es in einer separaten Datei, indem Sie den folgenden Befehl ausführen: 

    ```console
    oc get secrets pull-secret -n openshift-config -o template='{{index .data ".dockerconfigjson"}}' | base64 -d > pull-secret.json
    ```

    Die Ausgabe sollte in etwa wie folgt aussehen. (Beachten Sie, dass der tatsächliche geheime Wert entfernt wurde.)

    ```json
    {
        "auths": {
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

2. Navigieren Sie zum [Manager-Portal für Ihren Red Hat OpenShift-Cluster](https://cloud.redhat.com/openshift/install/azure/aro-provisioned), und wählen Sie **Download pull secret** (Pullgeheimnis herunterladen) aus. Ihr Red Hat-Pullgeheimnis sieht wie folgt aus. (Beachten Sie, dass die tatsächlichen geheimen Werte entfernt wurde.)

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            }
        }
    }
    ```

3. Bearbeiten Sie die Pullgeheimnisdatei, die Sie aus Ihrem Cluster abgerufen haben, indem Sie die Einträge aus Ihrem Red Hat-Pullgeheimnis hinzufügen. 

    > [!IMPORTANT]
    > Wenn Sie den Eintrag `cloud.openshift.com` aus Ihrem Red Hat-Pullgeheimnis hinzufügen, beginnt Ihr Cluster, Telemetriedaten an Red Hat zu senden. Schließen Sie diesen Abschnitt nur ein, wenn Sie Telemetriedaten senden möchten. Andernfalls lassen Sie den folgenden Abschnitt aus.    
    > ```json
    > {
    >         "cloud.openshift.com": {
    >             "auth": "<my-crc-secret>",
    >             "email": "klamenzo@redhat.com"
    >         }
    > ```

    > [!CAUTION]
    > Entfernen oder ändern Sie nicht den Eintrag `arosvc.azurecr.io` in Ihrem Pullgeheimnis. Dieser Abschnitt ist für die korrekte Funktion Ihres Clusters erforderlich.

    ```json
    "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
    ```

    Ihre endgültige Datei sollte folgendermaßen aussehen. (Beachten Sie, dass die tatsächlichen geheimen Werte entfernt wurde.)

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            },
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

4. Stellen Sie sicher, dass Ihre JSON-Datei gültig ist. Es gibt viele Möglichkeiten, die JSON-Datei zu überprüfen. Im folgenden Beispiel wird jq verwendet:

    ```json
    cat pull-secret.json | jq
    ```

    > [!NOTE]
    > Wenn ein Fehler in der Datei vorliegt, wird `parse error` angezeigt.

## <a name="add-your-pull-secret-to-your-cluster"></a>Hinzufügen Ihres Pullgeheimnisses zum Cluster

Führen Sie den folgenden Befehl aus, um Ihr Pullgeheimnis zu aktualisieren.

> [!NOTE]
> Durch die Ausführung dieses Befehls werden Ihre Clusterknoten aktualisiert und nacheinander neu gestartet. 

```console
oc set data secret/pull-secret -n openshift-config --from-file=.dockerconfigjson=./pull-secret.json
```

Nachdem das Geheimnis festgelegt wurde, können Sie Red Hat-zertifizierte Operatoren aktivieren.

### <a name="modify-the-configuration-files"></a>Ändern der Konfigurationsdateien

Ändern Sie die folgenden Objekte, um Red Hat-Operatoren zu aktivieren.

Ändern Sie zuerst die Samples Operator-Konfigurationsdatei. Anschließend können Sie den folgenden Befehl ausführen, um die Konfigurationsdatei zu bearbeiten:

```
oc edit configs.samples.operator.openshift.io/cluster -o yaml
```

Ändern Sie die Werte für `spec.architectures.managementState` und `status.architecture.managementState` von `Removed` in `Managed`. 

Der folgende YAML-Codeausschnitt zeigt nur die relevanten Abschnitte der bearbeiteten YAML-Datei:

```yaml
apiVersion: samples.operator.openshift.io/v1
kind: Config
metadata:
  
  ...
  
spec:
  architectures:
  - x86_64
  managementState: Managed
status:
  architectures:

  ...

  managementState: Managed
  version: 4.3.27
```

Führen Sie als Nächstes den folgenden Befehl aus, um die OperatorHub-Konfigurationsdatei zu bearbeiten:  

```console
oc edit operatorhub cluster -o yaml
```

Ändern Sie die Werte für `Spec.Sources.Disabled` und `Status.Sources.Disabled` für alle Quellen, die Sie aktivieren möchten, von `true` in `false`.

Der folgende YAML-Codeausschnitt zeigt nur die relevanten Abschnitte der bearbeiteten YAML-Datei:

```yaml
Name:         cluster

...
                 dd3310b9-e520-4a85-98e5-8b4779ee0f61
Spec:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Disabled:  false
    Name:      redhat-operators
Status:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Status:    Success
    Disabled:  false
    Name:      community-operators
    Status:    Success
    Disabled:  false
    Name:      redhat-operators
    Status:    Success
Events:        <none>
```

Speichern Sie die Datei, um Ihre Änderungen anzuwenden.

## <a name="validate-that-your-secret-is-working"></a>Überprüfen, ob Ihr Geheimnis funktioniert

Nachdem Sie Ihr Pullgeheimnis hinzugefügt und die richtigen Konfigurationsdateien geändert haben, kann es einige Minuten dauern, bis Ihr Cluster aktualisiert wird. Um zu überprüfen, ob der Cluster aktualisiert wurde, zeigen Sie mit dem folgenden Befehl die verfügbaren Quellen für zertifizierte Operatoren und Red Hat-Operatoren an:

```console
$ oc get catalogsource -A
NAMESPACE               NAME                  DISPLAY               TYPE   PUBLISHER   AGE
openshift-marketplace   certified-operators   Certified Operators   grpc   Red Hat     10s
openshift-marketplace   community-operators   Community Operators   grpc   Red Hat     18h
openshift-marketplace   redhat-operators      Red Hat Operators     grpc   Red Hat     11s
```

Wenn die zertifizierten Operatoren und Red Hat-Operatoren nicht angezeigt werden, warten Sie einige Minuten, und versuchen Sie es dann nochmal.

Um sicherzustellen, dass Ihr Pullgeheimnis aktualisiert wurde und korrekt funktioniert, öffnen Sie OperatorHub, und überprüfen Sie, ob von Red Hat verifizierte Operatoren angezeigt werden. Überprüfen Sie beispielsweise, ob der Operator „OpenShift Container Storage“ verfügbar ist und Sie berechtigt sind, ihn zu installieren.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Red Hat-Pullgeheimnissen finden Sie unter [Verwenden von Imagepullgeheimnissen](https://docs.openshift.com/container-platform/4.6/openshift_images/managing_images/using-image-pull-secrets.html).

Weitere Informationen zu Red Hat OpenShift 4 finden Sie in der [Dokumentation zu Red Hat OpenShift Container Platform](https://docs.openshift.com/container-platform/4.6/welcome/index.html).

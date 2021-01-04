---
title: Zugriffsschlüssel für Speicherkonten ändern
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Zugriffsschlüssel für das von Ihrem Arbeitsbereich verwendete Azure Storage-Konto ändern können. Azure Machine Learning verwendet ein Azure Storage-Konto, um Daten und Modelle zu speichern.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/19/2020
ms.openlocfilehash: 78829ae52d74cf6ec58c12779c51bca9a98e0af1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450798"
---
# <a name="regenerate-storage-account-access-keys"></a>Neugenerieren der Zugriffsschlüssel für Speicherkonten


Erfahren Sie, wie Sie die Zugriffsschlüssel für Azure Storage-Konten ändern, die von Azure Machine Learning verwendet werden. Azure Machine Learning kann Speicherkonten verwenden, um Daten oder trainierte Modelle zu speichern.

Aus Sicherheitsgründen müssen Sie möglicherweise die Zugriffsschlüssel für ein Azure Storage-Konto ändern. Wenn Sie den Zugriffsschlüssel neu generieren, muss Azure Machine Learning aktualisiert werden, um den neuen Schlüssel zu verwenden. Azure Machine Learning kann das Speicherkonto sowohl für die Modellspeicherung als auch als Datenspeicher verwenden.

> [!IMPORTANT]

> Anmeldeinformationen, die bei Datenspeichern registriert sind, werden in Ihrer Azure Key Vault-Instanz gespeichert, die dem Arbeitsbereich zugeordnet ist. Wenn Sie das [vorläufige Löschen](../key-vault/general/soft-delete-overview.md) für Ihren Schlüsseltresor aktiviert haben, finden Sie in diesem Artikel Informationen zum Aktualisieren von Anmeldeinformationen. Wenn Sie die Registrierung des Datenspeichers aufheben und versuchen, ihn unter demselben Namen erneut zu registrieren, tritt bei dieser Aktion ein Fehler auf. Informationen zum Aktivieren des vorläufigen Löschens in diesem Szenario finden Sie unter [Aktivieren des vorläufigen Löschens für einen vorhandenen Schlüsseltresor]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie im Artikel [Erstellen eines Arbeitsbereichs](how-to-manage-workspace.md).

* Das [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).

* Die [CLI-Erweiterung für Azure Machine Learning](reference-azure-machine-learning-cli.md).

> [!NOTE]
> Die Codeausschnitte in diesem Dokument wurden mit Version 1.0.83 des Python SDK getestet.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Was aktualisiert werden muss

Speicherkonten können vom Azure Machine Learning-Arbeitsbereich (Speichern von Protokollen, Modellen, Snapshots usw.) und als Datenspeicher verwendet werden. Der Prozess zur Aktualisierung des Arbeitsbereichs ist ein einzelner Azure CLI-Befehl und kann nach der Aktualisierung des Speicherschlüssels ausgeführt werden. Der Prozess der Aktualisierung von Datenspeichern ist aufwändiger und erfordert es, herauszufinden, welche Datenspeicher derzeit das Speicherkonto verwenden, und sie dann neu zu registrieren.

> [!IMPORTANT]
> Aktualisieren Sie den Arbeitsbereich mit der Azure CLI und die Datenspeicher mit Python gleichzeitig. Die Aktualisierung nur des einen oder anderen ist nicht ausreichend und kann zu Fehlern führen, bis beide aktualisiert werden.

Um die Speicherkonten zu ermitteln, die von Ihren Datenspeichern verwendet werden, verwenden Sie den folgenden Code:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Dieser Code sucht nach allen registrierten Datenspeichern, die Azure Storage verwenden, und listet die folgenden Informationen auf:

* Datenspeichername: Der Name des Datenspeichers, unter dem das Speicherkonto registriert ist.
* Azure Storage-Kontoname: Der Name des Azure Storage-Kontos.
* Container: Der Container im Speicherkonto, der für diese Registrierung verwendet wird.

Außerdem wird angegeben, ob der Datenspeicher für ein Azure-Blob oder eine Azure-Dateifreigabe gedacht ist, da verschiedene Methoden zum erneuten Registrieren der einzelnen Datenspeichertypen vorhanden sind.

Wenn ein Eintrag für das Speicherkonto existiert, für das Sie eine Neugenerierung der Zugriffsschlüssel planen, speichern Sie den Namen des Datenspeichers, den Namen des Speicherkonto-Namens und den Containernamen.

## <a name="update-the-access-key"></a>Aktualisieren des Zugriffsschlüssels

Um Azure Machine Learning so zu aktualisieren, dass der neue Schlüssel verwendet wird, gehen Sie wie folgt vor:

> [!IMPORTANT]
> Führen Sie alle Schritte aus, indem Sie sowohl den Arbeitsbereich über die CLI als auch die Datenspeicher über Python aktualisieren. Die Aktualisierung nur des einen oder anderen kann zu Fehlern führen, bis beide aktualisiert werden.

1. Generieren Sie den Schlüssel neu. Informationen zur Neugenerierung eines Zugriffsschlüssels finden Sie im Artikel [Verwaltung von Speicherkonto-Zugriffsschlüsseln](../storage/common/storage-account-keys-manage.md). Speichern Sie den neuen Schlüssel.

1. Im Azure Machine Learning-Arbeitsbereich wird der neue Schlüssel automatisch synchronisiert und nach einer Stunde verwendet. Wenn Sie erzwingen möchten, dass der Arbeitsbereich sofort mit dem neuen Schlüssel synchronisiert wird, führen Sie die folgenden Schritte aus:

    1. Um sich bei dem Azure-Abonnement anzumelden, das Ihren Arbeitsbereich enthält, verwenden Sie den folgenden Azure CLI-Befehl:

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. Um den Arbeitsbereich so zu aktualisieren, dass der neue Schlüssel verwendet wird, verwenden Sie den folgenden Befehl. Ersetzen Sie `myworkspace` durch den Namen Ihres Azure Machine Learning-Arbeitsbereichs, und ersetzen Sie `myresourcegroup` durch den Namen der Azure-Ressourcengruppe, die den Arbeitsbereich enthält.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        Dieser Befehl synchronisiert automatisch die neuen Schlüssel für das vom Arbeitsbereich verwendete Azure-Speicherkonto.

1. Sie können Datenspeicher erneut registrieren, die das Speicherkonto über das SDK oder [Azure Machine Learning Studio](https://ml.azure.com) verwenden.
    1. Verwenden Sie die Werte aus dem Abschnitt [Was aktualisiert werden muss](#whattoupdate) und den Schlüssel aus Schritt 1 mit dem folgenden Code, **um Datenspeicher über das Python SDK erneut zu registrieren**. 
    
        Da `overwrite=True` angegeben ist, überschreibt dieser Code die bestehende Registrierung und aktualisiert sie, um den neuen Schlüssel zu verwenden.
    
        ```python
        # Re-register the blob container
        ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                                  datastore_name='your datastore name',
                                                  container_name='your container name',
                                                  account_name='your storage account name',
                                                  account_key='new storage account key',
                                                  overwrite=True)
        # Re-register file shares
        ds_file = Datastore.register_azure_file_share(workspace=ws,
                                              datastore_name='your datastore name',
                                              file_share_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
        
        ```
    
    1. Klicken Sie im linken Bereich von Studio auf **Datenspeicher**, **um Datenspeicher über Studio erneut zu registrieren**. 
        1. Wählen Sie den Datenspeicher aus, den Sie aktualisieren möchten.
        1. Klicken Sie oben links auf die Schaltfläche **Update credentials** (Anmeldeinformationen aktualisieren). 
        1. Verwenden Sie den neuen Zugriffsschlüssel aus Schritt 1, um das Formular auszufüllen, und klicken Sie auf **Speichern**.
        
            Wenn Sie Anmeldeinformationen für Ihren **Standarddatenspeicher** aktualisieren, führen Sie diesen Schritt aus, und wiederholen Sie Schritt 2b, um den neuen Schlüssel mit dem Standarddatenspeicher des Arbeitsbereichs neu zu synchronisieren. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Registrierung von Datenspeichern finden Sie in der Referenz der [`Datastore`](/python/api/azureml-core/azureml.core.datastore%28class%29?preserve-view=true&view=azure-ml-py)-Klasse.
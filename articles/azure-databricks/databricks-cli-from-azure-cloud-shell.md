---
title: 'Verwenden der Databricks-Befehlszeilenschnittstelle über Azure Cloud Shell '
description: Hier erfahren Sie, wie Sie die Databricks-Befehlszeilenschnittstelle über Azure Cloud Shell verwenden.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: dae481fb477223f149404c6a09cad024bc15cd90
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62108736"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Verwenden der Databricks-Befehlszeilenschnittstelle über Azure Cloud Shell

Hier erfahren Sie, wie Sie die Databricks-Befehlszeilenschnittstelle über Azure Cloud Shell verwenden, um Vorgänge für Databricks auszuführen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Databricks Arbeitsbereich und -Cluster. Anweisungen finden Sie unter [Schnellstart: Ausführen eines Spark-Auftrags in Azure Databricks mit dem Azure-Portal](quickstart-create-databricks-workspace-portal.md). 

* Richten Sie ein persönliches Zugriffstoken in Databricks ein. Anweisungen hierzu finden Sie im Artikel zur [Tokenverwaltung](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="use-the-azure-cloud-shell"></a>Verwenden der Azure Cloud Shell

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
 
2. Klicken Sie in der Ecke oben rechts auf das Symbol für **Cloud Shell**.

   ![Starten von Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Starten von Azure Cloud Shell")

3. Stellen Sie sicher, dass für die Cloud Shell-Umgebung **Bash** ausgewählt ist. Sie können die Option in der Dropdownliste auswählen, wie im folgenden Screenshot gezeigt:

   ![Auswählen von Bash für die Cloud Shell-Umgebung](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Auswählen von Bash") 

4. Erstellen Sie eine virtuelle Umgebung, in der Sie die Databricks-Befehlszeilenschnittstelle installieren können. Im folgenden Codeausschnitt erstellen Sie eine virtuelle Umgebung namens `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Wechseln Sie zu der von Ihnen erstellten virtuellen Umgebung.

       source databrickscli/bin/activate

6. Installieren Sie die Databricks-Befehlszeilenschnittstelle.

       pip install databricks-cli

7. Richten Sie die Authentifizierung mit Databricks ein. Verwenden Sie dabei das Zugriffstoken, das Sie wie unter „Voraussetzungen“ angegeben erstellen mussten. Verwenden Sie den folgenden Befehl:

       databricks configure --token

    Sie erhalten folgende Aufforderungen:

    * Sie werden zunächst zur Eingabe des Databricks-Hosts aufgefordert. Geben Sie den Wert im Format `https://eastus2.azuredatabricks.net` ein. **USA, Osten 2** ist in diesem Fall die Azure-Region, in der Sie Ihren Azure Databricks-Arbeitsbereich erstellt haben.

    * Als Nächstes werden Sie zur Eingabe eines Benutzernamens aufgefordert. Geben Sie das Token ein, das Sie zuvor erstellt haben.

Nachdem Sie diese Schritte abgeschlossen haben, können Sie die Databricks-Befehlszeilenschnittstelle über Azure Cloud Shell verwenden.

## <a name="use-databricks-cli"></a>Verwenden der Databricks-Befehlszeilenschnittstelle

Sie können die Databricks-Befehlszeilenschnittstelle nun verwenden. Führen Sie beispielsweise den folgenden Befehl aus, um alle Databricks-Cluster in Ihrem Arbeitsbereich aufzulisten.

    databricks clusters list

Sie können auch den folgenden Befehl verwenden, um auf das Databricks-Dateisystem (Databricks Filesystem, DBFS) zuzugreifen:

    databricks fs ls


Eine vollständige Referenz zu Befehlen finden Sie im Thema zur [Databricks-Befehlszeilenschnittstelle](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).


## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Azure-Befehlszeilenschnittstelle finden Sie unter [Übersicht über Azure Cloud Shell](../cloud-shell/overview.md).
* Eine Liste mit Befehlen für die Azure-Befehlszeilenschnittstelle finden Sie in der [Azure-CLI-Referenz](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest).
* Eine Liste mit Befehlen für die Databricks-Befehlszeilenschnittstelle finden Sie im Thema zur [Databricks-Befehlszeilenschnittstelle](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).



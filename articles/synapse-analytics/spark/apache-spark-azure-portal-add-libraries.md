---
title: Verwalten von Bibliotheken für Apache Spark
description: Erfahren Sie, wie Sie Bibliotheken hinzufügen und verwalten, die von Apache Spark in Azure Synapse Analytics verwendet werden.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 62610e1b86671021e66891ae232bacbd4b3e40ed
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458810"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Verwalten von Bibliotheken für Apache Spark in Azure Synapse Analytics

Bibliotheken stellen wiederverwendbaren Code bereit, den Sie in Ihre Programme oder Projekte einschließen können. Wenn Sie Code eines Drittanbieters oder lokal erstellten Code für Ihre Anwendungen verfügbar machen möchten, können Sie in einem Ihrer serverlosen Apache Spark-Pools eine Bibliothek installieren. Nachdem eine Bibliothek für einen Spark-Pool installiert wurde, ist sie für alle Sitzungen verfügbar, die denselben Pool verwenden. 

## <a name="before-you-begin"></a>Voraussetzungen
- Um Bibliotheken zu installieren und zu aktualisieren, müssen Sie über die Berechtigung **Mitwirkender an Storage-Blobdaten** oder **Besitzer von Speicherblobdaten** für das primäre Gen2-Speicherkonto verfügen, das mit dem Azure Synapse Analytics-Arbeitsbereich verknüpft ist.
  
## <a name="default-installation"></a>Standardinstallation
Apache Spark in Azure Synapse Analytics verfügt über eine vollständige Anacondas-Installation und zusätzliche Bibliotheken. Die vollständige Liste der Bibliotheken finden Sie unter [Apache Spark-Versionsunterstützung](apache-spark-version-support.md). 

Wenn eine Spark-Instanz gestartet wird, werden diese Bibliotheken automatisch eingeschlossen. Zusätzliche Python- und benutzerdefinierte Pakete können auf der Ebene des Spark-Pools hinzugefügt werden.


## <a name="manage-python-packages"></a>Verwalten von Python-Paketen
Nachdem Sie die Bibliotheken identifiziert haben, die Sie für Ihre Spark-Anwendung verwenden möchten, können Sie diese Bibliotheken in einem Spark-Pool installieren. 

 Sie können eine *requirements.txt*-Datei (Ausgabe des Befehls `pip freeze`) verwenden, um die virtuelle Umgebung zu aktualisieren. Die in dieser Datei für Installation oder Upgrade aufgeführten Pakete werden zum Zeitpunkt des Poolstarts von PyPi heruntergeladen. Diese Anforderungsdatei wird jedes Mal verwendet, wenn eine Spark-Instanz aus diesem Spark-Pool erstellt wird.

> [!IMPORTANT]
> - Wenn das Paket, das Sie installieren, groß ist oder seine Installation lange dauert, wirkt sich dies auf die Startzeit der Spark-Instanz aus.
> - Pakete, die zur Installationszeit Compilerunterstützung erfordern, z. B. GCC, werden nicht unterstützt.
> - Pakete können nicht herabgestuft, sondern nur hinzugefügt oder aktualisiert werden.
> - Zum Installieren von Bibliotheken benötigen Sie für das primäre Gen2-Speicherkonto, das mit dem Arbeitsbereich „Synapse“ verknüpft ist, die Berechtigung „Mitwirkender an Storage-Blobdaten“ oder „Besitzer von Speicherblobdaten“.

### <a name="requirements-format"></a>Anforderungsformat

Der folgende Codeausschnitt zeigt das Format für die Anforderungsdatei. Der Name des PyPi-Pakets wird zusammen mit einer exakten Version aufgeführt. Diese Datei hält das Format ein, das in der Referenzdokumentation zu [pip freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/) beschrieben wird. Dieses Beispiel verwendet eine bestimmte Version. 

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="install-python-packages"></a>Installieren von Python-Paketen
Während der Entwicklung Ihrer Spark-Anwendung stellen Sie möglicherweise fest, dass Sie vorhandene Bibliotheken aktualisieren oder neue installieren müssen. Bibliotheken können während oder nach der Erstellung des Pools aktualisiert werden.

#### <a name="install-packages-during-pool-creation"></a>Installieren von Paketen während der Poolerstellung
Gehen Sie wie folgt vor, um Bibliotheken während der Poolerstellung in einem Spark-Pool zu installieren:
   
1. Navigieren Sie im Azure-Portal zu Ihrem Azure Synapse Analytics-Arbeitsbereich.
   
2. Wählen Sie **Apache Spark-Pool erstellen** und dann die Registerkarte **Zusätzliche Einstellungen** aus. 
   
3. Laden Sie die Umgebungskonfigurationsdatei mithilfe der Dateiauswahl im Abschnitt **Pakete** auf der Seite hoch. 
   
    ![Hinzufügen von Python-Bibliotheken während der Poolerstellung](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-add-library-python.png "Hinzufügen von Python-Bibliotheken")
 

#### <a name="install-packages-from-the-synapse-workspace"></a>Installieren von Paketen aus dem Synapse-Arbeitsbereich
Gehen Sie wie folgt vor, um im Azure Synapse Analytics-Portal Bibliotheken zu aktualisieren oder einem Spark-Pool weitere Bibliotheken hinzuzufügen:

1.  Navigieren Sie im Azure-Portal zu Ihrem Azure Synapse Analytics-Arbeitsbereich.
   
2.  Starten Sie Ihren Azure Synapse Analytics-Arbeitsbereich über das Azure-Portal.

3.  Wählen Sie im Hauptnavigationsbereich **Verwalten** aus und dann **Apache Spark-Pools**.
   
4. Wählen Sie einen einzelnen Spark-Pool aus, und laden Sie die Umgebungskonfigurationsdatei mithilfe der Dateiauswahl im Abschnitt **Pakete** der Seite hoch.

    ![Hinzufügen von Python-Bibliotheken in Synapse](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-update.png)
   
#### <a name="install-packages-from-the-azure-portal"></a>Installieren von Paketen über das Azure-Portal
Gehen Sie wie folgt vor, um eine Bibliothek direkt im Azure-Portal in einem Spark-Pool zu installieren:
   
 1. Navigieren Sie im Azure-Portal zu Ihrem Azure Synapse Analytics-Arbeitsbereich.
   
 2. Wählen Sie im Abschnitt **Synapse-Ressourcen** die Registerkarte **Apache Spark-Pools** aus, und wählen Sie einen Spark-Pool aus der Liste aus.
   
 3. Wählen Sie im Abschnitt **Einstellungen** des Spark-Pools **Pakete** aus. 

 4. Laden Sie die Umgebungskonfigurationsdatei mithilfe der Dateiauswahl hoch.

    ![Screenshot, der die Schaltfläche zum Hochladen der Umgebungskonfigurationsdatei hervorhebt.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Hinzufügen von Python-Bibliotheken")

### <a name="verify-installed-libraries"></a>Überprüfen von installierten Bibliotheken

Führen Sie den folgenden Code aus, um zu überprüfen, ob die richtigen Versionen der richtigen Bibliotheken installiert sind

```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
### <a name="update-python-packages"></a>Aktualisieren von Python-Paketen
Pakete können jederzeit zwischen Sitzungen hinzugefügt oder geändert werden. Wenn eine neue Paketkonfigurationsdatei hochgeladen wird, überschreibt dies die vorhandenen Pakete und Versionen.  

So aktualisieren oder deinstallieren Sie eine Bibliothek
1. Navigieren Sie zu Ihrem Azure Synapse Analytics-Arbeitsbereich. 

2. Wählen Sie im Azure-Portal oder im Azure Synapse-Arbeitsbereich den **Apache Spark-Pool** aus, den Sie aktualisieren möchten.

3. Navigieren Sie zum Abschnitt **Pakete**, und laden Sie eine neue Umgebungskonfigurationsdatei hoch.
   
4. Nachdem Sie Ihre Änderungen gespeichert haben, müssen Sie aktive Sitzungen beenden und den Pool neu starten lassen. Optional können Sie das Beenden aktiver Sitzungen erzwingen, indem Sie das Kontrollkästchen **Neue Einstellungen erzwingen** aktivieren.

    ![Hinzufügen von Python-Bibliotheken](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Hinzufügen von Python-Bibliotheken")
   

> [!IMPORTANT]
> Wenn Sie die Option **Neue Einstellungen erzwingen** aktivieren, beenden Sie damit alle aktuellen Sitzungen für den ausgewählten Spark-Pool. Nachdem die Sitzungen beendet wurden, müssen Sie warten, bis der Pool neu gestartet wird. 
>
> Wenn diese Einstellung deaktiviert ist, müssen Sie warten, bis die aktuelle Spark-Sitzung beendet wird, oder sie manuell beenden. Nachdem die Sitzung beendet wurde, müssen Sie den Pool neu starten. 


## <a name="manage-a-python-wheel"></a>Verwalten eines Python-Wheels

### <a name="install-a-custom-wheel-file"></a>Installieren einer benutzerdefinierten Wheel-Datei
Benutzerdefiniert erstellte Wheel-Pakete können im Apache Spark-Pool installiert werden, indem alle Wheel-Dateien in Azure Data Lake Storage (Gen2)-Konto hochgeladen werden, das mit dem Synapse-Arbeitsbereich verknüpft ist. 

Die Dateien sollten in den folgenden Pfad im Standardcontainer des Speicherkontos hochgeladen werden: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

>[!IMPORTANT]
>Benutzerdefinierte Pakete können zwischen Sitzungen hinzugefügt oder geändert werden. Sie müssen jedoch warten, bis der Pool und die Sitzung neu gestartet werden, damit das aktualisierte Paket angezeigt wird.

## <a name="next-steps"></a>Nächste Schritte
- Anzeigen der Standardbibliotheken: [Versionsunterstützung für Apache Spark](apache-spark-version-support.md)

---
title: Erstellen einer verwalteten Azure SQL-Instanz in Azure Arc
description: Erstellen einer verwalteten Azure SQL-Instanz in Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0d1d6c7349adc65270e13d09d771963cb9cacea3
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280401"
---
# <a name="create-an-azure-sql-managed-instance-on-azure-arc"></a>Erstellen einer verwalteten Azure SQL-Instanz in Azure Arc

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="login-to-the-azure-arc-data-controller"></a>Anmelden beim Azure Arc-Datencontroller

Bevor Sie eine Instanz erstellen können, müssen Sie sich beim Azure Arc-Datencontroller anmelden, wenn dies noch nicht geschehen ist.

```console
azdata login
```

Dann werden Sie zur Eingabe von Benutzername, Kennwort und Systemnamespace aufgefordert.  

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-an-azure-sql-managed-instance"></a>Erstellen einer verwalteten Azure SQL-Instanz

Verwenden Sie den folgenden Befehl, damit verfügbare Optionen zum Erstellen der verwalteten Azure SQL-Instanz angezeigt werden:
```console
azdata arc sql mi create --help
```

Führen Sie zum Erstellen einer SQL Managed Instance-Instanz den folgenden Befehl aus:

```console
azdata arc sql mi create -n <instanceName> --storage-class-data <storage class> --storage-class-logs <storage class>
```

Beispiel:

```console
azdata arc sql mi create -n sqldemo --storage-class-data managed-premium --storage-class-logs managed-premium
```
> [!NOTE]
>  Namen dürfen nicht mehr als 13 Zeichen lang sein und müssen den [DNS-Benennungskonventionen](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-label-names) entsprechen.
>
>  Verwenden Sie beim Angeben einer Arbeitsspeicherbelegung und einer Zuordnung virtueller Kerne diese Formel, damit die Erstellung erfolgreich verläuft. Für jeden virtuellen Kern benötigen Sie eine Kapazität von mindestens 4 GB RAM auf dem Kubernetes-Knoten, auf dem der SQL Managed Instance-Pod ausgeführt wird.
>
>  Verwenden Sie beim Erstellen einer SQL-Instanz für den Namen keine Großbuchstaben, wenn Sie in Azure bereitstellen.
>
>  Führen Sie `kubectl get storageclass` aus, damit verfügbare Speicherklassen in Ihrem Kubernetes-Cluster angezeigt werden. 


> [!NOTE]
> Wenn Sie die Erstellung von SQL-Instanzen automatisieren und die interaktive Eingabeaufforderung für das Administratorenkennwort vermeiden möchten, können Sie die Umgebungsvariablen `AZDATA_USERNAME` und `AZDATA_PASSWORD` auf den gewünschten Benutzernamen und das gewünschte Kennwort festlegen, bevor Sie den Befehl `azdata arc sql mi create` ausführen.
> 
>  Wenn Sie den Datencontroller mithilfe von AZDATA_USERNAME und AZDATA_PASSWORD in derselben Terminalsitzung erstellt haben, werden die Werte für AZDATA_USERNAME und AZDATA_PASSWORD ebenfalls verwendet, um die SQL Managed Instance-Instanz zu erstellen.

> [!NOTE]
> Das Erstellen der Azure SQL Managed Instance-Instanz registriert die Ressourcen in Azure nicht. Die zum Registrieren der Ressource erforderlichen Schritte finden Sie in den folgenden Artikeln: 
> - [Anzeigen von Protokollen und Metriken mithilfe von Kibana und Grafana](monitor-grafana-kibana.md)
> - [Hochladen von Abrechnungsdaten in Azure und Anzeigen im Azure-Portal](view-billing-data-in-azure.md) 


## <a name="view-instance-on-azure-arc"></a>Anzeigen der Instanz in Azure Arc

Verwenden Sie den folgenden Befehl, um die Instanz anzuzeigen:

```console
azdata arc sql mi list
```

Das Ergebnis sollte wie folgt aussehen:

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

Wenn Sie z. B. AKS oder `kubeadm` oder OpenShift verwenden, können Sie die externe IP-Adresse und Portnummer von dort kopieren und mithilfe eines Tools Ihrer Wahl für die Verbindungsherstellung zu einer SQL Server-/Azure SQL-Instanz wie Azure Data Studio oder SQL Server Management Studio eine Verbindung dazu herstellen. Wenn Sie jedoch die Schnellstart-VM nutzen, finden Sie im Artikel [Herstellen einer Verbindung zu Azure Arc-fähigen SQL Managed Instance-Instanzen](connect-managed-instance.md) eine gesonderte Anleitung.


## <a name="next-steps"></a>Nächste Schritte
- [Herstellen einer Verbindung zu Azure Arc-fähigen SQL Managed Instance-Instanzen](connect-managed-instance.md)
- [Registrieren Ihrer Instanz mit Azure und Hochladen von Metriken und Protokollen für Ihre Instanz](upload-metrics-and-logs-to-azure-monitor.md)
- [Bereitstellen einer verwalteten Azure SQL-Instanz mithilfe von Azure Data Studio](create-sql-managed-instance-azure-data-studio.md)

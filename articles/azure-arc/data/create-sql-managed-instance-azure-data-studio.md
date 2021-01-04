---
title: Erstellen einer verwalteten Azure SQL-Instanz mithilfe von Azure Data Studio
description: Erstellen einer verwalteten Azure SQL-Instanz mithilfe von Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 651811384a8e831c41c48ca8c4849e5ee3852054
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280465"
---
# <a name="create-sql-managed-instance---azure-arc-using-azure-data-studio"></a>Erstellen einer verwalteten SQL-Instanz in Azure Arc mithilfe von Azure Data Studio

In diesem Artikel erhalten Sie Informationen zu den Schritten für das Installieren einer verwalteten Azure SQL-Instanz in Azure Arc mithilfe von Azure Data Studio.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="log-in-to-the-azure-arc-data-controller"></a>Anmelden beim Azure Arc-Datencontroller

Bevor Sie eine Instanz erstellen können, müssen Sie sich beim Azure Arc-Datencontroller anmelden, wenn dies noch nicht geschehen ist.

```console
azdata login
```

Sie werden dann dazu aufgefordert, den Namespace anzugeben, in dem der Datencontroller erstellt wurde, sowie den Benutzernamen und das Kennwort, um sich beim Controller anzumelden.  

> Wenn Sie den Namespace validieren müssen, können Sie ```kubectl get pods -A``` ausführen, um eine Liste aller Namespaces im Cluster abzurufen.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-azure-sql-managed-instance-on-azure-arc"></a>Erstellen einer verwalteten Azure SQL-Instanz in Azure Arc

- Starten Sie Azure Data Studio.
- Klicken Sie auf der Registerkarte „Verbindungen“ auf die drei Punkte oben links, und klicken Sie auf „Neue Bereitstellung“.
- Klicken Sie bei den Bereitstellungsoptionen auf **Azure SQL managed instance – Azure Arc** (verwaltete Azure SQL-Instanz – Azure Arc). 
  > [!NOTE]
  > Möglicherweise werden Sie hier dazu aufgefordert, die [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] zu installieren, wenn dies noch nicht geschehen ist.
- Akzeptieren Sie die Datenschutz- und Lizenzbedingungen, und klicken Sie unten auf **Auswählen**.



- Geben Sie auf dem Blatt „Deploy Azure SQL managed instance – Azure Arc “ (Verwaltete Azure SQL-Instanz bereitstellen – Azure Arc) die folgenden Informationen ein:
  - Geben Sie einen Namen für die SQL Server-Instanz ein.
  - Geben Sie ein Kennwort für die SQL Server-Instanz ein, und bestätigen Sie dieses.
  - Wählen Sie die Speicherklasse aus, die sich für Daten eignet.
  - Wählen Sie die Speicherklasse aus, die sich für Protokolle eignet.

- Klicken Sie auf die Schaltfläche **Bereitstellen**.

- Dadurch sollte die Erstellung der verwalteten Azure SQL-Instanz in Azure Arc auf dem Datencontroller gestartet werden.

- Nach einigen Minuten sollte die Erstellung erfolgreich abgeschlossen werden.

## <a name="connect-to-azure-sql-managed-instance---azure-arc-from-azure-data-studio"></a>Stellen Sie in Azure Data Studio eine Verbindung zur verwalteten Azure SQL-Instanz in Azure Arc her.

- Melden Sie sich beim Azure Arc-Datencontroller an, indem Sie Namespace, Benutzernamen und Kennwort für den Datencontroller angeben: 
```console
azdata login
```

- Zeigen Sie mithilfe der folgenden Befehle alle bereitgestellten verwalteten Azure SQL-Instanzen an:

```console
azdata arc sql mi list
```

Die Ausgabe sollte folgendermaßen aussehen. Kopieren Sie den ServerEndpoint-Wert (einschließlich der Portnummer) von dort.

```console

Name          Replicas    ServerEndpoint     State
------------  ----------  -----------------  -------
sqlinstance1  1/1         25.51.65.109:1433  Ready
```

- Klicken Sie in Azure Data Studio unter der Registerkarte **Verbindungen** in der Ansicht **Server** auf **Neue Verbindung**.
- Fügen Sie auf dem Blatt **Verbindung** den ServerEndpoint-Wert in das Textfeld „Server“ ein.
- Wählen Sie **SQL-Anmeldung** als Authentifizierungstyp aus.
- Geben Sie als Benutzernamen *sa* ein.
- Geben Sie das Kennwort für das Konto `sa` ein.
- Geben Sie optional den genauen Datenbanknamen ein, um eine Verbindung dazu herzustellen.
- Klicken Sie außerdem optional bei Bedarf auf die Option „Add new server group“ (Neue Servergruppe hinzufügen).
- Klicken Sie auf **Verbinden** , um eine Verbindung zur verwalteten Azure SQL-Instanz in Azure Arc herzustellen.




## <a name="next-steps"></a>Nächste Schritte

Als nächsten Schritt können Sie [Ihre SQL-Instanz überwachen](monitor-grafana-kibana.md).

---
title: Erstellen einer Azure Database for PostgreSQL Hyperscale-Servergruppe in Azure Arc
description: Erstellen einer Azure Database for PostgreSQL Hyperscale-Servergruppe in Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 45bb045e7bad2d5f8a56b71787b3abb5921cb7d5
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985885"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Erstellen einer Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe

In diesem Dokument werden die Schritte zur Erstellung einer PostgreSQL Hyperscale-Servergruppe in Azure Arc beschrieben.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>Erste Schritte
Wenn Sie bereits mit den folgenden Themen vertraut sind, können Sie diesen Absatz überspringen.
Dabei handelt es sich um wichtige Themen, die Sie lesen sollten, bevor Sie mit dem Erstellen beginnen:
- [Übersicht über Azure Arc-fähige Datendienste](overview.md)
- [Konnektivitätsmodi und -anforderungen](connectivity.md)
- [Speicherkonfiguration und Kubernetes-Speicherkonzepte](storage-configuration.md)
- [Kubernetes-Ressourcenmodell](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

Wenn Sie es vorziehen, dies auszuprobieren, ohne selbst eine vollständige Umgebung bereitzustellen, können Sie mit dem [Azure Arc-Schnelleinstieg](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) in Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) oder auf einer Azure-VM einsteigen.


## <a name="login-to-the-azure-arc-data-controller"></a>Anmelden beim Azure Arc-Datencontroller

Bevor Sie eine Instanz erstellen können, müssen Sie sich zuerst beim Azure Arc-Datencontroller anmelden. Wenn Sie bereits beim Datencontroller angemeldet sind, können Sie diesen Schritt überspringen.

```console
azdata login
```

Sie werden dazu aufgefordert, den Benutzernamen, Ihr Kennwort und den Systemnamespace einzugeben.  

> Wenn Sie das Skript zum Erstellen des Datencontrollers verwendet haben, sollte Ihr Namespace **arc** lauten.

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Vorläufiger und temporärer Schritt nur für OpenShift-Benutzer

Implementieren Sie diesen Schritt, bevor Sie mit dem nächsten fortfahren. Sie müssen die folgenden Befehle zum Updaten der Sicherheitseinschränkungen für Ihren Cluster ausführen, um eine PostgreSQL Hyperscale-Servergruppe in Red Hat OpenShift in einem Projekt bereitzustellen, das nicht dem Standardprojekt entspricht. Dieser Befehl gewährt den Dienstkonten, unter denen Ihre PostgreSQL Hyperscale-Servergruppe ausgeführt wird, die erforderlichen Berechtigungen. Die Sicherheitskontexteinschränkung **_arc-data-scc_** haben Sie hinzugefügt, als Sie den Azure Arc-Datencontroller bereitgestellt haben.

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**Server-group-name** entspricht dem Namen der Servergruppe, die Sie im nächsten Schritt erstellen._
   
Weitere Informationen zu Sicherheitskontexteinschränkungen in OpenShift finden Sie in der [OpenShift-Dokumentation](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html).
Sie können nun mit dem nächsten Schritt fortfahren.

## <a name="create-an-azure-database-for-postgresql-hyperscale-server-group"></a>Erstellen einer Azure Database for PostgreSQL Hyperscale-Servergruppe

Verwenden Sie den folgenden Befehl zum Erstellen einer Azure Database for PostgreSQL Hyperscale-Servergruppe in Azure Arc:

```console
azdata arc postgres server create -n <name> --workers <# worker nodes with #>=2> --storage-class-data <storage class name> --storage-class-logs <storage class name> --storage-class-backups <storage class name>

#Example
#azdata arc postgres server create -n postgres01 --workers 2
```

> [!IMPORTANT]
> - Ohne Angabe wird die für Sicherungen verwendete Speicherklasse ( _--storage-class-backups -scb_) standardmäßig auf die Datenspeicherklasse des Datencontrollers festgelegt.
> - Zum Wiederherstellen einer Servergruppe in einer separaten Servergruppe (z. B. Zeitpunktwiederherstellung) müssen Sie die Servergruppe für die Verwendung von PVCs im Zugriffsmodus ReadWriteMany konfigurieren. Dies muss bei der Erstellung der Servergruppe erfolgen. Nach dem Erstellen ist keine Änderung mehr möglich. Weitere Informationen:
>    - [Abschnitt zur Sicherung und Wiederherstellung](https://docs.microsoft.com/azure/azure-arc/data/backup-restore-postgresql-hyperscale#create-a-server-group-that-is-ready-for-backups-and-restores)
>    - [Abschnitt zu den Einschränkungen von PostgreSQL Hyperscale mit Azure Arc-Unterstützung](https://docs.microsoft.com/azure/azure-arc/data/limitations-postgresql-hyperscale)


> [!NOTE]
> - **Es stehen auch andere Befehlszeilenparameter zur Verfügung.  Sie können eine vollständige Liste der Optionen anzeigen, indem Sie `azdata arc postgres server create --help` ausführen.**

> - Die von den Parametern „--volume-size-*“ akzeptierte Einheit ist eine Kubernetes-Ressourcenmenge (ein Integer gefolgt von einem SI-Suffix (T, G, M, K, m) oder einem Äquivalent mit Zweierpotenz (Ti, Gi, Mi, Ki)).
> - Namen dürfen maximal 12 Zeichen lang sein und müssen den DNS-Benennungskonventionen entsprechen.
> - Sie werden dazu aufgefordert, das Kennwort für den Standardadministrator für _Postgre_ einzugeben.  Sie können die interaktive Eingabeaufforderung überspringen, indem Sie die Sitzungsumgebungsvariable `AZDATA_PASSWORD` festlegen, bevor Sie den Befehl zum Erstellen ausführen.
> - Wenn Sie den Datencontroller mithilfe der Sitzungsumgebungsvariablen „AZDATA_USERNAME“ und „AZDATA_PASSWORD“ in der gleichen Terminalsitzung bereitgestellt haben, werden die Werte für „AZDATA_PASSWORD“ auch verwendet, um die PostgreSQL Hyperscale-Servergruppe bereitzustellen. Wenn Sie lieber ein anderes Kennwort verwenden möchten, können Sie entweder (1) den Wert für „AZDATA_PASSWORD“ aktualisieren oder (2) die Umgebungsvariable „AZDATA_PASSWORD“ oder deren Wert löschen. Im letzteren Fall werden Sie beim Erstellen einer Servergruppe interaktiv zur Eingabe eines Kennworts aufgefordert.
> - Der Name des Standardadministrators für die PostgreSQL Hyperscale-Datenbank-Engine lautet _postgres_ und kann zu diesem Zeitpunkt nicht geändert werden.
> - Durch das Erstellen einer PostgreSQL Hyperscale-Servergruppe werden nicht sofort Ressourcen in Azure registriert. Im Rahmen des Uploads des [Ressourcenbestands](upload-metrics-and-logs-to-azure-monitor.md) oder der [Nutzungsdaten](view-billing-data-in-azure.md) in Azure, werden die Ressourcen in Azure erstellt, und Sie können Ihre Ressourcen im Azure-Portal anzeigen.



## <a name="list-your-azure-database-for-postgresql-server-groups-created-in-your-arc-setup"></a>Auflisten der Azure Database for PostgreSQL-Servergruppen, die in Ihrem Arc-Setup erstellt wurden

Verwenden Sie den folgenden Befehl, um die PostgreSQL Hyperscale-Servergruppen in Azure Arc anzuzeigen:

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-database-for-postgresql-server-groups"></a>Abrufen der Endpunkte zum Herstellen einer Verbindung mit Ihren Azure Database for PostgreSQL-Servergruppen

Führen Sie den folgenden Befehl aus, um die Endpunkte für eine PostgreSQL-Instanz anzuzeigen:

```console
azdata arc postgres endpoint list -n <server group name>
```
Beispiel:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

Sie können den Endpunkt der PostgreSQL-Instanz verwenden, um über Ihr bevorzugtes Tool eine Verbindung mit der PostgreSQL Hyperscale-Servergruppe herzustellen:  [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [pgcli](https://www.pgcli.com/) psql, pgAdmin, usw.

Wenn Sie eine Azure-VM zum Testen verwenden, führen Sie die folgenden Anweisungen aus:

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Besonderer Hinweis zu Azure-VM-Bereitstellungen

Wenn Sie eine Azure-VM verwenden, zeigt die Endpunkt-IP-Adresse die _öffentliche_ IP-Adresse nicht an. Verwenden Sie den folgenden Befehl, um die öffentliche IP-Adresse zu ermitteln:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Sie können die öffentliche IP-Adresse mit dem Port kombinieren, um Ihre Verbindung herzustellen.

Möglicherweise müssen Sie den Port der PostgreSQL Hyperscale-Servergruppe über das Netzwerksicherheitsgateway zur Verfügung stellen. Sie müssen eine Regel hinzufügen, um Datenverkehr über das Netzwerksicherheitsgateway zuzulassen. Hierzu können Sie den folgenden Befehl verwenden:

Zum Festlegen einer Regel müssen Sie den Namen Ihres Netzwerksicherheitsgateways kennen. Sie bestimmen das Netzwerksicherheitsgateway mithilfe des folgenden Befehls:

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Sobald Sie den Namen des Netzwerksicherheitsgateways kennen, können Sie mithilfe des folgenden Befehls eine Firewallregel hinzufügen. Mit den hier gezeigten Beispielwerten wird eine Netzwerksicherheitsgateway-Regel für Port 30655 erstellt und das Herstellen von Verbindungen über **beliebige** Quell-IP-Adressen zugelassen.  Dies ist keine bewährte Methode hinsichtlich der Sicherheit!  Sie können mehr Sicherheit gewährleisten, indem Sie einen Wert „-source-address-prefixes“ festlegen, der spezifisch für Ihre Client-IP-Adresse oder einen IP-Adressbereich gilt, der die IP-Adressen Ihres Teams oder Ihrer Organisation umfasst.

Ersetzen Sie den Wert des Parameters „--destination-port-ranges“ unten durch die Portnummer, die Sie zuvor mithilfe des Befehls „azdata arc postgres server list“ abgerufen haben.

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>Herstellen einer Verbindung mit Azure Data Studio

Öffnen Sie Azure Data Studio, und stellen Sie mit der Endpunkt-IP-Adresse und der Portnummer oben eine Verbindung mit Ihrer Instanz her. Verwenden Sie hierzu das Kennwort, das Sie beim Erstellen der Instanz festgelegt haben.  Wenn PostgreSQL nicht im Dropdownmenü *Verbindungstyp* verfügbar ist, können Sie die PostgreSQL-Erweiterung installieren, indem Sie in der Registerkarte „Erweiterungen“ nach „PostgreSQL“ suchen.

> [!NOTE]
> Sie müssen im Verbindungsbereich auf die Schaltfläche „[Advanced]“ (Erweitert) klicken, um die Portnummer einzugeben.

Denken Sie daran, dass Sie die _öffentliche_ IP-Adresse benötigen, auf die Sie mithilfe des folgenden Befehls zugreifen können, wenn Sie eine Azure-VM verwenden:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>Herstellen einer Verbindung mit psql

Übergeben Sie den externen Endpunkt der PostgreSQL Hyperscale-Servergruppe, den Sie oben abgerufen haben, um auf Ihre PostgreSQL Hyperscale-Servergruppe zuzugreifen:

Sie können nun eine Verbindung mit psql herstellen:

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie die Konzepte und Schrittanleitungen zu Azure Database for PostgreSQL Hyperscale, um Ihre Daten auf mehrere PostgreSQL Hyperscale-Knoten zu verteilen und von der gesamten Leistung von Azure Database for PostgreSQL Hyperscale zu profitieren. :
    * [Knoten und Tabellen](../../postgresql/concepts-hyperscale-nodes.md)
    * [Festlegen des Anwendungstyps](../../postgresql/concepts-hyperscale-app-type.md)
    * [Auswählen einer Verteilungsspalte](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tabellenzusammenstellung](../../postgresql/concepts-hyperscale-colocation.md)
    * [Verteilen und Ändern von Tabellen](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Entwerfen einer Datenbank mit mehreren Mandanten](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Entwerfen eines Dashboards für Echtzeitanalysen](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* Überspringen Sie die Abschnitte **Anmelden am Azure-Portal** und **Erstellen einer Azure Database for PostgreSQL-Instanz für Hyperscale (Citus)** in den oben aufgeführten Dokumenten. Implementieren Sie die restlichen Schritte in Ihrer Azure Arc-Bereitstellung. Diese Abschnitte sind speziell für den PaaS-Dienst „Azure Database for PostgreSQL Hyperscale (Citus)“ in der Azure-Cloud vorgesehen. Die anderen Abschnitte der Dokumente sind jedoch direkt auf Ihre Azure Arc-fähige PostgreSQL Hyperscale-Instanz übertragbar.

- [Skalieren der Azure Database for PostgreSQL Hyperscale-Servergruppe](scale-out-postgresql-hyperscale-server-group.md)
- [Speicherkonfiguration und Kubernetes-Speicherkonzepte](storage-configuration.md)
- [Erweitern von Ansprüchen persistenter Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes-Ressourcenmodell](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
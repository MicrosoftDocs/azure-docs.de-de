---
title: Visualisieren von Daten über Azure Data Explorer mit Grafana
description: In diesem Artikel erfahren Sie, wie Sie den Azure Data Explorer als Datenquelle für Grafana einrichten und dann Daten aus einem Beispielcluster visualisieren.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/05/2018
ms.openlocfilehash: 135f8f1c9c352f9d2307a8bf9ad1bec892aac179
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399911"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Visualisieren von Daten über Azure Data Explorer in Grafana

Grafana ist eine Analyseplattform, mit der Sie Daten abfragen und visualisieren sowie Dashboards auf Basis Ihrer Visualisierungen erstellen und freigeben können. Grafana stellt ein *Plug-In* für den Azure Data Explorer zur Verfügung, mit dem Sie Daten aus dem Azure Data Explorer verbinden und visualisieren können. In diesem Artikel erfahren Sie, wie Sie den Azure Data Explorer als Datenquelle für Grafana einrichten und dann Daten aus einem Beispielcluster visualisieren.

## <a name="prerequisites"></a>Voraussetzungen

Folgendes ist zum Abschließen dieser Vorgehensweise erforderlich:

* [Grafana, Version 5.3.0 oder höher,](https://docs.grafana.org/installation/) für Ihr Betriebssystem

* [Azure Data Explorer-Plug-In](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) für Grafana

* Ein Cluster, der die StormEvents-Beispieldaten enthält Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines Azure Data Explorer-Clusters und einer Datenbank](create-cluster-database-portal.md) und [Erfassen von Beispieldaten in Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-data-source"></a>Konfigurieren der Datenquelle

Sie führen die folgenden Schritte aus, um den Azure Data Explorer als Datenquelle für Grafana zu konfigurieren. Diese Schritte werden in diesem Abschnitt näher erläutert:

1. Erstellen Sie einen Azure Active Directory-Dienstprinzipal (Azure AD). Der Dienstprinzipal wird von Grafana für den Zugriff auf den Azure Data Explorer-Dienst verwendet.

1. Fügen Sie den Azure AD-Dienstprinzipal der Rolle *Betrachter* in der Azure Data Explorer-Datenbank hinzu.

1. Geben Sie die Grafana-Verbindungseigenschaften basierend auf Informationen des Azure AD-Dienstprinzipals an, und testen Sie die Verbindung.

### <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

Sie können den Dienstprinzipal im [Azure-Portal](#azure-portal) oder mit der [Azure-Befehlszeilenschnittstelle](#azure-cli) erstellen. Unabhängig von der verwendeten Methode erhalten Sie nach der Erstellung Werte für vier Verbindungseigenschaften, die Sie in späteren Schritten verwenden.

#### <a name="azure-portal"></a>Azure-Portal

1. Befolgen Sie zum Erstellen des Dienstprinzipals die Anweisungen in der [Dokumentation zum Azure-Portal](/azure/active-directory/develop/howto-create-service-principal-portal).

    1. Weisen Sie im Abschnitt [Zuweisen der Anwendung zu einer Rolle](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) den Rollentyp **Leser** zu Ihrem Azure Data Explorer-Cluster zu.

    1. Kopieren Sie im Abschnitt [Abrufen von Werten für die Anmeldung](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) die drei in den Schritten behandelten Eigenschaftswerte: **Verzeichnis-ID** (Mandanten-ID), **Anwendungs-ID** und **Kennwort**.

1. Wählen Sie im Azure-Portal **Abonnements**, und kopieren Sie dann die ID für das Abonnement, in dem Sie den Dienstprinzipal erstellt haben.

    ![Abonnement-ID – Portal](media/grafana/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

1. Erstellen eines Dienstprinzipals Legen Sie einen geeigneten Bereich und den Rollentyp `reader` fest.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourGrafana}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Weitere Informationen finden Sie unter [Erstellen eines Azure-Dienstprinzipals mit Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Der Befehl gibt ein Resultset wie das folgende zurück. Kopieren Sie die drei Eigenschaftswerte: **appID**, **password** und **tenant**.

    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourGrafana}:{PortNumber}",
      "name": "https://{UrlToYourGrafana}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Rufen Sie eine Liste Ihrer Abonnements ab.

    ```azurecli
    az account list --output table
    ```

    Kopieren Sie die entsprechende Abonnement-ID.

    ![Abonnement-ID – CLI](media/grafana/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Hinzufügen des Dienstprinzipals zur Rolle des Betrachters

Nachdem Sie jetzt über einen Dienstprinzipal verfügen, fügen Sie ihn der Rolle *Betrachter* in der Azure Data Explorer-Datenbank hinzu. Sie können diese Aufgabe unter **Berechtigungen** im Azure-Portal oder unter **Abfrage** mit einem Verwaltungsbefehl ausführen.

#### <a name="azure-portal---permissions"></a>Azure-Portal – Berechtigungen

1. Navigieren Sie im Azure-Portal zum Azure Data Explorer-Cluster.

1. Wählen Sie im Abschnitt **Übersicht** die Datenbank mit den StormEvents-Beispieldaten aus.

    ![Datenbank auswählen](media/grafana/select-database.png)

1. Wählen Sie **Berechtigungen** und dann **Hinzufügen** aus.

    ![Datenbankberechtigungen](media/grafana/database-permissions.png)

1. Wählen Sie unter **Datenbankberechtigungen hinzufügen** die Rolle **Betrachter** und dann **Prinzipale auswählen** aus.

    ![Hinzufügen von Datenbankberechtigungen](media/grafana/add-permission.png)

1. Suchen Sie nach dem von Ihnen erstellten Dienstprinzipal (das Beispiel zeigt den Prinzipal **mb-grafana**). Wählen Sie den Prinzipal und dann **Auswählen** aus.

    ![Verwalten von Berechtigungen im Azure-Portal](media/grafana/new-principals.png)

1. Wählen Sie **Speichern** aus.

    ![Verwalten von Berechtigungen im Azure-Portal](media/grafana/save-permission.png)

#### <a name="management-command---query"></a>Verwaltungsbefehl – Abfrage

1. Navigieren Sie im Azure-Portal zum Azure Data Explorer-Cluster, und wählen Sie **Abfrage** aus.

    ![Abfragen](media/grafana/query.png)

1. Führen Sie den folgenden Befehl im Abfragefenster aus. Verwenden Sie die Anwendungs-ID und die Mandanten-ID aus dem Azure-Portal oder der Befehlszeilenschnittstelle.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    Der Befehl gibt ein Resultset wie das folgende zurück. In diesem Beispiel ist die erste Zeile für einen vorhandenen Benutzer in der Datenbank und die zweite Zeile für den gerade hinzugefügten Dienstprinzipal.

    ![Resultset](media/grafana/result-set.png)

### <a name="specify-properties-and-test-the-connection"></a>Angeben von Eigenschaften und Testen der Verbindung

Wenn der Dienstprinzipal der Rolle *Betrachter* zugeordnet ist, geben Sie jetzt Eigenschaften in Ihrer Instanz von Grafana an und testen die Verbindung zum Azure Data Explorer.

1. Wählen Sie in Grafana im linken Menü das Zahnradsymbol und dann **Datenquellen** aus.

    ![Datenquellen](media/grafana/data-sources.png)

1. Wählen Sie die Option **Datenquelle hinzufügen**.

1. Geben Sie auf der Seite **Datenquellen/Neu** einen Namen für die Datenquelle ein, und wählen Sie dann den Typ **Azure Data Explorer-Datenquelle** aus.

    ![Verbindungsname und -typ](media/grafana/connection-name-type.png)

1. Geben Sie den Namen Ihres Clusters in der Form „https://{ClusterName}.{Region}.kusto.windows.net“ ein. Geben Sie die anderen Werte aus dem Azure-Portal oder der Befehlszeilenschnittstelle ein. Eine Zuordnung finden Sie in der Tabelle unten dem folgenden Bild.

    ![Verbindungseigenschaften](media/grafana/connection-properties.png)

    | Grafana-Benutzeroberfläche | Azure-Portal | Azure-Befehlszeilenschnittstelle |
    | --- | --- | --- |
    | Subscription Id (Abonnement-ID) | ABONNEMENT-ID | SubscriptionId |
    | Mandanten-ID | Verzeichnis-ID | tenant |
    | Client-ID | Anwendungs-ID | appId |
    | Geheimer Clientschlüssel | Kennwort | password |
    | | | |

1. Wählen Sie **Speichern und testen** aus.

    Wenn der Test erfolgreich war, wechseln Sie zum nächsten Abschnitt. Wenn Probleme auftreten, überprüfen Sie die in Grafana angegebenen Werte, und wiederholen Sie die vorherigen Schritte.

## <a name="visualize-data"></a>Visualisieren von Daten

Nachdem Sie den Azure Data Explorer als Datenquelle für Grafana konfiguriert haben, ist es an der Zeit, Daten zu visualisieren. Hier wird ein einfaches Beispiel gezeigt, aber es sind noch viele weitere Schritte verfügbar. Es wird empfohlen, sich [Schreiben von Abfragen für den Azure Data Explorer](write-queries.md) anzusehen, um Beispiele für andere Abfragen zu erhalten, die für das Beispieldataset ausgeführt werden.

1. Wählen Sie in Grafana im linken Menü das Plussymbol und dann **Dashboard** aus.

    ![Erstellen von Dashboards](media/grafana/create-dashboard.png)

1. Wählen Sie unter der Registerkarte **Hinzufügen** die Option **Diagramm** aus.

    ![Hinzufügen des Diagramms](media/grafana/add-graph.png)

1. Wählen Sie im Diagrammpanel **Paneltitel** und dann **Bearbeiten** aus.

    ![Bearbeiten des Panels](media/grafana/edit-panel.png)

1. Wählen Sie unten im Panel **Datenquelle** und dann die Datenquelle aus, die Sie konfiguriert haben.

    ![Auswählen einer Datenquelle](media/grafana/select-data-source.png)

1. Kopieren Sie im Abfragepanel die folgende Abfrage, und wählen Sie dann **Ausführen** aus. Die Abfrage berücksichtigt die Anzahl der Ereignisse pro Tag für das Beispieldataset.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Abfrage ausführen](media/grafana/run-query.png)

1. Das Diagramm zeigt keine Ergebnisse, da es standardmäßig auf Daten der letzten sechs Stunden ausgerichtet ist. Wählen Sie im oberen Menü **Letzte 6 Stunden** aus.

    ![Letzte sechs Stunden](media/grafana/last-six-hours.png)

1. Geben Sie einen benutzerdefinierten Bereich an, der das Jahr 2007 umfasst, das in unserem StormEvents-Beispieldataset enthalten ist. Wählen Sie **Übernehmen**.

    ![Benutzerdefinierter Datumsbereich](media/grafana/custom-date-range.png)

    Jetzt zeigt das Diagramm die nach Tag gegliederten Daten von 2007 an.

    ![Fertiges Diagramm](media/grafana/finished-graph.png)

1. Wählen Sie im oberen Menü das Speichersymbol aus: ![Symbol „Speichern“](media/grafana/save-icon.png).

## <a name="next-steps"></a>Nächste Schritte

* [Schreiben von Abfragen für den Azure-Daten-Explorer](write-queries.md)

* [Tutorial: Visualisieren von Daten über Azure Data Explorer in Power BI](visualize-power-bi.md)
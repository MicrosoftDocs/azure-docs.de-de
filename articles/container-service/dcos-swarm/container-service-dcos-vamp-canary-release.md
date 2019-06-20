---
title: (VERALTET) Canary-Release mit Vamp im Azure-DC/OS-Cluster
description: 'Gewusst wie: Verwendung von VAMP mit Canary-Releasediensten und Anwendung von intelligenten Datenverkehrsfiltern in einem Azure Container Service-DC/OS-Cluster'
services: container-service
author: gggina
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: f1b3c08cce2cb33feab899ea082fc6fb40225182
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61458196"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>(VERALTET) Canary-Release-Microservices mit VAMP in einem Azure Container Service-DC/OS-Cluster

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

In dieser exemplarischen Vorgehensweise richten wir VAMP in Azure Container Service mit einem DC/OS-Cluster ein. Wir führen ein Canary-Releasing für den VAMP-Demodienst „sava“ durch, und beheben dann eine Inkompatibilität des Diensts mit Firefox durch intelligente Datenverkehrsfilter. 

> [!TIP] 
> In dieser exemplarischen Vorgehensweise wird VAMP in einem DC/OS-Cluster ausgeführt, Sie können jedoch auch VAMP mit Kubernetes als Orchestrator verwenden.
>

## <a name="about-canary-releases-and-vamp"></a>Informationen zu Canary-Releases und VAMP


[Canary-Releasing](https://martinfowler.com/bliki/CanaryRelease.html) ist eine intelligente Bereitstellungsstrategie, die von innovativen Unternehmen wie Netflix, Facebook und Spotify übernommen wurde. Sie stellt einen sinnvollen Ansatz dar, da sie Probleme verringert, Sicherheitsnetze schafft und Innovationen fördert. Warum wenden daher nicht alle Unternehmen diesen Ansatz an? Die Erweiterung einer CI/CD-Pipeline um Canary-Strategien vergrößert die Komplexität und erfordert Kenntnisse und Erfahrungen zu DevOps. Dies reicht aus, um kleinere wie auch größere Unternehmen zu blockieren, bevor sie überhaupt angefangen haben. 

[VAMP](https://vamp.io/) ist ein Open-Source-System, das die Umstellung vereinfachen und Ihren bevorzugten Containerplaner um Canary-Releasing-Funktionen erweitern soll. Die Canary-Funktionen von VAMP gehen über auf Prozentsätzen basierende Rollouts hinaus. Der Datenverkehr kann gefiltert und in eine Vielzahl von Bedingungen unterteilt werden, z.B. zur Anvisierung von bestimmten Benutzern, IP-Adressbereichen oder Geräten. VAMP verfolgt Leistungsmetriken nach und analysiert diese, was die Automatisierung auf Basis von realistischen Daten ermöglicht. Zudem können Sie automatische Rollbacks für Fehler einrichten oder einzelne Dienstvarianten basierend auf Lasten oder Latenzen skalieren.

## <a name="set-up-azure-container-service-with-dcos"></a>Einrichten von Azure Container Service mit DC/OS



1. [Stellen Sie einen DC/OS-Cluster](container-service-deployment.md) mit einem Master- und zwei Agents in Standardgröße bereit. 

2. [Erstellen Sie einen SSH-Tunnel](../container-service-connect.md), um eine Verbindung mit dem DC/OS-Cluster herzustellen. In diesem Artikel wird davon ausgegangen, dass der Tunnel zum Cluster über den lokalen Port 80 eingerichtet wird.


## <a name="set-up-vamp"></a>Einrichten von VAMP

Nachdem das DC/OS-Cluster in Betrieb genommen wurde, können Sie VAMP über die DC/OS-Benutzeroberfläche (http:\//localhost:80) installieren. 

![DC/OS-Benutzeroberfläche](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

Die Installation erfolgt in zwei Phasen:

1. **Bereitstellen von Elasticsearch**

2. Bereitstellen von **VAMP** durch Installieren des VAMP-DC/OS-Universe-Pakets

### <a name="deploy-elasticsearch"></a>Bereitstellen von Elasticsearch

Für VAMP ist Elasticsearch zur Sammlung und Aggregation von Metriken erforderlich. Durch [Magneticio-Docker-Images](https://hub.docker.com/r/magneticio/elastic/) können Sie einen kompatiblen VAMP-Elasticsearch-Stapel bereitstellen.

1. Navigieren Sie auf der DC/OS-Benutzeroberfläche zu **Dienste**, und klicken Sie auf **Dienst bereitstellen**.

2. Wählen Sie im Popupmenü **Neuen Dienst bereitstellen** den **JSON-Modus** aus.

   ![Auswählen des JSON-Modus](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Fügen Sie folgenden JSON-Code ein. In dieser Konfiguration wird der Container mit 1 GB RAM und einer grundlegenden Integritätsprüfung über den Elasticsearch-Port ausgeführt.
  
   ```JSON
   {
    "id": "elasticsearch",
    "instances": 1,
    "cpus": 0.2,
    "mem": 1024.0,
    "container": {
      "docker": {
        "image": "magneticio/elastic:2.2",
        "network": "HOST",
        "forcePullImage": true
      }
    },
    "healthChecks": [
      {
        "protocol": "TCP",
        "gracePeriodSeconds": 30,
        "intervalSeconds": 10,
        "timeoutSeconds": 5,
        "port": 9200,
        "maxConsecutiveFailures": 0
      }
    ]
   }
   ```
  

3. Klicken Sie auf **Bereitstellen**.

   DC/OS stellt den Elasticsearch-Container bereit. Der Fortschritt kann auf der Seite **Dienste** nachverfolgt werden.  

   ![Bereitstellen von Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Bereitstellen von VAMP

Sobald Elasticsearch als **Wird ausgeführt** gemeldet wird, können Sie das VAMP-DC/OS-Universe-Paket hinzufügen. 

1. Navigieren Sie zu **Universe**, und suchen Sie nach **VAMP**. 
   ![VAMP in DC/OS Universe](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Klicken Sie neben dem VAMP-Paket auf **Installieren**, und wählen Sie **Erweiterte Installation** aus.

3. Scrollen Sie nach unten, und geben Sie folgende Elasticsearch-URL ein: `http://elasticsearch.marathon.mesos:9200`. 

   ![Eingeben der Elasticsearch-URL](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Klicken Sie auf **Überprüfen und installieren**, und dann auf **Installieren**, um die Bereitstellung zu starten.  

   DC/OS stellt alle erforderlichen VAMP-Komponenten bereit. Der Fortschritt kann auf der Seite **Dienste** nachverfolgt werden.
  
   ![Bereitstellen von VAMP als Universe-Paket](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Nach Abschluss der Bereitstellung können Sie auf die VAMP-Benutzeroberfläche zugreifen:

   ![VAMP-Dienst in DC/OS](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
   ![VAMP-Benutzeroberfläche](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Bereitstellen des ersten Diensts

Stellen Sie nach der Einrichtung von VAMP einen Dienst aus einer Blaupause bereit. 

In der einfachsten Form beschreibt eine [VAMP-Blaupause](https://vamp.io/documentation/using-vamp/blueprints/) die bereitzustellenden Endpunkte (Gateways), Cluster und Dienste. VAMP verwendet Cluster, um unterschiedliche Varianten desselben Diensts zur Durchführung eines Canary-Release oder A/B-Tests in logische Gruppen zu gruppieren.  

In diesem Szenario wird eine monolithische Beispielanwendung namens [ **sava**](https://github.com/magneticio/sava) in der Version 1.0 verwendet. Die monolithische Anwendung wird in einem Docker-Container verpackt, der sich im Docker-Hub unter „magneticio/sava:1.0.0“ befindet. Die App wird normalerweise auf Port 8080 ausgeführt, Sie sollten sie in diesem Fall jedoch über Port 9050 bereitstellen. Stellen Sie die App mithilfe einer einfachen Blaupause über VAMP bereit.

1. Navigieren Sie zu **Bereitstellungen**.

2. Klicken Sie auf **Hinzufügen**.

3. Fügen Sie die folgende YAML-Blaupausendatei ein. Diese Blaupause enthält einen Cluster mit nur einer Dienstvariante, die wir in einem späteren Schritt ändern:

   ```YAML
   name: sava                        # deployment name
   gateways:
    9050: sava_cluster/webport      # stable endpoint
   clusters:
    sava_cluster:               # cluster to create
     services:
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
   ```

4. Klicken Sie auf **Speichern**. VAMP initiiert die Bereitstellung.

Die Bereitstellung wird auf der Seite **Bereitstellungen** aufgeführt. Klicken Sie auf die Bereitstellung, um den jeweiligen Status zu überwachen.

![VAMP-Benutzeroberfläche – Bereitstellen von sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![sava-Dienst auf der VAMP-Benutzeroberfläche](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Zwei Gateways wurden erstellt, die auf der Seite **Gateways** aufgeführt werden:

* ein stabiler Endpunkt für den Zugriff auf den ausgeführten Dienst (Port 9050) 
* ein mit VAMP verwaltetes internes Gateway (weitere Informationen zu diesem Gateway folgen noch) 

![VAMP-Benutzeroberfläche – sava-Gateways](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

Der sava-Dienst wurde nun bereitgestellt, Sie können jedoch nicht extern darauf zugreifen, da Azure Load Balancer noch keinen Datenverkehr an diesen weiterleiten kann. Um auf den Dienst zugreifen zu können, aktualisieren Sie die Azure-Netzwerkkonfiguration.


## <a name="update-the-azure-network-configuration"></a>Aktualisieren der Azure-Netzwerkkonfiguration

VAMP hat den sava-Dienst in den DC/OS-Agent-Knoten bereitgestellt, die einen stabilen Endpunkt über Port 9050 bereitstellen. Um außerhalb des DC/OS-Clusters auf den Dienst zugreifen zu können, führen Sie folgende Änderungen an der Konfiguration des Azure-Netzwerks in Ihrer Clusterbereitstellung durch: 

1. **Konfigurieren Sie Azure Load Balancer** für die Agents (die Ressource mit dem Namen **dcos-agent-lb-xxxx**) mit einem Integritätstest und einer Regel zur Weiterleitung des Datenverkehrs über Port 9050 an die sava-Instanzen. 

2. **Aktualisieren Sie die Netzwerksicherheitsgruppe** für die öffentlichen Agents (die Ressource mit dem Namen **XXXX-agent-public-nsg-XXXX**), um den Datenverkehr über Port 9050 zuzulassen.

Ausführliche Schritte zur Durchführung dieser Aufgaben mithilfe des Azure-Portals finden Sie unter [Aktivieren des öffentlichen Zugriffs auf eine Azure Container Service-Anwendung](container-service-enable-public-access.md). Legen Sie Port 9050 für alle Porteinstellungen fest.


Nachdem alle Komponenten erstellt wurden, navigieren Sie zum Blatt **Übersicht** des DC/OS-Agent-Lastenausgleichs (die Ressource mit dem Namen **dcos-agent-lb-xxxx**). Suchen Sie nach **Öffentliche IP-Adresse**, und verwenden Sie die Adresse, um über Port 9050 auf sava zuzugreifen.

![Azure-Portal – Abrufen der öffentlichen IP-Adresse](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Ausführen eines Canary-Release

Nehmen wir an, Sie besitzen eine neue Version dieser Anwendung, für die Sie ein Canary-Release im Produktivbetrieb durchführen möchten. Sie haben diese als Container unter „magneticio/sava:1.1.0“ verwaltet, sodass Sie nun loslegen können. Mit VAMP können Sie problemlos neue Dienste zur ausgeführten Bereitstellung hinzufügen. Diese „zusammengeführten“ Dienste werden zusammen mit den vorhandenen Diensten im Cluster bereitgestellt. Ihnen wird eine Gewichtung von 0 % zugewiesen. Es wird erst Datenverkehr an einen neu zusammengeführten Dienst weitergeleitet, bis Sie die Verteilung des Datenverkehrs anpassen. Der Schieberegler zur Gewichtung auf der VAMP-Benutzeroberfläche ermöglicht Ihnen eine umfassende Steuerung der Verteilung, wie etwa inkrementelle Anpassungen (Canary-Release) oder ein sofortiges Rollback.

### <a name="merge-a-new-service-variant"></a>Zusammenführen einer neuen Dienstvariante

So führen Sie den neuen sava 1.1-Dienst mit der ausgeführten Bereitstellung zusammen:

1. Klicken Sie auf der VAMP-Benutzeroberfläche auf **Blaupausen**.

2. Klicken Sie auf **Hinzufügen**, und fügen Sie die folgende YAML-Datei der Blaupause ein: Diese Blaupause beschreibt eine neue Dienstvariante (sava:1.1.0) für die Bereitstellung des vorhandenen Clusters (sava_cluster).

   ```YAML
   name: sava:1.1.0      # blueprint name
   clusters:
    sava_cluster:       # cluster to update
      services:
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
   ```
  
3. Klicken Sie auf **Speichern**. Die Blaupause wird auf der Seite **Blaupausen** gespeichert und aufgeführt.

4. Öffnen Sie das Aktionsmenü in der sava:1.1-Blaupause, und klicken Sie auf **Zusammenführen zu**.

   ![VAMP-Benutzeroberfläche – Blaupausen](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Wählen Sie die **sava**-Bereitstellung aus, und klicken Sie auf **Zusammenführen**.

   ![VAMP-Benutzeroberfläche – Zusammenführen der Blaupause für die Bereitstellung](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

VAMP stellt die neue Dienstvariante „sava:1.1.0“ bereit, die in der Blaupause zusammen mit sava:1.0.0 im **sava_cluster** der ausgeführten Bereitstellung beschrieben wird. 

![VAMP-Benutzeroberfläche – Aktualisierte sava-Bereitstellung](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

Das Gateway **sava/sava_cluster/webport** (der Clusterendpunkt) wird ebenfalls aktualisiert, sodass eine Route zur neu bereitgestellten Dienstvariante sava:1.1.0 hinzugefügt wird. An dieser Stelle wird kein Datenverkehr hierhin weitergeleitet (die **GEWICHTUNG** wird auf 0 % festgelegt).

![VAMP-Benutzeroberfläche – Clustergateway](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Canary-Release

Stellen Sie bei beiden sava-Versionen, die im selben Cluster bereitgestellt werden, die Verteilung des Datenverkehrs zwischen ihnen ein, indem Sie den Schieberegler **GEWICHTUNG** verschieben.

1. Klicken Sie neben **GEWICHTUNG** auf ![VAMP-Benutzeroberfläche – Bearbeiten](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png).

2. Legen Sie die Gewichtsverteilung auf 50/50 % fest, und klicken Sie auf **Speichern**.

   ![VAMP-Benutzeroberfläche – Schieberegler für die Gewichtung des Gateways](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Kehren Sie zu Ihrem Browser zurück, und aktualisieren Sie die sava-Seite einige Male. Die sava-Anwendung wechselt nun zwischen einer sava:1.0-Seite und einer sava:1.1-Seite.

   ![Wechseln zwischen den Diensten sava1.0 und sava1.1](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Dieser Seitenwechsel funktioniert aufgrund der Zwischenspeicherung statischer Objekte am besten mit den Modi „Inkognito“ oder „Anonym“ Ihres Browsers.
  >

### <a name="filter-traffic"></a>Filtern von Datenverkehr

Nehmen wir an, Sie haben nach der Bereitstellung eine Inkompatibilität in sava:1.1.0 festgestellt, die zu Anzeigeproblemen in Firefox-Browsern führt. Sie können VAMP so konfigurieren, dass eingehender Datenverkehr gefiltert und alle Firefox-Benutzer wieder zur bekannten stabilen sava:1.0.0-Seite umgeleitet werden. Dieser Filter behebt umgehend die Unterbrechung für Firefox-Benutzer, während alle anderen Benutzer weiterhin von den Vorteilen der verbesserten sava:1.1.0-Seite profitieren.

VAMP verwendet **Bedingungen** zum Filtern des Datenverkehrs zwischen Routen in einem Gateway. Der Datenverkehr wird zuerst gefiltert und gemäß den Bedingungen, die auf jede Route angewendet werden, umgeleitet. Der gesamte verbleibende Datenverkehr wird gemäß der Einstellung der Gatewaygewichtung verteilt.

Sie können eine Bedingung erstellen, um alle Firefox-Benutzer zu filtern und an die alte sava:1.0.0-Seite umzuleiten:

1. Klicken Sie auf der Seite **Gateways** unter „sava/sava_cluster/webport“ auf ![VAMP-Benutzeroberfläche – Bearbeiten](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png), um eine **BEDINGUNG** zur Route „sava/sava_cluster/sava:1.0.0/webport“ hinzuzufügen. 

2. Geben Sie die Bedingung **user-agent == Firefox** ein, und klicken Sie auf ![VAMP-Benutzeroberfläche – Speichern](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

   VAMP fügt die Bedingung mit einer Standardstärke von 0 % hinzu. Um die Filterung des Datenverkehrs zu starten, müssen Sie die Stärke der Bedingung anzupassen.

3. Klicken Sie auf ![VAMP-Benutzeroberfläche – Bearbeiten](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png), um die auf die Bedingung angewendete **STÄRKE** zu ändern.
 
4. Legen Sie die **STÄRKE** auf 100 % fest, und klicken Sie zum Speichern auf ![VAMP-Benutzeroberfläche – Speichern](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

   VAMP sendet nun sämtlichen Datenverkehr, der die Bedingung erfüllt (alle Firefox-Benutzer), an sava:1.0.0.

   ![VAMP-Benutzeroberfläche – Anwenden der Bedingung auf das Gateway](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Abschließend stellen Sie die Gewichtung des Gateways so ein, dass sämtlicher verbleibender Datenverkehr (alle Benutzer, die kein Firefox verwenden) an die neue sava:1.1.0-Seite gesendet wird. Klicken Sie neben **GEWICHTUNG** auf ![VAMP-Benutzeroberfläche – Bearbeiten](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png), und legen Sie die Gewichtungsverteilung so fest, dass der gesamte Datenverkehr an die Route „sava/sava_cluster/sava:1.1.0/webport“ umgeleitet wird.

   Sämtlicher Datenverkehr, der nicht von der Bedingung gefiltert wurde, wird nun an die neue sava:1.1.0-Seite umgeleitet.

6. Um den Filter selbst anzuwenden, öffnen Sie zwei verschiedene Browser (einen Firefox- und einen anderen Browser), und rufen Sie über beide Browser den sava-Dienst auf. Alle Firefox-Anforderungen werden an sava:1.0.0 gesendet, während alle anderen Browser an sava:1.1.0 umgeleitet werden.

   ![VAMP-Benutzeroberfläche – Filtern von Datenverkehr](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Zusammenfassung

Dieser Artikel enthält eine kurze Einführung zu VAMP in einem DC/OS-Cluster. Zunächst haben Sie VAMP in Ihrem Azure Container Service-DC/OS-Cluster eingerichtet, einen Dienst mit einer VAMP-Blaupause bereitgestellt und über den bereitgestellten Endpunkt (Gateway) darauf zugegriffen.

Zudem haben wir einige leistungsstarke Funktionen von VAMP besprochen, z.B. die Zusammenführung einer neuen Dienstvariante in der ausgeführten Bereitstellung, die inkrementelle Einführung und anschließend die Filterung des Datenverkehrs zur Behebung einer bekannten Inkompatibilität.


## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Verwalten von VAMP-Aktionen finden Sie unter [VAMP-REST-API](https://vamp.io/documentation/api/api-reference/).

* Erstellen Sie VAMP-Automatisierungsskripts in Node.js, und führen Sie sie als [VAMP-Workflows](https://vamp.io/documentation/using-vamp/v1.0.0/workflows/#create-a-workflow) aus.

* Weitere Informationen finden Sie in den zusätzlichen [VAMP-Tutorials](https://vamp.io/documentation/tutorials/).


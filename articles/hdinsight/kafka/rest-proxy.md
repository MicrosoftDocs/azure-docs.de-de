---
title: Apache Kafka-REST-Proxy – Azure HDInsight
description: Erfahren Sie, wie Sie Apache Kafka-Vorgänge mithilfe eines Kafka-REST-Proxys in Azure HDInsight ausführen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: how-to
ms.custom: has-adal-ref, devx-track-python
ms.date: 04/03/2020
ms.openlocfilehash: d6c45a5c8062c3b6441309361037f8755a552074
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95791897"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interagieren mit Apache Kafka-Clustern in Azure HDInsight mithilfe eines REST-Proxys

Mithilfe des REST-Proxys von Kafka können Sie über eine REST-API per HTTPS mit Ihrem Kafka-Cluster interagieren. Das bedeutet, dass sich Ihre Kafka-Clients außerhalb des virtuellen Netzwerks befinden können. Clients können einfache sichere HTTPS-Aufrufe an den Kafka-Cluster richten, statt Kafka-Bibliotheken zu verwenden. In diesem Artikel erfahren Sie, wie Sie einen REST-Proxy-fähigen Kafka-Cluster erstellen. Er enthält auch Beispielcode, der zeigt, wie der REST-Proxy aufgerufen werden kann.

## <a name="rest-api-reference"></a>REST-API-Referenz

Informationen zu den Vorgängen, die von der Kafka-REST-API unterstützt werden, finden Sie in der [Referenz zur HDInsight Kafka-REST-Proxy-API](/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Hintergrund

![Design für Kafka-REST-Proxy](./media/rest-proxy/rest-proxy-architecture.png)

Eine vollständige Spezifikation der Vorgänge, die von der API unterstützt werden, finden Sie unter [Apache Kafka-REST-Proxy-API](/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>REST-Proxyendpunkt

Beim Erstellen eines HDInsight Kafka-Clusters mit REST-Proxy wird ein neuer öffentlicher Endpunkt für Ihren Cluster erstellt, den Sie unter den **Eigenschaften** für Ihren HDInsight-Cluster im Azure-Portal finden können.

### <a name="security"></a>Sicherheit

Der Zugriff auf den Kafka-REST-Proxy wird mit Azure Active Directory-Sicherheitsgruppen verwaltet. Stellen Sie beim Erstellen des Kafka-Clusters die Azure AD-Sicherheitsgruppe mit Zugriff auf den REST-Endpunkt bereit. Kafka-Clients, die Zugriff auf den REST-Proxy benötigen, sollten für diese Gruppe vom Gruppenbesitzer registriert werden. Der Gruppenbesitzer kann die Registrierung über das Portal oder über PowerShell vornehmen.

Für REST-Proxy-Endpunktanforderungen sollten Clientanwendungen ein OAuth-Token abrufen. Das Token wird verwendet, um die Mitgliedschaft in Sicherheitsgruppen zu überprüfen. Weiter unten finden Sie ein [Beispiel für eine Clientanwendung](#client-application-sample), in dem das Abrufen eines OAuth-Tokens gezeigt wird. Die Clientanwendung übergibt das OAuth-Token in der HTTPS-Anforderung an den REST-Proxy.

> [!NOTE]
> Weitere Informationen zu AAD-Sicherheitsgruppen finden Sie unter [Verwalten des Zugriffs auf Apps und Ressourcen mithilfe von Azure Active Directory-Gruppen](../../active-directory/fundamentals/active-directory-manage-groups.md). Weitere Informationen zur Funktionsweise von OAuth-Token finden Sie unter [Autorisieren des Zugriffs auf Azure Active Directory-Webanwendungen mit dem Flow zum Erteilen des OAuth 2.0-Codes](../../active-directory/azuread-dev/v1-protocols-oauth-code.md).

## <a name="kafka-rest-proxy-with-network-security-groups"></a>Kafka REST-Proxy mit Netzwerksicherheitsgruppen
Wenn Sie Ihr eigenes virtuelles Netzwerk bereitstellen und den Netzwerkdatenverkehr mit Netzwerksicherheitsgruppen steuern, lassen Sie **eingehenden** Datenverkehr zusätzlich zu Port 443 auch an Port **9400** zu. Dadurch wird sichergestellt, dass der Kafka REST-Proxyserver erreichbar ist.

## <a name="prerequisites"></a>Voraussetzungen

1. Registrieren Sie eine Anwendung in Azure AD. Die Clientanwendungen, die Sie für die Interaktion mit dem Kafka-REST-Proxy schreiben, verwenden die ID und den geheimen Schlüssel dieser Anwendung zur Authentifizierung bei Azure.

1. Erstellen Sie eine Azure AD-Sicherheitsgruppe. Fügen Sie die Anwendung, die Sie in Azure AD registriert haben, der Sicherheitsgruppe als **Mitglied** der Gruppe hinzu. Mit dieser Sicherheitsgruppe wird gesteuert, welche Anwendungen mit dem REST-Proxy interagieren dürfen. Weitere Informationen zum Erstellen von Azure AD-Gruppen finden Sie unter [Erstellen einer einfachen Gruppe und Hinzufügen von Mitgliedern mithilfe von Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Überprüfen Sie, ob die Gruppe vom Typ **Sicherheit** ist.
    ![Sicherheitsgruppe](./media/rest-proxy/rest-proxy-group.png)

    Überprüfen Sie, ob die Anwendung Mitglied der Gruppe ist.
    ![Überprüfen der Mitgliedschaft](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Erstellen eines Kafka-Clusters mit aktiviertem REST-Proxy

Die folgenden Schritte verwenden das Azure-Portal. Ein Beispiel für die Verwendung der Azure-Befehlszeilenschnittstelle finden Sie unter [Erstellen eines Apache Kafka REST-Proxycluster mithilfe der Azure-Befehlszeilenschnittstelle](tutorial-cli-rest-proxy.md).

1. Aktivieren Sie während des Workflows zum Erstellen eines Kafka-Clusters auf der Registerkarte **Sicherheit + Netzwerkbetrieb** die Option **Kafka-REST-Proxy aktivieren**.

     ![Screenshot der Seite „HDInsight-Cluster erstellen“ mit der ausgewählten Registerkarte „Sicherheit + Netzwerkbetrieb“](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Klicken Sie auf **Sicherheitsgruppe auswählen**. Wählen Sie in der Liste der Sicherheitsgruppen die Sicherheitsgruppe aus, die Zugriff auf den REST-Proxy haben soll. Sie können das Suchfeld verwenden, um nach der entsprechenden Sicherheitsgruppe zu suchen. Klicken Sie unten auf die Schaltfläche **Auswählen**.

     ![Screenshot der Seite „HDInsight-Cluster erstellen“ mit der Option zum Auswählen einer Sicherheitsgruppe](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Führen Sie die restlichen Schritte zum Erstellen Ihres Clusters aus, wie es unter [Erstellen eines Apache Kafka-Clusters in Azure HDInsight im Azure-Portal](./apache-kafka-get-started.md) beschrieben ist.

1. Nachdem der Cluster erstellt wurde, wechseln Sie zu den Clustereigenschaften, um die URL für den Kafka-REST-Proxy zu erfassen.

     ![Anzeigen der REST-Proxy-URL](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Beispiel für eine Clientanwendung

Sie können den unten gezeigten Python-Code verwenden, um mit dem REST-Proxy in Ihrem Kafka-Cluster zu interagieren. Gehen Sie zum Verwenden des Beispielcodes folgendermaßen vor:

1. Speichern Sie den Beispielcode auf einem Computer mit installiertem Python.
1. Installieren Sie erforderliche Python-Abhängigkeiten, indem Sie `pip3 install msal` ausführen.
1. Ändern Sie den Codeabschnitt **Configure these properties**, und aktualisieren Sie die folgenden Eigenschaften entsprechend Ihrer Umgebung:

    |Eigenschaft |BESCHREIBUNG |
    |---|---|
    |Mandanten-ID|Der Azure-Mandant, in dem sich Ihr Abonnement befindet.|
    |Client-ID|Die ID für die Anwendung, die Sie in der Sicherheitsgruppe registriert haben.|
    |Geheimer Clientschlüssel|Der geheime Schlüssel für die Anwendung, die Sie in der Sicherheitsgruppe registriert haben.|
    |Kafkarest_endpoint|Entnehmen Sie diesen Wert der Registerkarte **Eigenschaften** in der Clusterübersicht, wie im [Abschnitt zur Bereitstellung](#create-a-kafka-cluster-with-rest-proxy-enabled) beschrieben. Er sollte das folgende Format aufweisen: `https://<clustername>-kafkarest.azurehdinsight.net`|

1. Führen Sie die Python-Datei über die Befehlszeile aus, indem Sie `sudo python3 <filename.py>` ausführen.

Im Code wird Folgendes ausgeführt:

1. Abrufen eines OAuth-Tokens von Azure AD
1. Senden einer Anforderung an den Kafka-REST-Proxy

Weitere Informationen zum Abrufen von OAuth-Token in Python finden Sie unter der [Klasse „AuthenticationContext“ in Python](/python/api/adal/adal.authentication_context.authenticationcontext). Es tritt möglicherweise eine Verzögerung auf, während `topics`, die nicht über den Kafka-REST-Proxy erstellt oder gelöscht werden, dort widergespiegelt werden. Die Ursache für diese Verzögerung ist eine Cacheaktualisierung. Das Feld **Wert** der Producer-API wurde erweitert. Nun werden JSON-Objekte und alle serialisierten Formate akzeptiert.

```python
#Required python packages
#pip3 install msal

import json
import msal
import random
import requests
import string
import sys
import time

def get_random_string():
    letters = string.ascii_letters
    random_string = ''.join(random.choice(letters) for i in range(7))

    return random_string


#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Get access token
# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
)

# The pattern to acquire a token looks like this.
result = None
result = app.acquire_token_for_client(scopes=[scope])
accessToken = result['access_token']
verify_https = True
request_timeout = 10

# Print access token
print("Access token: " + accessToken)

# API format
api_version = 'v1'
api_format = kafkarest_endpoint + '/{api_version}/{rest_api}'
get_topic_api = 'metadata/topics'
topic_api_format = 'topics/{topic_name}'
producer_api_format = 'producer/topics/{topic_name}'
consumer_api_format = 'consumer/topics/{topic_name}/partitions/{partition_id}/offsets/{offset}?count={count}'  # by default count = 1

# Request header
headers = {
    'Authorization': 'Bearer ' + accessToken,
    'Content-type': 'application/json'          # set Content-type to 'application/json'
}

# New topic
new_topic = 'hello_topic_' + get_random_string()
print("Topic " + new_topic + " is going to be used for demo.")

topics = []

# Create a  new topic
# Example of topic config
topic_config = {
    "partition_count": 1,
    "replication_factor": 1,
    "topic_properties": {
        "retention.ms": 604800000,
        "min.insync.replicas": "1"
    }
}

create_topic_url = api_format.format(api_version=api_version, rest_api=topic_api_format.format(topic_name=new_topic))
response = requests.put(create_topic_url, headers=headers, json=topic_config, timeout=request_timeout, verify=verify_https)
print(response.content)

if response.ok:
    while new_topic not in topics:
        print("The new topic " + new_topic + " is not visible yet. sleep 30 seconds...")
        time.sleep(30)
        # List Topic
        get_topic_url = api_format.format(api_version=api_version, rest_api=get_topic_api)

        response = requests.get(get_topic_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
        topic_list = response.json()
        topics = topic_list.get("topics", [])
else:
    print("Topic " + new_topic + " was created. Exit.")
    sys.exit(1)

# Produce messages to new_topic
# Example payload of Producer REST API
payload_json = {
    "records": [
        {
            "key": "key1",
            "value": "**********"         # A string                              
        },
        {
            "partition": 0,
            "value": 5                    # An integer
        },
        {
            "value": 3.14                 # A floating number
        },
        {
            "value": {                    # A JSON object
                "id": 1,
                "name": "HDInsight Kafka REST proxy"
            }
        },
        {
            "value": [                    # A list of JSON objects
                {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                {
                    "id": 2,
                    "name": "HDInsight Kafka REST proxy 2"
                },
                {
                    "id": 3,
                    "name": "HDInsight Kafka REST proxy 3"
                }
            ]
        },
        {
            "value": {                  # A nested JSON object
                "group id": 1,
                "HDI Kafka REST": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                "HDI Kafka REST server info": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1",
                    "servers": [
                        {
                            "server id": 1,
                            "server name": "HDInsight Kafka REST proxy server 1"
                        },
                        {
                            "server id": 2,
                            "server name": "HDInsight Kafka REST proxy server 2"
                        },
                        {
                            "server id": 3,
                            "server name": "HDInsight Kafka REST proxy server 3"
                        }
                    ]
                }
            }
        }
    ]
}

print("Payloads in a Producer request: \n", payload_json)
producer_url = api_format.format(api_version=api_version, rest_api=producer_api_format.format(topic_name=new_topic))
response = requests.post(producer_url, headers=headers, json=payload_json, timeout=request_timeout, verify=verify_https)
print(response.content)

# Consume messages from the topic
partition_id = 0
offset = 0
count = 2

while True:
    consumer_url = api_format.format(api_version=api_version, rest_api=consumer_api_format.format(topic_name=new_topic, partition_id=partition_id, offset=offset, count=count))
    print("Consuming " + str(count) + " messages from offset " + str(offset))

    response = requests.get(consumer_url, headers=headers, timeout=request_timeout, verify=verify_https)

    if response.ok:
        messages = response.json()
        print("Consumed messages: \n" + json.dumps(messages, indent=2))
        next_offset = response.headers.get("NextOffset")
        if offset == next_offset or not messages.get("records", []):
            print("Consumer caught up with producer. Exit for now...")
            break

        offset = next_offset

    else:
        print("Error " + str(response.status_code))
        break
        
# List partitions
get_partitions_url = api_format.format(api_version=api_version, rest_api=partitions_api_format.format(topic_name=new_topic))
print("Fetching partitions from  " + get_partitions_url)

response = requests.get(get_partitions_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition_list = response.json()
print("Partition list: \n" + json.dumps(partition_list, indent=2))

# List a partition
get_partition_url = api_format.format(api_version=api_version, rest_api=partition_api_format.format(topic_name=new_topic, partition_id=partition_id))
print("Fetching metadata of a partition from  " + get_partition_url)

response = requests.get(get_partition_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition = response.json()
print("Partition metadata: \n" + json.dumps(partition, indent=2))

```

Weiter unten finden Sie ein weiteres Beispiel zum Abrufen eines Tokens von Azure für den REST-Proxy mithilfe eines curl-Befehls. **Beachten Sie, dass beim Abrufen des Tokens `scope=https://hib.azurehdinsight.net/.default` angegeben werden muss.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Nächste Schritte

* [Referenzdokumente zur Kafka-REST-Proxy-API](/rest/api/hdinsight-kafka-rest-proxy/)

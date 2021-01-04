---
title: Konfigurieren des API-Proxymoduls – Azure IoT Edge | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie das API-Proxymodul für IoT Edge-Gatewayhierarchien anpassen.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 894398d63e326db3c6ee9de9bebc426a6e621600
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024669"
---
# <a name="configure-the-api-proxy-module-for-your-gateway-hierarchy-scenario-preview"></a>Konfigurieren des API-Proxymoduls für Ihr Gatewayhierarchieszenario (Vorschau)

Das API-Proxymodul ermöglicht IoT Edge-Geräten das Senden von HTTP-Anforderungen über Gateways, statt direkte Verbindungen mit Clouddiensten herstellen zu müssen. In diesem Artikel werden die Konfigurationsoptionen erörtert, damit Sie das Modul anpassen können, um die Anforderungen Ihrer Gatewayhierarchie zu unterstützen.

>[!NOTE]
>Dieses Feature erfordert die IoT Edge-Version 1.2, die sich in der öffentlichen Vorschau befindet und Linux-Container ausführt.

In einigen Netzwerkarchitekturen verfügen IoT Edge-Geräte hinter Gateways nicht über direkten Zugriff auf die Cloud. Alle Module, die versuchen, eine Verbindung mit Clouddiensten herzustellen, können nicht ausgeführt werden. Das API-Proxymodul unterstützt nachgeschaltete IoT Edge-Geräte in dieser Konfiguration, indem die Modulverbindungen stattdessen so weitergeleitet werden, dass sie die Ebenen einer Gatewayhierarchie durchlaufen. Es ermöglicht Clients die sichere Kommunikation mit mehreren Diensten über HTTPS ohne Tunnelung, sondern durch Beenden der Verbindungen auf jeder Ebene. Das API-Proxymodul fungiert als Proxymodul zwischen den IoT Edge-Geräten in einer Gatewayhierarchie, bis sie das IoT Edge-Gerät der obersten Ebene erreichen. An diesem Punkt werden diese Anforderungen von auf dem IoT Edge-Gerät der obersten Ebene ausgeführten Diensten verarbeitet, und das API-Proxymodul sendet den gesamten HTTP-Datenverkehr von lokalen Diensten über einen einzigen Port an die Cloud.

Das API-Proxymodul kann viele Szenarien für Gatewayhierarchien unterstützen und beispielsweise Geräten niedrigerer Ebenen das Pullen von Containerimages oder das Pushen von Blobs in den Speicher ermöglichen. Die Konfiguration der Proxyregeln definiert, wie Daten weitergeleitet werden. In diesem Artikel werden mehrere gängige Konfigurationsoptionen erläutert.

## <a name="deploy-the-proxy-module"></a>Bereitstellen des Proxymoduls

Das API-Proxymodul ist über Microsoft Container Registry (MCR) verfügbar: `mcr.microsoft.com/azureiotedge-api-proxy:latest`.

Sie können das API-Proxymodul auch direkt über Azure Marketplace bereitstellen: [IoT Edge-API-Proxy](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview).

## <a name="understand-the-proxy-module"></a>Grundlegendes zum Proxymodul

Das API-Proxymodul nutzt einen nginx-Reverseproxy zum Weiterleiten von Daten über Netzwerkebenen. Ein Proxy ist in das Modul eingebettet, was bedeutet, dass das Modulimage die Proxykonfiguration unterstützen muss. Wenn der Proxy z. B. an einem bestimmten Port lauscht, muss das Modul diesen Port geöffnet haben.

Der Proxy beginnt mit einer in das Modul eingebetteten Standardkonfigurationsdatei. Sie können mithilfe des [Modulzwillings](../iot-hub/iot-hub-devguide-module-twins.md) über die Cloud eine neue Konfiguration an das Modul übergeben. Darüber hinaus können Sie Umgebungsvariablen verwenden, um Konfigurationseinstellungen zum Zeitpunkt der Bereitstellung zu aktivieren oder zu deaktivieren.

In diesem Artikel konzentrieren wir uns zunächst auf die Standardkonfigurationsdatei und auf die Verwendung von Umgebungsvariablen zum Aktivieren der Einstellungen. Und danach erläutern wir, wie die Konfigurationsdatei angepasst werden kann.

### <a name="default-configuration"></a>Standardkonfiguration

Das API-Proxymodul wird mit einer Standardkonfiguration bereitgestellt, die gängige Szenarien unterstützt und angepasst werden kann. Sie können die Standardkonfiguration über die Umgebungsvariablen des Moduls steuern.

Derzeit umfassen die Standardumgebungsvariablen Folgendes:

| Umgebungsvariable | BESCHREIBUNG |
| -------------------- | ----------- |
| `PROXY_CONFIG_ENV_VAR_LIST` | Listet alle Variablen auf, die Sie in einer durch Trennzeichen getrennten Liste aktualisieren möchten. Durch diesen Schritt wird verhindert, dass versehentlich die falschen Konfigurationseinstellungen geändert werden.
| `NGINX_DEFAULT_PORT` | Ändert den Port, an dem der nginx-Proxy lauscht. Wenn Sie diese Umgebungsvariable aktualisieren, stellen Sie sicher, dass der von Ihnen ausgewählte Port auch in der Dockerfile des Moduls verfügbar gemacht und im Bereitstellungsmanifest als Portbindung deklariert wird.<br><br>Der Standardport ist 443.<br><br>Wenn die Bereitstellung über Azure Marketplace erfolgt, wird der Standardport auf 8000 aktualisiert, um Konflikte mit dem edgeHub-Modul zu vermeiden. Weitere Informationen finden Sie unter [Minimieren offener Ports](#minimize-open-ports). |
| `DOCKER_REQUEST_ROUTE_ADDRESS` | Adresse zum Weiterleiten von Docker-Anforderungen. Ändern Sie diese Variable auf dem Gerät der obersten Ebene so, dass sie auf das Registrierungsmodul verweist.<br><br>Standardmäßig wird der übergeordnete Hostname verwendet. |
| `BLOB_UPLOAD_ROUTE_ADDRESS` | Adresse zum Weiterleiten von Blobregistrierungsanforderungen. Ändern Sie diese Variable auf dem Gerät der obersten Ebene so, dass sie auf das Blobspeichermodul verweist.<br><br>Standardmäßig wird der übergeordnete Hostname verwendet. |

## <a name="minimize-open-ports"></a>Minimieren offener Ports

Um die Anzahl offener Ports zu minimieren, sollte das API-Proxymodul den gesamten HTTPS-Datenverkehr (Port 443) einschließlich des Datenverkehrs für das edgeHub-Modul weiterleiten. Das API-Proxymodul ist standardmäßig so konfiguriert, dass der gesamte edgeHub-Datenverkehr an Port 443 umgeleitet wird.

Führen Sie die folgenden Schritte aus, um Ihre Bereitstellung so zu konfigurieren, dass die Anzahl offener Ports minimiert wird:

1. Aktualisieren Sie die Einstellungen des edgeHub-Moduls so, dass keine Bindung an Port 443 erfolgt. Andernfalls treten Portbindungskonflikte auf. Standardmäßig bindet das edgeHub-Modul an die Ports 443, 5671 und 8883. Löschen Sie die Bindung an Port 443, und lassen Sie die anderen beiden bestehen:

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Konfigurieren Sie das API-Proxymodul für die Bindung an Port 443.

   1. Legen Sie den Wert der Umgebungsvariable **NGINX_DEFAULT_PORT** auf `443` fest.
   1. Aktualisieren Sie die Optionen für die Containererstellung so, dass die Bindung an Port 443 erfolgt.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

Wenn Sie die Anzahl offener Ports nicht minimieren müssen, können Sie zulassen, dass das edgeHub-Modul Port 443 verwendet, und konfigurieren Sie das API-Proxymodul so, dass es an einem anderen Port lauscht. Beispielsweise kann das API-Proxymodul an Port 8000 lauschen, indem der Wert der Umgebungsvariable **NGINX_DEFAULT_PORT** auf `8000` festgelegt und eine Portbindung für Port 8000 erstellt wird.

## <a name="enable-container-image-download"></a>Ermöglichen des Herunterladens von Containerimages

Ein häufiger Anwendungsfall für das API-Proxymodul besteht darin, IoT Edge-Geräten niedrigerer Ebenen das Pullen von Containerimages zu ermöglichen. In diesem Szenario wird das [Docker-Registrierungsmodul](https://hub.docker.com/_/registry) verwendet, um Containerimages aus der Cloud abzurufen und auf der obersten Ebene zwischenzuspeichern. Der API-Proxy leitet alle HTTPS-Anforderungen zum Herunterladen eines Containerimages aus den unteren Ebenen weiter, um durch das Registrierungsmodul auf der obersten Ebene bedient zu werden.

Dieses Szenario erfordert, dass nachgeschaltete IoT Edge-Geräte auf den Domänennamen `$upstream` gefolgt von der Portnummer des API-Proxymoduls anstelle der Containerregistrierung eines Images verweisen. Beispiel: `$upstream:8000/azureiotedge-api-proxy:1.0`.

Dieser Anwendungsfall wird im Tutorial [Erstellen einer Hierarchie von IoT Edge-Geräten mit Gateways](tutorial-nested-iot-edge.md) veranschaulicht.

Konfigurieren Sie die folgenden Module auf der **obersten Ebene**:

* Ein Docker-Registrierungsmodul
  * Konfigurieren Sie das Modul mit einem einprägsamen Namen wie *Registrierung*, und machen Sie für den Empfang von Anforderungen einen Port im Modul verfügbar.
  * Konfigurieren Sie das Modul für die Zuordnung zu Ihrer Containerregistrierung.
* Ein API-Proxymodul
  * Konfigurieren Sie die folgenden Umgebungsvariablen:

    | Name | Wert |
    | ---- | ----- |
    | `DOCKER_REQUEST_ROUTE_ADDRESS` | Der Name des Registrierungsmoduls und der offene Port. Beispiel: `registry:5000`. |
    | `NGINX_DEFAULT_PORT` | Der Port, an dem der nginx-Proxy nach Anforderungen von nachgeschalteten Geräten lauscht. Beispiel: `8000`. |

  * Konfigurieren Sie die folgenden Erstellungsoptionen:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Konfigurieren Sie das folgende Modul auf allen **unteren Ebenen** für dieses Szenario:

* Ein API-Proxymodul
  * Konfigurieren Sie die folgenden Umgebungsvariablen:

    | Name | Wert |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Der Port, an dem der nginx-Proxy nach Anforderungen von nachgeschalteten Geräten lauscht. Beispiel: `8000`. |

  * Konfigurieren Sie die folgenden Erstellungsoptionen:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```  

## <a name="enable-blob-upload"></a>Ermöglichen des Hochladens von Blobs

Ein weiterer Anwendungsfall für das API-Proxymodul besteht darin, IoT Edge-Geräten niedrigerer Ebenen das Hochladen von Blobs zu ermöglichen. Dieser Anwendungsfall ermöglicht die Problembehandlung auf Geräten niedrigerer Ebenen, beispielsweise das Hochladen von Modulprotokollen oder das Hochladen des Supportbundles.

In diesem Szenario wird das Modul [Azure Blob Storage on IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/azure-blob-storage.edge-azure-blob-storage) auf der obersten Ebene verwendet, um das Erstellen und Hochladen von Blobs zu verarbeiten.

Konfigurieren Sie die folgenden Module auf der **obersten Ebene**:

* Ein „Azure Blob Storage on IoT Edge“-Modul.
* Ein API-Proxymodul
  * Konfigurieren Sie die folgenden Umgebungsvariablen:

    | Name | Wert |
    | ---- | ----- |
    | `BLOB_UPLOAD_ROUTE_ADDRESS` | Der Name des Blob Storage-Moduls und der offene Port. Beispiel: `azureblobstorageoniotedge:1102`. |
    | `NGINX_DEFAULT_PORT` | Der Port, an dem der nginx-Proxy nach Anforderungen von nachgeschalteten Geräten lauscht. Beispiel: `8000`. |

  * Konfigurieren Sie die folgenden Erstellungsoptionen:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Konfigurieren Sie das folgende Modul auf allen **unteren Ebenen** für dieses Szenario:

* Ein API-Proxymodul
  * Konfigurieren Sie die folgenden Umgebungsvariablen:

    | Name | Wert |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Der Port, an dem der nginx-Proxy nach Anforderungen von nachgeschalteten Geräten lauscht. Beispiel: `8000`. |

  * Konfigurieren Sie die folgenden Erstellungsoptionen:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Führen Sie die folgenden Schritte aus, um das Supportbundle oder die Protokolldatei in das Blob Storage-Modul auf der obersten Ebene hochzuladen:

1. Erstellen Sie einen Blobcontainer. Verwenden Sie dazu entweder Azure Storage-Explorer oder die REST-APIs. Weitere Informationen finden Sie unter [Speichern von Daten im Edgebereich mit Azure Blob Storage in IoT Edge](how-to-store-data-blob.md).
1. Senden Sie eine Anforderung zum Hochladen eines Protokolls oder Supportbundles gemäß den Schritten im Artikel [Abrufen von Protokollen aus IoT Edge-Bereitstellungen](how-to-retrieve-iot-edge-logs.md), aber verwenden Sie dabei den Domänennamen `$upstream` und den offenen Proxyport anstelle der Adresse des Blob Storage-Moduls. Zum Beispiel:

   ```json
   {
      "schemaVersion": "1.0",
      "sasUrl": "https://$upstream:8000/myBlobStorageName/myContainerName?SAS_key",
      "since": "2d",
      "until": "1d",
      "edgeRuntimeOnly": false
   }
   ```

## <a name="edit-the-proxy-configuration"></a>Bearbeiten der Proxykonfiguration

In das API-Proxymodul ist eine Standardkonfigurationsdatei eingebettet, Sie können jedoch mithilfe des Modulzwillings über die Cloud eine neue Konfiguration an das Modul übergeben.

Wenn Sie Ihre eigene Konfiguration schreiben, können Sie trotzdem Umgebungsvariablen verwenden, um die Einstellungen pro Bereitstellung anzupassen. Verwenden Sie die folgende Syntax:

* Verwenden Sie `${MY_ENVIRONMENT_VARIABLE}`, um den Wert einer Umgebungsvariable abzurufen.
* Verwenden Sie bedingte Anweisungen, um Einstellungen basierend auf dem Wert einer Umgebungsvariable zu aktivieren oder zu deaktivieren:

   ```conf
   #if_tag ${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 1
   #endif_tag ${MY_ENVIRONMENT_VARIABLE}

   #if_tag !${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 0
   #endif_tag !${MY_ENVIRONMENT_VARIABLE}
   ```

Wenn das API-Proxymodul eine Proxykonfiguration analysiert, ersetzt es zuerst alle in der `PROXY_CONFIG_ENV_VAR_LIST` aufgeführten Umgebungsvariablen durch ihre bereitgestellten Werte mittels Substitution. Dann wird alles zwischen einem `#if_tag`-`#endif_tag`-Paar ersetzt. Anschließend stellt das Modul die analysierte Konfiguration für den nginx-Reverseproxy bereit.

Führen Sie die folgenden Schritte aus, um die Proxykonfiguration dynamisch zu aktualisieren:

1. Schreiben Sie Ihre Konfigurationsdatei. Sie können die folgende Standardvorlage als Referenz verwenden: [nginx_default_config.conf](https://github.com/Azure/iotedge/blob/master/edge-modules/api-proxy-module/templates/nginx_default_config.conf)
1. Kopieren Sie den Text der Konfigurationsdatei, und konvertieren Sie ihn in Base64.
1. Fügen Sie die codierte Konfigurationsdatei als Wert der gewünschten `proxy_config`-Eigenschaft in den Modulzwilling ein.

   ![Einfügen der codierten Konfigurationsdatei als Wert der „proxy_config“-Eigenschaft](./media/how-to-configure-api-proxy-module/change-config.png)

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie das API-Proxymodul zum [Verbinden eines nachgeschalteten IoT Edge-Geräts mit einem Azure IoT Edge-Gateway](how-to-connect-downstream-iot-edge-device.md).
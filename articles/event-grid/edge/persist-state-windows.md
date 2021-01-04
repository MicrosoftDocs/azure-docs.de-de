---
title: Beibehalten des Status in Windows – Azure Event Grid IoT Edge | Microsoft-Dokumentation
description: Beibehalten des Status in Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: f38e23a3af1e2c81ee012a4f3c268cbff3fc1bee
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002816"
---
# <a name="persist-state-in-windows"></a>Beibehalten des Status in Windows

Themen und Abonnements, die im Event Grid-Modul erstellt wurden, werden standardmäßig im Containerdateisystem gespeichert. Ohne Persistenz gehen beim erneuten Bereitstellen des Moduls alle erstellten Metadaten verloren. Um die Daten für mehrere Bereitstellungen und über Neustarts hinweg beizubehalten, müssen Sie sie außerhalb des Containerdateisystems persistent speichern. 

Standardmäßig werden nur Metadaten persistent gespeichert, und Ereignisse werden weiterhin im Arbeitsspeicher gespeichert, um die Leistung zu steigern. Befolgen Sie den Abschnitt zum persistenten Speichern von Ereignissen, um auch Ereignispersistenz zu aktivieren.

In diesem Artikel werden die Schritte beschrieben, die zum Bereitstellen des Event Grid-Moduls mit Persistenz in Windows-Bereitstellungen erforderlich sind.

> [!NOTE]
>Das Event Grid-Modul wird als Benutzer mit geringfügigen Rechten (**ContainerUser**) in Windows ausgeführt.

## <a name="persistence-via-volume-mount"></a>Persistenz über Volumeeinbindung

[Docker-Volumes](https://docs.docker.com/storage/volumes/) werden verwendet, um Daten über Bereitstellungen hinweg beizubehalten. Zum Bereitstellen eines Volumes müssen Sie es mit Docker-Befehlen erstellen und Berechtigungen zuweisen, sodass Lese- und Schreibvorgänge des Containers im Volume möglich sind. Anschließend müssen Sie das Modul bereitstellen.

1. Erstellen Sie ein Volume, indem Sie den folgenden Befehl ausführen:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Beispiel:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Rufen Sie das Hostverzeichnis ab, dem das Volume zugeordnet ist. Führen Sie dazu den unten stehenden Befehl aus:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Beispiel:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Beispielausgabe:

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. Fügen Sie die Gruppe **Benutzer** wie folgt dem Wert hinzu, auf den **Mountpoint** verweist:
    1. Starten Sie den Datei-Explorer.
    1. Navigieren Sie zu dem Ordner, auf den **Mountpoint** verweist.
    1. Klicken Sie mit der rechten Maustaste, und wählen Sie **Eigenschaften** aus.
    1. Wählen Sie **Sicherheit** aus.
    1. Wählen Sie unter „Gruppen- oder Benutzernamen“ die Option **Bearbeiten** aus.
    1. Wählen Sie **Hinzufügen** aus, geben Sie `Users` ein, wählen Sie **Namen überprüfen** und anschließend **OK** aus.
    1. Wählen Sie unter *Permissions for Users* (Berechtigungen für Benutzer) **Ändern** aus, und wählen Sie **OK** aus.
1. Verwenden Sie **Bindungen**, um dieses Volume bereitzustellen und das Event Grid-Modul über das Azure-Portal erneut bereitzustellen.

   Beispiel:

    ```json
        {
              "Env": [
                "inbound__serverAuth__tlsPolicy=strict",
                "inbound__serverAuth__serverCert__source=IoTEdge",
                "inbound__clientAuth__sasKeys__enabled=false",
                "inbound__clientAuth__clientCert__enabled=true",
                "inbound__clientAuth__clientCert__source=IoTEdge",
                "inbound__clientAuth__clientCert__allowUnknownCA=true",
                "outbound__clientAuth__clientCert__enabled=true",
                "outbound__clientAuth__clientCert__source=IoTEdge",
                "outbound__webhook__httpsOnly=true",
                "outbound__webhook__skipServerCertValidation=false",
                "outbound__webhook__allowUnknownCA=true"
              ],
              "HostConfig": {
                "Binds": [
                  "<your-volume-name-here>:C:\\app\\metadataDb"
                ],
                "PortBindings": {
                  "4438/tcp": [
                     {
                        "HostPort": "4438"
                     }
                  ]
                }
              }
        }
    ```

   >[!IMPORTANT]
   >Ändern Sie nicht den zweiten Teil des Bindungswerts. Er verweist auf eine bestimmte Adresse im Modul. Für das Event Grid-Modul unter Windows muss dies **C:\\app\\metadataDb** sein.


    Beispiel:

    ```json
    {
        "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
         ],
         "HostConfig": {
            "Binds": [
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
             ],
             "PortBindings": {
                    "4438/tcp": [
                         {
                            "HostPort": "4438"
                          }
                    ]
              }
         }
    }
    ```

## <a name="persistence-via-host-directory-mount"></a>Persistenz über Einbindung des Hostverzeichnisses

Anstatt ein Volume einzubinden, können Sie auch ein Verzeichnis auf dem Hostsystem erstellen und dieses Verzeichnis einbinden.

1. Erstellen Sie ein Verzeichnis im Hostdateisystem, indem Sie den folgenden Befehl ausführen:

   ```sh
   mkdir <your-directory-name-here>
   ```

   Beispiel:

   ```sh
   mkdir C:\myhostdir
   ```
1. Verwenden Sie **Bindungen**, um Ihr Verzeichnis bereitzustellen und das Event Grid-Modul über das Azure-Portal erneut bereitzustellen.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >Ändern Sie nicht den zweiten Teil des Bindungswerts. Er verweist auf eine bestimmte Adresse im Modul. Für das Event Grid-Modul unter Windows muss dies **C:\\app\\metadataDb** sein.

    Beispiel:

    ```json
    {
        "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
         ],
         "HostConfig": {
            "Binds": [
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
             ],
             "PortBindings": {
                    "4438/tcp": [
                         {
                            "HostPort": "4438"
                          }
                    ]
              }
         }
    }
    ```
## <a name="persist-events"></a>Persistentes Speichern von Ereignissen

Um Ereignispersistenz zu aktivieren, müssen Sie Ereignispersistenz zunächst gemäß den Abschnitten weiter oben zur Volumeeinbindung oder Hostverzeichniseinbindung aktivieren.

Wichtige Punkte beim persistenten Speichern von Ereignissen:

* Das persistente Speichern von Ereignissen wird pro Ereignisabonnement aktiviert und kann wahlweise verwendet werden, sobald ein Volume oder Verzeichnis eingebunden wurde.
* Ereignispersistenz wird zum Erstellungszeitpunkt für ein Ereignisabonnement konfiguriert und kann nicht geändert werden, nachdem das Ereignisabonnement erstellt wurde. Zum Umschalten von Ereignispersistenz müssen Sie das Ereignisabonnement löschen und dann neu erstellen.
* Das persistente Speichern von Ereignissen ist fast immer langsamer als Vorgänge im Arbeitsspeicher, aber der Geschwindigkeitsunterschied hängt stark von den Merkmalen des Laufwerks ab. Der Kompromiss zwischen Geschwindigkeit und Zuverlässigkeit ist allen Messagingsystemen inhärent, jedoch nur bei großer Skalierung auffällig.

Legen Sie `persistencePolicy` auf `true` fest, um Ereignispersistenz für ein Ereignisabonnement zu aktivieren:

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```
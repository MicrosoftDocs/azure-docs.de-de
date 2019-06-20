---
title: 'Senden von Ereignissen unter Verwendung von C: Azure Event Hubs | Microsoft-Dokumentation'
description: Dieser Artikel enthält eine exemplarische Vorgehensweise für die Erstellung einer C-Anwendung, die Ereignisse an Azure Event Hubs sendet.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: a8f647018ba6ed3c9e951db2054036b60c7d4ab5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60822472"
---
# <a name="send-events-to-azure-event-hubs-using-c"></a>Senden von Ereignissen an Azure Event Hubs mithilfe von C

## <a name="introduction"></a>Einführung
Azure Event Hubs ist eine Big Data-Streamingplattform und ein Ereigniserfassungsdienst, der pro Sekunde Millionen von Ereignissen empfangen und verarbeiten kann. Event Hubs kann Ereignisse, Daten oder Telemetriedaten, die von verteilter Software und verteilten Geräten erzeugt wurden, verarbeiten und speichern. An einen Event Hub gesendete Daten können transformiert und mit einem beliebigen Echtzeitanalyse-Anbieter oder Batchverarbeitungs-/Speicheradapter gespeichert werden. Eine ausführliche Übersicht über Event Hubs finden Sie unter [Was ist Azure Event Hubs?](event-hubs-about.md) und [Event Hubs-Features im Überblick](event-hubs-features.md).

In diesem Tutorial wird beschrieben, wie Sie mithilfe einer Konsolenanwendung in C Ereignisse an einen Event Hub senden. 

## <a name="prerequisites"></a>Voraussetzungen
Für dieses Tutorial benötigen Sie Folgendes:

* Eine C-Entwicklungsumgebung. In diesem Tutorial wird vom gcc-Stack auf einem virtuellen Azure-Computer unter Linux mit Ubuntu 14.04 ausgegangen.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* **Erstellen eines Event Hubs-Namespace und eines Event Hubs** Verwenden Sie das [Azure-Portal](https://portal.azure.com), um einen Namespace des Typs „Event Hubs“ zu erstellen, und rufen Sie die Verwaltungsanmeldeinformationen ab, die Ihre Anwendung für die Kommunikation mit dem Event Hub benötigt. Erstellen Sie anhand der Anleitung in [diesem Artikel](event-hubs-create.md) einen Namespace und einen Event Hub. Gehen Sie wie im Artikel beschrieben vor, um den Wert des Zugriffsschlüssels für den Event Hub abzurufen: [Abrufen der Verbindungszeichenfolge](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Sie verwenden den Zugriffsschlüssel im Code, den Sie später in diesem Tutorial schreiben. Der Standardschlüsselname lautet: **RootManageSharedAccessKey**.

## <a name="write-code-to-send-messages-to-event-hubs"></a>Schreiben von Code zum Senden von Nachrichten an Event Hubs
In diesem Abschnitt wird das Schreiben einer C-App beschrieben, mit der Ereignisse an den Event Hub gesendet werden. Im Code wird die Proton AMQP-Bibliothek aus dem [Apache Qpid-Projekt](https://qpid.apache.org/) verwendet. Dies entspricht der Verwendung von Service Bus-Warteschlangen und -Themen mit AMQP aus C, wie [in diesem Beispiel](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504) beschrieben. Weitere Informationen finden Sie in der [Qpid Proton-Dokumentation](https://qpid.apache.org/proton/index.html).

1. Befolgen Sie auf der Seite [Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html) die Anweisungen zum Installieren von Qpid Proton für Ihre Umgebung.
2. Um die Proton-Bibliothek zu kompilieren, installieren Sie die folgenden Pakete:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Laden Sie die [Qpid Proton-Bibliothek](https://qpid.apache.org/proton/index.html) herunter, und extrahieren Sie sie, z.B.:
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Erstellen Sie einen Build-Verzeichnis, kompilieren und installieren Sie:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. Erstellen Sie in Ihrem Arbeitsverzeichnis eine Datei namens **sender.c** mit folgendem Code. Denken Sie daran, die Werte durch Ihren SAS-Schlüssel/-Namen, den Event Hub-Namen und den Namespace zu ersetzen. Sie müssen auch eine URL-codierte Version des Schlüssels für das zuvor erstellte **SendRule**-Element eingeben. Die URL-Codierung können Sie [hier](https://www.w3schools.com/tags/ref_urlencode.asp) vornehmen.
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. Kompilieren Sie die Datei, wobei **gcc**vorausgesetzt wird:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Dieser Code verwendet ein Ausgabefenster von 1, um eine sofortige Ausgabe der Meldungen zu erzwingen. Es wird empfohlen, dass Ihre Anwendungen Nachrichten stapelweise ausgeben, um den Durchsatz zu erhöhen. Informationen zur Verwendung der Qpid Proton-Bibliothek in dieser und anderen Umgebungen und auf Plattformen, für die Bindungen bereitgestellt werden (derzeit Perl, PHP, Python und Ruby), finden Sie auf der Seite [Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html).

Führen Sie die Anwendung aus, um Nachrichten an den Event Hub zu senden. 

Glückwunsch! Sie haben jetzt Nachrichten an einen Event Hub gesendet.

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die folgenden Artikel:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Features und Terminologie in Azure Event Hubs](event-hubs-features.md)


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

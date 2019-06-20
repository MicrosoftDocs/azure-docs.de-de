---
title: Verwenden von Notification Hubs mit Java
description: Erfahren Sie mehr über die Verwendung von Azure Notification Hubs von einem Java-Back-End.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 993eafd5a0b74be706d13fe8e06483c223f81eb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61461203"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Verwenden von Notification Hubs von Java aus

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

In diesem Thema werden die wichtigsten Features des neuen, vollständig unterstützten, offiziellen Azure Notification Hub Java-SDKs beschrieben.
Dieses Projekt ist ein Open-Source-Projekt, und Sie können den gesamten SDK-Code unter [Java-SDK] anzeigen.

Sie können von einem Java-/PHP-/Python-/Ruby-Back-End aus über die REST-Schnittstelle für Notification Hubs, die im MSDN-Thema [REST-APIs für Notification Hubs](https://msdn.microsoft.com/library/dn223264.aspx)beschrieben ist, auf alle Notification Hubs-Features zugreifen. Dieses Java-SDK stellt einen dünnen Wrapper über diese REST-Schnittstellen in Java bereit.

Das SDK unterstützt derzeit:

* CRUD auf Notification Hubs
* CRUD für Registrierungen
* Installationsverwaltung
* Importieren/Exportieren von Registrierungen
* Reguläre Sendevorgänge
* Geplante Sendevorgänge
* Asynchrone Vorgänge über Java NIO
* Unterstützte Plattformen: APNS (iOS), FCM (Android), WNS (Windows Store-Apps), MPNS (Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android ohne Google-Dienste)

## <a name="sdk-usage"></a>SDK-Verwendung

### <a name="compile-and-build"></a>Kompilieren und Erstellen

Verwenden von [Maven]

Zum Erstellen von:

    mvn package

## <a name="code"></a>Code

### <a name="notification-hub-cruds"></a>Notification Hub-CRUDs

**"NamespaceManager"-Objekt erstellen:**

    ```java
    NamespaceManager namespaceManager = new NamespaceManager("connection string")
    ```

**Notification Hub erstellen:**

    ```java
    NotificationHubDescription hub = new NotificationHubDescription("hubname");
    hub.setWindowsCredential(new WindowsCredential("sid","key"));
    hub = namespaceManager.createNotificationHub(hub);
    ```

 OR

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Notification Hub abrufen:**

    ```java
    hub = namespaceManager.getNotificationHub("hubname");
    ```

**Notification Hub aktualisieren:**

    ```java
    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);
    ```

**Notification Hub löschen:**

    ```java
    namespaceManager.deleteNotificationHub("hubname");
    ```

### <a name="registration-cruds"></a>Registrierungs-CRUDs

**Notification Hub-Client erstellen:**

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Windows-Registrierung erstellen:**

    ```java
    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

**iOS-Registrierung erstellen:**

    ```java
    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

Auf ähnliche Weise können Sie die Registrierungen für Android (FCM), Windows Phone (MPNS) und Kindle Fire (ADM) erstellen.

**Vorlagen-Registrierungen erstellen:**

    ```java
    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);
    ```

**Registrierungen mit Muster „createRegistrationId + upsert“ erstellen:**

Entfernt Duplikate aufgrund verlorener Antworten beim Speichern von Registrierungs-IDs auf dem Gerät:

    ```java
    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);
    ```

**Aktualisieren von Registrierungen:**

    ```java
    hub.updateRegistration(reg);
    ```

**Löschen von Registrierungen:**

    ```java
    hub.deleteRegistration(regid);
    ```

**Abfragen von Registrierungen:**

* **Abrufen einer einzelnen Registrierung:**

    ```java
    hub.getRegistration(regid);
    ```

* **Abrufen aller Registrierungen im Hub:**

    ```java
    hub.getRegistrations();
    ```

* **Abrufen von Registrierungen mit Tags:**

    ```java
    hub.getRegistrationsByTag("myTag");
    ```

* **Abrufen von Registrierungen nach Kanal:**

    ```java
    hub.getRegistrationsByChannel("devicetoken");
    ```

Alle Sammlungsabfragen unterstützen $top und Fortsetzungstoken.

### <a name="installation-api-usage"></a>Verwendung der Installations-API

Die Installations-API ist ein alternativer Mechanismus für die Registrierungsverwaltung. Anstatt mehrere Registrierungen zu verwalten, was keine leichte Aufgabe ist und möglicherweise falsch oder ineffizient durchgeführt wird, ist es nun möglich, ein EINZIGES Installationsobjekt zu verwenden.

Die Installation enthält alles, was Sie benötigen: Pushkanal (Gerätetoken), Tags, Vorlagen, sekundäre Kacheln (für WNS und APNS). Sie müssen den Dienst nicht mehr aufrufen, um die ID zu erhalten – generieren Sie lediglich die GUID oder einen anderen Bezeichner, speichern Sie sie auf dem Gerät, und senden Sie sie zusammen mit dem Pushkanal (Gerätetoken) an das Back-End.

Auf dem Back-End sollten Sie nur einen einzigen Aufruf durchführen: `CreateOrUpdateInstallation`. Er ist vollständig idempotent, daher können Sie ihn bei Bedarf beliebig wiederholen.

Beispiel für Amazon Kindle Fire:

    ```java
    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);
    ```

Wenn Sie ihn aktualisieren möchten:

    ```java
    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);
    ```

Verwenden Sie für erweiterte Szenarien die Funktion für die Teilaktualisierung, mit der nur bestimmte Eigenschaften des Installationsobjekts geändert werden können. Die Teilaktualisierung entspricht einer Teilmenge der JSON Patch-Vorgänge, die Sie für ein Installationsobjekt ausführen können.

    ```java
    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);
    ```

Löschen der Installation:

    ```java
    hub.deleteInstallation(installation.getInstallationId());
    ```

`CreateOrUpdate`, `Patch` und `Delete` sind letztendlich konsistent mit `Get`. Der angeforderte Vorgang wird während des Aufrufs zunächst in die Systemwarteschlange gesetzt und später im Hintergrund ausgeführt. „Get“ ist nicht für das Hauptlaufzeitszenario ausgelegt, sondern nur für Debug- und Problembehandlungszwecke bestimmt. Es wird durch den Dienst stark eingeschränkt.

Der Sendefluss für Installationen entspricht dem für Registrierungen. Um Benachrichtigungen an eine bestimmte Installation zu adressieren, verwenden Sie einfach das Tag „InstallationId:{desired-id}“. In diesem Fall lautet der Code:

    ```java
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");
    ```

Für eine von mehreren Vorlagen:

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");
    ```

### <a name="schedule-notifications-available-for-standard-tier"></a>Planen von Benachrichtigungen (für STANDARD-Ebene verfügbar)

Entspricht einem regulären Sendevorgang, aber mit einem zusätzlichen Parameter (scheduledTime) der angibt, wann die Benachrichtigung gesendet werden soll. Der Dienst akzeptiert jeden beliebigen Zeitpunkt zwischen "jetzt + 5 Minuten" und "jetzt + 7 Tage".

**Planen einer systemeigenen Windows-Benachrichtigung:**

    ```java
    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());
    ```

### <a name="importexport-available-for-standard-tier"></a>Import/Export (für STANDARD-Ebene verfügbar)

Möglicherweise müssen Sie Registrierungen als Massenvorgang auszuführen. In der Regel ist dies zur Integration in ein anderes System oder als umfangreiche Korrektur zur Aktualisierung der Tags erforderlich. Bei Tausenden von Registrierungen wird die Verwendung des „Get/Update“-Flusses nicht empfohlen. Für dieses Szenario wurde die Import/Export-Funktion des Systems entwickelt. Sie gewähren Zugriff auf einen Blobcontainer unter Ihrem Speicherkonto als Quelle für eingehende Daten und Speicherort für die Ausgabe.

**Übermitteln eines Exportauftrags:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Übermitteln eines Importauftrags:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Warten, bis ein Auftrag abgeschlossen ist:**

    ```java
    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }
    ```

**Alle Aufträge abrufen:**

    ```java
    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
    ```

**URI mit SAS-Signatur:**

 Diese URL ist die URL einer Blobdatei oder eines Blobcontainers, einschließlich bestimmter Parameter, wie Berechtigungen und Ablaufzeitpunkt, sowie die Signatur dieser Objekte unter Verwendung des SAS-Schlüssels des Kontos. Das Java-SDK für Azure Storage verfügt über umfangreiche Funktionen, beispielsweise zum Erstellen dieser URIs. Als einfache Alternative sehen Sie sich die Testklasse `ImportExportE2E` (unter GitHub) an, die über eine einfache und kompakte Implementierung des Signaturalgorithmus verfügt.

### <a name="send-notifications"></a>Senden von Benachrichtigungen

Das Benachrichtigungsobjekt ist lediglich ein Text mit Headern. Einige Dienstprogrammmethoden helfen beim Erstellen von system- oder vorlagenbasierten Benachrichtigungsobjekten.

* **Windows Store und Windows Phone 8.1 (nicht-Silverlight)**

    ```java
    String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
    Notification n = Notification.createWindowsNotification(toast);
    hub.sendNotification(n);
    ```

* **iOS**

    ```java
    String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
    Notification n = Notification.createAppleNotification(alert);
    hub.sendNotification(n);
    ```

* **Android**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createFcmNotification(message);
    hub.sendNotification(n);
    ```

* **Windows Phone 8.0 und 8.1 Silverlight**

    ```java
    String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>Hello from Java!</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
    Notification n = Notification.createMpnsNotification(toast);
    hub.sendNotification(n);
    ```

* **Kindle Fire**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createAdmNotification(message);
    hub.sendNotification(n);
    ```

* **Senden an Tags**
  
    ```java
    Set<String> tags = new HashSet<String>();
    tags.add("boo");
    tags.add("foo");
    hub.sendNotification(n, tags);
    ```

* **Senden an Tagausdruck**

    ```java
    hub.sendNotification(n, "foo && ! bar");
    ```

* **Senden einer Vorlagenbenachrichtigung**

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("prop1", "v1");
    prop.put("prop2", "v2");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n);
    ```

Beim Ausführen des Java-Codes sollte jetzt eine Benachrichtigung erstellt werden, die auf dem Zielgerät angezeigt wird.

## <a name="next-steps"></a>Nächste Schritte

In diesem Thema wurde veranschaulicht, wie Sie einen einfachen Java REST-Client für Notification Hubs erstellen. Mögliche nächste Schritte:

* Laden Sie das vollständige [Java-SDK]herunter, das den gesamten SDK-Code enthält.
* Arbeiten Sie die Beispiele durch:
  * [Erste Schritte mit Notification Hubs]
  * [Senden aktueller Nachrichten]
  * [Senden lokalisierter aktueller Nachrichten]
  * [Senden von Benachrichtigungen an authentifizierte Benutzer]
  * [Senden plattformübergreifender Benachrichtigungen an authentifizierte Benutzer]

[Java-SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Erste Schritte mit Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Senden aktueller Nachrichten]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Senden lokalisierter aktueller Nachrichten]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Senden von Benachrichtigungen an authentifizierte Benutzer]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Senden plattformübergreifender Benachrichtigungen an authentifizierte Benutzer]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven]: https://maven.apache.org/

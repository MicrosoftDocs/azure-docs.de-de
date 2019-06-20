---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: b8cf4217ca6c80be998b92e71c3ba29c4f68bce2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178340"
---
## <a name="webapi-project"></a>WebAPI-Projekt
1. Öffnen Sie in Visual Studio das Projekt **AppBackend** , das Sie im Tutorial **Benachrichtigen von Benutzern** erstellt haben.
2. Ersetzen Sie in der Datei "Notifications.cs" die gesamte **Notifications** -Klasse durch den folgenden Code. Stellen Sie sicher, dass Sie die Platzhalter durch Ihre Verbindungszeichenfolge (mit vollständigem Zugriff) für Ihr Notification Hub und den Hub-Namen ersetzen. Diese Werte erhalten Sie im [Azure-Portal](http://portal.azure.com). Dieses Modul stellt nun die unterschiedlichen sicheren Benachrichtigungen dar, die gesendet werden. In einer vollständigen Implementierung werden die Benachrichtigungen in einer Datenbank gespeichert; der Einfachheit halber speichern wir Sie in diesem Fall im Speicher.
   
        public class Notification
        {
            public int Id { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public class Notifications
        {
            public static Notifications Instance = new Notifications();

            private List<Notification> notifications = new List<Notification>();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",     "{hub name}");
            }

            public Notification CreateNotification(string payload)
            {
                var notification = new Notification() {
                Id = notifications.Count,
                Payload = payload,
                Read = false
                };

                notifications.Add(notification);

                return notification;
            }

            public Notification ReadNotification(int id)
            {
                return notifications.ElementAt(id);
            }
        }

1. Ersetzen Sie in der Datei "NotificationsController.cs" den Code innerhalb der **NotificationsController** -Klassendefinition durch den folgenden Code. Durch diese Komponente wird für das Gerät eine Möglichkeit zum sicheren Abrufen der Benachrichtigung implementiert, und sie ermöglicht außerdem (zu Lernzwecken in diesem Lernprogramm) das Auslösen einer sicheren Pushbenachrichtigung an Ihre Geräte. Beachten Sie, dass wenn Sie die Benachrichtigung an das Notification Hub senden, nur eine reine Benachrichtigung mit der ID der Benachrichtigung (und nicht der tatsächlichen Meldung) gesendet wird.
   
       public NotificationsController()
       {
           Notifications.Instance.CreateNotification("This is a secure notification!");
       }
   
       // GET api/notifications/id
       public Notification Get(int id)
       {
           return Notifications.Instance.ReadNotification(id);
       }
   
       public async Task<HttpResponseMessage> Post()
       {
           var secureNotificationInTheBackend = Notifications.Instance.CreateNotification("Secure confirmation.");
           var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;
   
           // windows
           var rawNotificationToBeSent = new Microsoft.Azure.NotificationHubs.WindowsNotification(secureNotificationInTheBackend.Id.ToString(),
                           new Dictionary<string, string> {
                               {"X-WNS-Type", "wns/raw"}
                           });
           await Notifications.Instance.Hub.SendNotificationAsync(rawNotificationToBeSent, usernameTag);
   
           // apns
           await Notifications.Instance.Hub.SendAppleNativeNotificationAsync("{\"aps\": {\"content-available\": 1}, \"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}", usernameTag);
   
           // gcm
           await Notifications.Instance.Hub.SendGcmNativeNotificationAsync("{\"data\": {\"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}}", usernameTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }


Beachten Sie, dass die `Post` -Methode jetzt keine Popupbenachrichtigung sendet. Es wird eine reine Benachrichtigung gesendet, die nur die Benachrichtigungs-ID und keine vertraulichen Inhalte enthält. Kommentieren Sie auch unbedingt den Sendevorgang für die Plattformen, für die Sie keine Anmeldeinformationen in Ihrem Notification Hub konfiguriert haben, da diese zu Fehlern führen.

1. Nun werden wir diese App erneut auf einer Azure-Website bereitstellen, damit von allen Geräten darauf zugegriffen werden kann. Klicken Sie mit der rechten Maustaste auf das Projekt **AppBackend**, und wählen Sie **Veröffentlichen**.
2. Wählen Sie die Azure-Website als Ihr Veröffentlichungsziel aus. Melden Sie sich mit Ihrem Azure-Konto an, wählen Sie eine vorhandene oder neue Website, und notieren Sie sich die **Ziel-URL**-Eigenschaft auf der Registerkarte **Verbindung**. Diese URL wird später in diesem Lernprogramm als *Back-End-Endpunkt* bezeichnet. Klicken Sie auf **Veröffentlichen**.


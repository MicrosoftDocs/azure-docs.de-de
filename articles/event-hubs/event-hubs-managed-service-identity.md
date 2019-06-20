---
title: Verwaltete Identitäten für Azure-Ressourcen – Azure Event Hubs | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zur Verwendung verwalteter Identitäten für Azure-Ressourcen mit Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 05/20/2019
ms.author: shvija
ms.openlocfilehash: 4e6f16a15547583baab63f452504d36eb2e43b85
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978462"
---
# <a name="managed-identities-for-azure-resources-with-event-hubs"></a>Verwaltete Identitäten für Azure-Ressourcen mit Azure Event Hubs

[Verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md) ist ein Azure-übergreifendes Feature, mit dem Sie eine sichere Identität für die Bereitstellung erstellen können, in der Ihr Anwendungscode ausgeführt wird. Sie können dieser Identität dann Zugriffssteuerungsrollen zuordnen, um benutzerdefinierte Berechtigungen für den Zugriff auf bestimmte Azure-Ressourcen zu gewähren, die Ihre Anwendung benötigt. 

Die Azure-Plattform verwaltet diese Laufzeitidentität mit verwalteten Identitäten. Sie müssen keine Zugriffsschlüssel in Ihrem Anwendungscode speichern und schützen – weder für die Identität selbst noch für die Ressourcen, auf die zugegriffen werden muss. Eine Event Hubs-Client-App, die innerhalb einer Azure App Service-Anwendung oder in einem virtuellen Computer mit aktivierter Unterstützung für verwaltete Identitäten für Azure-Ressourcen ausgeführt wird, muss keine SAS-Regeln und -Schlüssel oder andere Zugriffstoken verarbeiten. Die Client-App benötigt nur die Endpunktadresse des Event Hubs-Namespace. Wenn die App eine Verbindung herstellt, bindet Event Hubs den Kontext der verwalteten Identität an den Client. Der entsprechende Vorgang wird weiter unten in diesem Artikel in einem Beispiel gezeigt.

Nach der Zuordnung zu einer verwalteten Identität kann ein Event Hubs-Client alle autorisierten Vorgänge ausführen. Die Autorisierung wird durch Zuordnung von Event Hubs-Rollen zu verwalteten Identitäten gewährt. 

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs-Rollen und -Berechtigungen
Sie können der Rolle **Event Hubs-Datenbesitzer** eines Event Hubs-Namespace eine verwaltete Identität hinzufügen. Diese Rolle gewährt der Identität vollständige Kontrolle (für Verwaltungs- und Datenvorgänge) über alle Entitäten im Namespace.

>[!IMPORTANT]
> Früher haben wir das Hinzufügen einer verwalteten Identität zur Rolle **Besitzer** oder **Mitwirkender** unterstützt. Die Berechtigungen für den Datenzugriff für die Rolle **Besitzer** und **Mitwirkender** werden jedoch nicht mehr berücksichtigt. Wenn Sie die Rolle **Besitzer** oder **Mitwirkender** verwenden, wechseln Sie zur Verwendung der Rolle **Event Hubs-Datenbesitzer**.

Um die neue integrierte Rolle zu verwenden, gehen Sie wie folgt vor: 

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
2. Navigieren Sie zu dem Event Hubs-Namespace.
3. Wählen Sie auf der Seite **Event Hubs-Namespace** im linken Menü die Option **Zugriffssteuerung (IAM)** aus.
4. Wählen Sie auf der Seite **Zugriffssteuerung (IAM)** im Abschnitt **Rollenzuweisung hinzufügen** den Befehl **Hinzufügen** aus. 

    ![Rollenzuweisung hinzufügen (Schaltfläche)](./media/event-hubs-managed-service-identity/add-role-assignment-button.png)
5. Führen Sie auf der Seite **Rollenzuweisung hinzufügen** die folgenden Schritte aus: 
    1. Wählen Sie für **Rolle** die Option **Azure Event Hubs-Datenbesitzer** aus. 
    2. Wählen Sie die **Identität** aus, die der Rolle hinzugefügt werden soll.
    3. Wählen Sie **Speichern** aus. 

        ![Rolle „Event Hubs-Datenbesitzer“](./media/event-hubs-managed-service-identity/add-role-assignment-dialog.png)
6. Wechseln Sie zur Seite **Rollenzuweisungen**, und vergewissern Sie sich, dass der Benutzer der Rolle **Azure Event Hubs-Datenbesitzer** hinzugefügt wurde. 

    ![Bestätigen, dass der Benutzer der Rolle hinzugefügt wurde](./media/event-hubs-managed-service-identity/role-assignments.png)
 
## <a name="use-event-hubs-with-managed-identities-for-azure-resources"></a>Verwenden von Event Hubs mit verwalteten Identitäten für Azure-Ressourcen

Im folgenden Abschnitt werden die folgenden Schritte beschrieben:

1. Erstellen und Bereitstellen einer Beispielanwendung, die unter einer verwalteten Identität ausgeführt wird.
2. Gewähren des Zugriffs auf einen Event Hubs-Namespace für diese Identität.
3. Funktionsweise der Interaktion zwischen der Anwendung und Event Hubs mit dieser Identität.

Diese Einführung beschreibt eine Webanwendung, die in [Azure App Service](https://azure.microsoft.com/services/app-service/) gehostet wird. Die Schritte für eine durch einen virtuellen Computer gehostete Anwendung sind ähnlich.

### <a name="create-an-app-service-web-application"></a>Erstellen einer App Service-Webanwendung

Der erste Schritt besteht darin, eine App Service-ASP.NET-Anwendung zu erstellen. Wenn Sie mit der Vorgehensweise in Azure nicht vertraut sind, befolgen Sie diese [Anleitung](../app-service/app-service-web-get-started-dotnet-framework.md). Anstatt jedoch, wie in diesem Tutorial gezeigt, eine MVC-Anwendung zu erstellen, erstellen Sie eine Web Forms-Anwendung.

### <a name="set-up-the-managed-identity"></a>Einrichten der verwalteten Identität

Nachdem Sie die Anwendung erstellt haben, navigieren Sie im Azure-Portal zu der neu erstellten Webanwendung (ebenfalls in der Anleitung gezeigt). Navigieren Sie dann zur Seite **Verwaltete Dienstidentität**, und aktivieren Sie das Feature: 

![Seite „Verwaltete Dienstidentität“](./media/event-hubs-managed-service-identity/msi1.png)
 
Wenn Sie das Feature aktiviert haben, wird in Ihrem Azure Active Directory eine neue Dienstidentität erstellt und auf dem App Service-Host konfiguriert.

### <a name="create-a-new-event-hubs-namespace"></a>Erstellen eines neuen Event Hubs-Namespace

Als Nächstes [erstellen Sie einen Event Hubs-Namespace](event-hubs-create.md). 

Navigieren Sie im Portal zur Seite **Zugriffssteuerung (IAM)** des Namespace, und klicken Sie auf **Rollenzuweisung hinzufügen**, um die verwaltete Identität der Rolle **Besitzer** hinzuzufügen. Suchen Sie hierzu im Bereich **Berechtigungen hinzufügen** im Feld **Auswählen** nach dem Namen der Webanwendung, und klicken Sie auf den entsprechenden Eintrag. Klicken Sie anschließend auf **Speichern**. Die verwaltete Identität für die Webanwendung verfügt jetzt über Zugriff auf den Event Hubs-Namespace und den zuvor erstellten Event Hub. 

### <a name="run-the-app"></a>Ausführen der App

Bearbeiten Sie jetzt die Standardseite der von Ihnen erstellten ASP.NET-Anwendung. Sie können auch den Webanwendungscode aus [diesem GitHub-Repository](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp) verwenden. 

Nachdem Sie die App gestartet haben, zeigen Sie in Ihrem Browser auf „EventHubsMSIDemo.aspx“. Sie können die Seite auch als Startseite festlegen. Sie finden den Code in der Datei „EventHubsMSIDemo.aspx.cs“. Das Ergebnis ist eine kleine Webanwendung mit einigen wenigen Eingabefeldern sowie Schaltflächen zum **Senden** und **Empfangen**, mit denen eine Verbindung mit Event Hubs hergestellt wird, um Ereignisse zu senden oder zu empfangen. 

Beachten Sie, wie das Objekt [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) initialisiert wird. Der Code verwendet nicht den SAS-Tokenanbieter (Shared Access Signature), sondern erstellt mit dem Aufruf `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` einen Tokenanbieter für die verwaltete Identität. Daher müssen keine Geheimnisse gespeichert und verwendet werden. Der Datenfluss aus dem Kontext der verwalteten Identität zu Event Hubs und der Autorisierungshandshake werden automatisch vom Tokenanbieter verarbeitet. Dies ist ein einfacheres Modell als die Verwendung von SAS.

Nachdem Sie diese Änderungen vorgenommen haben, veröffentlichen Sie die Anwendung und führen sie aus. Sie können die richtigen Veröffentlichungsdaten abrufen, indem Sie ein Veröffentlichungsprofil herunterladen und dann in Visual Studio importieren:

![Veröffentlichungsprofil importieren](./media/event-hubs-managed-service-identity/msi3.png)
 
Um Nachrichten zu senden oder zu empfangen, geben Sie den Namen des Namespace und der von Ihnen erstellten Entität ein, und klicken Sie entweder auf **Senden** oder **Empfangen**. 
 
Die verwaltete Identität funktioniert nur innerhalb der Azure-Umgebung und nur in der App Service-Bereitstellung, in der Sie sie konfiguriert haben. Verwaltete Identitäten funktionieren zurzeit nicht mit App Service-Bereitstellungsslots.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Event Hubs erhalten Sie unter den folgenden Links:

* Erste Schritte mit einem [Event Hubs-Tutorial](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs – häufig gestellte Fragen](event-hubs-faq.md)
* [Event Hubs – Preisübersicht](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Beispielanwendungen mit Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

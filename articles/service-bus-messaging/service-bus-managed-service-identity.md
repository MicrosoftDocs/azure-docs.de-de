---
title: Verwaltete Identitäten für Azure-Ressourcen mit Azure Service Bus (Vorschau) | Microsoft-Dokumentation
description: Verwenden von verwalteten Identitäten für Azure-Ressourcen mit Azure Service Bus
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/01/2018
ms.author: aschhab
ms.openlocfilehash: 8477ff8c8ff0bc1629ff4cdc61f7c28c6eed778c
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978794"
---
# <a name="managed-identities-for-azure-resources-with-service-bus"></a>Verwaltete Identitäten für Azure-Ressourcen mit Service Bus 

[Verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md) ist ein Azure-übergreifendes Feature, mit dem Sie eine sichere Identität für die Bereitstellung erstellen können, in der Ihr Anwendungscode ausgeführt wird. Sie können dieser Identität dann Zugriffssteuerungsrollen zuordnen, um benutzerdefinierte Berechtigungen für den Zugriff auf bestimmte Azure-Ressourcen zu gewähren, die Ihre Anwendung benötigt.

Die Azure-Plattform verwaltet diese Laufzeitidentität mit verwalteten Identitäten. Sie müssen keine Zugriffsschlüssel in Ihrem Anwendungscode speichern und schützen – weder für die Identität selbst noch für die Ressourcen, auf die zugegriffen werden muss. Eine Service Bus-Client-App, die innerhalb einer Azure App Service-Anwendung oder in einem virtuellen Computer mit aktivierter Unterstützung für verwaltete Identitäten für Azure-Ressourcen ausgeführt wird, muss keine SAS-Regeln und -Schlüssel oder andere Zugriffstoken verarbeiten. Die Client-App benötigt nur die Endpunktadresse des Service Bus Messaging-Namespace. Wenn die App eine Verbindung herstellt, bindet Service Bus den Kontext der verwalteten Identität an den Client. Der entsprechende Vorgang wird weiter unten in diesem Artikel in einem Beispiel gezeigt. Nach der Zuordnung zu einer verwalteten Identität kann ein Service Bus-Client alle autorisierten Vorgänge ausführen. Die Autorisierung wird durch Zuordnung von Service Bus-Rollen zu einer verwalteten Identität gewährt. 

## <a name="service-bus-roles-and-permissions"></a>Service Bus-Rollen und -Berechtigungen

Sie können der Rolle „Service Bus-Datenbesitzer“ eines Service Bus-Namespace eine verwaltete Identität hinzufügen. Damit erhält die Identität vollständige Kontrolle (für Verwaltungs- und Datenvorgänge) über alle Entitäten im Namespace.

>[!IMPORTANT]
> Früher haben wir das Hinzufügen einer verwalteten Identität zur Rolle **Besitzer** oder **Mitwirkender** unterstützt.
>
> Die Berechtigungen für den Datenzugriff für die Rolle **Besitzer** und **Mitwirkender** werden jedoch nicht mehr berücksichtigt. Wenn Sie die Rolle **Besitzer** oder **Mitwirkender** verwendet haben, müssen diese so angepasst werden, dass sie nun die Rolle **Service Bus-Datenbesitzer** verwenden.

Um die neue integrierte Rolle zu verwenden, führen Sie die folgenden Schritte aus:

1. wechseln Sie zum [Azure-Portal](https://portal.azure.com)
2. Navigieren Sie zu dem Service Bus-Namespace, in dem Sie aktuell die Rolle „Besitzer“ oder „Mitwirkender“ eingerichtet haben.
3. Klicken Sie im linken Bereich auf „Zugriffssteuerung (IAM)“.
4. Fahren Sie fort damit, dass Sie wie unten gezeigt eine neue Rollenzuweisung hinzufügen.

    ![](./media/service-bus-role-based-access-control/ServiceBus_RBAC_SBDataOwner.png)

5. Klicken Sie auf „Speichern“, um die Rollenzuweisung zu speichern.

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Verwenden von Service Bus mit verwalteten Identitäten für Azure-Ressourcen

Der folgende Abschnitt beschreibt die Schritte, die erforderlich sind, um eine Beispielanwendung zu erstellen und bereitzustellen, die mit einer verwalteten Identität ausgeführt wird. Zudem wird erläutert, wie Sie dieser Identität Zugriff auf einen Service Bus Messaging-Namespace gewähren und wie die Anwendung mit den Service Bus-Entitäten interagiert, die diese Identität verwenden.

Diese Einführung beschreibt eine Webanwendung, die in [Azure App Service](https://azure.microsoft.com/services/app-service/) gehostet wird. Die Schritte für eine durch einen virtuellen Computer gehostete Anwendung sind ähnlich.

### <a name="create-an-app-service-web-application"></a>Erstellen einer App Service-Webanwendung

Der erste Schritt besteht darin, eine App Service-ASP.NET-Anwendung zu erstellen. Wenn Sie mit der Vorgehensweise in Azure nicht vertraut sind, befolgen Sie diese [Anleitung](../app-service/app-service-web-get-started-dotnet-framework.md). Anstatt jedoch, wie in diesem Tutorial gezeigt, eine MVC-Anwendung zu erstellen, erstellen Sie eine Web Forms-Anwendung.

### <a name="set-up-the-managed-identity"></a>Einrichten der verwalteten Identität

Nachdem Sie die Anwendung erstellt haben, navigieren Sie im Azure-Portal zu der neu erstellten Webanwendung (ebenfalls in der Anleitung gezeigt). Navigieren Sie dann zur Seite **Verwaltete Dienstidentität**, und aktivieren Sie das Feature: 

![](./media/service-bus-managed-service-identity/msi1.png)

Wenn Sie das Feature aktiviert haben, wird in Ihrem Azure Active Directory eine neue Dienstidentität erstellt und auf dem App Service-Host konfiguriert.

### <a name="create-a-new-service-bus-messaging-namespace"></a>Erstellen eines neuen Service Bus-Namespace

Als Nächstes [erstellen Sie einen Service Bus Messaging-Namespace](service-bus-create-namespace-portal.md). 

Navigieren Sie im Portal zur Seite **Zugriffssteuerung (IAM)** des Namespace, und klicken Sie auf **Rollenzuweisung hinzufügen**, um die verwaltete Identität der Rolle **Besitzer** hinzuzufügen. Suchen Sie hierzu im Bereich **Berechtigungen hinzufügen** im Feld **Auswählen** nach dem Namen der Webanwendung, und klicken Sie auf den entsprechenden Eintrag. Klicken Sie anschließend auf **Speichern**.

Die verwaltete Identität der Webanwendung verfügt jetzt über Zugriff auf den Service Bus-Namespace und die zuvor erstellte Warteschlange. 

### <a name="run-the-app"></a>Ausführen der App

Bearbeiten Sie jetzt die Standardseite der von Ihnen erstellten ASP.NET-Anwendung. Sie können den Webanwendungscode aus [diesem GitHub-Repository](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet) verwenden.  

Die Seite „Default.aspx“ ist Ihre Zielseite. Sie finden den Code in der Datei „Default.aspx.cs“. Das Ergebnis ist eine kleine Webanwendung mit einigen wenigen Eingabefeldern sowie Schaltflächen zum **Senden** und **Empfangen**, mit denen eine Verbindung mit Service Bus hergestellt wird, um Nachrichten zu senden oder zu empfangen.

Beachten Sie, wie das Objekt [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) initialisiert wird. Der Code verwendet nicht den SAS-Tokenanbieter (Shared Access Signature), sondern erstellt mit dem Aufruf `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)` einen Tokenanbieter für die verwaltete Identität. Daher müssen keine Geheimnisse aufbewahrt und verwendet werden. Der Flow vom Kontext der verwalteten Identität zu Service Bus und der Autorisierungshandshake werden automatisch vom Tokenanbieter verarbeitet. Dies ist ein einfacheres Modell als die Verwendung von SAS.

Nachdem Sie diese Änderungen vorgenommen haben, veröffentlichen Sie die Anwendung und führen sie aus. Sie können die richtigen Veröffentlichungsdaten einfach abrufen, indem Sie ein Veröffentlichungsprofil herunterladen und dann in Visual Studio importieren:

![](./media/service-bus-managed-service-identity/msi3.png)
 
Um Nachrichten zu senden oder zu empfangen, geben Sie den Namen des Namespace und der von Ihnen erstellten Entität ein. Klicken Sie dann entweder auf **Senden** oder auf **Empfangen**.


> [!NOTE]
> - Die verwaltete Identität kann nur in der Azure-Umgebung, in App Services, auf virtuellen Azure-Computern und in Skalierungsgruppen verwendet werden. Für .NET-Anwendungen bietet die vom Service Bus-NuGet-Paket verwendete Bibliothek „Microsoft.Azure.Services.AppAuthentication“ eine Abstraktion über dieses Protokoll und unterstützt eine lokale Entwicklungsumgebung. Mithilfe dieser Bibliothek können Sie zudem Ihren Code lokal auf dem Entwicklungscomputer testen. Hierzu verwenden Sie Ihr Benutzerkonto aus Visual Studio, aus der Azure CLI 2.0 oder der integrierten Active Directory-Authentifizierung. Weitere Informationen zu Optionen für die lokale Entwicklung mit dieser Bibliothek finden Sie unter [Dienst-zu-Dienst-Authentifizierung in Azure Key Vault mithilfe von .NET](../key-vault/service-to-service-authentication.md).  
> 
> - Verwaltete Identitäten funktionieren zurzeit nicht mit App Service-Bereitstellungsslots.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Service Bus-Messaging finden Sie in folgenden Themen:

* [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md)
* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Verwenden von Service Bus-Themen und -Abonnements](service-bus-dotnet-how-to-use-topics-subscriptions.md)

---
title: Verwaltete Identitäten für Azure-Ressourcen mit Service Bus
description: In diesem Artikel wird beschrieben, wie Sie mit verwalteten Identitäten auf Azure Service Bus-Entitäten (Warteschlangen, Themen und Abonnements) zugreifen.
ms.topic: article
ms.date: 10/21/2020
ms.openlocfilehash: 1efcd3c48e7e4a431a0c72c4b3b84531b44e973e
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425520"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Authentifizieren einer verwalteten Identität mit Azure Active Directory für den Zugriff auf Azure Service Bus-Ressourcen
[Verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md) ist ein Azure-übergreifendes Feature, mit dem Sie eine sichere Identität für die Bereitstellung erstellen können, in der Ihr Anwendungscode ausgeführt wird. Sie können dieser Identität dann Zugriffssteuerungsrollen zuordnen, um benutzerdefinierte Berechtigungen für den Zugriff auf bestimmte Azure-Ressourcen zu gewähren, die Ihre Anwendung benötigt.

Die Azure-Plattform verwaltet diese Laufzeitidentität mit verwalteten Identitäten. Sie müssen keine Zugriffsschlüssel in Ihrem Anwendungscode speichern und schützen – weder für die Identität selbst noch für die Ressourcen, auf die zugegriffen werden muss. Eine Service Bus-Client-App, die innerhalb einer Azure App Service-Anwendung oder in einem virtuellen Computer mit aktivierter Unterstützung für verwaltete Identitäten für Azure-Ressourcen ausgeführt wird, muss keine SAS-Regeln und -Schlüssel oder andere Zugriffstoken verarbeiten. Die Client-App benötigt nur die Endpunktadresse des Service Bus Messaging-Namespace. Wenn die App eine Verbindung herstellt, bindet Service Bus den Kontext der verwalteten Identität an den Client. Der entsprechende Vorgang wird weiter unten in diesem Artikel in einem Beispiel gezeigt. Nach der Zuordnung zu einer verwalteten Identität kann ein Service Bus-Client alle autorisierten Vorgänge ausführen. Die Autorisierung wird durch Zuordnung von Service Bus-Rollen zu einer verwalteten Identität gewährt. 

## <a name="overview"></a>Übersicht
Wenn ein Sicherheitsprinzipal (ein Benutzer, eine Gruppe oder eine Anwendung) versucht, auf eine Service Bus-Entität zuzugreifen, muss die Anforderung autorisiert werden. Mit Azure AD ist der Zugriff auf eine Ressource ein zweistufiger Prozess. 

 1. Zunächst wird die Identität des Sicherheitsprinzipals authentifiziert, und ein OAuth 2.0-Token wird zurückgegeben. Der Ressourcenname zum Anfordern eines Tokens ist `https://servicebus.azure.net`.
 1. Anschließend wird das Token als Teil einer Anforderung an den Service Bus-Dienst übergeben, um den Zugriff auf die angegebene Ressource zu autorisieren.

Für den Authentifizierungsschritt ist es erforderlich, dass eine Anwendungsanforderung zur Laufzeit ein OAuth 2.0-Zugriffstoken enthält. Wenn eine Anwendung in einer Azure-Entität, z.B. einem virtuellen Azure-Computer, einer VM-Skalierungsgruppe oder einer Azure Functions-App, ausgeführt wird, kann der Zugriff auf die Ressourcen über eine verwaltete Identität erfolgen. 

Der Autorisierungsschritt erfordert, dass dem Sicherheitsprinzipal mindestens eine Azure-Rolle zugewiesen wird. Azure Service Bus stellt Azure-Rollen bereit, die Berechtigungssätze für Service Bus-Ressourcen enthalten. Die möglichen Berechtigungen eines Sicherheitsprinzipals sind durch die Rollen vorgegeben, die dem Prinzipal zugewiesen sind. Weitere Informationen zur Zuweisung von Azure-Rollen zu Azure Service Bus finden Sie unter [Integrierte Azure-Rollen für Azure Service Bus](#azure-built-in-roles-for-azure-service-bus). 

Native Anwendungen und Webanwendungen, die Anforderungen an Service Bus senden, können die Autorisierung ebenfalls mit Azure AD durchführen. In diesem Artikel erfahren Sie, wie Sie ein Zugriffstoken anfordern und dieses zum Autorisieren von Anforderungen für Service Bus-Ressourcen verwenden. 


## <a name="assigning-azure-roles-for-access-rights"></a>Zuweisen von Azure-Rollen für Zugriffsrechte
Azure Active Directory (Azure AD) autorisiert Rechte für den Zugriff auf geschützte Ressourcen über die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../role-based-access-control/overview.md). Azure Service Bus definiert eine Reihe von integrierten Azure-Rollen, die allgemeine Berechtigungen für den Zugriff auf Service Bus-Entitäten umfassen. Sie können auch benutzerdefinierte Rollen für den Zugriff auf die Daten definieren.

Wenn einem Azure AD-Sicherheitsprinzipal eine Azure-Rolle zugewiesen wird, gewährt Azure diesem Sicherheitsprinzipal Zugriff auf diese Ressourcen. Der Zugriff kann sich auf den Bereich des Abonnements, der Ressourcengruppe oder des Namespace von Service Bus beziehen. Ein Azure AD-Sicherheitsprinzipal kann ein Benutzer, eine Gruppe, ein Anwendungsdienstprinzipal oder eine verwaltete Identität für Azure-Ressourcen sein.

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Integrierte Azure-Rollen für Azure Service Bus
Bei Azure Service Bus ist die Verwaltung der Namespaces und aller zugehörigen Ressourcen über das Azure-Portal und die Azure-Ressourcenverwaltungs-API bereits durch das Azure RBAC-Modell geschützt. Azure stellt die folgenden integrierten Azure-Rollen zum Autorisieren des Zugriffs auf einen Service Bus-Namespace bereit:

- [Besitzer „Azure Service Bus-Daten“](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Ermöglicht den Datenzugriff auf einen Service Bus-Namespace und seine Entitäten (Warteschlangen, Themen, Abonnements und Filter).
- [Azure Service Bus-Datensender](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Verwenden Sie diese Rolle, um dem Service Bus-Namespace und seinen Entitäten Sendezugriff zu erteilen.
- [Azure Service Bus-Datenempfänger](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Verwenden Sie diese Rolle, um dem Service Bus-Namespace und seinen Entitäten Empfangszugriff zu erteilen. 

## <a name="resource-scope"></a>Ressourcenumfang 
Bevor Sie einem Sicherheitsprinzipal eine Azure-Rolle zuweisen, legen Sie den Zugriffsbereich fest, den der Sicherheitsprinzipal haben soll. Es hat sich als am besten bewährt, stets nur den kleinstmöglichen Umfang an Zugriffsrechten zu gewähren.

In der folgenden Liste werden die Ebenen beschrieben, auf denen Sie den Zugriff auf Service Bus-Ressourcen einschränken können, beginnend mit dem kleinstmöglichen Bereich:

- **Warteschlange** , **Thema** oder **Abonnement** : Die Rollenzuweisung gilt für die jeweilige Service Bus-Entität. Derzeit wird das Zuweisen von Benutzern/Gruppen/verwalteten Identitäten zu Service Bus Azure-Rollen auf Abonnementebene vom Azure-Portal nicht unterstützt. Im Folgenden finden Sie ein Beispiel für die Verwendung des Azure CLI-Befehls [az-role-assignment-create](/cli/azure/role/assignment?#az-role-assignment-create) für das Zuweisen einer Identität zu einer Service Bus-Azure-Rolle: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Service Bus-Namespace:** Die Rollenzuweisung umfasst die gesamte Topologie von Service Bus unter dem Namespace und für die zugeordnete Consumergruppe.
- **Ressourcengruppe** : Die Rollenzuweisung gilt für alle Service Bus-Ressourcen unter der Ressourcengruppe.
- **Abonnement** : Die Rollenzuweisung gilt für alle Service Bus-Ressourcen in allen Ressourcengruppen im Abonnement.

> [!NOTE]
> Denken Sie daran, dass die Weitergabe von Azure-Rollenzuweisungen bis zu fünf Minuten dauern kann. 

Weitere Informationen dazu, wie integrierte Rollen definiert sind, finden Sie unter [Grundlegendes zu Rollendefinitionen](../role-based-access-control/role-definitions.md#management-and-data-operations). Informationen zum Erstellen von benutzerdefinierten Azure-Rollen finden Sie unter [Benutzerdefinierte Azure-Rollen](../role-based-access-control/custom-roles.md).

## <a name="enable-managed-identities-on-a-vm"></a>Aktivieren von verwalteten Identitäten auf einem virtuellen Computer
Damit Sie verwaltete Identitäten für Azure-Ressourcen zum Autorisieren des Zugriffs auf Service Bus-Ressourcen über Ihren virtuellen Computer verwenden können, müssen Sie zunächst verwaltete Identitäten für Azure-Ressourcen auf dem virtuellen Computer aktivieren. Informationen zum Aktivieren von verwalteten Identitäten für Azure-Ressourcen finden Sie in diesen Artikeln:

- [Azure portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-Befehlszeilenschnittstelle](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-Vorlage](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager-Clientbibliotheken](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Erteilen von Berechtigungen für eine verwaltete Identität in Azure AD
Damit Sie eine Anforderung an den Service Bus-Dienst über eine verwaltete Identität in Ihrer Anwendung autorisieren können, konfigurieren Sie die Einstellungen der rollenbasierten Zugriffssteuerung in Azure (Role-Based Access Control, Azure RBAC) für diese verwaltete Identität. Azure Service Bus definiert Azure-Rollen, die Berechtigungen zum Senden an und Lesen aus Service Bus umfassen. Wenn die Azure-Rolle einer verwalteten Identität zugewiesen wird, wird der verwalteten Identität der Zugriff auf Service Bus-Entitäten im entsprechenden Bereich erteilt.

Weitere Informationen zum Zuweisen von Azure-Rollen finden Sie unter [Authentifizierung und Autorisierung mit Azure Active Directory für den Zugriff auf Service Bus-Ressourcen](authenticate-application.md#azure-built-in-roles-for-azure-service-bus).

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Verwenden von Service Bus mit verwalteten Identitäten für Azure-Ressourcen
Wenn Sie Service Bus mit verwalteten Identitäten verwenden möchten, müssen Sie der Identität die Rolle und den entsprechenden Bereich zuweisen. Das Verfahren in diesem Abschnitt verwendet eine einfache Anwendung, die unter einer verwalteten Identität ausgeführt wird und auf Service Bus-Ressourcen zugreift.

Hier verwenden wir eine einfache Beispielwebanwendung, die in [Azure App Service](https://azure.microsoft.com/services/app-service/) gehostet wird. Eine Schritt-für-Schritt-Anleitung zum Erstellen einer Webanwendung finden Sie unter [Erstellen einer ASP.NET Core-Web-App in Azure](../app-service/quickstart-dotnetcore.md).

Führen Sie nach der Erstellung der Anwendung die folgenden Schritte aus: 

1. Navigieren Sie zu **Einstellungen** , und wählen Sie **Identität** aus. 
1. Legen Sie den **Status** auf **Ein** fest. 
1. Klicken Sie auf **Speichern** , um die Einstellung zu speichern. 

    ![Verwaltete Identität für eine Web-App](./media/service-bus-managed-service-identity/identity-web-app.png)

Nachdem Sie diese Einstellung aktiviert haben, wird in Ihrer Azure AD-Instanz (Azure Active Directory) eine neue Dienstidentität erstellt und auf dem App Service-Host konfiguriert.

> [!NOTE]
> Bei Verwendung einer verwalteten Identität muss die Verbindungszeichenfolge im folgenden Format angegeben werden: `Endpoint=sb://<NAMESPACE NAME>.servicebus.windows.net/;Authentication=Managed Identity`.

Weisen Sie dieser Dienstidentität jetzt eine Rolle im erforderlichen Bereich in Ihren Service Bus-Ressourcen zu.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>So weisen Sie Azure-Rollen mit dem Azure-Portal zu
Um einem Service Bus-Namespace eine Rolle zuzuweisen, navigieren Sie im Azure-Portal zum betreffenden Namespace. Zeigen Sie die Einstellungen für die Zugriffssteuerung (IAM) für die Ressource an, und befolgen Sie diese Anleitung zum Verwalten von Rollenzuweisungen:

> [!NOTE]
> Mit den folgenden Schritten wird den Service Bus-Namespaces eine Dienstidentitätsrolle zugewiesen. Sie können die gleichen Schritte ausführen, um anderen unterstützten Bereichen (Ressourcengruppe und Abonnement) eine Rolle zuzuweisen. 
> 
> [Erstellen Sie einen Service Bus-Messagingnamespace](service-bus-create-namespace-portal.md), wenn dieser noch nicht vorhanden ist. 

1. Navigieren Sie im Azure-Portal zu Ihrem Service Bus-Namespace, und zeigen Sie die **Übersicht** für den Namespace an. 
1. Wählen Sie im linken Menü **Zugriffssteuerung (IAM)** aus, um Zugriffssteuerungseinstellungen für den Service Bus-Namespace anzuzeigen.
1.  Wählen Sie die Registerkarte **Rollenzuweisungen** aus, um die Liste mit den Rollenzuweisungen anzuzeigen.
3.  Klicken Sie auf **Hinzufügen** , um eine neue Rolle hinzuzufügen.
4.  Wählen Sie auf der Seite **Rollenzuweisung hinzufügen** die Azure Service Bus-Rollen aus, die Sie zuweisen möchten. Suchen Sie dann nach der Dienstidentität, die Sie registriert haben, um die Rolle zuzuweisen.
    
    ![Seite „Rollenzuweisung hinzufügen“](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  Wählen Sie **Speichern** aus. Die Identität, der Sie die Rolle zugewiesen haben, wird unter dieser Rolle angezeigt. Die folgende Abbildung zeigt, dass die Dienstidentität den Besitzer „Azure Service Bus-Daten“ verwendet.
    
    ![Identität, die einer Rolle zugewiesen ist](./media/service-bus-managed-service-identity/role-assigned.png)

Nachdem Sie die Rolle zugewiesen haben, verfügt die Webanwendung über Zugriff auf die Service Bus-Entitäten unter dem definierten Bereich. 




### <a name="run-the-app"></a>Ausführen der App
Bearbeiten Sie jetzt die Standardseite der von Ihnen erstellten ASP.NET-Anwendung. Sie können den Webanwendungscode aus [diesem GitHub-Repository](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet) verwenden.  

Die Seite „Default.aspx“ ist Ihre Zielseite. Sie finden den Code in der Datei „Default.aspx.cs“. Das Ergebnis ist eine kleine Webanwendung mit einigen wenigen Eingabefeldern sowie Schaltflächen zum **Senden** und **Empfangen** , mit denen eine Verbindung mit Service Bus hergestellt wird, um Nachrichten zu senden oder zu empfangen.

Beachten Sie, wie das Objekt [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) initialisiert wird. Der Code verwendet nicht den SAS-Tokenanbieter (Shared Access Signature), sondern erstellt mit dem Aufruf `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` einen Tokenanbieter für die verwaltete Identität. Daher müssen keine Geheimnisse aufbewahrt und verwendet werden. Der Flow vom Kontext der verwalteten Identität zu Service Bus und der Autorisierungshandshake werden automatisch vom Tokenanbieter verarbeitet. Dies ist ein einfacheres Modell als die Verwendung von SAS.

Nachdem Sie diese Änderungen vorgenommen haben, veröffentlichen Sie die Anwendung und führen sie aus. Sie können die richtigen Veröffentlichungsdaten einfach abrufen, indem Sie ein Veröffentlichungsprofil herunterladen und dann in Visual Studio importieren:

![Abrufen des Veröffentlichungsprofils](./media/service-bus-managed-service-identity/msi3.png)
 
Um Nachrichten zu senden oder zu empfangen, geben Sie den Namen des Namespace und der von Ihnen erstellten Entität ein. Klicken Sie dann entweder auf **Senden** oder auf **Empfangen**.


> [!NOTE]
> - Die verwaltete Identität kann nur in der Azure-Umgebung, in App Services, auf virtuellen Azure-Computern und in Skalierungsgruppen verwendet werden. Für .NET-Anwendungen bietet die vom Service Bus-NuGet-Paket verwendete Bibliothek „Microsoft.Azure.Services.AppAuthentication“ eine Abstraktion über dieses Protokoll und unterstützt eine lokale Entwicklungsumgebung. Mithilfe dieser Bibliothek können Sie zudem Ihren Code lokal auf dem Entwicklungscomputer testen. Hierzu verwenden Sie Ihr Benutzerkonto aus Visual Studio, aus der Azure CLI 2.0 oder der integrierten Active Directory-Authentifizierung. Weitere Informationen zu Optionen für die lokale Entwicklung mit dieser Bibliothek finden Sie unter [Dienst-zu-Dienst-Authentifizierung in Azure Key Vault mithilfe von .NET](../key-vault/general/service-to-service-authentication.md).  
> 
> - Verwaltete Identitäten funktionieren zurzeit nicht mit App Service-Bereitstellungsslots.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Service Bus-Messaging finden Sie in folgenden Themen:

* [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md)
* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Verwenden von Service Bus-Themen und -Abonnements](service-bus-dotnet-how-to-use-topics-subscriptions.md)
---
title: Authentifizieren einer Anwendung für den Zugriff auf Azure Event Hubs-Ressourcen
description: Dieser Artikel enthält Informationen zur Authentifizierung einer Anwendung mit Azure Active Directory, um auf Azure Event Hubs-Ressourcen zuzugreifen.
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 25ec5f11ca7b5e801e18155f1a3da6474c8e66e2
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913312"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Authentifizieren einer Anwendung mit Azure Active Directory, um auf Azure Event Hubs-Ressourcen zuzugreifen
Microsoft Azure bietet eine integrierte Verwaltung der Zugriffssteuerung für Ressourcen und Anwendungen, basierend auf Azure Active Directory (Azure AD). Ein wesentlicher Vorteil der Verwendung von Azure AD mit Azure Event Hubs besteht darin, dass Sie Ihre Anmeldeinformationen nicht mehr im Code speichern müssen. Stattdessen können Sie ein OAuth 2.0-Zugriffstoken von der Microsoft Identity Platform anfordern. Der Ressourcenname zum Anfordern eines Tokens lautet `https://eventhubs.azure.net/`. Dieser Wert ist für alle Clouds/Mandanten gleich. (Für Kafka-Clients lautet die Ressource zum Anfordern eines Tokens `https://<namespace>.servicebus.windows.net`.) Azure AD übernimmt die Authentifizierung des Sicherheitsprinzipals (Benutzer, Gruppe oder Dienstprinzipal), der die Anwendung ausführt. Wenn die Authentifizierung erfolgreich ist, gibt Azure AD ein Zugriffstoken an die Anwendung zurück, und die Anwendung kann dann das Zugriffstoken zum Autorisieren von Anforderungen an Azure Event Hubs verwenden.

Wenn einem Azure AD-Sicherheitsprinzipal eine Rolle zugewiesen wird, gewährt Azure diesem Sicherheitsprinzipal Zugriff auf diese Ressourcen. Der Zugriff kann sich auf den Bereich des Abonnements, der Ressourcengruppe, des Event Hubs-Namespace oder jeder darin vorhandenen Ressource beziehen. Ein Azure AD-Sicherheitsprinzipal kann einem Benutzer, einer Gruppe, einem Anwendungsdienstprinzipal oder einer [verwalteten Identität für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md) Rollen zuweisen. 

> [!NOTE]
> Eine Rollendefinition ist eine Sammlung von Berechtigungen. Die rollenbasierte Zugriffssteuerung in Azure (Role-Based Access Control, Azure RBAC) steuert, wie diese Berechtigungen mittels Rollenzuweisung erzwungen werden. Eine Rollenzuweisung besteht aus drei Elementen: Sicherheitsprinzipal, Rollendefinition und Bereich. Weitere Informationen finden Sie unter [Grundlegendes zu den verschiedenen Rollen](../role-based-access-control/overview.md).

## <a name="built-in-roles-for-azure-event-hubs"></a>Integrierte Rollen für Azure Event Hubs
Azure stellt die folgenden integrierten Azure-Rollen zum Autorisieren des Zugriffs auf Event Hubs-Daten mithilfe von Azure AD und OAuth bereit:

- [Azure Event Hubs-Datenbesitzer](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): Mit dieser Rolle gewähren Sie Vollzugriff auf Event Hubs-Ressourcen.
- [Azure Event Hubs-Datensender](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): Mit dieser Rolle gewähren Sie Sendezugriff auf Event Hubs-Ressourcen.
- [Azure Event Hubs-Datenempfänger](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): Mit dieser Rolle gewähren Sie Empfangszugriff auf Event Hubs-Ressourcen.   

Informationen zu den integrierten Rollen für die Schemaregistrierung finden Sie unter [Schemaregistrierungsrollen](schema-registry-overview.md#azure-role-based-access-control).

> [!IMPORTANT]
> In unserer Vorschauversion wurde das Hinzufügen von Event Hubs-Datenzugriffsberechtigungen für die Rollen „Besitzer“ und „Mitwirkender“ unterstützt. Datenzugriffsberechtigungen für die Rollen „Besitzer“ und „Mitwirkender“ werden jedoch nicht mehr berücksichtigt. Wenn Sie die Rolle „Besitzer“ oder „Mitwirkender“ verwenden, wechseln Sie zur Verwendung der Rolle „Azure Event Hubs-Datenbesitzer“.


## <a name="authenticate-from-an-application"></a>Authentifizieren über eine Anwendung
Ein wesentlicher Vorteil der Verwendung von Azure AD mit Event Hubs besteht darin, dass Ihre Anmeldeinformationen nicht mehr im Code gespeichert werden müssen. Stattdessen können Sie ein OAuth 2.0-Zugriffstoken von Microsoft Identity Platform anfordern. Azure AD übernimmt die Authentifizierung des Sicherheitsprinzipals (Benutzer, Gruppe oder Dienstprinzipal), der die Anwendung ausführt. Wenn die Authentifizierung erfolgreich ist, gibt Azure AD das Zugriffstoken an die Anwendung zurück, und die Anwendung kann dann das Zugriffstoken zum Autorisieren von Anforderungen an Azure Event Hubs verwenden.

In den folgenden Abschnitten wird gezeigt, wie Sie Ihre native Anwendung oder Webanwendung für die Authentifizierung mit Microsoft Identity Platform 2.0 konfigurieren. Weitere Informationen zu Microsoft Identity Platform 2.0 finden Sie unter [Übersicht über Microsoft Identity Platform (v2.0)](../active-directory/develop/v2-overview.md).

Eine Übersicht über den Datenfluss für OAuth 2.0-Codeberechtigungen finden Sie unter [Autorisieren des Zugriffs auf Azure Active Directory-Webanwendungen mit dem Flow zum Erteilen des OAuth 2.0-Codes](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrieren der Anwendung bei einem Azure AD-Mandanten
Der erste Schritt bei der Verwendung von Azure AD zum Autorisieren von Event Hubs-Ressourcen ist die Registrierung Ihrer Clientanwendung mit einem Azure AD-Mandanten im [Azure-Portal](https://portal.azure.com/). Wenn Sie Ihre Clientanwendung registrieren, stellen Sie Azure AD Informationen zu Ihrer Anwendung bereit. Azure AD stellt dann eine Client-ID (auch als Anwendungs-ID bezeichnet) bereit, mit der Sie Ihre Anwendung zur Laufzeit Azure AD zuordnen können. Weitere Informationen zur Client-ID finden Sie unter [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory (Azure AD)](../active-directory/develop/app-objects-and-service-principals.md). 

Die folgenden Abbildungen zeigen Schritte zum Registrieren einer Webanwendung:

![Registrieren einer Anwendung](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Wenn Sie Ihre Anwendung als native Anwendung registrieren, können Sie jeden gültigen URI als Umleitungs-URI angeben. Bei nativen Anwendungen muss dieser Wert keine echte URL sein. Bei Webanwendungen muss der Umleitungs-URI ein gültiger URI sein, da er die URL definiert, für die Token bereitgestellt werden.

Nachdem Sie Ihre Anwendung registriert haben, wird die **Anwendungs-ID (Client-ID)** unter **Einstellungen** angezeigt:

![Anwendungs-ID der registrierten Anwendung](./media/authenticate-application/application-id.png)

Ausführlichere Informationen zum Registrieren einer Anwendung bei Azure AD finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](../active-directory/develop/quickstart-register-app.md).


### <a name="create-a-client-secret"></a>Erstellen eines Clientgeheimnisses   
Die Anwendung benötigt zum Beweis ihrer Identität einen geheimen Clientschlüssel, wenn sie ein Token anfordert. Führen Sie folgende Schritte aus, um den geheimen Clientschlüssel hinzuzufügen.

1. Navigieren Sie im Azure-Portal zu Ihrer App-Registrierung.
1. Klicken Sie auf die Einstellung **Certificates & secrets** (Zertifikate & Geheime Schlüssel).
1. Wählen Sie unter **Geheime Clientschlüssel** die Option **Neuer geheimer Clientschlüssel** aus, um einen neuen geheimen Schlüssel zu erstellen.
1. Geben Sie eine Beschreibung für den geheimen Schlüssel an, und wählen Sie das gewünschte Ablaufintervall aus.
1. Kopieren Sie den Wert des neuen geheimen Schlüssels sofort, und legen Sie die Kopie an einem sicheren Speicherort ab. Der Wert wird Ihnen nur ein Mal angezeigt.

    ![Geheimer Clientschlüssel](./media/authenticate-application/client-secret.png)


## <a name="assign-azure-roles-using-the-azure-portal"></a>Zuweisen von Azure-Rollen über das Azure-Portal  
Nach dem Registrieren der Anwendung weisen Sie den Dienstprinzipal der Anwendung einer Event Hubs-Azure AD-Rolle zu. Eine Beschreibung finden Sie im Abschnitt [Integrierte Rollen für Azure Event Hubs](#built-in-roles-for-azure-event-hubs). 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem Event Hubs-Namespace.
2. Wählen Sie auf der Seite **Übersicht** den Event Hub aus, für den Sie eine Rolle zuweisen möchten.

    ![Auswählen Ihres Event Hubs](./media/authenticate-application/select-event-hub.png)
1. Wählen Sie **Zugriffssteuerung (IAM)** aus, um Zugriffssteuerungseinstellungen für den Event Hub anzuzeigen. 
1. Wählen Sie die Registerkarte **Rollenzuweisungen** aus, um die Liste mit den Rollenzuweisungen anzuzeigen. Wählen Sie die Schaltfläche **Hinzufügen** auf der Symbolleiste und dann **Rollenzuweisung hinzufügen** aus. 

    ![Schaltfläche „Hinzufügen“ auf der Symbolleiste](./media/authenticate-application/role-assignments-add-button.png)
1. Führen Sie auf der Seite **Rollenzuweisung hinzufügen** die folgenden Schritte aus:
    1. Wählen Sie die **Event Hubs-Rolle** aus, die Sie zuweisen möchten. 
    1. Suchen Sie nach dem **Sicherheitsprinzipal** (Benutzer, Gruppe, Dienstprinzipal), dem Sie die Rolle zuweisen möchten. Wählen Sie die **registrierte Anwendung** aus der Liste aus. 
    1. Klicken Sie auf **Speichern** , um die Rollenzuweisung zu speichern. 

        ![Zuweisen einer Rolle zu einem Benutzer](./media/authenticate-application/assign-role-to-user.png)
    4. Wechseln Sie zur Registerkarte **Rollenzuweisungen** , und überprüfen Sie die Rollenzuweisung. Die folgende Abbildung zeigt beispielsweise, dass sich **mywebapp** in der Rolle **Azure Event Hubs-Datensender** befindet. 
        
        ![Benutzer in der Liste](./media/authenticate-application/user-in-list.png)

Sie können ähnliche Schritte ausführen, um eine Rolle zuzuweisen, deren Umfang für einen Event Hubs-Namespace, eine Ressourcengruppe oder ein Abonnement gilt. Nachdem Sie die Rolle und ihren Bereich definiert haben, können Sie dieses Verhalten mit den Beispielen [an diesem GitHub-Speicherort](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) testen. Weitere Informationen zum Verwalten des Zugriffs auf Azure-Ressourcen mithilfe der Azure RBAC und des Azure-Portals finden Sie in [diesem Artikel](..//role-based-access-control/role-assignments-portal.md). 


### <a name="client-libraries-for-token-acquisition"></a>Clientbibliotheken für die Tokenbeschaffung  
Sobald Sie Ihre Anwendung registriert und ihr die Berechtigung zum Senden/Empfangen von Daten in Azure Event Hubs erteilt haben, können Sie Ihrer Anwendung Code hinzufügen, um einen Sicherheitsprinzipal zu authentifizieren und ein OAuth 2.0-Token abzurufen. Zum Authentifizieren und Abrufen des Tokens können Sie entweder eine der [Authentifizierungsbibliotheken von Microsoft Identity Platform](../active-directory/develop/reference-v2-libraries.md) oder eine andere Open-Source-Bibliothek verwenden, die OpenID oder Connect 1.0 unterstützt. Ihre Anwendung kann dann mit dem Zugriffstoken eine Anforderung an Azure Event Hubs autorisieren.

Eine Liste der Szenarien, für die das Abrufen von Token unterstützt wird, finden Sie im GitHub-Repository [Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) im Bereich [Scenarios](https://aka.ms/msal-net-scenarios).

## <a name="samples"></a>Beispiele
- [Microsoft.Azure.EventHubs-Beispiele](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    In diesen Beispielen wird die alte **Microsoft.Azure.EventHubs** -Bibliothek verwendet, aber Sie können sie problemlos so aktualisieren, dass die neueste **Azure.Messaging.EventHubs** -Bibliothek verwendet wird. Informationen dazu, wie Sie das Beispiel nicht mit der alten, sondern mit der neuen Bibliothek verwenden, finden Sie in der [Anleitung für die Migration von Microsoft.Azure.EventHubs zu Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [Azure.Messaging.EventHubs-Beispiele](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Dieses Beispiel wurde so aktualisiert, dass die aktuelle **Azure.Messaging.EventHubs** -Bibliothek verwendet wird.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zur Azure RBAC finden Sie im Artikel [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../role-based-access-control/overview.md).
- Informationen zum Zuweisen und Verwalten von Azure-Rollenzuweisungen mit Azure PowerShell, der Azure-Befehlszeilenschnittstelle oder der REST-API finden Sie in diesen Artikeln:
    - [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe von Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure-Befehlszeilenschnittstelle](../role-based-access-control/role-assignments-cli.md)
    - [Hinzufügen oder Entfernen von Rollenzuweisungen mithilfe von Azure RBAC und der REST-API](../role-based-access-control/role-assignments-rest.md)
    - [Hinzufügen von Azure-Rollenzuweisungen mithilfe von Azure Resource Manager-Vorlagen](../role-based-access-control/role-assignments-template.md)

Weitere Informationen finden Sie in den folgenden verwandten Artikeln:
- [Authentifizieren einer verwalteten Identität mit Azure Active Directory für den Zugriff auf Event Hubs-Ressourcen](authenticate-managed-identity.md)
- [Authentifizieren von Anforderungen an Azure Event Hubs mithilfe von Shared Access Signature](authenticate-shared-access-signature.md)
- [Autorisieren des Zugriffs auf Event Hubs-Ressourcen mit Azure Active Directory](authorize-access-azure-active-directory.md)
- [Autorisieren des Zugriffs auf Event Hubs-Ressourcen mit Shared Access Signature](authorize-access-shared-access-signature.md)

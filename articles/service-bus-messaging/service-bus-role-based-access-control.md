---
title: Rollenbasierte Zugriffssteuerung in Azure Service Bus (Vorschau) | Microsoft-Dokumentation
description: Rollenbasierte Zugriffssteuerung in Azure Service Bus
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
ms.date: 09/19/2018
ms.author: aschhab
ms.openlocfilehash: e4571a8918b7877b728b54129e47ffcf4af9b46a
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979642"
---
# <a name="active-directory-role-based-access-control-preview"></a>Rollenbasierte Zugriffssteuerung in Active Directory (Vorschau)

Microsoft Azure bietet eine integrierte Verwaltung der Zugriffssteuerung für Ressourcen und Anwendungen, basierend auf Azure Active Directory (Azure AD). Mit Azure AD können Sie Benutzerkonten und Anwendungen speziell für Ihre Azure-basierten Anwendungen verwalten. Alternativ dazu können Sie einen Verbund aus Ihrer vorhandenen Active Directory-Infrastruktur und Azure AD erstellen, um eine unternehmensweite Funktion für das einmalige Anmelden zu erhalten, die auch für Azure-Ressourcen und in Azure gehostete Anwendungen gilt. Diese Benutzer- und Anwendungsidentitäten aus Azure AD können Sie dann globalen und dienstspezifischen Rollen zuweisen, um Zugriff auf Azure-Ressourcen zu gewähren.

Bei Azure Service Bus ist die Verwaltung der Namespaces und aller zugehörigen Ressourcen über das Azure-Portal und die Azure-Ressourcenverwaltungs-API bereits durch das Modell der *rollenbasierten Zugriffssteuerung* geschützt. Das Feature der rollenbasierten Zugriffssteuerung für Laufzeitvorgänge ist jetzt in der öffentlichen Vorschau verfügbar.

Eine Anwendung, die die rollenbasierte Zugriffssteuerung von Azure AD verwendet, muss keine SAS-Regeln und Schlüssel oder andere Zugriffstoken für Service Bus verarbeiten. Die Client-App interagiert mit Azure AD, um einen Authentifizierungskontext herzustellen, und erwirbt ein Zugriffstoken für Service Bus. Bei Domänenbenutzerkonten, die eine interaktive Anmeldung erfordern, verarbeitet die Anwendung die Anmeldeinformationen niemals direkt.

## <a name="service-bus-roles-and-permissions"></a>Service Bus-Rollen und -Berechtigungen

Azure stellt die folgenden integrierten Rollen für die rollenbasierte Zugriffssteuerung zum Autorisieren des Zugriffs auf einen Service Bus-Namespace bereit:

* [Service Bus-Datenbesitzer (Vorschau)](../role-based-access-control/built-in-roles.md#service-bus-data-owner): Ermöglicht den Datenzugriff auf einen Service Bus-Namespace und seine Entitäten (Warteschlangen, Themen, Abonnements und Filter).

>[!IMPORTANT]
> Früher haben wir das Hinzufügen einer verwalteten Identität zur Rolle **Besitzer** oder **Mitwirkender** unterstützt.
>
> Die Berechtigungen für den Datenzugriff für die Rolle **Besitzer** und **Mitwirkender** werden jedoch nicht mehr berücksichtigt. Wenn Sie die Rolle **Besitzer** oder **Mitwirkender** verwendet haben, müssen diese so angepasst werden, dass sie nun die Rolle **Service Bus-Datenbesitzer** verwenden.

## <a name="use-service-bus-with-an-azure-ad-domain-user-account"></a>Verwenden von Service Bus mit einem Azure AD-Domänenbenutzerkonto

Der folgende Abschnitt beschreibt die Schritte, die erforderlich sind, um eine Beispielanwendung zu erstellen und bereitzustellen, die einen Benutzer zur interaktiven Azure AD-Anmeldung auffordert. Zudem erläutert der Abschnitt, wie Sie Service Bus Zugriff auf dieses Benutzerkonto gewähren und wie Sie diese Identität für den Zugriff auf Event Hubs verwenden.

Diese Einführung beschreibt eine einfache Konsolenanwendung. Den [zugehörigen Code finden Sie auf GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).

### <a name="create-an-active-directory-user-account"></a>Erstellen eines Active Directory-Benutzerkontos

Dieser erste Schritt ist optional. Jedes Azure-Abonnement ist automatisch an einen Azure Active Directory-Mandanten gekoppelt, und wenn Sie Zugriff auf ein Azure-Abonnement haben, ist Ihr Benutzerkonto bereits registriert. Das bedeutet, dass Sie einfach Ihr Konto verwenden können.

Wenn Sie dennoch ein spezifisches Konto für dieses Szenario erstellen möchten, [führen Sie diese Schritte aus](../automation/automation-create-aduser-account.md). Sie müssen über die Berechtigung verfügen, Konten im Azure Active Directory-Mandanten zu erstellen. Dies ist in größeren Unternehmensszenarien möglicherweise nicht der Fall.

### <a name="create-a-service-bus-namespace"></a>Erstellen eines Service Bus-Namespace

Als Nächstes [erstellen Sie einen Service Bus Messaging-Namespace](service-bus-create-namespace-portal.md).

Wenn der Namespace erstellt wurde, navigieren Sie im Portal zur Seite **Zugriffssteuerung (IAM)** des Namespace, und klicken Sie auf **Rollenzuweisung hinzufügen**, um das Azure AD-Benutzerkonto der Rolle „Besitzer“ hinzuzufügen. Wenn Sie Ihr eigenes Benutzerkonto verwenden und den Namespace selbst erstellt haben, befinden Sie sich bereits in der Rolle „Besitzer“. Um der Rolle ein anderes Konto hinzuzufügen, suchen Sie im Bereich **Berechtigungen hinzufügen** im Feld **Auswählen** nach dem Namen der Webanwendung, und klicken Sie auf den entsprechenden Eintrag. Klicken Sie anschließend auf **Speichern**.

Das Benutzerkonto verfügt jetzt über Zugriff auf den Service Bus-Namespace und die zuvor erstellte Warteschlange.

### <a name="register-the-application"></a>Registrieren der Anwendung

Bevor Sie die Beispielanwendung ausführen können, registrieren Sie sie in Azure AD, und bestätigen Sie die Zustimmungsaufforderung, die der Anwendung in Ihrem Namen Zugriff auf Azure Service Bus gewährt.

Da es sich bei der Beispielanwendung um eine Konsolenanwendung handelt, müssen Sie eine native Anwendung registrieren und den „erforderlichen Berechtigungen“ API-Berechtigungen für **Microsoft.ServiceBus** hinzufügen. Native Anwendungen benötigen auch einen **redirect-URI**-Wert in Azure AD, der als Bezeichner fungiert. Bei dem URI muss es sich nicht um ein Netzwerkziel handeln. Verwenden Sie in diesem Beispiel `https://servicebus.microsoft.com`, da der Beispielcode diesen URI bereits verwendet.

Die detaillierten Schritte für die Registrierung werden in [diesem Tutorial](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) erläutert. Führen Sie die Schritte zum Registrieren einer **nativen** App aus, und befolgen Sie dann die Aktualisierungsanweisungen, um die **Microsoft.ServiceBus**-API zu den erforderlichen Berechtigungen hinzuzufügen. Notieren Sie sich die Werte für **TenantId** und **ApplicationId** – diese werden Sie zum Ausführen der Anwendung benötigen.

### <a name="run-the-app"></a>Ausführen der App

Bevor Sie das Beispiel ausführen können, müssen Sie die Datei „App.config“ bearbeiten und gemäß Ihrem Szenario die folgenden Werte festlegen:

- `tenantId`: Legen Sie diesen Parameter auf den Wert **TenantId** fest.
- `clientId`: Legen Sie diesen Parameter auf den Wert **ApplicationId** fest.
- `clientSecret`: Wenn Sie beim Anmelden einen geheimen Clientschlüssel verwenden möchten, erstellen Sie diesen in Azure AD. Verwenden Sie zudem eine Web-App oder eine API anstelle einer nativen App. Fügen Sie die App außerdem unter **Zugriffssteuerung (IAM)** zum zuvor erstellten Namespace hinzu.
- `serviceBusNamespaceFQDN`: Legen Sie diesen Parameter auf den vollständigen DNS-Namen Ihres neu erstellten Service Bus-Namespace fest, z.B. `example.servicebus.windows.net`.
- `queueName`: Legen Sie diesen Parameter auf die von Ihnen erstellte Warteschlange fest.
- Der Umleitungs-URI, den Sie in den vorherigen Schritten in Ihrer App angegeben haben.

Wenn Sie die Konsolenanwendung ausführen, werden Sie aufgefordert, ein Szenario auszuwählen. Klicken Sie nach Eingabe der zugehörigen Zahl auf **Interaktive Benutzeranmeldung**, und drücken Sie die EINGABETASTE. Die Anwendung zeigt ein Anmeldefenster an, fordert Ihre Zustimmung zum Zugriff auf Service Bus an und verwendet den Dienst dann, um das Senden/Empfangen-Szenario unter Verwendung der Anmeldeidentität auszuführen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Service Bus Messaging finden Sie in folgenden Themen.

* [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md)
* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Verwenden von Service Bus-Themen und -Abonnements](service-bus-dotnet-how-to-use-topics-subscriptions.md)

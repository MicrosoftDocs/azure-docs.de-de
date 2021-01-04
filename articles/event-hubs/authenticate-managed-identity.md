---
title: Authentifizieren einer verwalteten Identität mit Azure Active Directory
description: Dieser Artikel enthält Informationen zur Authentifizierung einer verwalteten Identität mit Azure Active Directory, um auf Azure Event Hubs-Ressourcen zuzugreifen.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: c6b43cc48663be28d12fa788d92286be6f47ef08
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993532"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Authentifizieren einer verwalteten Identität mit Azure Active Directory für den Zugriff auf Event Hubs-Ressourcen
Azure Event Hubs unterstützt die Azure AD-Authentifizierung (Azure Active Directory) mit [verwalteten Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md). Sie können verwaltete Identitäten für Azure-Ressourcen verwenden, um den Zugriff auf Event Hubs-Ressourcen mithilfe von Azure AD-Anmeldeinformationen über Anwendungen zu autorisieren, die auf virtuellen Azure-Computern, in Funktions-Apps, in VM-Skalierungsgruppen und anderen Diensten ausgeführt werden. Durch Verwendung von verwalteten Identitäten für Azure-Ressourcen zusammen mit der Azure AD-Authentifizierung können Sie vermeiden, dass Anmeldeinformationen mit den in der Cloud ausgeführten Anwendungen gespeichert werden.

In diesem Artikel wird die Autorisierung des Zugriffs auf einen Event Hub mit einer verwalteten Identität über einen virtuellen Azure-Computer erläutert.

## <a name="enable-managed-identities-on-a-vm"></a>Aktivieren von verwalteten Identitäten auf einem virtuellen Computer
Damit Sie verwaltete Identitäten für Azure-Ressourcen zum Autorisieren des Zugriffs auf Event Hubs-Ressourcen über Ihren virtuellen Computer verwenden können, müssen Sie zunächst verwaltete Identitäten für Azure-Ressourcen auf dem virtuellen Computer aktivieren. Informationen zum Aktivieren von verwalteten Identitäten für Azure-Ressourcen finden Sie in diesen Artikeln:

- [Azure portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-Befehlszeilenschnittstelle](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-Vorlage](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager-Clientbibliotheken](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Erteilen von Berechtigungen für eine verwaltete Identität in Azure AD
Damit Sie eine Anforderung an den Event Hubs-Dienst über eine verwaltete Identität in Ihrer Anwendung autorisieren können, müssen Sie zuerst die Einstellungen der rollenbasierten Zugriffssteuerung in Azure (Role-Based Access Control, Azure RBAC) für diese verwaltete Identität konfigurieren. Für Azure Event Hubs werden Azure-Rollen definiert, die Berechtigungen zum Senden an und Lesen aus Event Hubs umfassen. Wenn die Azure-Rolle einer verwalteten Identität zugewiesen wird, wird der verwalteten Identität der Zugriff auf Event Hubs-Daten im entsprechenden Bereich erteilt.

Weitere Informationen zum Zuweisen von Azure-Rollen finden Sie unter [Autorisieren des Zugriffs auf Event Hubs-Ressourcen mit Azure Active Directory](authorize-access-azure-active-directory.md).

## <a name="use-event-hubs-with-managed-identities"></a>Verwenden von Event Hubs mit verwalteten Identitäten
Wenn Sie Event Hubs mit verwalteten Identitäten verwenden möchten, müssen Sie der Identität die Rolle und den entsprechenden Bereich zuweisen. Im Verfahren in diesem Abschnitt wird eine einfache Anwendung verwendet, die unter einer verwalteten Identität ausgeführt wird und auf Event Hubs-Ressourcen zugreift.

Hier verwenden wir eine einfache Beispielwebanwendung, die in [Azure App Service](https://azure.microsoft.com/services/app-service/) gehostet wird. Eine Schritt-für-Schritt-Anleitung zum Erstellen einer Webanwendung finden Sie unter [Erstellen einer ASP.NET Core-Web-App in Azure](../app-service/quickstart-dotnetcore.md).

Führen Sie nach der Erstellung der Anwendung die folgenden Schritte aus: 

1. Navigieren Sie zu **Einstellungen**, und wählen Sie **Identität** aus. 
1. Legen Sie den **Status** auf **Ein** fest. 
1. Klicken Sie auf **Speichern**, um die Einstellung zu speichern. 

    ![Verwaltete Identität für eine Web-App](./media/authenticate-managed-identity/identity-web-app.png)

Nachdem Sie diese Einstellung aktiviert haben, wird in Ihrer Azure AD-Instanz (Azure Active Directory) eine neue Dienstidentität erstellt und auf dem App Service-Host konfiguriert.

Weisen Sie dieser Dienstidentität jetzt eine Rolle im erforderlichen Bereich in Ihren Event Hubs-Ressourcen zu.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>So weisen Sie Azure-Rollen mit dem Azure-Portal zu
Navigieren Sie im Azure-Portal zur jeweiligen Ressource, um Event Hubs-Ressourcen eine Rolle zuzuweisen. Zeigen Sie die Einstellungen für die Zugriffssteuerung (IAM) für die Ressource an, und befolgen Sie diese Anleitung zum Verwalten von Rollenzuweisungen:

> [!NOTE]
> Mit den folgenden Schritten wird Ihren Event Hubs-Namespaces eine Dienstidentitätsrolle zugewiesen. Sie können dieselben Schritte ausführen, um eine Rolle zuzuweisen, die für eine Event Hubs-Ressource bestimmt ist. 

1. Navigieren Sie im Azure-Portal zu Ihrem Event Hubs-Namespace, und zeigen Sie die **Übersicht** für den Namespace an. 
1. Wählen Sie im linken Menü die Option **Zugriffssteuerung (IAM)** aus, um Zugriffssteuerungseinstellungen für den Event Hub anzuzeigen.
1.  Wählen Sie die Registerkarte **Rollenzuweisungen** aus, um die Liste mit den Rollenzuweisungen anzuzeigen.
3.  Klicken Sie auf **Hinzufügen**, um eine neue Rolle hinzuzufügen.
4.  Wählen Sie auf der Seite **Rollenzuweisung hinzufügen** die Event Hubs-Rollen aus, die Sie zuweisen möchten. Suchen Sie dann nach der Dienstidentität, die Sie registriert haben, um die Rolle zuzuweisen.
    
    ![Seite „Rollenzuweisung hinzufügen“](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  Wählen Sie **Speichern** aus. Die Identität, der Sie die Rolle zugewiesen haben, wird unter dieser Rolle angezeigt. Die folgende Abbildung zeigt, dass für die Dienstidentität der Besitzer „Event Hubs-Daten“ verwendet wird.
    
    ![Identität, die einer Rolle zugewiesen ist](./media/authenticate-managed-identity/role-assigned.png)

Nachdem Sie die Rolle zugewiesen haben, verfügt die Webanwendung über Zugriff auf die Event Hubs-Ressourcen unter dem definierten Bereich. 

### <a name="test-the-web-application"></a>Testen der Webanwendung
1. Erstellen Sie einen Event Hubs-Namespace und einen Event Hub. 
2. Bereitstellen der Web-App in Azure Links zur Webanwendung auf GitHub finden Sie im folgenden Abschnitt im Registerkartenformat. 
3. Stellen Sie sicher, dass „SendReceive.aspx“ als Standarddokument für die Web-App festgelegt ist. 
3. Aktivieren Sie **Identität** für die Web-App. 
4. Weisen Sie diese Identität der Rolle **Event Hubs-Datenbesitzer** auf Namespace- oder Event Hub-Ebene zu. 
5. Führen Sie die Webanwendung aus, geben Sie den Namespacenamen und Event Hub-Namen sowie eine Nachricht ein, und wählen Sie dann **Senden** aus. Um das Ereignis zu empfangen, wählen Sie **Empfangen** aus. 

#### <a name="azuremessagingeventhubs-latest"></a>[Azure.Messaging.EventHubs (neuestes Paket)](#tab/latest)
Sie können Ihre Webanwendung jetzt starten und für Ihren Browser auf die ASPX-Beispielseite verweisen. Sie finden die Beispielwebanwendung, mit der Daten für Event Hubs-Ressourcen gesendet und empfangen werden, im [GitHub-Repository](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp).

Installieren Sie das neueste Paket von [NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/), und beginnen Sie mit dem Senden von Ereignissen an Event Hubs mit **EventHubProducerClient** und dem Empfangen von Ereignissen mit **EventHubConsumerClient**. 

> [!NOTE]
> Ein Java-Beispiel, in dem eine verwaltete Identität zum Veröffentlichen von Ereignissen für einen Event Hub verwendet wird, finden Sie unter [„Identitätsbeispiel: Veröffentlichen von Ereignissen mit Azure“ auf GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs).

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs (Legacypaket)](#tab/old)
Sie können Ihre Webanwendung jetzt starten und für Ihren Browser auf die ASPX-Beispielseite verweisen. Sie finden die Beispielwebanwendung, mit der Daten für Event Hubs-Ressourcen gesendet und empfangen werden, im [GitHub-Repository](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp).

Installieren Sie das aktuelle Paket von [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/), und beginnen Sie mit dem Senden und Empfangen von Daten von Event Hubs, indem Sie wie im folgenden Code das EventHubClient-Element verwenden: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>Event Hubs für Kafka
Sie können Apache Kafka-Anwendungen verwenden, um mithilfe von mit OAuth verwalteter Identität Nachrichten an Azure Event Hubs zu senden und von diesen zu empfangen. Auf GitHub ist das folgende Beispiele verfügbar: [Event Hubs für Kafka: Senden und Empfangen von Nachrichten mithilfe von mit OAuth verwalteter Identität](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity).

## <a name="samples"></a>Beispiele
- **Azure.Messaging.EventHubs**-Beispiele
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Microsoft.Azure.EventHubs-Beispiele](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    In diesen Beispielen wird die alte **Microsoft.Azure.EventHubs**-Bibliothek verwendet, aber Sie können sie problemlos so aktualisieren, dass die neueste **Azure.Messaging.EventHubs**-Bibliothek verwendet wird. Informationen dazu, wie Sie das Beispiel nicht mit der alten, sondern mit der neuen Bibliothek verwenden, finden Sie in der [Anleitung für die Migration von Microsoft.Azure.EventHubs zu Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
    Dieses Beispiel wurde so aktualisiert, dass die aktuelle **Azure.Messaging.EventHubs**-Bibliothek verwendet wird.
- [Event Hubs für Kafka: Senden und Empfangen von Nachrichten mithilfe von mit OAuth verwalteter Identität](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>Nächste Schritte
- Informationen zu verwalteten Identitäten für Azure-Ressourcen finden Sie im folgenden Artikel: [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md)
- Weitere Informationen finden Sie in den folgenden verwandten Artikeln:
    - [Authentifizieren von Anforderungen an Event Hubs über eine Anwendung mithilfe von Azure Active Directory](authenticate-application.md)
    - [Authentifizieren von Anforderungen an Azure Event Hubs mithilfe von Shared Access Signature](authenticate-shared-access-signature.md)
    - [Autorisieren des Zugriffs auf Event Hubs-Ressourcen mit Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autorisieren des Zugriffs auf Event Hubs-Ressourcen mit Shared Access Signature](authorize-access-shared-access-signature.md)

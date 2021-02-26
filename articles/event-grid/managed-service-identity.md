---
title: Ereignisbereitstellung, verwaltete Dienstidentität und private Verbindung
description: In diesem Artikel wird beschrieben, wie Sie die verwaltete Dienstidentität für ein Azure Event Grid-Thema aktivieren. So können Sie Ereignisse an unterstützte Ziele weiterleiten.
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 3e643465db7cc918499ca962c4697cb61cb4b594
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007770"
---
# <a name="event-delivery-with-a-managed-identity"></a>Ereignisübermittlung mit einer verwalteten Identität
In diesem Artikel wird beschrieben, wie Sie die [verwaltete Dienstidentität](../active-directory/managed-identities-azure-resources/overview.md) für benutzerdefinierte Azure Event Grid-Themen oder -Domänen aktivieren. Verwenden Sie diese Methode, um Ereignisse an unterstützte Ziele wie Service Bus-Warteschlangen und -Themen, Event Hubs und Speicherkonten weiterzuleiten.

Folgende Schritte werden in diesem Artikel ausführlich behandelt:
1. Erstellen Sie ein benutzerdefiniertes Thema oder eine benutzerdefinierte Domäne mit einer vom System zugewiesenen Identität, oder aktualisieren Sie ein vorhandenes benutzerdefiniertes Thema oder eine vorhandene benutzerdefinierte Domäne, um die Identität zu aktivieren. 
1. Fügen Sie die Identität einer geeigneten Rolle (z. B. Azure Service Bus-Datensender) für das Ziel (z. B. Service Bus-Warteschlange) zu.
1. Aktivieren Sie beim Erstellen von Ereignisabonnements die Verwendung der Identität, um Ereignisse an das Ziel zu übermitteln. 

> [!NOTE]
> Derzeit ist es nicht möglich, Ereignisse über [private Endpunkte](../private-link/private-endpoint-overview.md) zu übermitteln. Weitere Informationen finden Sie im Abschnitt [Private Endpunkte](#private-endpoints) am Ende dieses Artikels. 

## <a name="create-a-custom-topic-or-domain-with-an-identity"></a>Erstellen eines benutzerdefinierten Themas/einer benutzerdefinierten Domäne mit einer Identität
Sehen Sie sich zunächst an, wie Sie ein Thema oder eine Domäne mit einer vom System verwalteten Identität erstellen.

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals
Beim Erstellen im Azure-Portal können Sie die vom System zugewiesene Identität für ein benutzerdefiniertes Thema/eine benutzerdefinierte Domäne aktivieren. Die folgende Abbildung zeigt, wie die vom System verwaltete Identität für ein benutzerdefiniertes Thema aktiviert wird. Im Wesentlichen wählen Sie im Assistenten zum Erstellen von Themen auf der Seite **Erweitert** die Option **Systemseitig zugewiesene Identität aktivieren** aus. Diese Option wird auch auf der Seite **Erweitert** des Assistenten zum Erstellen von Domänen angezeigt. 

![Aktivieren der Identität beim Erstellen eines benutzerdefinierten Themas](./media/managed-service-identity/create-topic-identity.png)

### <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI
Sie können ein benutzerdefiniertes Thema oder eine benutzerdefinierte Domäne mit einer vom System zugewiesenen Identität auch mit der Azure-Befehlszeilenschnittstelle erstellen. Verwenden Sie den Befehl `az eventgrid topic create`, wobei der Parameter `--identity` auf `systemassigned` festgelegt ist. Wenn Sie für diesen Parameter keinen Wert angeben, wird der Standardwert `noidentity` verwendet. 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Entsprechend können Sie den Befehl `az eventgrid domain create` verwenden, um eine Domäne mit einer vom System verwalteten Identität zu erstellen.

## <a name="enable-an-identity-for-an-existing-custom-topic-or-domain"></a>Aktivieren der Identität für ein vorhandenes benutzerdefiniertes Thema/eine vorhandene benutzerdefinierte Domäne
Im letzten Abschnitt haben Sie erfahren, wie Sie die vom System verwaltete Identität beim Erstellen eines benutzerdefinierten Themas oder einer benutzerdefinierten Domäne aktivieren. In diesem Abschnitt erfahren Sie, wie Sie eine vom System verwaltete Identität für ein vorhandenes benutzerdefiniertes Thema oder eine vorhandene benutzerdefinierte Domäne aktivieren. 

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals
Das folgende Verfahren zeigt, wie die vom System verwaltete Identität für ein benutzerdefiniertes Thema aktiviert wird. Die Schritte zum Aktivieren einer Identität für eine Domäne sind ähnlich. 

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Suchen Sie oben in der Suchleiste nach **Event Grid-Themen**.
3. Wählen Sie das **benutzerdefinierte Thema** aus, für das Sie die verwaltete Identität aktivieren möchten. 
4. Wechseln Sie zur Registerkarte **Identität**. 
5. **Aktivieren** Sie den Schalter, um die Identität zu aktivieren. 
1. Wählen Sie auf der Symbolleiste **Speichern** aus, um die Einstellung zu speichern. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Seite „Identität“ für ein benutzerdefiniertes Thema"::: 

Sie können ähnliche Schritte zum Aktivieren der Identität für eine Event Grid-Domäne verwenden.

### <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI
Verwenden Sie den Befehl `az eventgrid topic update`, wobei Sie `--identity` auf `systemassigned` festlegen, um die vom System zugewiesene Identität für ein vorhandenes benutzerdefiniertes Thema zu aktivieren. Wenn Sie die Identität deaktivieren möchten, geben Sie als Wert `noidentity` an. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

Der Befehl zum Aktualisieren einer vorhandenen Domäne lautet entsprechend (`az eventgrid domain update`).

## <a name="supported-destinations-and-azure-roles"></a>Unterstützte Ziele und Azure-Rollen
Nachdem Sie die Identität für das benutzerdefinierte Event Grid-Thema bzw. die benutzerdefinierte Event Grid-Domäne aktiviert haben, erstellt Azure automatisch eine Identität in Azure Active Directory. Fügen Sie diese Identität den entsprechenden Azure-Rollen hinzu, damit das benutzerdefinierte Thema bzw. die benutzerdefinierte Domäne Ereignisse an unterstützte Ziele weiterleiten kann. Fügen Sie die Identität z. B. der Rolle **Azure Event Hubs-Datensender** für einen Azure Event Hubs-Namespace hinzu, damit das benutzerdefinierte Event Grid-Thema Ereignisse an Event Hubs in diesem Namespace weiterleiten kann. 

Derzeit unterstützt Azure Event Grid benutzerdefinierte Themen oder Domänen, die mit der vom System zugewiesenen verwalteten Identität konfiguriert wurden, um Ereignisse an die folgenden Ziele weiterzuleiten. Diese Tabelle enthält auch die Rollen, die die Identität aufweisen sollte, damit das benutzerdefinierte Thema die Ereignisse weiterleiten kann.

| Destination | Azure-Rolle | 
| ----------- | --------- | 
| Service Bus-Warteschlangen und -Themen | [Azure Service Bus-Datensender](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure Event Hubs | [Azure Event Hubs-Datensender](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Azure Blob Storage | [Mitwirkender an Speicherblobdaten](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Azure Queue Storage |[Absender der Speicherwarteschlangen-Datennachricht](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 

## <a name="add-an-identity-to-azure-roles-on-destinations"></a>Hinzufügen von Identitäten zu Azure-Rollen für Ziele
In diesem Abschnitt wird beschrieben, wie Sie die Identität für das benutzerdefinierte Thema bzw. die benutzerdefinierte Domäne einer Azure-Rolle hinzufügen. 

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals
Sie können das Azure-Portal verwenden, um die Identität des benutzerdefinierten Themas bzw. der benutzerdefinierten Domäne einer entsprechenden Rolle zuzuweisen, damit das Thema/die Domäne Ereignisse an das Ziel weiterleiten kann. 

Im folgenden Beispiel wird eine verwaltete Identität für ein benutzerdefiniertes Event Grid-Thema mit dem Namen **msitesttopic** der Rolle **Azure Service Bus-Datensender** für einen Service Bus-Namespace hinzugefügt, der eine Warteschlangen- oder Themenressource enthält. Wenn Sie das benutzerdefinierte Event Grid-Thema der Rolle auf Namespaceebene hinzufügen, kann es Ereignisse an alle Entitäten im Namespace weiterleiten. 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem **Service Bus-Namespace**. 
1. Klicken Sie im linken Bereich auf **Zugriffssteuerung**. 
1. Klicken Sie im Bereich **Rollenzuweisung hinzufügen** auf **Hinzufügen**. 
1. Führen Sie die folgenden Schritte auf der Seite **Rollenzuweisung hinzufügen** durch:
    1. Wählen Sie die Rolle aus. In diesem Fall wird die Rolle **Azure Service Bus-Datensender** verwendet. 
    1. Wählen Sie die **Identität** für das benutzerdefinierte Event Grid-Thema bzw. die benutzerdefinierte Event Grid-Domäne aus. 
    1. Wählen Sie zum Speichern der Konfiguration **Speichern** aus.

Die Schritte entsprechen dem Hinzufügen einer Identität zu anderen Rollen, die in der Tabelle aufgeführt sind. 

### <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI
Im Beispiel in diesem Abschnitt wird gezeigt, wie Sie mit der Azure-Befehlszeilenschnittstelle eine Identität einer Azure-Rolle hinzufügen. Die Beispielbefehle beziehen sich auf benutzerdefinierte Event Grid-Themen. Die Befehle für Event Grid-Domänen sind ähnlich. 

#### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>Abrufen der Prinzipal-ID für die Systemidentität des benutzerdefinierten Themas 
Rufen Sie zunächst die Prinzipal-ID der vom System verwalteten Identität des benutzerdefinierten Themas ab, und weisen Sie die Identität den entsprechenden Rollen zu.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Erstellen einer Rollenzuweisung für Event Hubs in verschiedenen Bereichen 
Im folgenden Beispiel für die Befehlszeilenschnittstelle wird veranschaulicht, wie die Identität eines benutzerdefinierten Themas der Rolle **Azure Event Hubs-Datensender** auf Namespaceebene oder auf Event Hub-Ebene hinzugefügt wird. Wenn Sie die Rollenzuweisung auf Namespaceebene erstellen, kann das benutzerdefinierte Thema Ereignisse an alle Event Hubs in diesem Namespace weiterleiten. Wenn Sie das Thema auf Event Hub-Ebene erstellen, kann das benutzerdefinierte Thema Ereignisse nur an diesen Event Hub weiterleiten. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

#### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>Erstellen einer Rollenzuweisung für ein Service Bus-Thema in verschiedenen Bereichen 
Im folgenden Beispiel für die Befehlszeilenschnittstelle wird gezeigt, wie die Identität eines benutzerdefinierten Event Grid-Themas zur Rolle **Azure Service Bus-Datensender** auf Namespaceebene oder auf Ebene des Service Bus-Themas hinzugefügt wird. Wenn Sie die Rollenzuweisung auf Namespaceebene erstellen, kann das Event Grid-Thema Ereignisse an alle Entitäten (Service Bus-Warteschlangen oder -Themen) innerhalb dieses Namespace weiterleiten. Beim Erstellen einer Rollenzuweisung auf Service Bus-Warteschlangen- oder -Themenebene kann das benutzerdefinierte Event Grid-Thema Ereignisse nur an die betreffende Service Bus-Warteschlange oder das Service Bus-Thema weiterleiten. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="create-event-subscriptions-that-use-an-identity"></a>Erstellen von Ereignisabonnements, die eine Identität verwenden
Sobald Sie über ein benutzerdefiniertes Event Grid-Thema oder eine benutzerdefinierte Event Grid-Domäne mit einer vom System verwalteten Identität verfügen und die Identität der entsprechenden Rolle auf dem Ziel hinzugefügt haben, können Sie Abonnements erstellen, die die Identität verwenden. 

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals
Wenn Sie ein Ereignisabonnement erstellen, wird im Abschnitt **ENDPUNKTDETAILS** eine Option angezeigt, mit der die Verwendung der vom System zugewiesenen Identität für einen Endpunkt aktiviert werden kann. 

![Aktivieren der Identität beim Erstellen eines Ereignisabonnements für die Service Bus-Warteschlange](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

Auf der Registerkarte **Zusätzliche Features** können Sie auch die Verwendung der vom System zugewiesenen Identität für unzustellbare Nachrichten aktivieren. 

![Aktivieren der vom System zugewiesenen Identität zur Verarbeitung unzustellbarer Nachrichten](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="use-the-azure-cli---service-bus-queue"></a>Verwenden der Azure-Befehlszeilenschnittstelle: Service Bus-Warteschlange 
In diesem Abschnitt erfahren Sie, wie Sie die vom System zugewiesene Identität zum Übermitteln von Ereignissen an eine Service Bus-Warteschlange mit der Azure-Befehlszeilenschnittstelle aktivieren. Die Identität muss Mitglied der Rolle **Azure Service Bus-Datensender** sein. Sie muss auch Mitglied der Rolle **Mitwirkender an Storage-Blobdaten** für das Speicherkonto sein, das für unzustellbare Nachrichten verwendet wird. 

#### <a name="define-variables"></a>Definieren von Variablen
Geben Sie zunächst Werte für die folgenden Variablen an, die im Befehl für die Befehlszeilenschnittstelle verwendet werden sollen. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Erstellen eines Ereignisabonnements mithilfe der verwalteten Identität für die Übermittlung 
Mit diesem Beispielbefehl wird ein Ereignisabonnement für ein benutzerdefiniertes Event Grid-Thema erstellt, wobei der Endpunkttyp auf **Service Bus-Warteschlange** festgelegt ist. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>Erstellen eines Ereignisabonnements mithilfe der verwalteten Identität für die Übermittlung und unzustellbare Nachrichten
Mit diesem Beispielbefehl wird ein Ereignisabonnement für ein benutzerdefiniertes Event Grid-Thema erstellt, wobei der Endpunkttyp auf **Service Bus-Warteschlange** festgelegt ist. Außerdem wird angegeben, dass die vom System verwaltete Identität für unzustellbare Nachrichten verwendet werden soll. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="use-the-azure-cli---event-hubs"></a>Verwenden der Azure-Befehlszeilenschnittstelle: Event Hubs 
In diesem Abschnitt erfahren Sie, wie Sie die vom System zugewiesene Identität zum Übermitteln von Ereignissen an einen Event Hub mit der Azure-Befehlszeilenschnittstelle aktivieren. Die Identität muss Mitglied der Rolle **Azure Event Hubs-Datensender** sein. Sie muss auch Mitglied der Rolle **Mitwirkender an Storage-Blobdaten** für das Speicherkonto sein, das für unzustellbare Nachrichten verwendet wird. 

#### <a name="define-variables"></a>Definieren von Variablen
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Erstellen eines Ereignisabonnements mithilfe der verwalteten Identität für die Übermittlung 
Mit diesem Beispielbefehl wird ein Ereignisabonnement für ein benutzerdefiniertes Event Grid-Thema erstellt, wobei der Endpunkttyp auf **Event Hubs** festgelegt ist. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Erstellen eines Ereignisabonnements mithilfe der verwalteten Identität für die Übermittlung und unzustellbare Nachrichten 
Mit diesem Beispielbefehl wird ein Ereignisabonnement für ein benutzerdefiniertes Event Grid-Thema erstellt, wobei der Endpunkttyp auf **Event Hubs** festgelegt ist. Außerdem wird angegeben, dass die vom System verwaltete Identität für unzustellbare Nachrichten verwendet werden soll. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="use-the-azure-cli---azure-storage-queue"></a>Verwenden der Azure-Befehlszeilenschnittstelle: Azure Storage-Warteschlange 
In diesem Abschnitt erfahren Sie, wie Sie die vom System zugewiesene Identität zum Übermitteln von Ereignissen an eine Azure Storage-Warteschlange mit der Azure-Befehlszeilenschnittstelle aktivieren. Die Identität muss Mitglied der Rolle **Absender der Speicherwarteschlangen-Datennachricht** im Speicherkonto sein. Sie muss auch Mitglied der Rolle **Mitwirkender an Storage-Blobdaten** für das Speicherkonto sein, das für unzustellbare Nachrichten verwendet wird.

#### <a name="define-variables"></a>Definieren von Variablen  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Erstellen eines Ereignisabonnements mithilfe der verwalteten Identität für die Übermittlung 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Erstellen eines Ereignisabonnements mithilfe der verwalteten Identität für die Übermittlung und unzustellbare Nachrichten 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```

## <a name="private-endpoints"></a>Private Endpunkte
Derzeit ist es nicht möglich, Ereignisse über [private Endpunkte](../private-link/private-endpoint-overview.md) zu übermitteln. Das heißt, es gibt keine Unterstützung, wenn Sie strikte Anforderungen an die Netzwerkisolation haben, bei denen der Datenverkehr der übermittelten Ereignisse den privaten IP-Adressraum nicht verlassen darf. 

Wenn Ihre Anforderungen jedoch eine sichere Methode zum Senden von Ereignissen über einen verschlüsselten Kanal und eine bekannte Identität des Absenders (in diesem Fall Event Grid) unter Verwendung eines öffentlichen IP-Adressraums erfordern, könnten Sie Ereignisse an Event Hubs, Service Bus oder Azure Storage Service unter Verwendung eines benutzerdefinierten Azure Event Grid-Themas oder einer Domäne mit vom System verwalteter Identität bereitstellen, die wie in diesem Artikel gezeigt konfiguriert ist. Anschließend können Sie eine private Verbindung verwenden, die in Azure Functions konfiguriert ist, oder Ihren Webhook, der in Ihrem virtuellen Netzwerk zum Pullen von Ereignissen bereitgestellt wurde. Weitere Informationen finden Sie im Beispiel: [Herstellen einer Verbindung mit privaten Endpunkten mit Azure Functions](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).

Bei dieser Konfiguration wird der Datenverkehr über die öffentliche IP-Adresse bzw. das Internet aus Event Grid an Event Hubs, Service Bus oder Azure Storage übermittelt. Der Kanal kann jedoch verschlüsselt werden, und eine verwaltete Identität von Event Grid wird verwendet. Wenn Sie Ihre Azure Functions-Funktion oder Ihren Webhook, der in Ihrem virtuellen Netzwerk bereitgestellt wurde, für die Verwendung von Event Hubs, Service Bus oder Azure Storage über eine private Verbindung konfigurieren, verbleibt dieser Teil des Datenverkehrs offensichtlich in Azure.


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu verwalteten Dienstidentitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md). 

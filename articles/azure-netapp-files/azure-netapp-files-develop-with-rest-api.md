---
title: Entwickeln für Azure NetApp Files mit REST-API | Microsoft-Dokumentation
description: Beschreibt die ersten Schritte zum Verwenden der Azure NetApp Files-REST-API.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: b-juche
ms.openlocfilehash: 996fbcc7c3c9af0da9160216785ecd54840660e8
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957039"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Entwickeln für Azure NetApp Files mit REST-API 

Die REST-API für den Azure NetApp Files-Dienst definiert HTTP-Vorgänge für Ressourcen, wie etwa das NetApp-Konto, den Kapazitätspool, die Volumes und Momentaufnahmen. Dieser Artikel unterstützt Sie beim Einstieg in die Verwendung der Azure NetApp Files-REST-API.

## <a name="azure-netapp-files-rest-api-specification"></a>Spezifikation der Azure NetApp Files-REST-API

Die REST-API-Spezifikation für Azure NetApp Files wird über [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) veröffentlicht:

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Zugriff auf die Azure NetApp Files-REST-API  

1. [Installieren Sie die Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), wenn Sie dies noch nicht getan haben.
2. Erstellen Sie einen Dienstprinzipal in Ihrem Azure Active Directory (Azure AD):
   1. Überprüfen Sie, ob Sie über [ausreichende Berechtigungen](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions) verfügen.

   1. Geben Sie über die Azure-Befehlszeilenschnittstelle den folgenden Befehl ein:  

           az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE--password $YOURGENERATEDPASSWORDGOESHERE

      Die Befehlsausgabe ähnelt dem folgenden Beispiel:  

           { 
               "appId": "appIDgoeshere", 
               "displayName": "APPNAME", 
               "name": "http://APPNAME", 
               "password": "supersecretpassword", 
               "tenant": "tenantIDgoeshere" 
           } 

      Bewahren Sie die Befehlsausgabe auf.  Sie benötigen die Werte für `appId`, `password` und `tenant`. 

3. Fordern Sie ein OAuth-Zugriffstoken an:

    Die Beispiele in diesem Artikel verwenden cURL.  Sie können aber auch eine Reihe von API-Tools verwenden, z.B. [Postman](https://www.getpostman.com/), [Insomnia](https://insomnia.rest/) und [Paw](https://paw.cloud/).  

    Ersetzen Sie die Variablen im folgenden Beispiel durch die Befehlsausgabe aus Schritt 2 oben. 

        curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token

    Die Ausgabe stellt ein Zugriffstoken ähnlich dem folgenden Beispiel zur Verfügung:

        eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9

    Das angezeigte Token ist 3600 Sekunden lang gültig. Danach müssen Sie ein neues Token anfordern. 
    Speichern Sie das Token in einem Text-Editor.  Sie benötigen es für den nächsten Schritt.

4. Senden Sie einen Testaufruf, und schließen Sie das Token ein, um Ihren Zugriff auf die REST-API zu testen:

        curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2016-08-01

## <a name="examples-using-the-api"></a>Beispiele zur Verwendung der API  

In diesem Artikel wird die folgende URL als Grundlage von Anforderungen verwendet. Diese URL verweist auf den Stamm des Azure NetApp Files-Namespace. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15`

Sie sollten die Werte von `subID` und `resourceGroups` in den folgenden Beispielen durch Ihre eigenen Werte ersetzen. 

### <a name="get-request-examples"></a>Beispiele für GET-Anforderungen

Sie verwenden eine GET-Anforderung, um Objekte von Azure NetApp Files in einem Abonnement abzufragen, wie in den folgenden Beispielen zu sehen: 

        #get NetApp accounts 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15

        #get capacity pools for NetApp account 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2017-08-15

        #get volumes in NetApp account & capacity pool 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2017-08-15

        #get snapshots for a volume 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2017-08-15

### <a name="put-request-examples"></a>Beispiele für PUT-Anforderungen

Sie verwenden eine PUT-Anforderung, um neue Objekte in Azure NetApp Files zu erstellen, wie in den folgenden Beispielen zu sehen. Der Text der PUT-Anforderung kann die Daten für die Änderungen im JSON-Format enthalten oder eine Datei angeben, aus der gelesen werden soll. 

        #create a NetApp account  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2017-08-15

        #create a capacity pool  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2017-08-15

        #create a volume  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2017-08-15

        #create a volume snapshot  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2017-08-15

### <a name="json-examples"></a>JSON-Beispiele

Im folgenden Beispiel sehen Sie, wie Sie ein NetApp-Konto erstellen können:

    { 
        "name": "MYNETAPPACCOUNT", 
        "type": "Microsoft.NetApp/netAppAccounts", 
        "location": "westus2", 
        "properties": { 
            "name": "MYNETAPPACCOUNT" 
        }
    } 

Das folgende Beispiel veranschaulicht das Erstellen eines Kapazitätspools: 

    {
        "name": "MYNETAPPACCOUNT/POOLNAME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
        "location": "westus2",
        "properties": {
            "name": "POOLNAME"
            "size": "4398046511104",
            "serviceLevel": "Premium"
        }
    }

Im folgenden Beispiel ist zu sehen, wie Sie ein neues Volume erstellen können: 

    {
        "name": "MYNEWVOLUME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes",
        "location": "westus2",
        "properties": {
            "serviceLevel": "Premium",
            "usageThreshold": "322122547200",
            "creationToken": "MY-FILEPATH",
            "snapshotId": "",
            "subnetId": "/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
            }
    }

Das folgende Beispiel zeigt Ihnen, wie eine Momentaufnahme eines Volumes erstellt wird: 

    {
        "name": "apitest2/apiPool01/apiVol01/snap02",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
        "location": "westus2",
        "properties": {
            "name": "snap02",
            "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
        }
    }

> [!NOTE] 
> Zum Erstellen einer Momentaufnahme müssen Sie `fileSystemId` angeben.  Den Wert von `fileSystemId` können Sie durch eine GET-Anforderung an ein Volume erhalten. 

## <a name="next-steps"></a>Nächste Schritte

[Mehr finden Sie in der Azure NetApp Files-REST-API-Referenz](https://docs.microsoft.com/rest/api/netapp/)

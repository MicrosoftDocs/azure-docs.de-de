---
title: Erstellen eines NetApp-Kontos für den Zugriff auf Azure NetApp Files | Microsoft-Dokumentation
description: Beschreibt das Zugreifen auf Azure NetApp Files sowie das Erstellen eines NetApp-Kontos, um einen Kapazitätspool einrichten und ein Volume erstellen zu können.
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
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 25cae58663f6fa7ef27995c10509eb33e49dd4c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522812"
---
# <a name="create-a-netapp-account"></a>Erstellen eines NetApp-Kontos
Nach der Erstellung eines NetApp-Kontos können Sie einen Kapazitätspool einrichten und anschließend ein Volume erstellen. Neue NetApp-Konten werden über das Blatt „Azure NetApp Files“ erstellt.

## <a name="before-you-begin"></a>Voraussetzungen
Sie müssen eine E-Mail vom Azure NetApp Files-Team erhalten haben, in der bestätigt wird, dass Sie Zugriff auf den Dienst erhalten haben. Siehe [Übermitteln einer Wartelistenanforderung für den Zugriff auf den Dienst](azure-netapp-files-register.md#waitlist).

Sie müssen außerdem Ihr Abonnement registriert haben, um den NetApp-Ressourcenanbieter zu nutzen. Siehe [Registrieren des NetApp-Ressourcenanbieters](azure-netapp-files-register.md#resource-provider).

## <a name="steps"></a>Schritte 

1. Melden Sie sich beim Azure-Portal an. 
2. Greifen Sie mithilfe einer der folgenden Methoden auf das Blatt „Azure NetApp Files“ zu:  
   * Suchen Sie im Suchfeld des Azure-Portals nach **Azure NetApp Files**.  
   * Klicken Sie im Navigationsbereich auf **Alle Dienste**, und filtern Sie nach „Azure NetApp Files“.  

   Sie können das Blatt „Azure NetApp Files“ als Favorit speichern, indem Sie daneben auf das Sternsymbol klicken. 

3. Klicken Sie auf **+ Hinzufügen**, um ein neues NetApp-Konto zu erstellen.  
   Das Fenster für das neue NetApp-Konto wird angezeigt.  

4. Geben Sie für Ihr NetApp-Konto folgende Informationen an: 
   * **Kontoname**  
     Geben Sie einen eindeutigen Namen für das Abonnement an.
   * **Abonnement**  
     Wählen Sie eines der vorhandenen Abonnements aus.
   * **Ressourcengruppe**   
     Verwenden Sie eine vorhandene Ressourcengruppe, oder erstellen Sie eine neue.
   * **Location**  
     Wählen Sie die Region aus, in der sich das Konto und dessen untergeordnete Ressourcen befinden sollen.  

     ![Neues NetApp-Konto](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Klicken Sie auf **Create**.     
   Das erstellte NetApp-Konto wird nun auf dem Blatt „Azure NetApp Files“ angezeigt. 

> [!NOTE] 
> Wenn Ihnen nicht der Zugriff auf den Azure NetApp Files-Dienst gewährt wurde, erhalten Sie den folgenden Fehler, wenn Sie versuchen, das erste NetApp-Konto zu erstellen:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`

## <a name="next-steps"></a>Nächste Schritte  

[Einrichten eines Kapazitätspools](azure-netapp-files-set-up-capacity-pool.md)


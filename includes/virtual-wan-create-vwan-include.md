---
title: include file
description: include file
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/09/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5915830e4521399ad322dd4a6f3926428d811455
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94942885"
---
Navigieren Sie in einem Browser zum [Azure-Portal](https://portal.azure.com) , und melden Sie sich mit Ihrem Azure-Konto an.

1. Klicken Sie im Portal auf **+ Ressource erstellen**. Geben Sie **Virtual WAN** in das Suchfeld ein, und drücken Sie die **EINGABETASTE**.
1. Wählen Sie in den Ergebnissen **Virtual WAN** aus. Wählen Sie auf der Seite „Virtual WAN“ die Option **Erstellen** aus, um die Seite „WAN erstellen“ zu öffnen.
1. Füllen Sie auf der Seite **WAN erstellen** auf der Registerkarte **Grundlagen** die folgenden Felder aus:

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="Screenshot: Bereich „WAN erstellen“ mit ausgewählter Registerkarte „Grundlagen“":::

   * **Abonnement**: Wählen Sie das Abonnement aus, das Sie verwenden möchten.
   * **Ressourcengruppe**: Erstellen Sie eine neue Ressourcengruppe, oder verwenden Sie eine vorhandene.
   * **Ressourcengruppenstandort**: Wählen Sie in der Dropdownliste einen Ressourcengruppenstandort aus. Ein WAN ist eine globale Ressource, die nicht in einer bestimmten Region angeordnet ist. Sie müssen aber eine Region auswählen, damit Sie die von Ihnen erstellte WAN-Ressource verwalten und finden können.
   * **Name**: Geben Sie den Namen ein, den Sie Ihrem WAN geben möchten.
   * **Typ**: Basic oder Standard. Wählen Sie **Standard** aus. Wenn Sie „Basic-VWAN“ auswählen, müssen Sie wissen, dass Basic-VWANs nur Basic-Hubs enthalten können, wodurch Ihr Verbindungstyp auf Site-to-Site beschränkt ist.
1. Klicken Sie nach dem Ausfüllen der Felder auf **Überprüfen + erstellen**.
1. Klicken Sie nach bestandenen Überprüfung auf **Erstellen**, um das virtuelle WAN zu erstellen.

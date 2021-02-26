---
title: include file
description: include file
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 03/01/2020
ms.author: allensu
ms.custom: include file
ms.openlocfilehash: e1d4d29f8edca87ec1cca0ffced7b3e1bca90717
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808517"
---
## <a name="create-the-virtual-network-and-subnet"></a>Erstellen des virtuellen Netzwerks und des Subnetzes

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk und ein Subnetz.

1. Wählen Sie links oben auf dem Bildschirm **Ressource erstellen > Netzwerk > Virtuelles Netzwerk** aus, oder suchen Sie über das Suchfeld nach **Virtuelles Netzwerk**.

2. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Auswählen des Azure-Abonnements                                  |
    | Ressourcengruppe   | Wählen Sie **Neu erstellen** aus, geben Sie **\<resource-group-name>** ein, und wählen Sie dann „OK“ oder basierend auf den Parametern einen vorhandenen **\<resource-group-name>** aus. |
    | **Instanzendetails** |                                                                 |
    | Name             | Eingeben von **\<virtual-network-name>**                                    |
    | Region           | Auswählen von **\<region-name>** |

3. Wählen Sie die Registerkarte **IP-Adressen** oder die Schaltfläche **Weiter: IP-Adressen** am unteren Seitenrand aus.

4. Geben Sie auf der Registerkarte **IP-Adressen** die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | IPv4-Adressraum | Eingeben von **\<IPv4-address-space>** |

5. Wählen Sie unter **Subnetzname** das Wort **Standard** aus.

6. Geben Sie unter **Subnetz bearbeiten** die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | Subnetzname | Eingeben von **\<subnet-name>** |
    | Subnetzadressbereich | Eingeben von **\<subnet-address-range>**

7. Wählen Sie **Speichern** aus.

8. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die Schaltfläche **Überprüfen + erstellen** aus.

9. Klicken Sie auf **Erstellen**.
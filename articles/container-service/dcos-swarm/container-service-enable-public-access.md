---
title: (VERALTET) Aktivieren des Zugriffs auf die Azure-DC/OS-Container-App
description: Es wird beschrieben, wie Sie den öffentlichen Zugriff auf DC/OS-Container in Azure Container Service ermöglichen.
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3e4ba15fa1925ca40ad7760acbd14331fbdd1343
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61457330"
---
# <a name="deprecated-enable-public-access-to-an-azure-container-service-application"></a>(VERALTET) Ermöglichen des öffentlichen Zugriffs auf eine Azure Container Service-Anwendung

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Alle DC/OS-Container im [Pool mit den öffentlichen Agents](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) von ACS werden automatisch für das Internet verfügbar gemacht. Standardmäßig sind die Ports **80**, **443** und **8080** geöffnet, und alle (öffentlichen) Container, die an diesen Ports lauschen, sind zugänglich. In diesem Artikel wird beschrieben, wie Sie mehr Ports für Ihre Anwendungen im Azure Container Service öffnen.

## <a name="open-a-port-portal"></a>Öffnen eines Ports (Portal)
Zunächst müssen wir den gewünschten Port öffnen.

1. Melden Sie sich beim Portal an.
2. Suchen Sie nach der Ressourcengruppe, in der Sie den Azure Container Service bereitgestellt haben.
3. Wählen Sie das Agent-Lastenausgleichsmodul aus (der Name hat in der Regel das Format **XXXX-agent-lb-XXXX**).
   
    ![Azure Container Service-Lastenausgleich](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Klicken Sie auf **Tests** und dann auf **Hinzufügen**.
   
    ![Azure Container Service-Lastenausgleich – Tests](./media/container-service-enable-public-access/add-probe.png)
5. Füllen Sie das Testformular aus, und klicken Sie auf **OK**.
   
   | Feld | BESCHREIBUNG |
   | --- | --- |
   | NAME |Ein beschreibender Name des Tests. |
   | Port |Der Port des zu testenden Containers. |
   | `Path` |(Im HTTP-Modus) Der relative Websitepfad zum Test. HTTPS wird nicht unterstützt. |
   | Intervall |Der Zeitraum zwischen Testversuchen in Sekunden. |
   | Fehlerhafter Schwellenwert |Anzahl von aufeinander folgenden Testversuchen, bevor der Container als fehlerhaft angesehen wird. |
6. Wechseln Sie zurück zu den Eigenschaften des Agent-Lastenausgleichs, und klicken Sie auf **Lastenausgleichsregeln** und dann auf **Hinzufügen**.
   
    ![Azure Container Service-Lastenausgleich – Regeln](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Füllen Sie das Formular für den Lastenausgleich aus, und klicken Sie auf **OK**.
   
   | Feld | BESCHREIBUNG |
   | --- | --- |
   | NAME |Ein beschreibender Name für den Lastenausgleich. |
   | Port |Der öffentliche Port für eingehenden Datenverkehr. |
   | Back-End-Port |Der interne öffentliche Port des Containers, an den Datenverkehr geleitet wird. |
   | Back-End-Pool |Die Container in diesem Pool sind das Ziel für diesen Lastenausgleich. |
   | Test |Der Test für die Ermittlung, ob ein Ziel im **Back-End-Pool** fehlerfrei ist. |
   | Sitzungspersistenz |Bestimmt, wie Datenverkehr von einem Client für die Dauer der Sitzung behandelt werden soll.<br><br>**Keine:** Aufeinander folgende Anforderungen vom gleichen Client können von jedem Container verarbeitet werden.<br>**Client-IP**: Aufeinander folgende Anforderungen von der gleichen Client-IP werden vom gleichen Container verarbeitet.<br>**Client-IP und Protokoll**: Aufeinander folgende Anforderungen von derselben Kombination aus Client-IP und Protokoll werden von demselben Container verarbeitet. |
   | Leerlauftimeout |(Nur TCP) Der Zeitraum zur Beibehaltung des geöffneten Zustands eines TCP/HTTP-Clients in Minuten, ohne dass *Keep-Alive* -Nachrichten verwendet werden. |

## <a name="add-a-security-rule-portal"></a>Hinzufügen einer Sicherheitsregel (Portal)
Als Nächstes müssen wir eine Sicherheitsregel hinzufügen, mit der Datenverkehr von unserem geöffneten Port über die Firewall geleitet wird.

1. Melden Sie sich beim Portal an.
2. Suchen Sie nach der Ressourcengruppe, in der Sie den Azure Container Service bereitgestellt haben.
3. Wählen Sie die **öffentliche** Agent-Netzwerksicherheitsgruppe (der Name weist in der Regel das Format **XXXX-agent-public-nsg-XXXX** auf).
   
    ![Azure Container Service-Netzwerksicherheitsgruppe](./media/container-service-enable-public-access/agent-nsg.png)
4. Wählen Sie **Eingangssicherheitsregeln** und dann **Hinzufügen**.
   
    ![Azure Container Service-Netzwerksicherheitsgruppe – Regeln](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Füllen Sie die Firewallregel aus, um den öffentlichen Port zuzulassen, und klicken Sie auf **OK**.
   
   | Feld | BESCHREIBUNG |
   | --- | --- |
   | NAME |Ein beschreibender Name der Firewallregel. |
   | Priorität |Prioritätsrang für die Regel. Je niedriger die Nummer ist, desto höher ist die Priorität. |
   | `Source` |Schränkt den IP-Adressbereich für eingehenden Datenverkehr ein, der mit dieser Regel zugelassen oder abgelehnt wird. Verwenden Sie **Alle** , um keine Einschränkung anzugeben. |
   | Dienst |Wählen Sie einen Satz mit vordefinierten Diensten aus, für die diese Sicherheitsregel gilt. Verwenden Sie andernfalls **Benutzerdefiniert** , um einen eigenen Satz zu erstellen. |
   | Protocol |Schränkt den Datenverkehr basierend auf **TCP** oder **UDP** ein. Verwenden Sie **Alle** , um keine Einschränkung anzugeben. |
   | Portbereich |Wenn **Dienst** auf **Benutzerdefiniert** festgelegt ist, wird hiermit der Portbereich angegeben, auf den sich diese Regel auswirkt. Sie können einen einzelnen Port, z.B. **80**, oder einen Bereich wie **1024–1500** verwenden. |
   | Aktion |Dient zum Zulassen oder Verweigern von Datenverkehr, der die Kriterien erfüllt. |

## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich über den Unterschied zwischen [öffentlichen und privaten DC/OS-Agents](container-service-dcos-agents.md).

Erhalten Sie weitere Informationen zum [Verwalten Ihrer DC/OS-Container](container-service-mesos-marathon-ui.md).


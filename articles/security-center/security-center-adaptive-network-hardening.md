---
title: Adaptive Netzwerkhärtung in Azure Security Center | Microsoft-Dokumentation
description: " Hier erfahren Sie, wie Sie die adaptive Netzwerkhärtung in Azure Security Center aktivieren. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2019
ms.author: monhaber
ms.openlocfilehash: f35f410ddc039ee264fa1de317e152cb03f391b5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241518"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Adaptive Netzwerkhärtung in Azure Security Center
Hier erfahren Sie, wie Sie die adaptive Netzwerkhärtung in Azure Security Center konfigurieren.

## <a name="what-is-adaptive-network-hardening"></a>Was ist die adaptive Netzwerkhärtung?
Der Einsatz von [Netzwerksicherheitsgruppen (NSGs)](https://docs.microsoft.com/azure/virtual-network/security-overview) zur Filterung von ein- und ausgehendem Datenverkehr für Ressourcen verbessert den Sicherheitsstatus Ihres Netzwerks. Es gibt jedoch Situationen, in denen es sich bei dem Datenverkehr, der die NSG durchläuft, um eine Teilmenge der definierten NSG-Regeln handelt. In diesen Fällen lässt sich der Sicherheitsstatus durch eine Härtung der NSG-Regeln auf der Grundlage tatsächlicher Datenverkehrsmuster noch weiter verbessern.

Die adaptive Netzwerkhärtung liefert Empfehlungen zur weiteren Härtung der NSG-Regeln. Dabei kommt ein Machine Learning-Algorithmus zum Einsatz, der Faktoren wie tatsächlichen Datenverkehr, bekannte vertrauenswürdige Konfiguration und Bedrohungsinformationen sowie weitere Anzeichen einer Kompromittierung berücksichtigt und auf der Grundlage dieser Faktoren Empfehlungen abgibt, um nur Datenverkehr von bestimmten IP-/Port-Tupeln zuzulassen.

Ein Beispiel: Angenommen, die vorhandene NSG-Regel lässt Datenverkehr von 140.20.30.10/24 am Port 22 zu. Die analysebasierte Empfehlung der adaptiven Netzwerkhärtung lautet, den Bereich einzugrenzen und Datenverkehr von 140.23.30.10/29 (enger gefasster IP-Adressbereich) zuzulassen sowie jeglichen anderen Datenverkehr an diesem Port zu verweigern.

![Ansicht der Netzwerkhärtung](./media/security-center-adaptive-network-hardening/traffic-hardening.png)

> [!NOTE]
> Empfehlungen der adaptiven Netzwerkhärtung werden für folgende Ports unterstützt: 22, 3389, 21, 23, 445, 4333, 3306, 1433, 1434, 53, 20, 5985, 5986, 5432, 139, 66, 1128

## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Anzeigen von Warnungen und Regeln der adaptiven Netzwerkhärtung

1. Wählen Sie in Security Center **Netzwerk** -> **Adaptive Netzwerkhärtung** aus. Die virtuellen Netzwerkcomputer werden auf drei separaten Registerkarten aufgeführt:
   * **Fehlerhafte Ressourcen:** Virtuelle Computer, für die Empfehlungen und Warnungen vorliegen, die durch Ausführen des Algorithmus der adaptiven Netzwerkhärtung ausgelöst wurden. 
   * **Fehlerfreie Ressourcen:** Virtuelle Computer ohne Warnungen oder Empfehlungen.
   * **Nicht überprüfte Ressourcen:** Virtuelle Computer, auf denen der Algorithmus der adaptiven Netzwerkhärtung aus einem der folgenden Gründe nicht ausgeführt werden kann:
      * **Virtuelle Computer sind klassische virtuelle Computer**: Es werden nur virtuelle Azure Resource Manager-Computer unterstützt.
      * **Nicht genügend Daten verfügbar:** Security Center benötigt mindestens Datenverkehrsdaten eines Zeitraums von 30 Tagen, um fundierte Härtungsempfehlungen abgeben zu können.
      * **Virtueller Computer nicht durch ASC (Standard) geschützt:** Dieses Feature kann nur für virtuelle Computer genutzt werden, die auf den Standard-Tarif von Security Center festgelegt sind.

     ![Fehlerhafte Ressourcen](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. Wählen Sie auf der Registerkarte **Fehlerhafte Ressourcen** einen virtuellen Computer aus, um dessen Warnungen und die empfohlenen Härtungsregeln anzuzeigen.

    ![Härtungswarnungen](./media/security-center-adaptive-network-hardening/hardening-alerts.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Überprüfen und Anwenden der empfohlenen Regeln der adaptiven Netzwerkhärtung

1. Wählen Sie auf der Registerkarte **Fehlerhafte Ressourcen** einen virtuellen Computer aus. Die Warnungen und empfohlenen Härtungsregeln werden aufgeführt.

     ![Härtungsregeln](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > Auf der Registerkarte **Regeln** werden die Regeln aufgeführt, die laut adaptiver Netzwerkhärtung hinzugefügt werden sollten. Auf der Registerkarte **Warnungen** werden die Warnungen aufgeführt, die aufgrund von eingehendem Datenverkehr für die Ressource generiert wurden, der nicht innerhalb des durch die empfohlenen Regeln zugelassenen IP-Adressbereichs liegt.

2. Wenn Sie einige der Parameter für eine Regel ändern möchten, gehen Sie wie unter [Ändern einer Regel](#modify-rule) beschrieben vor.
   > [!NOTE]
   > Sie können auch eine Regel [löschen](#delete-rule) oder [hinzufügen](#add-rule).

3. Wählen Sie die Regeln aus, die Sie auf die NSG anwenden möchten, und klicken Sie auf **Erzwingen**.

      > [!NOTE]
      > Die erzwungenen Regeln werden den Netzwerksicherheitsgruppen hinzugefügt, die den virtuellen Computer schützen. (Ein virtueller Computer kann durch eine Netzwerksicherheitsgruppe geschützt werden, die der Schnittstellenkarte zugeordnet ist, oder das Subnetz, in dem der virtuelle Computer befindet, oder beides.)

    ![Erzwingen von Regeln](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### Ändern einer Regel  <a name ="modify-rule"> </a>

Die Parameter einer empfohlenen Regel können bei Bedarf geändert werden. So können Sie beispielsweise die empfohlenen IP-Adressbereiche ändern.

Im Anschluss finden Sie einige wichtige Richtlinien, die beim Ändern einer Regel der adaptiven Netzwerkhärtung berücksichtigt werden müssen:

* Es können nur die Parameter von Regeln des Typs „Zulassen“ geändert werden. 
* Regeln können nicht von „Zulassen“ in „Verweigern“ geändert werden. 

  > [!NOTE]
  > Regeln vom Typ „Verweigern“ werden direkt in der NSG erstellt und geändert. Ausführlichere Informationen finden Sie unter [Erstellen, Ändern oder Löschen einer Netzwerksicherheitsgruppe](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* Die einzige Regel vom Typ „Verweigern“, die hier ggf. angezeigt wird, ist eine Regel zum **Verweigern des gesamten Datenverkehrs**, und diese Regel kann nicht geändert werden. Sie kann allerdings gelöscht werden (siehe [Löschen einer Regel](#delete-rule)).
  > [!NOTE]
  > Eine Regel zum **Verweigern des gesamten Datenverkehrs** wird empfohlen, wenn Security Center nach Ausführung des Algorithmus keinen Datenverkehr erkennt, der zugelassen werden soll (basierend auf der vorhandenen NSG-Konfiguration). Aus diesem Grund ist die empfohlene Regel, jeglichen Datenverkehr für den angegebenen Port zu verweigern. Der Name dieser Art von Regel wird als *Vom System generiert* angezeigt. Nach Erzwingung dieser Regel ist ihr tatsächlicher Name in der NSG eine Zeichenfolge, die sich aus dem Protokoll, der Datenverkehrsrichtung, „DENY“ und einer Zufallszahl zusammensetzt.

*So ändern Sie eine Regel der adaptiven Netzwerkhärtung:*

1. Um Parameter einer Regel zu ändern, klicken Sie auf der Registerkarte **Regeln** auf die drei Punkte (...) am Ende der Zeile mit der gewünschten Regel, und klicken Sie anschließend auf **Bearbeiten**.

   ![Bearbeiten einer Regel](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. Aktualisieren Sie im Fenster **Regel bearbeiten** die Details, die Sie ändern möchten, und klicken Sie anschließend auf **Speichern**.

   > [!NOTE]
   > Nach dem Klicken auf **Speichern** haben Sie die Regel erfolgreich geändert. *Allerdings haben Sie sie noch nicht auf die NSG angewendet.* Um sie anzuwenden, müssen Sie die Regel in der Liste auswählen und auf **Erzwingen** klicken (wie im nächsten Schritt beschrieben).

   ![Bearbeiten einer Regel](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Wählen Sie in der Liste die aktualisierte Regel aus, und klicken Sie auf **Erzwingen**, um die aktualisierte Regel anzuwenden.

    ![Erzwingen einer Regel](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### Hinzufügen einer neuen Rolle <a name ="add-rule"> </a>

Sie können eine Regel vom Typ „Zulassen“ hinzufügen, die nicht von Security Center empfohlen wurde.

> [!NOTE]
> Nur Regeln vom Typ „Zulassen“ können hier hinzugefügt werden. Regeln vom Typ „Verweigern“ können direkt in der Netzwerksicherheitsgruppe hinzugefügt werden. Ausführlichere Informationen finden Sie unter [Erstellen, Ändern oder Löschen einer Netzwerksicherheitsgruppe](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*So fügen Sie eine Regel der adaptiven Netzwerkhärtung hinzu:*

1. Klicken Sie in der linken oberen Ecke auf **Regel hinzufügen**.

   ![Hinzufügen einer Regel](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Geben Sie im Fenster **Neue Regel** die Details ein, und klicken Sie auf **Hinzufügen**.

   > [!NOTE]
   > Nach dem Klicken auf **Hinzufügen** haben Sie die Regel erfolgreich hinzugefügt, und sie wird zusammen mit den anderen empfohlenen Regeln aufgeführt. Allerdings haben Sie sie noch nicht auf die NSG angewendet. Um sie zu aktivieren, müssen Sie die Regel in der Liste auswählen und auf **Erzwingen** klicken (wie im nächsten Schritt beschrieben).

3. Wählen Sie in der Liste die neue Regel aus, und klicken Sie auf **Erzwingen**, um die neue Regel anzuwenden.

    ![Erzwingen einer Regel](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### Löschen einer Regel <a name ="delete-rule"> </a>

Bei Bedarf können Sie eine empfohlene Regel löschen. Dies kann beispielsweise erforderlich sein, wenn Sie feststellen, dass eine vorgeschlagene Regel die Blockierung von zulässigem Datenverkehr zur Folge hätte.

*So löschen Sie eine Regel der adaptiven Netzwerkhärtung:*

1. Klicken Sie auf der Registerkarte **Regeln** auf die drei Punkte (...) am Ende der Zeile mit der Regel, und klicken Sie anschließend auf **Löschen**.  

    ![Härtungsregeln](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)







 


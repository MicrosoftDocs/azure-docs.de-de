---
title: Bewährte Methoden für sicheren Code
titleSuffix: Azure Machine Learning
description: Enthält Informationen zu potenziellen Sicherheitsbedrohungen bei der Entwicklung für Azure Machine Learning sowie zu Lösungen und bewährten Methoden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.date: 11/12/2019
ms.openlocfilehash: 37cb70bdbd1e3c87eeb994e0959c6214822d22ad
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322965"
---
# <a name="secure-code-best-practices-with-azure-machine-learning"></a>Bewährte Methoden für sicheren Code mit Azure Machine Learning

In Azure Machine Learning können Sie Dateien und Inhalte aus beliebigen Quellen in Azure hochladen. Über Inhalte in Jupyter Notebooks oder von Ihnen geladenen Skripts ist unter Umständen das Auslesen von Daten aus Ihren Sitzungen, das Zugreifen auf Daten Ihrer Organisation in Azure oder das Ausführen von schädlichen Prozessen in Ihrem Namen möglich.

> [!IMPORTANT]
> Führen Sie nur Notebooks oder Skripts aus, die aus vertrauenswürdigen Quellen stammen. Ein Beispiel hierfür ist ein Fall, in dem Sie oder Ihr Sicherheitsteam das Notebook bzw. Skript überprüft haben.

## <a name="potential-threats"></a>Potenzielle Bedrohungen

Bei der Entwicklungsarbeit mit Azure Machine Learning werden häufig webbasierte Entwicklungsumgebungen (Notebooks und Azure ML Studio) genutzt. Bei der Verwendung von webbasierten Entwicklungsumgebungen müssen die folgenden potenziellen Bedrohungen beachtet werden:

* [Cross-Site Scripting (XSS)](https://owasp.org/www-community/attacks/xss/)

    * __DOM-Einschleusung__ : Bei dieser Art von Angriff kann die im Browser angezeigte Benutzeroberfläche geändert werden. Beispielsweise kann das Verhalten der Schaltfläche „Ausführen“ in einem Jupyter Notebook geändert werden.
    * __Zugriffstoken/Cookies__ : Bei XSS-Angriffen kann auch auf den lokalen Speicher und auf die Browsercookies zugegriffen werden. Ihr AAD-Authentifizierungstoken (Azure Active Directory) ist im lokalen Speicher gespeichert. Bei einem XSS-Angriff kann dieses Token ggf. verwendet werden, um API-Aufrufe in Ihrem Namen durchzuführen und die Daten dann an ein externes System oder eine externe API zu senden.

* [Websiteübergreifende Anforderungsfälschung (Cross-Site Request Forgery, CSRF)](https://owasp.org/www-community/attacks/csrf): Bei diesem Angriff kann die URL eines Bilds oder Links durch die URL eines schädlichen Skripts oder einer schädlichen API ersetzt werden. Wenn das Bild geladen bzw. auf den Link geklickt wird, wird die URL aufgerufen.

## <a name="azure-ml-studio-notebooks"></a>Azure ML Studio-Notebooks

Azure Machine Learning Studio stellt in Ihrem Browser eine Umgebung mit einem gehosteten Notebook bereit. Über die Zellen eines Notebooks können HTML-Dokumente oder -Fragmente ausgegeben werden, die schädlichen Code enthalten.  Beim Rendern der Ausgabe kann der Code ausgeführt werden.

__Mögliche Bedrohungen__ :
* Cross-Site Scripting (XSS)
* Websiteübergreifende Anforderungsfälschung (Cross-Site Request Forgery, CSRF)

__Entschärfungsmöglichkeiten mit Azure Machine Learning__ :
* Die __Ausgabe von Codezellen__ wird in einem iFrame in einer Sandbox angeordnet. Der IFrame verhindert, dass über das Skript auf das übergeordnete DOM, Cookies oder den Sitzungsspeicher zugegriffen wird.
* Der Inhalt von __Markdownzellen__ wird mit der dompurify-Bibliothek bereinigt. Hierdurch wird verhindert, dass schädliche Skripts, die über Markdownzellen ausgeführt werden, gerendert werden.
* Die __Bild-URL__ und __Markdownlinks__ werden an einen Endpunkt gesendet, der sich im Besitz von Microsoft befindet und auf dem eine Überprüfung auf schädliche Werte erfolgt. Wenn ein schädlicher Wert erkannt wird, lehnt der Endpunkt die Anforderung ab.

__Empfohlene Aktionen__ :
* Vergewissern Sie sich, dass die Inhalte von Dateien vertrauenswürdig sind, bevor Sie sie in Studio hochladen. Beim Hochladen müssen Sie bestätigen, dass es sich um vertrauenswürdige Dateien handelt.
* Bei der Auswahl eines Links zum Öffnen einer externen Anwendung wird eine Aufforderung angezeigt, in der Sie die Vertrauenswürdigkeit der Anwendung bestätigen müssen.

## <a name="azure-ml-compute-instance"></a>Azure ML-Compute-Instanz

Auf einer Azure Machine Learning-Compute-Instanz werden __Jupyter__ und __Jupyter Lab__ gehostet. Bei Verwendung einer dieser Anwendungen können über Zellen in einem Notebook oder Codezeilen in einem Skript jeweils HTML-Dokumente oder -Fragmente ausgegeben werden, die schädlichen Code enthalten. Beim Rendern der Ausgabe kann der Code ausgeführt werden. Die gleichen Bedrohungen gelten auch, wenn eine Instanz von __RStudio__ verwendet wird, die auf einer Compute-Instanz gehostet wird.

__Mögliche Bedrohungen__ :
* Cross-Site Scripting (XSS)
* Websiteübergreifende Anforderungsfälschung (Cross-Site Request Forgery, CSRF)

__Entschärfungsmöglichkeiten mit Azure Machine Learning__ :
* Keine. Jupyter und Jupyter Lab sind Open-Source-Anwendungen, die auf der Compute-Instanz von Azure Machine Learning gehostet werden.

__Empfohlene Aktionen__ :
* Vergewissern Sie sich, dass die Inhalte von Dateien vertrauenswürdig sind, bevor Sie sie in Studio hochladen. Beim Hochladen müssen Sie bestätigen, dass es sich um vertrauenswürdige Dateien handelt.

## <a name="report-security-issues-or-concerns"></a>Melden von Sicherheitsproblemen oder -bedenken 

Azure Machine Learning unterliegt dem Microsoft Azure Bounty Program (Prämienprogramm). Weitere Informationen finden Sie unter [https://www.microsoft.com/msrc/bounty-microsoft-azure](https://www.microsoft.com/msrc/bounty-microsoft-azure).

## <a name="next-steps"></a>Nächste Schritte

* [Unternehmenssicherheit für Azure Machine Learning](concept-enterprise-security.md)
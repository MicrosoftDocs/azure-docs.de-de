---
title: Machine Learning Services in Azure SQL Managed Instance (Vorschauversion)
description: Dieser Artikel bietet eine Übersicht über Machine Learning Services in Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 06/03/2020
ms.openlocfilehash: c805bacbd4a2219fb79168ad6426efd8b0a390df
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324515"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance-preview"></a>Machine Learning Services in Azure SQL Managed Instance (Vorschauversion)

Machine Learning Services ist ein Feature von Azure SQL Managed Instance (Vorschauversion), das in die Datenbank integriertes maschinelles Lernen bereitstellt und sowohl Python- als auch R-Skripts unterstützt. Das Feature umfasst Python- und R-Pakete von Microsoft für leistungsstarke Predictive Analytics und maschinelles Lernen. Die relationalen Daten können in Skripts über gespeicherte Prozeduren, Python- oder R-Anweisungen in T-SQL-Skripts oder Python- oder R-Code mit T-SQL verwendet werden.

> [!IMPORTANT]
> Machine Learning Services ist ein Feature von Azure SQL Managed Instance, das sich derzeit in der Public Preview befindet.
> Diese Previewfunktion ist anfänglich in einer begrenzten Anzahl von Regionen in den USA, Asien, Europa und Australien verfügbar, wobei weitere Regionen später hinzukommen werden.
>
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Sie können sich unten [für die Vorschau registrieren](#signup).

## <a name="what-is-machine-learning-services"></a>Was ist Machine Learning Services?

Mit Machine Learning Services in Azure SQL Managed Instance können Sie Python- und R-Skripts in einer Datenbank ausführen. Sie können das Feature verwenden, um Daten vorzubereiten und zu bereinigen, Features zu entwickeln und Machine Learning-Modelle in einer Datenbank zu trainieren, auszuwerten und bereitzustellen. Mit dem Feature können Sie Skripts ausführen, in denen sich die Daten befinden. Die Übertragung der Daten über das Netzwerk auf einen anderen Server entfällt.

Verwenden Sie Machine Learning Services mit R-/Python-Unterstützung in Azure SQL Managed Instance für Folgendes:

- **Ausführen von R- und Python-Skripts für die Datenvorbereitung und allgemeine Datenverarbeitung:** Sie können jetzt Ihre R-/Python-Skripts direkt in Azure SQL Managed Instance übertragen, wo sich auch Ihre Daten befinden. Somit müssen Sie keine Daten mehr auf einen anderen Server verschieben, um R- oder Python-Skripts auszuführen. Sie können den Bedarf an Datenverschiebungen und die damit zusammenhängenden Probleme in Bezug auf Latenz, Sicherheit und Compliance vermeiden.

- **Trainieren von Machine Learning-Modellen in der Datenbank:** Sie können Modelle mit beliebigen Open-Source-Algorithmen trainieren. Sie können Ihr Training problemlos auf das gesamte Dataset skalieren, anstatt sich auf Datasets mit Stichproben zu verlassen, die aus der Datenbank gepullt wurden.

- **Bereitstellen Ihrer Modelle und Skripts in der Produktion in gespeicherten Prozeduren:** Die Skripts und trainierten Modelle können einfach durch Einbetten in gespeicherte T-SQL-Prozeduren operationalisiert werden. Apps, die eine Verbindung mit Azure SQL Managed Instance herstellen, können von Vorhersagen und der Datenanalyse in diesen Modellen profitieren, indem sie einfach eine gespeicherte Prozedur aufrufen. Sie können auch die native T-SQL-Funktion PREDICT verwenden, um Modelle für die schnelle Bewertung in Szenarien zu operationalisieren, in denen viele gleichzeitige Echtzeitbewertungen durchgeführt werden.

In Machine Learning Services sind Basisverteilungen von R und Python enthalten. Neben den Microsoft-Paketen [revoscalepy](/sql/advanced-analytics/python/ref-py-revoscalepy) und [microsoftml](/sql/advanced-analytics/python/ref-py-microsoftml) für Python und [RevoScaleR](/sql/advanced-analytics/r/ref-r-revoscaler), [MicrosoftML](/sql/advanced-analytics/r/ref-r-microsoftml), [olapR](/sql/advanced-analytics/r/ref-r-olapr) und [sqlrutils](/sql/advanced-analytics/r/ref-r-sqlrutils) für R können Sie Open-Source-Pakete und -Frameworks wie PyTorch, TensorFlow und scikit-learn installieren und verwenden.

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Anmelden für die Vorschau

Diese eingeschränkte Public Preview unterliegt den [Azure-Vorschaubestimmungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Wenn Sie am Vorschauprogramm teilnehmen möchten und diese Bedingungen akzeptieren, können Sie die Registrierung anfordern. Erstellen Sie dazu unter [ **https://azure.microsoft.com/support/create-ticket/**](https://azure.microsoft.com/support/create-ticket/) ein Azure-Supportticket. 

1. Klicken Sie auf der Seite **Supportticket erstellen** auf **Create an Incident** (Incident erstellen).

1. Klicken Sie auf der Seite **Hilfe und Support** auf **Neue Supportanfrage**, um ein neues Ticket zu erstellen.

1. Wählen Sie die folgenden Optionen:
   - Problemtyp: **Technisch**
   - Abonnement: *Wählen Sie Ihr Abonnement aus.*
   - Dienst: **SQL Managed Instance**
   - Ressource: *Wählen Sie Ihre verwaltete Instanz aus.*
   - Zusammenfassung: *Geben Sie eine kurze Beschreibung Ihrer Anfrage ein.*
   - Problemtyp: **Machine Learning Services in SQL Managed Instance (Vorschauversion)**
   - Problemuntertyp: **Weitere Probleme oder Fragen zur Vorgehensweise**

1. Klicken Sie auf **Weiter: Lösungen**.

1. Lesen Sie die Informationen zur Vorschau, und klicken Sie dann auf **Weiter: Details**.

1. Gehen Sie auf dieser Seite wie folgt vor:
   - Wählen Sie für die Frage **Are you trying to sign up for the Preview?** (Möchten Sie sich für die Vorschau registrieren?) die Option **Ja** aus. 
   - Geben Sie unter **Beschreibung** relevante Daten zu Ihrer Anforderung ein, einschließlich des Namens des logischen Servers, der Region und der Abonnement-ID, die Sie für die Vorschau registrieren möchten. Geben Sie bei Bedarf weitere Details ein.
   - Wählen Sie eine bevorzugte Kontaktmethode aus. 

1. Klicken Sie abschließend auf **Weiter: Überprüfen + erstellen** und dann auf **Erstellen**.

Nachdem Sie für das Programm registriert wurden, führt Microsoft für Sie das Onboarding für die Public Preview durch und aktiviert Machine Learning Services für Ihre vorhandenen oder neuen Datenbanken.

Es ist nicht empfehlenswert, Machine Learning Services in SQL Managed Instance während der Public Preview für Produktionsworkloads zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie [Key differences from SQL Server Machine Learning Services](machine-learning-services-differences.md) (Wichtige Unterschiede zwischen Machine Learning Services in Azure SQL Server).
- Informationen zur Verwendung von Python in Machine Learning Services finden Sie unter [Schnellstart: Ausführen einfacher Python-Skripts mit SQL Server Machine Learning Services](/sql/machine-learning/tutorials/quickstart-python-create-script?context=%2fazure%2fazure-sql%2fmanaged-instance%2fcontext%2fml-context&view=sql-server-ver15).
- Informationen zur Verwendung von R in Machine Learning Services finden Sie unter [Schnellstart: Ausführen einfacher R-Skripts mit SQL Machine Learning](/sql/machine-learning/tutorials/quickstart-r-create-script?context=%2fazure%2fazure-sql%2fmanaged-instance%2fcontext%2fml-context&view=sql-server-ver15).
- Weitere Informationen zu Machine Learning auf anderen SQL-Plattformen finden Sie in der [SQL Machine Learning-Dokumentation](/sql/machine-learning/).
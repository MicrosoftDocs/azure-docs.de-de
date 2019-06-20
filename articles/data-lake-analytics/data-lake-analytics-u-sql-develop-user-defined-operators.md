---
title: Entwickeln benutzerdefinierter Operatoren (UDOs) für U-SQL in Azure Data Lake Analytics
description: Hier erfahren Sie, wie Sie benutzerdefinierte Operatoren zur Verwendung und Wiederverwendung in Azure Data Lake Analytics-Aufträgen entwickeln.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 122a4b6af78a22f74d5057da75767077f8d9b978
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813793"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Entwickeln von benutzerdefinierten Operatoren von U-SQL (UDOs)
In diesem Artikel erfahren Sie, wie Sie benutzerdefinierte Operatoren entwickeln, um Daten in einem U-SQL-Auftrag zu verarbeiten.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Definieren und Verwenden eines benutzerdefinierten Operators in U-SQL
**So erstellen und übermitteln Sie einen U-SQL-Auftrag**

1. Wählen Sie in Visual Studio **Datei > Neu > Projekt > U-SQL-Projekt** aus.
2. Klicken Sie auf **OK**. Visual Studio erstellt eine Projektmappe mit der Datei „Script.usql“.
3. Erweitern Sie im **Projektmappen-Explorer** die Datei „Script.usql“, und doppelklicken Sie dann auf **Script.usql.cs**.
4. Fügen Sie den folgenden Code in die Datei ein:

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;

        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Suisse", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };

                public override IRow Process(IRow input, IUpdatableRow output)
                {

                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");

                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);

                    return output.AsReadOnly();
                }
            }
        }
6. Öffnen Sie die Datei **Script.usql**, und fügen Sie das folgende U-SQL-Skript ein:

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);

        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    

        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);
7. Geben Sie das Data Lake Analytics-Konto, die -Datenbank und das -Schema an.
8. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Script.usql**, und klicken Sie dann auf **Skript erstellen**.
9. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Script.usql**, und klicken Sie dann auf **Skript senden**.
10. Wenn Sie noch nicht mit Ihrem Azure-Abonnement verbunden sind, werden Sie zur Eingabe der Anmeldeinformationen für Ihr Azure-Konto aufgefordert.
11. Klicken Sie auf **Submit**. Nach Abschluss der Übermittlung werden Ergebnisse und ein Auftragslink im Fenster „Ergebnisse“ angezeigt.
12. Sie müssen auf die Schaltfläche **Aktualisieren** klicken, um den letzten Auftragsstatus anzuzeigen und den Bildschirm zu aktualisieren.

**So zeigen Sie die Ausgabe an**

1. Erweitern Sie im **Server-Explorer** nacheinander **Azure**, **Data Lake Analytics**, Ihr Data Lake Analytics-Konto und **Speicherkonten**. Klicken Sie mit der rechten Maustaste auf den Standardspeicher, und klicken Sie dann auf **Explorer**.
2. Erweitern Sie „Beispielcode“ und „Ausgaben“, und doppelklicken Sie dann auf **Drivers.csv**.

## <a name="see-also"></a>Weitere Informationen
* [Erweitern von U-SQL-Ausdrücken mit Benutzercode](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [Verwenden von Data Lake-Tools für Visual Studio zum Entwickeln von U-SQL-Anwendungen](data-lake-analytics-data-lake-tools-get-started.md)

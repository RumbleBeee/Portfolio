# Aufgabenstellung

Du arbeitest als Data Scientist bei einer P-2-P-Plattform, die vor einem Jahr gegründet wurde. Nun wollt ihr euer Geschäft erweitern. Euer Team hat sich aufgeteilt und jeder Analyst hat einen Teilbereich der Daten. Deine Aufgabe ist es in einer *explorativen Datenanalyse* Insights für eure Plattform herauszufinden.

Euer Geschäftsmodell ist das Betreiben einer Plattform (crowd-investing) bei der sich Personen die eine Geschäftsidee haben, aber nicht das benötigte Geld, anmelden und für ihr Projekt innerhalb einer vorgegebenen Zeit Geld sammeln können. Auf der anderen Seite habt ihr Geldgeber, die gern ihr Geld in Projekte anlegen möchten und nach Investitionen suchen. Als Vermittler bringt eure Plattform also Geldnehmer und Geldgeber zusammen. Ihr verdient euer Geld mit einer Provision für jedes Projekt was auf eurer Plattform landet.

Deine Datenbasis ist die Historie eurer Plattform. Alle Projekte sind abgeschlossene Projekte, d.h. die Zeit, um für sein Projekt Geld zu sammeln ist abgelaufen. Euer Geschäftsmodell sieht es vor, dass die gesammelten Gelder ausgezahlt werden, auch wenn der Zielbetrag nicht erreicht wurde.

Dafür ist der Datensatz zunächst vorzubereiten. In die Bewertung geht zudem die Dokumentation mit ein. Für die Visualisierung sind 3 verschiedenartige Plots zu benennen, die in die Bewertung eingehen sollen. Das bedeutet nicht, dass du nur 3 Plots ersellen darfst - ausschließlich für die Bewertung sind drei verschiedenartige Plots zu benennen! Vergiss dabei nicht die Erkenntnisse die aus jedem Plot gezogen werden können, zu notieren.

Die Dokumentation kann in englisch oder deutsch erfolgen.

Am Freitag erfolgt die Vorstellung der Plots innerhalb des Zeitrahmens von 5min - (+-1min) ist dabei ok.

Treff ist **Freitag um 14:30 Uhr im Hauptraum**. Da werden wir die Unterlagen in dokumentensichere Formate umwandeln. Das machen wir aber zusammen. Danach habt ihr bis 15:35 Uhr zeit, eure zu bewertenden Unterlagen in den Projektabgabeordner hochzuladen.


## Inhalt des Datensatzes
Der gesplittete Datensatz enthält folgende Spalten (inkl. Bedeutung):

- funded_amount ... mit Ablauf der "Crowding"zeit erhaltener Betrag/ ausgezahlter Betrag in USD
- loan_amount ... Zielbetrag (Betrag dem man für das Projekt erreichen wollte) in USD
- activity ... Unterkategory zu dem das Ziel des Crowdprojektes thematisch gehört
- sector ... Oberkategory in den das Crowdprojektes Thema fällt
- use ... Kurzbeschreibung wofür das Geld verwendet werden soll
- country_code ... Ländercode nach ISO Norm
- country ... Ländername nach ISO Norm
- region ... Region
- currency ... Währung in den der funded_amount dann ausgezahlt wurde
- term in months ... Dauer über die der Kredit ausgezahlt werden soll
- lender_count ...Darlehensgeber (also wieviele Personen Geld für das Projekt gegeben haben)
- borrower_genders ... Geschlecht und Anzahl der Darlehensnehmer, also diejenigen die das Crowdprojekt initiiert haben     
- repayment interval ... vertraglich vereinbarte Rückzahlungsmodalitäten/-rhythmus

# Projektbearbeitung - Crowd-Investing

## Vorbereitung - Importieren der Bibliotheken und Einlesen des Datensatzes

Bevor die eigentliche Arbeit beginnen kann, müssen zuvor einige notwendige Bibliotheken eingelesen werden. Danach wird der Datensatz eingelesen, welcher im vorliegenden Fall aus zwei Dateien besteht. Die Daten liegen in einem CSV-Format vor. Hier muss beachtet werden, was als Trennzeichen in der jeweiligen Datei benutzt wurde. Dies muss beim Import berücksichtigt werden. 


```python
import pandas as pd
import numpy as np

from currency_converter import CurrencyConverter
from pycountry_convert import country_alpha2_to_continent_code, country_name_to_country_alpha2

from datetime import date
from colorama import Fore, Back
```


```python
import warnings
warnings.filterwarnings('ignore')
```


```python
file1 = 'your_part1.csv'
file2 = 'your_part2.csv'
```


```python
df1 = pd.read_csv(file1, index_col=0)
df1.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>funded_amount</th>
      <th>loan_amount</th>
      <th>activity</th>
      <th>sector</th>
      <th>use</th>
      <th>country_code</th>
      <th>country</th>
      <th>region</th>
      <th>currency</th>
      <th>term_in_months</th>
      <th>lender_count</th>
      <th>borrower_genders</th>
      <th>repayment_interval</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>300.0</td>
      <td>300.0</td>
      <td>Fruits &amp; Vegetables</td>
      <td>Food</td>
      <td>To buy seasonal, fresh fruits to sell.</td>
      <td>PK</td>
      <td>Pakistan</td>
      <td>Lahore</td>
      <td>PKR</td>
      <td>12.0</td>
      <td>12</td>
      <td>female</td>
      <td>irregular</td>
    </tr>
    <tr>
      <th>1</th>
      <td>575.0</td>
      <td>575.0</td>
      <td>Rickshaw</td>
      <td>Transportation</td>
      <td>to repair and maintain the auto rickshaw used ...</td>
      <td>PK</td>
      <td>Pakistan</td>
      <td>Lahore</td>
      <td>PKR</td>
      <td>11.0</td>
      <td>14</td>
      <td>female, female</td>
      <td>irregular</td>
    </tr>
    <tr>
      <th>2</th>
      <td>150.0</td>
      <td>150.0</td>
      <td>Transportation</td>
      <td>Transportation</td>
      <td>To repair their old cycle-van and buy another ...</td>
      <td>IN</td>
      <td>India</td>
      <td>Maynaguri</td>
      <td>INR</td>
      <td>43.0</td>
      <td>6</td>
      <td>female</td>
      <td>bullet</td>
    </tr>
    <tr>
      <th>3</th>
      <td>200.0</td>
      <td>200.0</td>
      <td>Embroidery</td>
      <td>Arts</td>
      <td>to purchase an embroidery machine and a variet...</td>
      <td>PK</td>
      <td>Pakistan</td>
      <td>Lahore</td>
      <td>PKR</td>
      <td>11.0</td>
      <td>8</td>
      <td>female</td>
      <td>irregular</td>
    </tr>
    <tr>
      <th>4</th>
      <td>400.0</td>
      <td>400.0</td>
      <td>Milk Sales</td>
      <td>Food</td>
      <td>to purchase one buffalo.</td>
      <td>PK</td>
      <td>Pakistan</td>
      <td>Abdul Hakeem</td>
      <td>PKR</td>
      <td>14.0</td>
      <td>16</td>
      <td>female</td>
      <td>monthly</td>
    </tr>
  </tbody>
</table>
</div>




```python
df1.shape
```




    (335000, 13)




```python
df2 = pd.read_csv(file2, usecols=[1])
df2.head(1)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th># funded_amount# loan_amount# activity# sector# use# country_code# country# region# currency# term_in_months# lender_count# borrower_genders# repayment_interval</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Leyte#PHP#8.0#6#female#irregular</td>
    </tr>
  </tbody>
</table>
</div>



Beim Import der Datei gab es einen Fehler. Das Einlesen der ersten Spalte klappte hingegen und wie man sehen kann, muss das Trennzeichen (hier auf #) angepasst werden.


```python
df2 = pd.read_csv(file2,
                  delimiter='#', # Trennzeichen muss angepasst werden
                  index_col=0
                 ) 
df2.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>funded_amount</th>
      <th>loan_amount</th>
      <th>activity</th>
      <th>sector</th>
      <th>use</th>
      <th>country_code</th>
      <th>country</th>
      <th>region</th>
      <th>currency</th>
      <th>term_in_months</th>
      <th>lender_count</th>
      <th>borrower_genders</th>
      <th>repayment_interval</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>175.0</td>
      <td>175.0</td>
      <td>Liquor Store / Off-License</td>
      <td>Food</td>
      <td>to purchase additional stock of coconut wine t...</td>
      <td>PH</td>
      <td>Philippines</td>
      <td>Palo, Leyte</td>
      <td>PHP</td>
      <td>8.0</td>
      <td>6</td>
      <td>female</td>
      <td>irregular</td>
    </tr>
    <tr>
      <th>1</th>
      <td>325.0</td>
      <td>325.0</td>
      <td>Livestock</td>
      <td>Agriculture</td>
      <td>to buy 3 zebus and food to fatten them up.</td>
      <td>MG</td>
      <td>Madagascar</td>
      <td>Antsirabe</td>
      <td>MGA</td>
      <td>12.0</td>
      <td>13</td>
      <td>female</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>2</th>
      <td>550.0</td>
      <td>550.0</td>
      <td>Food Stall</td>
      <td>Food</td>
      <td>to buy ingredients for her food-vending busine...</td>
      <td>PH</td>
      <td>Philippines</td>
      <td>Cordova, Cebu</td>
      <td>PHP</td>
      <td>5.0</td>
      <td>6</td>
      <td>female</td>
      <td>irregular</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1300.0</td>
      <td>1300.0</td>
      <td>Cattle</td>
      <td>Agriculture</td>
      <td>to buy one head of cattle.</td>
      <td>EG</td>
      <td>Egypt</td>
      <td>Baniswef</td>
      <td>EGP</td>
      <td>14.0</td>
      <td>50</td>
      <td>male</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>4</th>
      <td>900.0</td>
      <td>900.0</td>
      <td>Consumer Goods</td>
      <td>Personal Use</td>
      <td>to buy consumer goods amongst others.</td>
      <td>PE</td>
      <td>Peru</td>
      <td>Urubamba - Urubamba - Cusco</td>
      <td>PEN</td>
      <td>6.0</td>
      <td>1</td>
      <td>female</td>
      <td>irregular</td>
    </tr>
  </tbody>
</table>
</div>




```python
df2.shape
```




    (336205, 13)



### Zusammenfügen der Datensätze

Nachdem nun beide Datensätze eingelesen wurden, konnten diese miteinander verglichen werden. Beide Datensätze bestehen aus der selben Anzahl an Spalten (hier sind es 13 an der Zahl) und können somit zeilenweise verbunden werden (d.h. es gibt keine neuen Eigenschaften im zweiten Datensatz und die Daten können einfach unten angehangen werden)


```python
df = pd.concat([df1, df2], axis=0)
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>funded_amount</th>
      <th>loan_amount</th>
      <th>activity</th>
      <th>sector</th>
      <th>use</th>
      <th>country_code</th>
      <th>country</th>
      <th>region</th>
      <th>currency</th>
      <th>term_in_months</th>
      <th>lender_count</th>
      <th>borrower_genders</th>
      <th>repayment_interval</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>300.0</td>
      <td>300.0</td>
      <td>Fruits &amp; Vegetables</td>
      <td>Food</td>
      <td>To buy seasonal, fresh fruits to sell.</td>
      <td>PK</td>
      <td>Pakistan</td>
      <td>Lahore</td>
      <td>PKR</td>
      <td>12.0</td>
      <td>12</td>
      <td>female</td>
      <td>irregular</td>
    </tr>
    <tr>
      <th>1</th>
      <td>575.0</td>
      <td>575.0</td>
      <td>Rickshaw</td>
      <td>Transportation</td>
      <td>to repair and maintain the auto rickshaw used ...</td>
      <td>PK</td>
      <td>Pakistan</td>
      <td>Lahore</td>
      <td>PKR</td>
      <td>11.0</td>
      <td>14</td>
      <td>female, female</td>
      <td>irregular</td>
    </tr>
    <tr>
      <th>2</th>
      <td>150.0</td>
      <td>150.0</td>
      <td>Transportation</td>
      <td>Transportation</td>
      <td>To repair their old cycle-van and buy another ...</td>
      <td>IN</td>
      <td>India</td>
      <td>Maynaguri</td>
      <td>INR</td>
      <td>43.0</td>
      <td>6</td>
      <td>female</td>
      <td>bullet</td>
    </tr>
    <tr>
      <th>3</th>
      <td>200.0</td>
      <td>200.0</td>
      <td>Embroidery</td>
      <td>Arts</td>
      <td>to purchase an embroidery machine and a variet...</td>
      <td>PK</td>
      <td>Pakistan</td>
      <td>Lahore</td>
      <td>PKR</td>
      <td>11.0</td>
      <td>8</td>
      <td>female</td>
      <td>irregular</td>
    </tr>
    <tr>
      <th>4</th>
      <td>400.0</td>
      <td>400.0</td>
      <td>Milk Sales</td>
      <td>Food</td>
      <td>to purchase one buffalo.</td>
      <td>PK</td>
      <td>Pakistan</td>
      <td>Abdul Hakeem</td>
      <td>PKR</td>
      <td>14.0</td>
      <td>16</td>
      <td>female</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>336200</th>
      <td>0.0</td>
      <td>25.0</td>
      <td>Livestock</td>
      <td>Agriculture</td>
      <td>[True, u'para compara: cemento, arenya y ladri...</td>
      <td>PY</td>
      <td>Paraguay</td>
      <td>Concepción</td>
      <td>USD</td>
      <td>13.0</td>
      <td>0</td>
      <td>female</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>336201</th>
      <td>25.0</td>
      <td>25.0</td>
      <td>Livestock</td>
      <td>Agriculture</td>
      <td>[True, u'to start a turducken farm.'] - this l...</td>
      <td>KE</td>
      <td>Kenya</td>
      <td>NaN</td>
      <td>KES</td>
      <td>13.0</td>
      <td>1</td>
      <td>female</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>336202</th>
      <td>0.0</td>
      <td>25.0</td>
      <td>Games</td>
      <td>Entertainment</td>
      <td>NaN</td>
      <td>KE</td>
      <td>Kenya</td>
      <td>NaN</td>
      <td>KES</td>
      <td>13.0</td>
      <td>0</td>
      <td>NaN</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>336203</th>
      <td>0.0</td>
      <td>25.0</td>
      <td>Livestock</td>
      <td>Agriculture</td>
      <td>[True, u'to start a turducken farm.'] - this l...</td>
      <td>KE</td>
      <td>Kenya</td>
      <td>NaN</td>
      <td>KES</td>
      <td>13.0</td>
      <td>0</td>
      <td>female</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>336204</th>
      <td>0.0</td>
      <td>25.0</td>
      <td>Livestock</td>
      <td>Agriculture</td>
      <td>[True, u'to start a turducken farm.'] - this l...</td>
      <td>KE</td>
      <td>Kenya</td>
      <td>NaN</td>
      <td>KES</td>
      <td>13.0</td>
      <td>0</td>
      <td>female</td>
      <td>monthly</td>
    </tr>
  </tbody>
</table>
<p>671205 rows × 13 columns</p>
</div>



### Prüfung des Resultates

Es wird geprüft, ob das Zusammenfügen der Dateien geklappt hat. Wir kennen die Dimensionen (Anzahl der Zeilen/Spalten) der ersten beiden Datensätze und können dies mit dem kompletten Datensatz vergleichen. Da wir die Daten als neue Zeilen unter den ersten angefügt haben, darf sich die Anzahl der Spalten nicht verändert haben, die Zeilen jedoch sollten der Summe aus Datensatz1 und Datensatz2 entsprechen.


```python
if df1.shape[1] == df2.shape[1] & df1.shape[1] == df.shape[1]:
    print('Alle Datensätze haben die gleiche Spaltenanzahl!\n')

if df.shape[0] == df1.shape[0] + df2.shape[0]:
    print('Zeilenweises Zusammenfügen hat funktioniert!\n')
    print(
        'Der komplette Datensatz besteht aus {rows} Zeilen und {columns} Spalten'
        .format(rows=df.shape[0], columns=df.shape[1]))
```

    Alle Datensätze haben die gleiche Spaltenanzahl!
    
    Zeilenweises Zusammenfügen hat funktioniert!
    
    Der komplette Datensatz besteht aus 671205 Zeilen und 13 Spalten
    

Das Zusammenfügen hat funktioniert. Nun sotiere ich den Datensatz noch nach dem Land und setze den Index entsprechend zurück. Anschließend können wir uns den Datensatz etwas genauer anschauen.

## Preprocessing des Datensatzes

Nachfolgend wird der Datensatz nach folgenden Gesichtspunkten geprüft:

- erste grobe statistische Übersicht anzeigen
- unnötige Leerzeichen bei Zeichenketten (um später die daten besser selektieren zu können)
- passen die Datentypen zu dem Inhalt der Spalten
- fehlende Werte (sowohl reale als auch synonymisierte Werte)
- können Daten kategorisiert werden (z.B. einige wenige Werte für ganz viele Zeilen)

### Einfache statistische Übersicht


```python
df.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>funded_amount</th>
      <th>loan_amount</th>
      <th>term_in_months</th>
      <th>lender_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>671205.000000</td>
      <td>671205.000000</td>
      <td>671205.000000</td>
      <td>671205.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>785.995061</td>
      <td>842.397107</td>
      <td>13.739022</td>
      <td>20.590922</td>
    </tr>
    <tr>
      <th>std</th>
      <td>1130.398941</td>
      <td>1198.660073</td>
      <td>8.598919</td>
      <td>28.459551</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
      <td>25.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>250.000000</td>
      <td>275.000000</td>
      <td>8.000000</td>
      <td>7.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>450.000000</td>
      <td>500.000000</td>
      <td>13.000000</td>
      <td>13.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>900.000000</td>
      <td>1000.000000</td>
      <td>14.000000</td>
      <td>24.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>100000.000000</td>
      <td>100000.000000</td>
      <td>158.000000</td>
      <td>2986.000000</td>
    </tr>
  </tbody>
</table>
</div>



Hier fällt auf, das wir in den Spalten **funded_amount** und **lender_count** als Minimalwert '0' angegeben haben. Das könnte ein ersten Hinweis auf fehlende Werte sein. Vielleicht hängen beide Werte voneinander ab, denn wo kein Dahrlehensgeber ist, da kann auch kein Geld geflossen sein. Aber dies wird zu einem späteren Zeitpunkt überprüft.


```python
df.nunique()
```




     funded_amount            610
     loan_amount              479
     activity                 163
     sector                    15
     use                   424912
     country_code              86
     country                   87
     region                 12695
     currency                  67
     term_in_months           148
     lender_count             503
     borrower_genders       11298
     repayment_interval         4
    dtype: int64



Hier fällt der besonders hohe Anteil der einzigartigen Werte in der Spalte **borrower_genders** auf. Beim Geschlecht geht man erstmal von einer geringeren Anzahl aus (nach heutigem Standard sind es drei), doch hier scheint man davon abzuweichen. Wir werden später prüfen, was es damit aufsich hat.

### Leerzeichen in Spaltenüberschriften

Es wird geprüft, ob es Leerzeichen in den Spaltenbeschriftungen gibt, welche das spätere selektieren/filtern der Daten beeinträchtigen kann. Wenn vorhanden, werden diese schließlich entfernt.


```python
print('Überschriften vorher:')
df.columns
```

    Überschriften vorher:
    




    Index([' funded_amount', ' loan_amount', ' activity', ' sector', ' use',
           ' country_code', ' country', ' region', ' currency', ' term_in_months',
           ' lender_count', ' borrower_genders', ' repayment_interval'],
          dtype='object')




```python
for column in df.columns:
    if column[0].isspace():
        df.rename(columns=lambda column: column.strip(), inplace=True)
```


```python
#check, ob Verarbeitung geklappt hat

print('Überschriften nacher:')
df.columns
```

    Überschriften nacher:
    




    Index(['funded_amount', 'loan_amount', 'activity', 'sector', 'use',
           'country_code', 'country', 'region', 'currency', 'term_in_months',
           'lender_count', 'borrower_genders', 'repayment_interval'],
          dtype='object')



### Sortieren und neu indizieren


```python
df.sort_values(by=['country'], inplace=True)
```


```python
df.reset_index(drop=True, inplace=True)
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>funded_amount</th>
      <th>loan_amount</th>
      <th>activity</th>
      <th>sector</th>
      <th>use</th>
      <th>country_code</th>
      <th>country</th>
      <th>region</th>
      <th>currency</th>
      <th>term_in_months</th>
      <th>lender_count</th>
      <th>borrower_genders</th>
      <th>repayment_interval</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>8000.0</td>
      <td>8000.0</td>
      <td>Textiles</td>
      <td>Arts</td>
      <td>To purchase raw materials, including fabrics, ...</td>
      <td>AF</td>
      <td>Afghanistan</td>
      <td>Kandahar City</td>
      <td>USD</td>
      <td>6.0</td>
      <td>308</td>
      <td>female</td>
      <td>bullet</td>
    </tr>
    <tr>
      <th>1</th>
      <td>6000.0</td>
      <td>6000.0</td>
      <td>Textiles</td>
      <td>Arts</td>
      <td>To buy 9 sewing machines that will increase pr...</td>
      <td>AF</td>
      <td>Afghanistan</td>
      <td>Kandahar City</td>
      <td>USD</td>
      <td>8.0</td>
      <td>204</td>
      <td>female</td>
      <td>bullet</td>
    </tr>
    <tr>
      <th>2</th>
      <td>825.0</td>
      <td>825.0</td>
      <td>Agriculture</td>
      <td>Agriculture</td>
      <td>to buy a dairy cow</td>
      <td>AL</td>
      <td>Albania</td>
      <td>Korce</td>
      <td>ALL</td>
      <td>20.0</td>
      <td>26</td>
      <td>female</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2775.0</td>
      <td>2775.0</td>
      <td>Agriculture</td>
      <td>Agriculture</td>
      <td>to repair the damaged animal stable and also d...</td>
      <td>AL</td>
      <td>Albania</td>
      <td>Korce</td>
      <td>ALL</td>
      <td>26.0</td>
      <td>101</td>
      <td>male</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>4</th>
      <td>825.0</td>
      <td>825.0</td>
      <td>Personal Housing Expenses</td>
      <td>Housing</td>
      <td>to repair windows and doors in her house.</td>
      <td>AL</td>
      <td>Albania</td>
      <td>Korce</td>
      <td>ALL</td>
      <td>14.0</td>
      <td>32</td>
      <td>female</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>671200</th>
      <td>1900.0</td>
      <td>1900.0</td>
      <td>Poultry</td>
      <td>Agriculture</td>
      <td>To expand her production of chickens.</td>
      <td>ZW</td>
      <td>Zimbabwe</td>
      <td>Harare</td>
      <td>USD</td>
      <td>8.0</td>
      <td>69</td>
      <td>female, female, female, female</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>671201</th>
      <td>1500.0</td>
      <td>1500.0</td>
      <td>Clothing</td>
      <td>Clothing</td>
      <td>to buy clothes and school uniforms for resale.</td>
      <td>ZW</td>
      <td>Zimbabwe</td>
      <td>GOKWE</td>
      <td>USD</td>
      <td>8.0</td>
      <td>36</td>
      <td>female</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>671202</th>
      <td>500.0</td>
      <td>500.0</td>
      <td>Clothing Sales</td>
      <td>Clothing</td>
      <td>To purchase more clothes to sell</td>
      <td>ZW</td>
      <td>Zimbabwe</td>
      <td>Hurungwe</td>
      <td>USD</td>
      <td>8.0</td>
      <td>19</td>
      <td>female</td>
      <td>irregular</td>
    </tr>
    <tr>
      <th>671203</th>
      <td>1100.0</td>
      <td>1100.0</td>
      <td>Grocery Store</td>
      <td>Food</td>
      <td>to restock a wider range of stock for her tabl...</td>
      <td>ZW</td>
      <td>Zimbabwe</td>
      <td>Harare</td>
      <td>USD</td>
      <td>9.0</td>
      <td>30</td>
      <td>female, female, female, female</td>
      <td>irregular</td>
    </tr>
    <tr>
      <th>671204</th>
      <td>500.0</td>
      <td>500.0</td>
      <td>Grocery Store</td>
      <td>Food</td>
      <td>To purchase more grocery stock in bulk</td>
      <td>ZW</td>
      <td>Zimbabwe</td>
      <td>Gokwe North</td>
      <td>USD</td>
      <td>8.0</td>
      <td>14</td>
      <td>female</td>
      <td>irregular</td>
    </tr>
  </tbody>
</table>
<p>671205 rows × 13 columns</p>
</div>




```python
# Funktion zur Ausgabe des genutzten Speicherplatzes in MiB
def my_memory_usage(dataframe):
    # Umrechnungsfaktor Byte --> MiB
    MiB = 2**(20)
    
    mem = dataframe.memory_usage(deep=True)
    n = round(mem.sum() / MiB, 1)
    print('genutzer Speicherplatz [MiB]:')
    return n
```


```python
mem_usage_init = my_memory_usage(df)
mem_usage_init
```

    genutzer Speicherplatz [MiB]:
    




    429.9




```python
df.info(verbose=True, memory_usage='deep')
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 671205 entries, 0 to 671204
    Data columns (total 13 columns):
     #   Column              Non-Null Count   Dtype  
    ---  ------              --------------   -----  
     0   funded_amount       671205 non-null  float64
     1   loan_amount         671205 non-null  float64
     2   activity            671205 non-null  object 
     3   sector              671205 non-null  object 
     4   use                 666973 non-null  object 
     5   country_code        671197 non-null  object 
     6   country             671205 non-null  object 
     7   region              614405 non-null  object 
     8   currency            671205 non-null  object 
     9   term_in_months      671205 non-null  float64
     10  lender_count        671205 non-null  int64  
     11  borrower_genders    666984 non-null  object 
     12  repayment_interval  671205 non-null  object 
    dtypes: float64(3), int64(1), object(9)
    memory usage: 429.9 MB
    

### Prüfung auf fehlende Werte

Nun prüfen wir den Datensatz auf fehlende Werte. Anschlißend wird bewertet, was wir mit diesen Daten machen (Löschen, Konvertieren, berechnete Werte eintragen).


```python
null_values = df.isnull().sum()
null_values
```




    funded_amount             0
    loan_amount               0
    activity                  0
    sector                    0
    use                    4232
    country_code              8
    country                   0
    region                56800
    currency                  0
    term_in_months            0
    lender_count              0
    borrower_genders       4221
    repayment_interval        0
    dtype: int64




```python
cols_with_nulls = null_values[null_values > 0] 
cols_with_nulls
```




    use                  4232
    country_code            8
    region              56800
    borrower_genders     4221
    dtype: int64




```python
cols_with_nulls.index
```




    Index(['use', 'country_code', 'region', 'borrower_genders'], dtype='object')




```python
n_rows = df.shape[0]

for i, column in enumerate(cols_with_nulls.index):
    name = 'Anzahl der fehlenden Werte in Spalte "{0}":'.format(column)
    missing = cols_with_nulls.values[i]
    perc = missing / n_rows
    print(name)
    print('{pos1} von {pos2} Zeilen enthalten fehlende Werte ({pos3:.2%}).\n'.
          format(pos1=missing, pos3=perc, pos2=n_rows))
```

    Anzahl der fehlenden Werte in Spalte "use":
    4232 von 671205 Zeilen enthalten fehlende Werte (0.63%).
    
    Anzahl der fehlenden Werte in Spalte "country_code":
    8 von 671205 Zeilen enthalten fehlende Werte (0.00%).
    
    Anzahl der fehlenden Werte in Spalte "region":
    56800 von 671205 Zeilen enthalten fehlende Werte (8.46%).
    
    Anzahl der fehlenden Werte in Spalte "borrower_genders":
    4221 von 671205 Zeilen enthalten fehlende Werte (0.63%).
    
    

#### Reminder: reale 0-Werte

Wir erinnern uns an die 0-Werte, welche wir in der statistischen Übersicht gefunden haben. Diese waren in den Spalten **funded_amount** sowie **lender_count** ersichtlich. Mit nachfolgender Methode prüfen wir, ob die Daten vielleich zusammen gehören. Der Gedanke ist der, das wenn die Anzahl der Zeilen beider Spalten übereinstimmt und auch die der Index bei beiden Spalten der gleiche ist, dann gibt es hier folgenden Zusammenhang:

Wo kein Investor, da kein Geld, welches investiert werden kann.


```python
funded_zeros = df.loc[:, 'funded_amount'] == 0
lender_zeros = df.loc[:, 'lender_count'] == 0

n_rows_funded = len(df.loc[funded_zeros])
n_rows_lender = len(df.loc[lender_zeros])

indicies_funded = df.loc[funded_zeros].index
indicies_lender = df.loc[lender_zeros].index

matching_indicies = len(indicies_funded.intersection(indicies_lender))
```


```python
if (n_rows_funded == n_rows_lender) & (n_rows_funded == matching_indicies):
    print('{} Zeilen betroffen'.format(n_rows_funded))
    print('Anzahl der Zeilen und Indizes passen zusammen!\n')
    print('Die Datensätze haben etwas miteinander zu tun!')
```

    3383 Zeilen betroffen
    Anzahl der Zeilen und Indizes passen zusammen!
    
    Die Datensätze haben etwas miteinander zu tun!
    

Die Vermutung war richtig und wir werden uns diesen Sachverhalt später in der explorativen Datenanalyse genauer anschauen. Zuvor werden wir aber noch die verbliebenden fehlenden Werte bearbeiten müssen.


```python
cols_with_nulls
```




    use                  4232
    country_code            8
    region              56800
    borrower_genders     4221
    dtype: int64



Wir fangen mal klein an und schauen uns die fehlenden Werte beim Ländercode an:

#### Fehlende Werte in Spalte 'country_code'


```python
null_values_countryCode = df.loc[:, cols_with_nulls.index[1]].isnull()
df.loc[null_values_countryCode == True, 'country']
```




    310649    Namibia
    310650    Namibia
    310651    Namibia
    310652    Namibia
    310653    Namibia
    310654    Namibia
    310655    Namibia
    310656    Namibia
    Name: country, dtype: object



Ok, hier fehlt offensichtlich der Code für Namibia. Zuvor können wir nachschauen, ob es wirklich alle Datensätze aus diesem Land sind und filtern nach der Spalte **country**


```python
df.loc[df.loc[:, 'country'] == 'Namibia', 'country'].count()
```




    8



Hier fehlt wirklich nur der Ländercode. Wir suchen diesen heraus, und tragen in entsprechend der ISO 3166 ein.

https://de.wikipedia.org/wiki/ISO-3166-1-Kodierliste#N


```python
df.loc[df.loc[:, 'country'] == 'Namibia', 'country_code'] = 'NA'
```


```python
# Check ob die Wertzuweisung funktioniert hat
df.loc[df.loc[:, 'country'] == 'Namibia', 'country_code']
```




    310649    NA
    310650    NA
    310651    NA
    310652    NA
    310653    NA
    310654    NA
    310655    NA
    310656    NA
    Name: country_code, dtype: object



#### Fehlende Werte in Spalte 'use'


```python
null_values_use = df.loc[:, cols_with_nulls.index[0]].isnull()
df_use = df.loc[null_values_use == True]
df_use
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>funded_amount</th>
      <th>loan_amount</th>
      <th>activity</th>
      <th>sector</th>
      <th>use</th>
      <th>country_code</th>
      <th>country</th>
      <th>region</th>
      <th>currency</th>
      <th>term_in_months</th>
      <th>lender_count</th>
      <th>borrower_genders</th>
      <th>repayment_interval</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>100</th>
      <td>1925.0</td>
      <td>1925.0</td>
      <td>Agriculture</td>
      <td>Agriculture</td>
      <td>NaN</td>
      <td>AL</td>
      <td>Albania</td>
      <td>NaN</td>
      <td>ALL</td>
      <td>39.0</td>
      <td>57</td>
      <td>NaN</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>192</th>
      <td>975.0</td>
      <td>975.0</td>
      <td>Personal Housing Expenses</td>
      <td>Housing</td>
      <td>NaN</td>
      <td>AL</td>
      <td>Albania</td>
      <td>NaN</td>
      <td>ALL</td>
      <td>27.0</td>
      <td>38</td>
      <td>NaN</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>255</th>
      <td>200.0</td>
      <td>775.0</td>
      <td>Used Clothing</td>
      <td>Clothing</td>
      <td>NaN</td>
      <td>AL</td>
      <td>Albania</td>
      <td>NaN</td>
      <td>ALL</td>
      <td>19.0</td>
      <td>7</td>
      <td>NaN</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>312</th>
      <td>900.0</td>
      <td>900.0</td>
      <td>Decorations Sales</td>
      <td>Retail</td>
      <td>NaN</td>
      <td>AL</td>
      <td>Albania</td>
      <td>NaN</td>
      <td>ALL</td>
      <td>14.0</td>
      <td>31</td>
      <td>NaN</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>619</th>
      <td>1475.0</td>
      <td>1475.0</td>
      <td>Dairy</td>
      <td>Agriculture</td>
      <td>NaN</td>
      <td>AL</td>
      <td>Albania</td>
      <td>NaN</td>
      <td>ALL</td>
      <td>27.0</td>
      <td>55</td>
      <td>NaN</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>670842</th>
      <td>500.0</td>
      <td>500.0</td>
      <td>Poultry</td>
      <td>Agriculture</td>
      <td>NaN</td>
      <td>ZW</td>
      <td>Zimbabwe</td>
      <td>NaN</td>
      <td>USD</td>
      <td>24.0</td>
      <td>19</td>
      <td>NaN</td>
      <td>irregular</td>
    </tr>
    <tr>
      <th>670917</th>
      <td>2950.0</td>
      <td>2950.0</td>
      <td>Manufacturing</td>
      <td>Manufacturing</td>
      <td>NaN</td>
      <td>ZW</td>
      <td>Zimbabwe</td>
      <td>NaN</td>
      <td>USD</td>
      <td>15.0</td>
      <td>87</td>
      <td>NaN</td>
      <td>irregular</td>
    </tr>
    <tr>
      <th>670930</th>
      <td>800.0</td>
      <td>800.0</td>
      <td>Used Clothing</td>
      <td>Clothing</td>
      <td>NaN</td>
      <td>ZW</td>
      <td>Zimbabwe</td>
      <td>NaN</td>
      <td>USD</td>
      <td>14.0</td>
      <td>24</td>
      <td>NaN</td>
      <td>irregular</td>
    </tr>
    <tr>
      <th>671132</th>
      <td>500.0</td>
      <td>500.0</td>
      <td>General Store</td>
      <td>Retail</td>
      <td>NaN</td>
      <td>ZW</td>
      <td>Zimbabwe</td>
      <td>NaN</td>
      <td>USD</td>
      <td>8.0</td>
      <td>17</td>
      <td>NaN</td>
      <td>irregular</td>
    </tr>
    <tr>
      <th>671167</th>
      <td>1800.0</td>
      <td>1800.0</td>
      <td>Poultry</td>
      <td>Agriculture</td>
      <td>NaN</td>
      <td>ZW</td>
      <td>Zimbabwe</td>
      <td>NaN</td>
      <td>USD</td>
      <td>17.0</td>
      <td>59</td>
      <td>NaN</td>
      <td>monthly</td>
    </tr>
  </tbody>
</table>
<p>4232 rows × 13 columns</p>
</div>



Hier fällt auf, das wir wohl gleich mehrere Spalten mit fehlenden Werten haben. (_use_, _region_ und _genders_). Um eine bessere Bewertung für ein eventuelles Löschen der Daten zu erhalten, schauen wir uns die anderen Spalten etwas genauer an.


```python
df_use.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>funded_amount</th>
      <th>loan_amount</th>
      <th>term_in_months</th>
      <th>lender_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>4232.000000</td>
      <td>4232.000000</td>
      <td>4232.000000</td>
      <td>4232.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>921.908081</td>
      <td>1177.055766</td>
      <td>15.126181</td>
      <td>26.870983</td>
    </tr>
    <tr>
      <th>std</th>
      <td>1453.171843</td>
      <td>2319.892785</td>
      <td>8.912859</td>
      <td>40.064176</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
      <td>25.000000</td>
      <td>2.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>250.000000</td>
      <td>325.000000</td>
      <td>9.000000</td>
      <td>7.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>500.000000</td>
      <td>625.000000</td>
      <td>14.000000</td>
      <td>16.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>1050.000000</td>
      <td>1200.000000</td>
      <td>18.000000</td>
      <td>33.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>50000.000000</td>
      <td>50000.000000</td>
      <td>133.000000</td>
      <td>1310.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_use.describe().columns
```




    Index(['funded_amount', 'loan_amount', 'term_in_months', 'lender_count'], dtype='object')




```python
df_use.isnull().sum()
```




    funded_amount            0
    loan_amount              0
    activity                 0
    sector                   0
    use                   4232
    country_code             0
    country                  0
    region                4227
    currency                 0
    term_in_months           0
    lender_count             0
    borrower_genders      4221
    repayment_interval       0
    dtype: int64




```python
df_use.nunique()
```




    funded_amount         245
    loan_amount           238
    activity              139
    sector                 15
    use                     0
    country_code           69
    country                69
    region                  5
    currency               53
    term_in_months         55
    lender_count          192
    borrower_genders        5
    repayment_interval      3
    dtype: int64




```python
for col in df_use.describe().columns:
    sum_col = df.loc[df.loc[:, col] == 0, col].count()
    if sum_col > 0:
        print('Summe der fehlenden Werte in Spalte {col}: {sum}'.format(
            col=col, sum=sum_col))
        perc = sum_col / cols_with_nulls[0]
        print('Dies entspricht {perc:.1%} der bisher nicht finanzierten Projekte\n'.format(perc=perc))
```

    Summe der fehlenden Werte in Spalte funded_amount: 3383
    Dies entspricht 79.9% der bisher nicht finanzierten Projekte
    
    Summe der fehlenden Werte in Spalte lender_count: 3383
    Dies entspricht 79.9% der bisher nicht finanzierten Projekte
    
    

##### Zusammenfassung für die Spalte 'use'

Wir haben hier also einen großen Teil der Daten vor uns, welche wie bereits bei der Untersuchung der 0-Werte gefunden haben. Somit halten wir fest, das wir später nur die Projekte anschauen, welche bisher auch finanziert wurden. Wir notieren aber auch die Anzahl derer, die noch keinen Investor anziehen konnten.

Da wir später auch wissen wollen, wofür die Investoren ihr Geld hergeben, muss zumindest eine kurze Beschreibung des Nutzungszweckes angegeben sein. Daher löschen wir die Datensätze ohne zusätzliche Beschreibung. Als positiver Nebeneffekt entledigen wir uns auch der Daten, welche keine Geschlechterangabe beinhalten.


```python
not_yet_invested = n_rows_funded
missing_description = len(df_use.index)

overlaps = len(indicies_funded.intersection(df_use.index))
indicies_to_drop = indicies_funded.union(df_use.index)
perc = len(indicies_to_drop) / df.shape[0]

print('Projekte, in die noch nicht inverstiert wurde: {proj}'.format(proj=not_yet_invested))
print('Projekte mit fehlendem Verwendungszweck: {miss}'.format(miss=missing_description))
print('Anzahl der sich überschneidenden Datensätze: {over}\n'.format(over=overlaps))


print('Datensätze, welche gelöscht werden {to_drop}'.format(to_drop=len(indicies_to_drop)))
print('Dies entspricht einem Prozentsatz von {perc:.1%}'.format(perc=perc))
```

    Projekte, in die noch nicht inverstiert wurde: 3383
    Projekte mit fehlendem Verwendungszweck: 4232
    Anzahl der sich überschneidenden Datensätze: 131
    
    Datensätze, welche gelöscht werden 7484
    Dies entspricht einem Prozentsatz von 1.1%
    

Bevor wir diese Daten jedoch löschen, sichern wir uns diese als separaten Datensatz. Wir wollen später noch prüfen, warum zB Investoren ihr Geld in Projekte investieren, obwohl sie den eigentlichen Zweck (den 'use') nicht kennen. Ebenso verfahren wir mit den Projekten, welche keine Investoren anziehen konnten. Auch wenn es gerade einmal ca. 1% unserer bisher gesammelten Daten entspricht, wollen wir uns diese Daten anschauen, um besser beurteilen zu können, ob bestimmte Kategorien besser beworben werden können.


```python
# Sichern der Daten in einen separaten Datensatz
df_check = df.loc[indicies_to_drop]
```


```python
# löschen der Datensätze
df.drop(index=indicies_to_drop, inplace=True)
```


```python
# Check, ob das Löschen funktioniert hat
df.isnull().sum()
```




    funded_amount             0
    loan_amount               0
    activity                  0
    sector                    0
    use                       0
    country_code              0
    country                   0
    region                51621
    currency                  0
    term_in_months            0
    lender_count              0
    borrower_genders          0
    repayment_interval        0
    dtype: int64



#### Fehlende Werte in Spalte 'borrower_genders'

Die fehlenden Werte in dieser Spalte haben wir bereits im vorherigen Schritt mit eleminiert.

#### Fehlende Werte in Spalte 'region'

In dieser Spalte haben wir die meisten fehlenden Werte mit knapp 8,5% unserer Gesamtdatenmenge. Auch hier schauen wir wieder etwas genauer hin, um keine wichtigen Datensätze zu entfernen.

Generell wäre es ideal, dass wir eine möglichst feinkörnige Granularität der Projekte bezüglich ihres Standortes erhalten. Da uns eine Spalte mit Städten fehlt, wäre die Region zumindest das detailliertes, was wir zur Verfügung haben. Daher überprüfen wir, ob nicht wieder irgendwelche Zusammenhänge erkennbar sind.


```python
df_region = df.loc[df.loc[:, 'region'].isnull() == True]
df_region
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>funded_amount</th>
      <th>loan_amount</th>
      <th>activity</th>
      <th>sector</th>
      <th>use</th>
      <th>country_code</th>
      <th>country</th>
      <th>region</th>
      <th>currency</th>
      <th>term_in_months</th>
      <th>lender_count</th>
      <th>borrower_genders</th>
      <th>repayment_interval</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>9</th>
      <td>925.0</td>
      <td>925.0</td>
      <td>Livestock</td>
      <td>Agriculture</td>
      <td>to build a roof for her animal stable.</td>
      <td>AL</td>
      <td>Albania</td>
      <td>NaN</td>
      <td>ALL</td>
      <td>26.0</td>
      <td>33</td>
      <td>female</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>17</th>
      <td>1275.0</td>
      <td>2575.0</td>
      <td>Retail</td>
      <td>Retail</td>
      <td>to purchase drapes, sheets and blankets to sell.</td>
      <td>AL</td>
      <td>Albania</td>
      <td>NaN</td>
      <td>ALL</td>
      <td>38.0</td>
      <td>24</td>
      <td>male</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>21</th>
      <td>925.0</td>
      <td>925.0</td>
      <td>Personal Housing Expenses</td>
      <td>Housing</td>
      <td>To make minor repairs to their washroom and bu...</td>
      <td>AL</td>
      <td>Albania</td>
      <td>NaN</td>
      <td>ALL</td>
      <td>18.0</td>
      <td>31</td>
      <td>female</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>22</th>
      <td>900.0</td>
      <td>900.0</td>
      <td>Higher education costs</td>
      <td>Education</td>
      <td>to pay her son's university tuition fees.</td>
      <td>AL</td>
      <td>Albania</td>
      <td>NaN</td>
      <td>ALL</td>
      <td>22.0</td>
      <td>31</td>
      <td>female</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>24</th>
      <td>1450.0</td>
      <td>1450.0</td>
      <td>Personal Housing Expenses</td>
      <td>Housing</td>
      <td>to make home improvements by installing energy...</td>
      <td>AL</td>
      <td>Albania</td>
      <td>NaN</td>
      <td>ALL</td>
      <td>26.0</td>
      <td>53</td>
      <td>female</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>653218</th>
      <td>2400.0</td>
      <td>2400.0</td>
      <td>Services</td>
      <td>Services</td>
      <td>replace an outdated computer, retain a busines...</td>
      <td>US</td>
      <td>United States</td>
      <td>NaN</td>
      <td>USD</td>
      <td>12.0</td>
      <td>25</td>
      <td>female</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>653219</th>
      <td>5000.0</td>
      <td>5000.0</td>
      <td>Clothing</td>
      <td>Clothing</td>
      <td>produce 100 pieces of my master piece, The Per...</td>
      <td>US</td>
      <td>United States</td>
      <td>NaN</td>
      <td>USD</td>
      <td>24.0</td>
      <td>107</td>
      <td>female</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>653222</th>
      <td>4800.0</td>
      <td>4800.0</td>
      <td>Agriculture</td>
      <td>Agriculture</td>
      <td>complete projects in a timely manner without b...</td>
      <td>US</td>
      <td>United States</td>
      <td>NaN</td>
      <td>USD</td>
      <td>30.0</td>
      <td>70</td>
      <td>male</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>661822</th>
      <td>1350.0</td>
      <td>1350.0</td>
      <td>Restaurant</td>
      <td>Food</td>
      <td>to purchase materials to maintain her small re...</td>
      <td>VN</td>
      <td>Vietnam</td>
      <td>NaN</td>
      <td>VND</td>
      <td>14.0</td>
      <td>42</td>
      <td>female, female, female</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>670918</th>
      <td>1150.0</td>
      <td>1150.0</td>
      <td>Used Clothing</td>
      <td>Clothing</td>
      <td>to restock secondhand clothing for sale.</td>
      <td>ZW</td>
      <td>Zimbabwe</td>
      <td>NaN</td>
      <td>USD</td>
      <td>9.0</td>
      <td>37</td>
      <td>female, female, female, female</td>
      <td>irregular</td>
    </tr>
  </tbody>
</table>
<p>51621 rows × 13 columns</p>
</div>




```python
print('{perc:.1%} der noch verbliebenden Daten sind betroffen.'.format(perc=len(df_region)/len(df)))
```

    7.8% der noch verbliebenden Daten sind betroffen.
    


```python
df_region.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>funded_amount</th>
      <th>loan_amount</th>
      <th>term_in_months</th>
      <th>lender_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>51621.000000</td>
      <td>51621.000000</td>
      <td>51621.000000</td>
      <td>51621.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>1222.389144</td>
      <td>1374.877472</td>
      <td>13.438930</td>
      <td>30.085605</td>
    </tr>
    <tr>
      <th>std</th>
      <td>1857.113618</td>
      <td>1998.741679</td>
      <td>7.916555</td>
      <td>38.714436</td>
    </tr>
    <tr>
      <th>min</th>
      <td>5.000000</td>
      <td>25.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>300.000000</td>
      <td>325.000000</td>
      <td>8.000000</td>
      <td>10.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>525.000000</td>
      <td>600.000000</td>
      <td>14.000000</td>
      <td>17.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>1125.000000</td>
      <td>1375.000000</td>
      <td>16.000000</td>
      <td>34.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>50000.000000</td>
      <td>50000.000000</td>
      <td>120.000000</td>
      <td>1465.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_region.nunique()
```




    funded_amount           519
    loan_amount             402
    activity                153
    sector                   15
    use                   45726
    country_code             40
    country                  40
    region                    0
    currency                 29
    term_in_months           50
    lender_count            344
    borrower_genders       2445
    repayment_interval        4
    dtype: int64



Schauen wir uns mal die dazugehörigen Länder und Kategorien an.


```python
df_region['sector'].unique()
```




    array(['Agriculture', 'Retail', 'Housing', 'Education', 'Clothing',
           'Health', 'Services', 'Entertainment', 'Personal Use',
           'Transportation', 'Manufacturing', 'Food', 'Construction',
           'Wholesale', 'Arts'], dtype=object)




```python
df_region['country'].unique()
```




    array(['Albania', 'Armenia', 'Azerbaijan', 'Benin', 'Bolivia', 'Burundi',
           'Cameroon', 'Costa Rica', 'Ecuador', 'El Salvador', 'Guam',
           'Guatemala', 'Haiti', 'Honduras', 'Indonesia', 'Iraq', 'Israel',
           'Jordan', 'Kenya', 'Kosovo', 'Lebanon', 'Madagascar', 'Malawi',
           'Mali', 'Mexico', 'Nicaragua', 'Pakistan', 'Palestine', 'Peru',
           'Philippines', 'Puerto Rico', 'Rwanda', 'Senegal', 'South Africa',
           'Tajikistan', 'The Democratic Republic of the Congo', 'Turkey',
           'United States', 'Vietnam', 'Zimbabwe'], dtype=object)



Es befinden sich größtenteils kleinere Länder, dessen Regionen nicht angegeben wurden. Schauen wir ins das ganze mal im kompletten Datensatz an.


```python
df.groupby(by=['country', 'region']).agg(Angabe_Regionen=('use',
                                                'size')).sort_values(
                                                    ['Angabe_Regionen'],
                                                    ascending=False).head(10)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Angabe_Regionen</th>
    </tr>
    <tr>
      <th>country</th>
      <th>region</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Nigeria</th>
      <th>Kaduna</th>
      <td>9875</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">Pakistan</th>
      <th>Lahore</th>
      <td>7132</td>
    </tr>
    <tr>
      <th>Rawalpindi</th>
      <td>4455</td>
    </tr>
    <tr>
      <th>Peru</th>
      <th>Cusco</th>
      <td>3841</td>
    </tr>
    <tr>
      <th>Tanzania</th>
      <th>Dar es Salaam</th>
      <td>3718</td>
    </tr>
    <tr>
      <th>Kenya</th>
      <th>Kisii</th>
      <td>3545</td>
    </tr>
    <tr>
      <th rowspan="3" valign="top">Philippines</th>
      <th>Palo, Leyte</th>
      <td>3320</td>
    </tr>
    <tr>
      <th>Narra, Palawan</th>
      <td>3197</td>
    </tr>
    <tr>
      <th>Quezon, Palawan</th>
      <td>3120</td>
    </tr>
    <tr>
      <th>Kenya</th>
      <th>Kitale</th>
      <td>3072</td>
    </tr>
  </tbody>
</table>
</div>



Interessante Erkenntnis:
Nigeria und Pakistan sind die Länder, bei denen am häufigsten die Region mit angegeben wurde.

Nun schauen wir uns mal die Top 10 der Länder an, welche am meisten Regionen angegeben haben. Vielleicht generieren wir damit einen gwissen Mehrwert.


```python
top = 10

d_region = {}

name = f'top{top}_regions'

d_region[name] = df.groupby(by=['country']).agg(
    Anzahl_Regionen=('region',
                     'nunique')).sort_values(['Anzahl_Regionen'],
                                             ascending=False).head(top)

perc_regions = d_region[name].sum() / df['region'].nunique()

print(
    '{perc:.1%} der angegebenen Regionen sind auf {best_of} Länder verteilt.'
    .format(perc=perc_regions[0], best_of=10))

d_region[name]
```

    68.5% der angegebenen Regionen sind auf 10 Länder verteilt.
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Anzahl_Regionen</th>
    </tr>
    <tr>
      <th>country</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Philippines</th>
      <td>3632</td>
    </tr>
    <tr>
      <th>Mexico</th>
      <td>761</td>
    </tr>
    <tr>
      <th>Kyrgyzstan</th>
      <td>719</td>
    </tr>
    <tr>
      <th>Armenia</th>
      <td>666</td>
    </tr>
    <tr>
      <th>Mali</th>
      <td>627</td>
    </tr>
    <tr>
      <th>Peru</th>
      <td>577</td>
    </tr>
    <tr>
      <th>Georgia</th>
      <td>560</td>
    </tr>
    <tr>
      <th>Kenya</th>
      <td>393</td>
    </tr>
    <tr>
      <th>Vietnam</th>
      <td>382</td>
    </tr>
    <tr>
      <th>Cambodia</th>
      <td>369</td>
    </tr>
  </tbody>
</table>
</div>



##### Zusammenfassung für die Spalte 'region'
Da sich ca. 70% der angegebenen Regionen auf so einen kleinen Anteil der Länder verteilt und wir leider keine anderen nützlichen Daten wie die dazugehörigen Städte oder besser noch Koordinaten haben, ist diese Spalte für uns weniger wertvoll als anfangs gedacht. Daher wird diese Spalte aus dem Datensatz gelöscht.


```python
df.drop(columns='region', inplace=True)
```


```python
# check ob Löschen funktioniert hat.
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>funded_amount</th>
      <th>loan_amount</th>
      <th>activity</th>
      <th>sector</th>
      <th>use</th>
      <th>country_code</th>
      <th>country</th>
      <th>currency</th>
      <th>term_in_months</th>
      <th>lender_count</th>
      <th>borrower_genders</th>
      <th>repayment_interval</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>8000.0</td>
      <td>8000.0</td>
      <td>Textiles</td>
      <td>Arts</td>
      <td>To purchase raw materials, including fabrics, ...</td>
      <td>AF</td>
      <td>Afghanistan</td>
      <td>USD</td>
      <td>6.0</td>
      <td>308</td>
      <td>female</td>
      <td>bullet</td>
    </tr>
    <tr>
      <th>1</th>
      <td>6000.0</td>
      <td>6000.0</td>
      <td>Textiles</td>
      <td>Arts</td>
      <td>To buy 9 sewing machines that will increase pr...</td>
      <td>AF</td>
      <td>Afghanistan</td>
      <td>USD</td>
      <td>8.0</td>
      <td>204</td>
      <td>female</td>
      <td>bullet</td>
    </tr>
    <tr>
      <th>2</th>
      <td>825.0</td>
      <td>825.0</td>
      <td>Agriculture</td>
      <td>Agriculture</td>
      <td>to buy a dairy cow</td>
      <td>AL</td>
      <td>Albania</td>
      <td>ALL</td>
      <td>20.0</td>
      <td>26</td>
      <td>female</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2775.0</td>
      <td>2775.0</td>
      <td>Agriculture</td>
      <td>Agriculture</td>
      <td>to repair the damaged animal stable and also d...</td>
      <td>AL</td>
      <td>Albania</td>
      <td>ALL</td>
      <td>26.0</td>
      <td>101</td>
      <td>male</td>
      <td>monthly</td>
    </tr>
    <tr>
      <th>4</th>
      <td>825.0</td>
      <td>825.0</td>
      <td>Personal Housing Expenses</td>
      <td>Housing</td>
      <td>to repair windows and doors in her house.</td>
      <td>AL</td>
      <td>Albania</td>
      <td>ALL</td>
      <td>14.0</td>
      <td>32</td>
      <td>female</td>
      <td>monthly</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.isnull().sum()
```




    funded_amount         0
    loan_amount           0
    activity              0
    sector                0
    use                   0
    country_code          0
    country               0
    currency              0
    term_in_months        0
    lender_count          0
    borrower_genders      0
    repayment_interval    0
    dtype: int64



### Generierung von Zusatzinformationen

Nachfolgend wollen wir uns den Datensatz noch einmal genauer anschauen und prüfen, ob wir noch zusätzliche Informationen ('_Features_') generieren können. Zum einen bietet die Spalte **borrower_genders** Potenzial aber auch die Spalte der Länder. Hier können wir die Tabelle ebenfalls noch etwas präzisieren.

#### Aufbereitung der Spalte 'borrower_genders'
Die Spalte **borrower_genders** viel bereits schon früher auf. Obwohl es 'nur' eine Spalte für das Geschlecht war, hatten wir bei der statistischen Übersicht bereits festgestellt, das die Werteangabe nicht dem entspricht, was wir uns vorstellen. Daher überprüfen wir diese Spalte nun genauer.


```python
df.nunique()
```




    funded_amount            597
    loan_amount              476
    activity                 163
    sector                    15
    use                   422600
    country_code              86
    country                   86
    currency                  67
    term_in_months           148
    lender_count             498
    borrower_genders       11280
    repayment_interval         4
    dtype: int64




```python
n_genders = df['borrower_genders'].value_counts()
n_genders
```




    female                                                                                                            424653
    male                                                                                                              133563
    female, female                                                                                                     12118
    female, female, female                                                                                             11647
    female, female, female, female                                                                                      9018
                                                                                                                       ...  
    male, male, male, female, male, male, male, male, male, male, male, male, female, female, female                       1
    female, female, female, female, female, female, male, male, male, male, female, male, female, male, male, male         1
    male, female, male, male, female, male, female, female, male, female, female                                           1
    female, female, male, female, male, female, female, male, female, male, male, male, male, male, female, male           1
    female, female, female, female, male, male, male, male, male, male, male, female, male                                 1
    Name: borrower_genders, Length: 11280, dtype: int64



#### Bereinigen/Vorbereiten der Daten
Diese Auflistung scheint sowohl die Anzahl der Investoren als auch dessen jeweiliges Geschlecht zu repräsentieren.
Wir können also die Spalte in entsprechend viele Zusatzspalten aufteilen und die jeweilige Summe der Projektteilnehmer einfügen. Zuvor prüfen wir, wieviele verschiedene Geschlechterbezeichnungen sich in dem Datensatz befinden.


```python
# Wir entfernen erst alle Leerzeichen und teilen die Zeichenkette anschließend am Komma
borrower = df['borrower_genders'].str.replace(' ', '').str.split(',')
borrower
```




    0                                 [female]
    1                                 [female]
    2                                 [female]
    3                                   [male]
    4                                 [female]
                            ...               
    671200    [female, female, female, female]
    671201                            [female]
    671202                            [female]
    671203    [female, female, female, female]
    671204                            [female]
    Name: borrower_genders, Length: 663721, dtype: object



Nun ermitteln wir das eindeutige Geschlecht für jeden Datensatz und erzeugen uns hierfür jeweils eine neue Spalte, in denen wir schließlich die jeweilige Anzahl der Projektinitiatoren eintragen können.

https://towardsdatascience.com/dealing-with-list-values-in-pandas-dataframes-a177e534f173


```python
# Funktion, welche die Geschlechterspalte in ein eindimensionales Objekt umwandelt

def to_1D(series):
    return pd.Series([x for _list in series for x in _list])

#to_1D(borrower)
```


```python
unique_genders = to_1D(borrower).unique()
#unique_genders
```

Wir haben also nur zwei verschiedene Geschlechter (male + female). Somit können wir jeweile eine Spalte erzeugen und die gezählten Ergebnisse in diese eintragen.

#### Auszählen der Geschlechter je Zeile


```python
d_genders = {}

for gender in unique_genders:
    name = f'borrower_{gender}s'
    d_genders[name] = borrower.apply(lambda lst: (lst.count(gender)))
    d_genders[name].rename(name, inplace=True)
    
#d_genders
```


```python
d_genders.keys()
```




    dict_keys(['borrower_females', 'borrower_males'])




```python
to_concat = [df]

for key in d_genders.keys():
    to_concat.append(d_genders[key])
```

#### Zusammenfügen der Datensätze

Nun haben wir sowohl die Spalten, als auch deren Menge. Im nächsten Schritt verbinden wir die einzelnen Spalten mit unserem  ursprünglichen Datensatz und können dann die Projektteilnehmer zuordnen. Danach brauchen wir die Spalte **borrower_genders** nicht mehr und können diese löschen.


```python
df = pd.concat(to_concat, axis=1)
# Option 'copy=False' scheint nicht zu funktionieren
# daher wird über Wertzuweisung der alte Datensatz überschrieben
```


```python
# check, ob das hinzufügen funktioniert hat
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>funded_amount</th>
      <th>loan_amount</th>
      <th>activity</th>
      <th>sector</th>
      <th>use</th>
      <th>country_code</th>
      <th>country</th>
      <th>currency</th>
      <th>term_in_months</th>
      <th>lender_count</th>
      <th>borrower_genders</th>
      <th>repayment_interval</th>
      <th>borrower_females</th>
      <th>borrower_males</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>8000.0</td>
      <td>8000.0</td>
      <td>Textiles</td>
      <td>Arts</td>
      <td>To purchase raw materials, including fabrics, ...</td>
      <td>AF</td>
      <td>Afghanistan</td>
      <td>USD</td>
      <td>6.0</td>
      <td>308</td>
      <td>female</td>
      <td>bullet</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>6000.0</td>
      <td>6000.0</td>
      <td>Textiles</td>
      <td>Arts</td>
      <td>To buy 9 sewing machines that will increase pr...</td>
      <td>AF</td>
      <td>Afghanistan</td>
      <td>USD</td>
      <td>8.0</td>
      <td>204</td>
      <td>female</td>
      <td>bullet</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>825.0</td>
      <td>825.0</td>
      <td>Agriculture</td>
      <td>Agriculture</td>
      <td>to buy a dairy cow</td>
      <td>AL</td>
      <td>Albania</td>
      <td>ALL</td>
      <td>20.0</td>
      <td>26</td>
      <td>female</td>
      <td>monthly</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2775.0</td>
      <td>2775.0</td>
      <td>Agriculture</td>
      <td>Agriculture</td>
      <td>to repair the damaged animal stable and also d...</td>
      <td>AL</td>
      <td>Albania</td>
      <td>ALL</td>
      <td>26.0</td>
      <td>101</td>
      <td>male</td>
      <td>monthly</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>825.0</td>
      <td>825.0</td>
      <td>Personal Housing Expenses</td>
      <td>Housing</td>
      <td>to repair windows and doors in her house.</td>
      <td>AL</td>
      <td>Albania</td>
      <td>ALL</td>
      <td>14.0</td>
      <td>32</td>
      <td>female</td>
      <td>monthly</td>
      <td>1</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Nun zählen wir noch die Spalten aus und fügen die Summe am Ende der Tabelle wieder an.
df['sum_borrowers'] = df.loc[:, 'borrower_females':].sum(axis=1)
```


```python
df['sum_borrowers'].nunique()
```




    50




```python
df['sum_borrowers'].describe()
```




    count    663721.000000
    mean          2.021601
    std           3.419356
    min           1.000000
    25%           1.000000
    50%           1.000000
    75%           1.000000
    max          50.000000
    Name: sum_borrowers, dtype: float64



Diese Daten können wir auch gefahrlos kategorisieren. Wir teilen die Personen in Gruppen ein:
 - Einzelpersonen bei n= 1
 - kleine Gruppen bei  1 < n <= 5
 - mittlere Gruppen bei 5 < n <=10
 - große Gruppen n > 10 


```python
group_cat = [
    'Einzelperson', 'kleine Gruppe', 'mittlere Gruppe', 'große Gruppe'
]
group_class = [0, df['sum_borrowers'].min(), 5, 10, df['sum_borrowers'].max()]

df['borrower_groupsize'] = pd.cut(df['sum_borrowers'],
                                  bins=group_class,
                                  labels=group_cat)
df['borrower_groupsize']
```




    0          Einzelperson
    1          Einzelperson
    2          Einzelperson
    3          Einzelperson
    4          Einzelperson
                  ...      
    671200    kleine Gruppe
    671201     Einzelperson
    671202     Einzelperson
    671203    kleine Gruppe
    671204     Einzelperson
    Name: borrower_groupsize, Length: 663721, dtype: category
    Categories (4, object): ['Einzelperson' < 'kleine Gruppe' < 'mittlere Gruppe' < 'große Gruppe']



#### Löschen nicht benötigter Spalten


```python
df.drop(columns='borrower_genders', inplace=True)
```


```python
# Check, ob Löschen der Zeile geklappt hat
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>funded_amount</th>
      <th>loan_amount</th>
      <th>activity</th>
      <th>sector</th>
      <th>use</th>
      <th>country_code</th>
      <th>country</th>
      <th>currency</th>
      <th>term_in_months</th>
      <th>lender_count</th>
      <th>repayment_interval</th>
      <th>borrower_females</th>
      <th>borrower_males</th>
      <th>sum_borrowers</th>
      <th>borrower_groupsize</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>8000.0</td>
      <td>8000.0</td>
      <td>Textiles</td>
      <td>Arts</td>
      <td>To purchase raw materials, including fabrics, ...</td>
      <td>AF</td>
      <td>Afghanistan</td>
      <td>USD</td>
      <td>6.0</td>
      <td>308</td>
      <td>bullet</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>Einzelperson</td>
    </tr>
    <tr>
      <th>1</th>
      <td>6000.0</td>
      <td>6000.0</td>
      <td>Textiles</td>
      <td>Arts</td>
      <td>To buy 9 sewing machines that will increase pr...</td>
      <td>AF</td>
      <td>Afghanistan</td>
      <td>USD</td>
      <td>8.0</td>
      <td>204</td>
      <td>bullet</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>Einzelperson</td>
    </tr>
    <tr>
      <th>2</th>
      <td>825.0</td>
      <td>825.0</td>
      <td>Agriculture</td>
      <td>Agriculture</td>
      <td>to buy a dairy cow</td>
      <td>AL</td>
      <td>Albania</td>
      <td>ALL</td>
      <td>20.0</td>
      <td>26</td>
      <td>monthly</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>Einzelperson</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2775.0</td>
      <td>2775.0</td>
      <td>Agriculture</td>
      <td>Agriculture</td>
      <td>to repair the damaged animal stable and also d...</td>
      <td>AL</td>
      <td>Albania</td>
      <td>ALL</td>
      <td>26.0</td>
      <td>101</td>
      <td>monthly</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>Einzelperson</td>
    </tr>
    <tr>
      <th>4</th>
      <td>825.0</td>
      <td>825.0</td>
      <td>Personal Housing Expenses</td>
      <td>Housing</td>
      <td>to repair windows and doors in her house.</td>
      <td>AL</td>
      <td>Albania</td>
      <td>ALL</td>
      <td>14.0</td>
      <td>32</td>
      <td>monthly</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>Einzelperson</td>
    </tr>
  </tbody>
</table>
</div>



#### Aufbereitung der Spalte 'country_code'

Hinter dem Ländercode verbergen sehr viele zusätzliche, zum Teil wirklich hilfreiche informationen. Jedes Land folgende , für uns wichtige Informationen:
 - einen eigenen Schwerpunkt (siehe auch https://de.wikipedia.org/wiki/Mittelpunkte_Deutschlands)
 - eine Bevölkerungszahl
 - BIP
 - Währung (mit Umrechnugskurs zu einer Referenzwährung) --> https://pypi.org/project/CurrencyConverter/
 
Da unser Unternehmen seinen Firmensitz in Deutschland hat, genauer gesagt im wunderschönen Stralsund, ist unsere Referenzwährung der Euro.

#### Umwandlung der Ländercodes


```python
def get_continent(col):
    try:
        cn_a2_code =  country_name_to_country_alpha2(col)
    except:
        cn_a2_code = 'Unknown' 
    try:
        cn_continent = country_alpha2_to_continent_code(cn_a2_code)
    except:
        cn_continent = 'Unknown' 
    return cn_continent
```


```python
df['continent'] = df['country'].apply(get_continent)
```


```python
df['continent']
```




    0         AS
    1         AS
    2         EU
    3         EU
    4         EU
              ..
    671200    AF
    671201    AF
    671202    AF
    671203    AF
    671204    AF
    Name: continent, Length: 663721, dtype: object




```python
# Prüfung der Ergebnisse
df['continent'].unique()
```




    array(['AS', 'EU', 'NA', 'AF', 'SA', 'Unknown', 'OC'], dtype=object)



Wir habel also einige Länder dabei gehabt, die nicht von der Bibliothek abgedeck sind. Schauen wir uns diese einmal an.


```python
missing_continent = df.loc[df.loc[:, 'continent']=='Unknown', ['country', 'country_code', 'continent']]
```


```python
missing_continent.groupby(['country']).count()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>country_code</th>
      <th>continent</th>
    </tr>
    <tr>
      <th>country</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Cote D'Ivoire</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Kosovo</th>
      <td>1398</td>
      <td>1398</td>
    </tr>
    <tr>
      <th>Myanmar (Burma)</th>
      <td>1818</td>
      <td>1818</td>
    </tr>
    <tr>
      <th>The Democratic Republic of the Congo</th>
      <td>3067</td>
      <td>3067</td>
    </tr>
    <tr>
      <th>Timor-Leste</th>
      <td>2676</td>
      <td>2676</td>
    </tr>
  </tbody>
</table>
</div>




```python
missing_continent['country_code'].unique()
```




    array(['CI', 'XK', 'MM', 'CD', 'TL'], dtype=object)



Das sind zum glück nicht viele Länder, diese können wir händisch eintragen. Welche Kontinente wir setzen müssen, können wir hier entnehmen:

https://gist.github.com/alyssaq/3415363a18610d22f0d307bcaac857cd


```python
continents_to_set = dict.fromkeys(missing_continent['country'].unique())
```


```python
continents_to_set["Cote D'Ivoire"] = 'AF'
continents_to_set['Kosovo'] = 'EU'
continents_to_set['Myanmar (Burma)'] = 'AS'
continents_to_set['The Democratic Republic of the Congo'] = 'AF'
continents_to_set['Timor-Leste'] = 'AS'

continents_to_set
```




    {"Cote D'Ivoire": 'AF',
     'Kosovo': 'EU',
     'Myanmar (Burma)': 'AS',
     'The Democratic Republic of the Congo': 'AF',
     'Timor-Leste': 'AS'}



Nun weisen wir den jeweiligen Ländern ihre Kontinente zu:


```python
#test = df.loc[:, 'country'] == "Cote D'Ivoire"
#df.loc[test == True, ['continent']]
```


```python
continents_to_set.keys()
```




    dict_keys(["Cote D'Ivoire", 'Kosovo', 'Myanmar (Burma)', 'The Democratic Republic of the Congo', 'Timor-Leste'])




```python
for key in continents_to_set:
    #print(key)
    country_to_set = df.loc[:, 'country'] == key
    df.loc[country_to_set == True, ['continent']] = continents_to_set[key]
```


```python
#Check ob es funktioniert hat
df['continent'].unique()
```




    array(['AS', 'EU', 'NA', 'AF', 'SA', 'OC'], dtype=object)



Da wir später die Kontinete als deutsche Langversion haben wollen, mappen wir die Iso-Kurzbezeichnungen entsprechend und fügen diese als neue Spalte hinzu.


```python
continent_mapping = {
    'AS': 'Asien',
    'EU': 'Europa',
    'NA': 'Nordamerika',
    'AF': 'Afrika',
    'SA': 'Südamerika',
    'OC': 'Australien/Ozeanien'
}
```


```python
for key in continent_mapping:
    #print(key)
    country_to_set = df.loc[:, 'continent'] == key
    df.loc[country_to_set == True, ['continent_long']] = continent_mapping[key]
```


```python
df['continent_long'].unique()
```




    array(['Asien', 'Europa', 'Nordamerika', 'Afrika', 'Südamerika',
           'Australien/Ozeanien'], dtype=object)



#### Konvertierung der Währung


```python
# Test des Umrechnungskures Dollar --> Euro
c = CurrencyConverter()

print(c.convert(100, 'USD', 'EUR'))

# Standardwährung ist Euro
print(c.convert(100, 'USD'))
```

    94.62528387585164
    94.62528387585164
    

Mit diesem Wissen können wir nun den jeweiligen Umrechnungskurs berechnen. Damit die Daten sich nicht ändern, nehmen wir den Umrechnungskurs vom jeweils letztmöglichen Eintrag.


```python
first_date, last_date = c.bounds['USD']
first_date, last_date
```




    (datetime.date(1999, 1, 4), datetime.date(2022, 5, 5))




```python
c.convert(100, 'USD', date=last_date)
```




    94.62528387585164



Auch müssen wir Prüfen, ob wir in unserem Datensatz keine Währungen haben, die nicht unterstützt werden. Die unterstützen Währungen können direkt aufgerufen werden.


```python
c.currencies
```




    {'AUD',
     'BGN',
     'BRL',
     'CAD',
     'CHF',
     'CNY',
     'CYP',
     'CZK',
     'DKK',
     'EEK',
     'EUR',
     'GBP',
     'HKD',
     'HRK',
     'HUF',
     'IDR',
     'ILS',
     'INR',
     'ISK',
     'JPY',
     'KRW',
     'LTL',
     'LVL',
     'MTL',
     'MXN',
     'MYR',
     'NOK',
     'NZD',
     'PHP',
     'PLN',
     'ROL',
     'RON',
     'RUB',
     'SEK',
     'SGD',
     'SIT',
     'SKK',
     'THB',
     'TRL',
     'TRY',
     'USD',
     'ZAR'}




```python
# Check, ob unser Datensatz nicht unterstützte Währungen enthält
df['currency'].unique()
```




    array(['USD', 'ALL', 'AMD', 'AZN', 'BZD', 'XOF', 'BOB', 'BRL', 'BIF',
           'KHR', 'XAF', 'CLP', 'CNY', 'COP', 'CRC', 'DOP', 'EGP', 'GEL',
           'GHS', 'GTQ', 'HTG', 'HNL', 'INR', 'IDR', 'ILS', 'JOD', 'KES',
           'EUR', 'KGS', 'LAK', 'LBP', 'LSL', 'LRD', 'MGA', 'MWK', 'MXN',
           'MDL', 'MNT', 'MZN', 'MMK', 'NAD', 'NPR', 'NIO', 'NGN', 'PKR',
           'PYG', 'PEN', 'PHP', 'RWF', 'XCD', 'WST', 'SLL', 'SBD', 'ZAR',
           'SSP', 'SRD', 'TJS', 'TZS', 'THB', 'TRY', 'UGX', 'UAH', 'VUV',
           'VND', 'YER', 'ZMW', 'ZWD'], dtype=object)




```python
'USD' in c.currencies
```




    True




```python
unsupported_currencies = df['currency'].apply(lambda x: x not in c.currencies)
```


```python
df.loc[unsupported_currencies, 'country'].unique()
```




    array(['Albania', 'Armenia', 'Azerbaijan', 'Belize', 'Benin', 'Bolivia',
           'Burkina Faso', 'Burundi', 'Cambodia', 'Cameroon', 'Chile',
           'Colombia', 'Congo', 'Costa Rica', 'Dominican Republic', 'Egypt',
           'Georgia', 'Ghana', 'Guatemala', 'Haiti', 'Honduras', 'Jordan',
           'Kenya', 'Kyrgyzstan', "Lao People's Democratic Republic",
           'Lebanon', 'Lesotho', 'Liberia', 'Madagascar', 'Malawi', 'Mali',
           'Moldova', 'Mongolia', 'Mozambique', 'Myanmar (Burma)', 'Namibia',
           'Nepal', 'Nicaragua', 'Nigeria', 'Pakistan', 'Palestine',
           'Paraguay', 'Peru', 'Rwanda', 'Saint Vincent and the Grenadines',
           'Samoa', 'Senegal', 'Sierra Leone', 'Solomon Islands',
           'South Sudan', 'Suriname', 'Tajikistan', 'Tanzania', 'Togo',
           'Uganda', 'Ukraine', 'Vanuatu', 'Vietnam', 'Yemen', 'Zambia',
           'Zimbabwe'], dtype=object)



##### Hinweis in eigener Sache:

Ein nachträglicher Blick in die Aufgabenstellung ergab, das der Kredit nur in die jeweilige Landeswährung ausgezahlt wurde. Gesammelt wurde das Geld in US-Dollar. Somit können wir, da unser Firmensitz weiterhin in Deutschland ist, die Währungen in Euro umwandeln. Der Einfachheithalber wird die Summe auf ganze Euro's gerundet.


```python
df['funded_amount_euro'] = df['funded_amount'].apply(
    lambda money_to_convert: round(c.convert(money_to_convert, 'USD', date=last_date), 0))
```


```python
df['loan_amount_euro'] = df['loan_amount'].apply(
    lambda money_to_convert: round(c.convert(money_to_convert, 'USD', date=last_date), 0))
```

#### Prozentualer Anteil der finanzierten Projekte

Später wollen wir wissen, wieviele Projekte komplett finanziert wurden und wie hoch der anteil derer ist, die es nicht ganz geschafft haben und wie groß der Fehlbetrag in Prozent ist. Daher erstellen wir eine neue Zusatzspalte, in der wir nach folgender Formel den Anteil errechnen:

\begin{equation}
perc_{recieved} = {\frac {funded_{amount}}{loan_{amount}}}
\end{equation}


```python
df['perc_recieved'] = round(df['funded_amount'] / df['loan_amount'] * 100,0)
```


```python
# Check ob es funktioniert hat
df['perc_recieved']
```




    0         100.0
    1         100.0
    2         100.0
    3         100.0
    4         100.0
              ...  
    671200    100.0
    671201    100.0
    671202    100.0
    671203    100.0
    671204    100.0
    Name: perc_recieved, Length: 663721, dtype: float64




```python
df['perc_recieved'].unique()
```




    array([100.,  60.,  50.,  31.,  86.,  91.,  52.,  37.,  67.,  35.,  47.,
            54.,  71.,   6.,  74.,  68.,  93.,  72.,  53.,  16.,  27.,  95.,
            41.,  63.,  15.,  36.,  21.,  87.,  28.,  79.,  82.,  19.,  45.,
            13.,  18.,  57.,  92.,  39.,  80.,  34.,  69.,  66.,   2.,  70.,
            73.,  64.,  29.,  51.,  89.,  65.,  38.,  75.,  83.,  59.,   5.,
            56.,  43.,  78.,  81.,  76.,  42.,  33.,  90.,  58.,  94.,  88.,
            48.,  30.,  85.,  40.,  17.,  49.,  24.,  26.,  55.,  32.,  84.,
            25.,  61.,  77.,  22.,  14.,  62.,  98.,   9.,  46.,  44.,  20.,
            11.,  12.,   7.,   4.,   3.,  96.,  10.,   1.,  23.,   8.,  97.,
           113.,  99.,   0., 106.])




```python
min_recieved = df['perc_recieved'].min()
max_recieved = df['perc_recieved'].max()

print('Maximalwert: {:3}%'.format(max_recieved))
print('Minimalwert: {:3}%'.format(min_recieved))
```

    Maximalwert: 113.0%
    Minimalwert: 0.0%
    

Interessante Erkenntnis. Es gibt also Projekte, in die mehr investiert wurde, als geplant. Dies schauen wir uns in der EDA genauer an. Wir werden die Projekte noch zusätzlich Kategorisieren.

Wir teilen die Werte in 20% Schritte bis 100% ein. Für alle über 100% gilt die Kategorie 'Überfinanziert'


```python
categories = [
    '<= 5%', '10%', '20%', '30%', '40%', '50%', '60%', '70%', '80%', '90%',
    '100%', 'Überfinanziert'
]
classification = [0, 5, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100, max_recieved]

df['funding_status'] = pd.cut(df['perc_recieved'],
                              bins=classification,
                              labels=categories)
df['funding_status']
```




    0         100%
    1         100%
    2         100%
    3         100%
    4         100%
              ... 
    671200    100%
    671201    100%
    671202    100%
    671203    100%
    671204    100%
    Name: funding_status, Length: 663721, dtype: category
    Categories (12, object): ['<= 5%' < '10%' < '20%' < '30%' ... '80%' < '90%' < '100%' < 'Überfinanziert']



### Optimierung des Speicherverbrauchs

Wir haben bereits am Anfang den Speicherplatz unsere Daten optimiert. Wir wollen aber trotzdem noch einmal schauen, ob es Optimierungsmöglichkeiten gibt. einen Anhaltspunkt, wie wir die Datentypen weiter optimieren können finden wir auf diesen Seiten:

https://pythonspeed.com/articles/pandas-load-less-data/
https://jakevdp.github.io/PythonDataScienceHandbook/02.01-understanding-data-types.html

Zuvor werden wir nur noch die Spalten neu anordnen, damit sie thematisch besser zusammenpassen. Auch übernehmen wir die Spalte 


```python
df.columns
```




    Index(['funded_amount', 'loan_amount', 'activity', 'sector', 'use',
           'country_code', 'country', 'currency', 'term_in_months', 'lender_count',
           'repayment_interval', 'borrower_females', 'borrower_males',
           'sum_borrowers', 'borrower_groupsize', 'continent', 'continent_long',
           'funded_amount_euro', 'loan_amount_euro', 'perc_recieved',
           'funding_status'],
          dtype='object')




```python
df_final = df[[
    'funded_amount_euro', 'loan_amount_euro', 'perc_recieved', 'funding_status', 'activity',
    'sector', 'use', 'country_code', 'continent', 'continent_long', 'country', 'currency', 'term_in_months',
    'lender_count', 'repayment_interval', 'borrower_females', 'borrower_males',
    'sum_borrowers', 'borrower_groupsize'
]]
```

#### Check ob Datentypen passen


```python
df_final.dtypes
```




    funded_amount_euro     float64
    loan_amount_euro       float64
    perc_recieved          float64
    funding_status        category
    activity                object
    sector                  object
    use                     object
    country_code            object
    continent               object
    continent_long          object
    country                 object
    currency                object
    term_in_months         float64
    lender_count             int64
    repayment_interval      object
    borrower_females         int64
    borrower_males           int64
    sum_borrowers            int64
    borrower_groupsize    category
    dtype: object



Eine erste Prüfung des Datensatzes zeigt, das wir die Spalten **borrower_genders** und **repayment_interval** möglicherweise kategorisieren können. Hierzu müssen wir aber noch die benutzen Einträge prüfen. Des weiteren wird geprüft, ob wir die Spalten **funded_amount**, **loan_amount** und **term_in_months** in Ganzzahlen umwandeln können. Falls nicht, ist dies ein Hinweis für eventuell fehlende Werte. Desweiteren können wir auf diese Weise schon eine Speicherplatzoptimierung erreichen.


```python
mem_opti_pre = my_memory_usage(df_final)
mem_opti_pre # verwendeter Speicherplatz nach Bearbeitung des Datensatzes
```

    genutzer Speicherplatz [MiB]:
    




    445.1




```python
df_final.info(verbose=True, memory_usage='deep')
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 663721 entries, 0 to 671204
    Data columns (total 19 columns):
     #   Column              Non-Null Count   Dtype   
    ---  ------              --------------   -----   
     0   funded_amount_euro  663721 non-null  float64 
     1   loan_amount_euro    663721 non-null  float64 
     2   perc_recieved       663721 non-null  float64 
     3   funding_status      663655 non-null  category
     4   activity            663721 non-null  object  
     5   sector              663721 non-null  object  
     6   use                 663721 non-null  object  
     7   country_code        663721 non-null  object  
     8   continent           663721 non-null  object  
     9   continent_long      663721 non-null  object  
     10  country             663721 non-null  object  
     11  currency            663721 non-null  object  
     12  term_in_months      663721 non-null  float64 
     13  lender_count        663721 non-null  int64   
     14  repayment_interval  663721 non-null  object  
     15  borrower_females    663721 non-null  int64   
     16  borrower_males      663721 non-null  int64   
     17  sum_borrowers       663721 non-null  int64   
     18  borrower_groupsize  663721 non-null  category
    dtypes: category(2), float64(4), int64(4), object(9)
    memory usage: 445.1 MB
    


```python
cols_to_check = []

unique_limit = 25

for col in df_final.columns:
    n_uniques = df_final[col].nunique()
    if n_uniques < unique_limit:
        print(Fore.GREEN +
              'In Spalte {col} befinden sich {n} eindeutige Werte\n'.format(
                  col=col, n=n_uniques))
        cols_to_check.append(col)
    else:
        print(Fore.RED +
              'Die Spalte {col} beinhaltet mehr als {n} eindeutige Werte\n'.
              format(col=col, n=unique_limit))
```

    [31mDie Spalte funded_amount_euro beinhaltet mehr als 25 eindeutige Werte
    
    [31mDie Spalte loan_amount_euro beinhaltet mehr als 25 eindeutige Werte
    
    [31mDie Spalte perc_recieved beinhaltet mehr als 25 eindeutige Werte
    
    [32mIn Spalte funding_status befinden sich 12 eindeutige Werte
    
    [31mDie Spalte activity beinhaltet mehr als 25 eindeutige Werte
    
    [32mIn Spalte sector befinden sich 15 eindeutige Werte
    
    [31mDie Spalte use beinhaltet mehr als 25 eindeutige Werte
    
    [31mDie Spalte country_code beinhaltet mehr als 25 eindeutige Werte
    
    [32mIn Spalte continent befinden sich 6 eindeutige Werte
    
    [32mIn Spalte continent_long befinden sich 6 eindeutige Werte
    
    [31mDie Spalte country beinhaltet mehr als 25 eindeutige Werte
    
    [31mDie Spalte currency beinhaltet mehr als 25 eindeutige Werte
    
    [31mDie Spalte term_in_months beinhaltet mehr als 25 eindeutige Werte
    
    [31mDie Spalte lender_count beinhaltet mehr als 25 eindeutige Werte
    
    [32mIn Spalte repayment_interval befinden sich 4 eindeutige Werte
    
    [31mDie Spalte borrower_females beinhaltet mehr als 25 eindeutige Werte
    
    [31mDie Spalte borrower_males beinhaltet mehr als 25 eindeutige Werte
    
    [31mDie Spalte sum_borrowers beinhaltet mehr als 25 eindeutige Werte
    
    [32mIn Spalte borrower_groupsize befinden sich 4 eindeutige Werte
    
    

Schauen wir uns die Ergebnisse für **repayment_interval** und **sector** einmal genauer an. Die Spalte **perc_recieved** haben wir schon zuvor kategorisiert.


```python
cols_to_check
```




    ['funding_status',
     'sector',
     'continent',
     'continent_long',
     'repayment_interval',
     'borrower_groupsize']



Wir können auch gefahrlos die Länder oder deren Ländercode kategorisieren. Da wir bei der Speicherplatzeinsparung über die Ländernamen prozentual mehr optimieren können, wählen wir noch diese Spalte zusätzlich aus.


```python
cols_to_check.append('country')
```


```python
for col in cols_to_check:
    print(col + ':')
    print(df_final[col].unique())
    print()
```

    funding_status:
    ['100%', '60%', '50%', '40%', '90%', ..., '20%', '30%', '<= 5%', 'Überfinanziert', NaN]
    Length: 13
    Categories (12, object): ['<= 5%' < '10%' < '20%' < '30%' ... '80%' < '90%' < '100%' < 'Überfinanziert']
    
    sector:
    ['Arts' 'Agriculture' 'Housing' 'Health' 'Construction' 'Food' 'Retail'
     'Services' 'Education' 'Transportation' 'Clothing' 'Personal Use'
     'Manufacturing' 'Entertainment' 'Wholesale']
    
    continent:
    ['AS' 'EU' 'NA' 'AF' 'SA' 'OC']
    
    continent_long:
    ['Asien' 'Europa' 'Nordamerika' 'Afrika' 'Südamerika'
     'Australien/Ozeanien']
    
    repayment_interval:
    ['bullet' 'monthly' 'irregular' 'weekly']
    
    borrower_groupsize:
    ['Einzelperson', 'kleine Gruppe', 'mittlere Gruppe', 'große Gruppe']
    Categories (4, object): ['Einzelperson' < 'kleine Gruppe' < 'mittlere Gruppe' < 'große Gruppe']
    
    country:
    ['Afghanistan' 'Albania' 'Armenia' 'Azerbaijan' 'Belize' 'Benin' 'Bhutan'
     'Bolivia' 'Brazil' 'Burkina Faso' 'Burundi' 'Cambodia' 'Cameroon' 'Chile'
     'China' 'Colombia' 'Congo' 'Costa Rica' "Cote D'Ivoire"
     'Dominican Republic' 'Ecuador' 'Egypt' 'El Salvador' 'Georgia' 'Ghana'
     'Guam' 'Guatemala' 'Haiti' 'Honduras' 'India' 'Indonesia' 'Iraq' 'Israel'
     'Jordan' 'Kenya' 'Kosovo' 'Kyrgyzstan' "Lao People's Democratic Republic"
     'Lebanon' 'Lesotho' 'Liberia' 'Madagascar' 'Malawi' 'Mali' 'Mauritania'
     'Mexico' 'Moldova' 'Mongolia' 'Mozambique' 'Myanmar (Burma)' 'Namibia'
     'Nepal' 'Nicaragua' 'Nigeria' 'Pakistan' 'Palestine' 'Panama' 'Paraguay'
     'Peru' 'Philippines' 'Puerto Rico' 'Rwanda'
     'Saint Vincent and the Grenadines' 'Samoa' 'Senegal' 'Sierra Leone'
     'Solomon Islands' 'Somalia' 'South Africa' 'South Sudan' 'Suriname'
     'Tajikistan' 'Tanzania' 'Thailand' 'The Democratic Republic of the Congo'
     'Timor-Leste' 'Togo' 'Turkey' 'Uganda' 'Ukraine' 'United States'
     'Vanuatu' 'Vietnam' 'Yemen' 'Zambia' 'Zimbabwe']
    
    

#### Daten kategorisieren

In diesen Spalten können wir die Daten gefahrlos kategorisieren, da die Daten keinerlei fehlende Werte enthalten und im Vergleich zur Anzahl der Spalten nur sehr wenige Werte annehmen können.


```python
for col in cols_to_check:
    if df_final[col].dtype.name == 'category':
        print('Spalte "{col}" hat bereits diesen Datentyp und wird nicht umgewandelt.\n'.format(col=col))
    else:
        df_final.loc[:, col] = df_final.loc[:, col].astype('category')
        #df_final.loc[:, col] = pd.Categorical(df_final[col])
        #df_final[col] = pd.Categorical(df_final[col])
        print('Spalte "{col}" kategorisiert.\n'.format(col=col))
```

    Spalte "funding_status" hat bereits diesen Datentyp und wird nicht umgewandelt.
    
    Spalte "sector" kategorisiert.
    
    Spalte "continent" kategorisiert.
    
    Spalte "continent_long" kategorisiert.
    
    Spalte "repayment_interval" kategorisiert.
    
    Spalte "borrower_groupsize" hat bereits diesen Datentyp und wird nicht umgewandelt.
    
    Spalte "country" kategorisiert.
    
    


```python
mem_opti_cat = my_memory_usage(df_final)
mem_opti_cat # verwendeter Speicherplatz nach Kategorisierung
```

    genutzer Speicherplatz [MiB]:
    




    244.4




```python
df_final.info(verbose=True, memory_usage='deep')
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 663721 entries, 0 to 671204
    Data columns (total 19 columns):
     #   Column              Non-Null Count   Dtype   
    ---  ------              --------------   -----   
     0   funded_amount_euro  663721 non-null  float64 
     1   loan_amount_euro    663721 non-null  float64 
     2   perc_recieved       663721 non-null  float64 
     3   funding_status      663655 non-null  category
     4   activity            663721 non-null  object  
     5   sector              663721 non-null  category
     6   use                 663721 non-null  object  
     7   country_code        663721 non-null  object  
     8   continent           663721 non-null  category
     9   continent_long      663721 non-null  category
     10  country             663721 non-null  category
     11  currency            663721 non-null  object  
     12  term_in_months      663721 non-null  float64 
     13  lender_count        663721 non-null  int64   
     14  repayment_interval  663721 non-null  category
     15  borrower_females    663721 non-null  int64   
     16  borrower_males      663721 non-null  int64   
     17  sum_borrowers       663721 non-null  int64   
     18  borrower_groupsize  663721 non-null  category
    dtypes: category(7), float64(4), int64(4), object(4)
    memory usage: 244.4 MB
    

#### Daten in Ganzzahlen umwandeln

Nun schauen wir uns die Datensätze an, welche Fließkommazahlen beinhalten. Durch einen einfache Check können wir herausfinden, ob wir nur unnötige Präzision mitschleppen. Wir können hier auch gefahrlos auf INT32 konvertieren, da unsere Ganzzahlwerte deutlich kleiner als 2.147.483.647 sind. Somit lässt sich auch hier eine Speicherplatzoptimierung durchführen.

Wir berechnen den Restwert der jeweiligen Zahl bei einer Division durch 1. Ist dieser Wert 0, können wir diese Zahl in eine Ganzzahl umwandeln (siehe Beispiel)


```python
print(str(251.5 % 1) + ' --> Keine Umwandlung in Ganzzahl möglich!')
print(str(251.0 % 1) + ' --> Umwandlung in Ganzzahl möglich')
```

    0.5 --> Keine Umwandlung in Ganzzahl möglich!
    0.0 --> Umwandlung in Ganzzahl möglich
    


```python
df_final.dtypes
```




    funded_amount_euro     float64
    loan_amount_euro       float64
    perc_recieved          float64
    funding_status        category
    activity                object
    sector                category
    use                     object
    country_code            object
    continent             category
    continent_long        category
    country               category
    currency                object
    term_in_months         float64
    lender_count             int64
    repayment_interval    category
    borrower_females         int64
    borrower_males           int64
    sum_borrowers            int64
    borrower_groupsize    category
    dtype: object




```python
# Selektieren der Spalten mit Fließkommazahlen
#cols_with_floats = df_final.dtypes.loc[df_final.dtypes.isin(['float', 'float64']) == True]
cols_with_floats = df_final.select_dtypes(include=['float', 'float64'])
cols_with_floats.columns
```




    Index(['funded_amount_euro', 'loan_amount_euro', 'perc_recieved',
           'term_in_months'],
          dtype='object')




```python
cols_with_floats.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>funded_amount_euro</th>
      <th>loan_amount_euro</th>
      <th>perc_recieved</th>
      <th>term_in_months</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7570.0</td>
      <td>7570.0</td>
      <td>100.0</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>5678.0</td>
      <td>5678.0</td>
      <td>100.0</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>781.0</td>
      <td>781.0</td>
      <td>100.0</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2626.0</td>
      <td>2626.0</td>
      <td>100.0</td>
      <td>26.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>781.0</td>
      <td>781.0</td>
      <td>100.0</td>
      <td>14.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
convert_to_int = []
nulls = []

for col in cols_with_floats.columns:
    check_zeros = df_final[col] % 1 == 0
    check_nan = df_final[col] % 1 != np.nan
    print('Spalte ' + col + ':')
    if df_final.loc[check_zeros == False].index.empty:
        print('Daten lassen sich in Ganzzahlen konvertieren\n')
        convert_to_int.append(col)
        continue
    if df_final.loc[check_nan == True].empty:
        print('NULL-Werte gefunden!')
        nulls.append(col)
        continue
    else:
        print('Daten können nicht konvertiert werden\n')
        continue
```

    Spalte funded_amount_euro:
    Daten lassen sich in Ganzzahlen konvertieren
    
    Spalte loan_amount_euro:
    Daten lassen sich in Ganzzahlen konvertieren
    
    Spalte perc_recieved:
    Daten lassen sich in Ganzzahlen konvertieren
    
    Spalte term_in_months:
    Daten lassen sich in Ganzzahlen konvertieren
    
    


```python
for col in convert_to_int:
    df_final.loc[:, col] = df_final.loc[:, col].astype(np.int32, copy=False)
    print('Spalte umgewandelt: {col}'.format(col=col))
```

    Spalte umgewandelt: funded_amount_euro
    Spalte umgewandelt: loan_amount_euro
    Spalte umgewandelt: perc_recieved
    Spalte umgewandelt: term_in_months
    


```python
mem_opti_float2int = my_memory_usage(df_final)
mem_opti_float2int # verwendeter Speicherplatz nach Umwandlung von Fließkommazahlemn in Ganzzahlen
```

    genutzer Speicherplatz [MiB]:
    




    234.3




```python
df_final.info(verbose=True, memory_usage='deep')
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 663721 entries, 0 to 671204
    Data columns (total 19 columns):
     #   Column              Non-Null Count   Dtype   
    ---  ------              --------------   -----   
     0   funded_amount_euro  663721 non-null  int32   
     1   loan_amount_euro    663721 non-null  int32   
     2   perc_recieved       663721 non-null  int32   
     3   funding_status      663655 non-null  category
     4   activity            663721 non-null  object  
     5   sector              663721 non-null  category
     6   use                 663721 non-null  object  
     7   country_code        663721 non-null  object  
     8   continent           663721 non-null  category
     9   continent_long      663721 non-null  category
     10  country             663721 non-null  category
     11  currency            663721 non-null  object  
     12  term_in_months      663721 non-null  int32   
     13  lender_count        663721 non-null  int64   
     14  repayment_interval  663721 non-null  category
     15  borrower_females    663721 non-null  int64   
     16  borrower_males      663721 non-null  int64   
     17  sum_borrowers       663721 non-null  int64   
     18  borrower_groupsize  663721 non-null  category
    dtypes: category(7), int32(4), int64(4), object(4)
    memory usage: 234.3 MB
    


```python
# Check, ob die Umwandlung funktioniert hat
df_final.dtypes
```




    funded_amount_euro       int32
    loan_amount_euro         int32
    perc_recieved            int32
    funding_status        category
    activity                object
    sector                category
    use                     object
    country_code            object
    continent             category
    continent_long        category
    country               category
    currency                object
    term_in_months           int32
    lender_count             int64
    repayment_interval    category
    borrower_females         int64
    borrower_males           int64
    sum_borrowers            int64
    borrower_groupsize    category
    dtype: object



#### Verkleinern der Ganzzahlen in passendere Datentypen


```python
cols_with_ints = df_final.select_dtypes(include=['int32', 'int64'])
cols_with_ints.dtypes
```




    funded_amount_euro    int32
    loan_amount_euro      int32
    perc_recieved         int32
    term_in_months        int32
    lender_count          int64
    borrower_females      int64
    borrower_males        int64
    sum_borrowers         int64
    dtype: object




```python
for col in cols_with_ints:
    print('{value:>10} --> Maximalwert in Spalte {col}'.format(col=col, value=df[col].max()))
```

       94625.0 --> Maximalwert in Spalte funded_amount_euro
       94625.0 --> Maximalwert in Spalte loan_amount_euro
         113.0 --> Maximalwert in Spalte perc_recieved
         158.0 --> Maximalwert in Spalte term_in_months
          2986 --> Maximalwert in Spalte lender_count
            50 --> Maximalwert in Spalte borrower_females
            44 --> Maximalwert in Spalte borrower_males
            50 --> Maximalwert in Spalte sum_borrowers
    

Wir haben hier also die Möglichkeit, in den letzten vier Spalten den Datentyp nochmals zu verringern.
Hierbei gilt zu beachten, das es zwei verschiedene Arten von Ganzzahlen gibt. Die Zahlen, die nur positiv sein können (_unsigned_) und auch negativ (_signed_), im deutschen auch als Vorzeichen bezeichnet..


Folgende Maximalgrößen sind, laut oben verlinkter Seiten, für die Datentypen zulässig:

| int_8bit | int_16bit |int_32bit | int_64bit |
| :---: | :---: | :---: | :---: |
| [$-2^7$, $2^7-1$] | [$-2^{15}$, $2^{15}-1$] | [$-2^{31}$, $2^{31}-1$] |[$-2^{63}$, $2^{63}-1$] |


| uint_8bit | uint_16bit |uint_32bit | uint_64bit |
| :---: | :---: | :---: | :---: |
| $(2^{7}-1)$ | $(2^{15}-1)$ | $(2^{31}-1)$ |$(2^{63}-1)$ |


```python
int_sizes = {}

for i in [1, 2, 4, 8]:
    name = 'uint{}'.format(8 * i)
    #print(name)
    int_sizes[name] = {'max_size': 2*2**(8 * i - 1) - 1, 'columns': []}

int_sizes
```




    {'uint8': {'max_size': 255, 'columns': []},
     'uint16': {'max_size': 65535, 'columns': []},
     'uint32': {'max_size': 4294967295, 'columns': []},
     'uint64': {'max_size': 18446744073709551615, 'columns': []}}




```python
for col in cols_with_ints.columns:
    for key in int_sizes.keys():
        #print(key)
        max_value = int_sizes[key]['max_size']
        if df_final[col].max() <= max_value:
            print(f'Spalte {col}:')
            print(f'vorhandener Maximalwert: {df_final[col].max():,}')
            print(
                f'Der ideale Datentyp für diese Spalte ist {key}.\nerlaubter Maximalwert = {max_value:,} bytes\n'
            )
            df_final.loc[:, col] = df_final.loc[:, col].astype(key, copy=False)
            break
```

    Spalte funded_amount_euro:
    vorhandener Maximalwert: 94,625
    Der ideale Datentyp für diese Spalte ist uint32.
    erlaubter Maximalwert = 4,294,967,295 bytes
    
    Spalte loan_amount_euro:
    vorhandener Maximalwert: 94,625
    Der ideale Datentyp für diese Spalte ist uint32.
    erlaubter Maximalwert = 4,294,967,295 bytes
    
    Spalte perc_recieved:
    vorhandener Maximalwert: 113
    Der ideale Datentyp für diese Spalte ist uint8.
    erlaubter Maximalwert = 255 bytes
    
    Spalte term_in_months:
    vorhandener Maximalwert: 158
    Der ideale Datentyp für diese Spalte ist uint8.
    erlaubter Maximalwert = 255 bytes
    
    Spalte lender_count:
    vorhandener Maximalwert: 2,986
    Der ideale Datentyp für diese Spalte ist uint16.
    erlaubter Maximalwert = 65,535 bytes
    
    Spalte borrower_females:
    vorhandener Maximalwert: 50
    Der ideale Datentyp für diese Spalte ist uint8.
    erlaubter Maximalwert = 255 bytes
    
    Spalte borrower_males:
    vorhandener Maximalwert: 44
    Der ideale Datentyp für diese Spalte ist uint8.
    erlaubter Maximalwert = 255 bytes
    
    Spalte sum_borrowers:
    vorhandener Maximalwert: 50
    Der ideale Datentyp für diese Spalte ist uint8.
    erlaubter Maximalwert = 255 bytes
    
    


```python
# Check ob umwandlung funktioniert hat
df_final.dtypes
```




    funded_amount_euro      uint32
    loan_amount_euro        uint32
    perc_recieved            uint8
    funding_status        category
    activity                object
    sector                category
    use                     object
    country_code            object
    continent             category
    continent_long        category
    country               category
    currency                object
    term_in_months           uint8
    lender_count            uint16
    repayment_interval    category
    borrower_females         uint8
    borrower_males           uint8
    sum_borrowers            uint8
    borrower_groupsize    category
    dtype: object




```python
mem_opti_int = my_memory_usage(df_final)
mem_opti_int # verwendeter Speicherplatz nach Optimierung der Ganzzahlspalten
```

    genutzer Speicherplatz [MiB]:
    




    213.4




```python
df_final.info(verbose=True, memory_usage='deep')
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 663721 entries, 0 to 671204
    Data columns (total 19 columns):
     #   Column              Non-Null Count   Dtype   
    ---  ------              --------------   -----   
     0   funded_amount_euro  663721 non-null  uint32  
     1   loan_amount_euro    663721 non-null  uint32  
     2   perc_recieved       663721 non-null  uint8   
     3   funding_status      663655 non-null  category
     4   activity            663721 non-null  object  
     5   sector              663721 non-null  category
     6   use                 663721 non-null  object  
     7   country_code        663721 non-null  object  
     8   continent           663721 non-null  category
     9   continent_long      663721 non-null  category
     10  country             663721 non-null  category
     11  currency            663721 non-null  object  
     12  term_in_months      663721 non-null  uint8   
     13  lender_count        663721 non-null  uint16  
     14  repayment_interval  663721 non-null  category
     15  borrower_females    663721 non-null  uint8   
     16  borrower_males      663721 non-null  uint8   
     17  sum_borrowers       663721 non-null  uint8   
     18  borrower_groupsize  663721 non-null  category
    dtypes: category(7), object(4), uint16(1), uint32(2), uint8(5)
    memory usage: 213.4 MB
    

#### Zusammenfassung


```python
saved_memory1 = 1 - mem_opti_int / mem_usage_init
saved_memory2 = round(mem_usage_init - mem_opti_int, 1)

print('''
Trotz der zusätzlichen Spalten, die wir im Laufe des Preprocessings eingefügt haben,
konnten wir mit den durchgeführten Maßnahmen den Speicherplatzverbrauch um ca. {mem:.0%} reduzieren.

Dies entspricht in absoluten Zahlen etwa {mem_saved} MiB.
      '''.format(mem=saved_memory1, mem_saved=saved_memory2))
```

    
    Trotz der zusätzlichen Spalten, die wir im Laufe des Preprocessings eingefügt haben,
    konnten wir mit den durchgeführten Maßnahmen den Speicherplatzverbrauch um ca. 50% reduzieren.
    
    Dies entspricht in absoluten Zahlen etwa 216.5 MiB.
          
    

### Sichern der Datensätze

Diesen Stand sichern wir uns nun als 'pickle'-Datei (wecken unseren Datensatz also ein und machen ihn haltbar :D ). Da dieses Dateiformat versionsabhängig ist, wird der Datensatz zusätzlich noch als CSV-Datei gespeichert.


```python
filename = 'SH_CrowdInvesting_preprocessing'

df_final.to_pickle(f'{filename}.pkl')
df_final.to_csv(f'{filename}.csv', index=False, sep=',')

df_check.to_pickle('dataset_to_check.pkl')
df_check.to_csv('dataset_to_check.csv', index=False, sep=',')
```

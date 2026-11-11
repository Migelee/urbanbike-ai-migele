# Raporti i studentit: UrbanBike AI

Studenti: Migele Bishka
Grupi (nëse punohet në grup):
Data: 2026-11-11
GitHub-i im:
Figma:

## Qëllimi

Problemi që zgjidhim: Sa qira biçikletash priten për ditën pasuese
në Washington, D.C.?
Target-i y: rentals
Predictorët X: rentals_lag1, rentals_lag7, rentals_mean7, temp_c_lag7,
rain_mm_lag7, wind_ms_lag7, trend_days, weekday, month, workingday, holiday
Koha kur parashikojmë dhe çfarë informacioni është i njohur: Parashikojmë
për ditën pasuese. Janë të njohura qirat e ditëve të mëparshme (lag1, lag7,
mesatarja 7-ditore), moti i vonuar dhe kalendari. Të dhënat janë historike,
2011–2012.

## Pastrimi

Burimet reale dhe çelësi i bashkimit: UCI Bike Sharing (day.csv, 731 rreshta)
dhe NASA POWER (T2M, PRECTOTCORR, WS2M, 731 rreshta). Bashkimi bëhet sipas
datës dhe jep 731 rreshta.
Dallimi mes origjinaleve dhe kopjes didaktike: Kopja didaktike ka 743 rreshta,
me 12 dublime të plota dhe 30 qeliza bosh. Origjinalet kanë 0 mungesa.
Rreshtat para/pas: 743 para, 731 pas pastrimit. 724 rreshta hyjnë në model,
pasi lag7 humbet 7 ditët e para.
Dublimet: 12 dublime të plota, të hequra.
Mungesat para/pas konvertimit: 30 qeliza bosh para. Pas konvertimit të
tipeve (temp_c nga object në float64) mbeten 36 vlera moti që mungojnë.
Arsyeja e imputimit vetëm me train: Mediana mësohet vetëm nga train brenda
Pipeline. Kështu validation dhe test nuk ndikojnë në trajnim dhe nuk ka
rrjedhje informacioni (data leakage).

## Grafikët

Vëzhgimi 1 dhe figura përkatëse: Kërkesa mesatare ishte 3446 qira/ditë në 2011
dhe 5600 në 2012, pra trend rritës i qartë (reports/figures/01_demand.png).
Vëzhgimi 2 dhe figura përkatëse: Temperatura dhe qiratë kanë korrelacion
rreth 0.61, ndërsa dita e punës ndryshon pak mesataren: 4616 kundrejt 4360
(reports/figures/02_workingday.png dhe 03_weather.png).
Pse një lidhje e dukshme nuk provon shkakësi: Temperatura dhe qiratë rriten
bashkë edhe për shkak të stinës dhe trendit afatgjatë. Korrelacioni tregon
shoqërim, jo që temperatura i shkakton qiratë.

## Vlerësimi

| Modeli | Validation MAE | Test MAE | Test RMSE | Test R² |
|---|---:|---:|---:|---:|
| Yesterday | 775.2 | 916.4 | 1330.4 | 0.539 |
| DummyMean | 2953.9 | 2494.6 | 2790.1 | -1.027 |
| Ridge | 687.6 | 893.3 | 1252.7 | 0.591 |
| RandomForest | 1821.5 | 1671.5 | 2042.7 | -0.087 |

Modeli i zgjedhur nga validation dhe arsyeja: Ridge, sepse ka MAE-n më të
vogël në validation (687.6). Testi nuk përdoret për zgjedhje.
Rezultati i provës me Random Forest: Test MAE 1671.5 dhe R² = -0.087, më keq
se Ridge dhe se baza "Yesterday". Random Forest nuk ekstrapolon trendin rritës
dhe e nënvlerëson kërkesën në periudhat e reja.
Interpretimi i gabimit në qira për ditë: Ridge gabon mesatarisht rreth 893
qira në ditë në test. Kërkesa mesatare në 2012 është rreth 5600, pra gabimi
është rreth 16%. Është vetëm pak më mirë se thjesht "e njëjta si dje" (916).
Rezultati i klasifikimit, nëse u realizua: Me pragun 3907 qira, Logistic
Regression arrin accuracy 0.918, balanced accuracy 0.780 dhe macro-F1 0.820.
Baza DummyMostFrequent ka accuracy 0.844, por balanced accuracy 0.5. Matrica e
konfuzionit: [[11, 8], [2, 101]].

## Kufizimet

1. Të dhënat janë historike (2011–2012) dhe vetëm për Washington, D.C. Modeli
   nuk vlen pa kontroll për qytete ose vite të tjera.
2. Test set ka vetëm 122 ditë. Përmirësimi i Ridge ndaj bazës "Yesterday" është
   i vogël dhe mund të mos jetë i qëndrueshëm.
3. Parashikimi mbështetet te qirat e ditëve të mëparshme dhe te moti i
   vonuar. Nuk përdor parashikim moti, ngjarje ose pushime të veçanta.

## Kontributet individuale

| Studenti | Skedari/ekrani | Puna dhe vendimi | Commit/prova |
|---|---|---|---|
| Migele Bishka | 01_clean.py, 02_visualize.py | Pastrimi dhe grafikët | Ekzekutuar me run_all.py |
| Migele Bishka | 03_train.py, 04_classify.py | Trajnimi dhe klasifikimi | Ekzekutuar me run_all.py |
| Migele Bishka | app.py | Aplikacioni lokal, provuar me datën 2012-09-01 | Ridge: 6834.7 parashikuar, 6140 reale |

## Burimet

UCI: https://doi.org/10.24432/C5W894
NASA: https://power.larc.nasa.gov/docs/services/api/temporal/daily/
Provenienca lokale: data/raw/provenance.json

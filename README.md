# Previsione_Pioggia_ML
Modello di rete neurale per la previsione della pioggia in diverse regioni dell’Australia, usando dati meteorologici storici. Il task è una classificazione binaria, in cui la variabile RainTomorrow assume 0 (non piove) o 1 (piove).
Il dataset originale, ottenuto dal Bureau of Meteorology australiano (via Kaggle), conteneva 99.516 osservazioni giornaliere da oltre 10 anni di registrazioni.
Il preprocessing è stato effettuato in un notebook separato (vedi PreparingAustraliaWeatherDataset), e include:
- rimozione osservazioni incomplete,
- normalizzazione numerica (MinMax),
- label encoding per le variabili categoriche,
- esportazione di un dataset pulito pronto per il training.
Il dataset finale contiene 39.574 righe e 21 feature predittive più la target.

## Background e letteratura
Recenti studi su dataset meteorologici australiani [1], [2] hanno evidenziato come le reti neurali artificiali (ANN, RNN) forniscano risultati migliori rispetto a metodi tradizionali (KNN, DT, RF) per la previsione della pioggia. In particolare, [2] mostra che combinazioni di ANN e Random Forest offrono prestazioni superiori su dati mensili australiani. Inoltre, altri lavori su modelli ibridi (MLP, ConvLSTM, CNN-LSTM) dimostrano l’efficacia delle reti profonde nella previsione meteorologica su scala globale [3]–[5].

## Importazione dei dati e analisi preliminare
Dataset originale:
Questo dataset meteorologico australiano è stato scaricato dal repository Kaggle:  [Rain in Australia on Kaggle](https://www.kaggle.com/datasets/jsphyg/weather-dataset-rattle-package). Contiene circa 10 anni di osservazioni meteorologiche giornaliere provenienti da numerose stazioni meteorologiche in tutta l'Australia. Le osservazioni giornaliere sono disponibili anche su: [Bureau of Meteorology - Australia](http://www.bom.gov.au/climate/dwo/IDCJDW0000.shtml)
[Copyright: Commonwealth of Australia 2010, Bureau of Meteorology. Licenza del dataset: CC0 – Pubblico Dominio.]

Nel notebook di preprocessing è stata eseguita una prima analisi delle correlazioni, che ha evidenziato una correlazione positiva tra Humidity3pm e RainTomorrow (+0.45), e negativa con Sunshine (-0.45).
Per quanto riguarda la distribuzione delle classi della variabile target (RainTomorrow), è risultata sbilanciata, con la classe “No Rain” molto più frequente della classe “Rain”. La variabile target presenta uno sbilanciamento (23% di casi positivi). Nonostante l’assenza di tecniche di riequilibrio, il modello ha raggiunto buone prestazioni generali, anche se con una minore sensibilità verso la classe minoritaria.

## Iperparametri: giustificazione della scelta
### Numero di epoche
È stato scelto un valore elevato (150) per garantire che il modello abbia abbastanza tempo per apprendere correttamente. Tuttavia, viene monitorato il rischio di overfitting: se l’accuratezza sul training aumenta mentre quella sul validation peggiora, interviene il meccanismo di EarlyStopping per fermare l’addestramento e ripristinare i pesi migliori.

### Tasso di apprendimento (Learning Rate)
Un learning rate basso (0.001) consente al modello di apprendere in modo stabile, evitando oscillazioni eccessive nella funzione di perdita. Un valore troppo alto potrebbe impedire la convergenza, mentre uno troppo basso renderebbe l’apprendimento troppo lento.

### Dimensione del batch (Batch Size)
È stato scelto un batch size intermedio (64) per bilanciare precisione e velocità di addestramento. Batch troppo piccoli possono introdurre rumore nel processo di ottimizzazione, mentre batch troppo grandi possono richiedere molta memoria e ridurre la capacità del modello di generalizzare.

## Architettura della rete
Il modello finale selezionato ha la seguente architettura:
- Input: 21 feature
- Hidden Layers: 512 → 256 → 128 → 64 → 32 → 8
- Output: 1 neurone (sigmoid)
- Attivazione: ReLU per i layer nascosti, sigmoide per l’output.
- Funzione di perdita: Binary Crossentropy
- Ottimizzatore: Adam (confrontato con altri)
- Callbacks: EarlyStopping e ReduceLROnPlateau
- Batch size: 64 — Learning rate: 0.001 — Epochs: 150

La scelta dell’architettura è stata giustificata con test empirici su bias e varianza, scegliendo il miglior compromesso tra accuratezza e generalizzazione.

## Conclusioni
In questo progetto è stata sviluppata una rete neurale profonda per la previsione della pioggia del giorno successivo in Australia, utilizzando un dataset meteorologico reale.
Dopo una fase di preprocessing condotta separatamente (normalizzazione, encoding, gestione dei missing values), è stato progettato e ottimizzato un modello con 6 layer nascosti, regolarizzato tramite dropout e batch normalization, e addestrato con l’ottimizzatore Adam.
Durante la fase sperimentale sono state testate diverse configurazioni di iperparametri e tecniche di ottimizzazione. Il modello finale ha raggiunto:

-Accuracy su test finale: 0.86
-Bias stimato: 8.0%
-Varianza stimata: 0.62%
-F1-score test: 0.64

Sebbene altri modelli come RMS-3 e Adam-2 abbiano fornito buoni risultati, Adam-3 si è distinto per avere: il bias più basso (8,03%), una varianza ben controllata (0,62%), una maggiore capacità di apprendimento grazie a un’architettura di rete profonda e stabilità durante l’addestramento, senza segni di possibile overfitting. Inoltre, i test con regolarizzazione L1/L2 hanno mostrato che, sebbene riducessero la varianza, aumentavano il bias. Per questo motivo, è stato scelto Adam-3 come modello finale, senza regolarizzazione L1/L2.
Il modello ha dimostrato buona capacità di generalizzazione, evitando overfitting e mantenendo performance stabili sia su training che test set.

## Bibliografia
[1] M. Sarasa‑Cabezuelo, J. A. Ortega and M. L. Sein-Echaluce, “Prediction of Rainfall in Australia Using Machine Learning” International Journal of Advanced Computer Science and Applications (IJACSA), vol. 13, no. 4, pp. 627–635, 2022.

[2] M. Farman, N. A. Bakar, and I. O. Basir, “A Comparative Study of Machine Learning and Deep Learning Models for Rainfall Prediction in Australian Cities” International Journal of Advanced Computer Science and Applications (IJACSA), vol. 16, no. 4, pp. 735–741, 2025.

[3] A. Abbot, T. Nguyen, and L. K. Thomas, “Hybrid Neural Network Models for Monthly Rainfall Prediction: A Case Study in Queensland, Australia” Expert Systems with Applications, vol. 220, p. 119846, 2023.

[4] A. Khan, S. Verma, and R. Gupta, “Weather Prediction Using CNN-LSTM for Time Series Analysis: A Case Study on Delhi Temperature Data” Procedia Computer Science, vol. 218, pp. 290–298, 2023.

[5] P. N. Soman, B. G. Raj and V. Diwan, “Rainfall Prediction Using Deep Learning Techniques: A Review” Information, vol. 13, no. 4, p. 163, 2022.

## NB: come eseguire
I notebook principali sono:
- `PreparingAustraliaWeatherDataset_.ipynb` – preprocessing e analisi dei dati
  [Notebook di preprocessing](PreparingAustraliaWeatherDataset_.ipynb)
- `Previsione_Pioggia_ML.ipynb` – addestramento e valutazione del modello
  [Notebook di training](Previsione_Pioggia_ML.ipynb)

Si possono eseguire su Google Colab oppure localmente con Python 3.8+.


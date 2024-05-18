# TuyaUIweb: Tuya WEBAPP User Interface #

Dopo aver progettato il mese scorso un'interfaccia WEB per [TuyaDAEMON](https://github.com/msillano/tuyaDEAMON-applications/tree/main/daemon.visUI.widget), poi ho elaborato anche una versione per TuyaCloud: _TuyaUIweb_. 
Questa webapp presenta tutti  i device Tuya, stanze e case, in una disposizione ad 'albero' dinamico, all'interno di un browser. Ogni nodo ha un popup che presenta i valori aggiornati delle proprietà del device. Per sicurezza, agisce solo in lettura: non altera i dati in Tuya Cloud in nessun modo.

### Prestazioni:

_In un solo colpo d'occhio si ha la situazione completa sotto controllo._

In fase iniziale tutti i dati necessari sono letti da Tuya Cloud e viene costruita una struttura locale contenente TUTTE le informazioni. Il grafo ad albero di visualizzazione è costruito in base a queste informazioni.<br>
Tutti i nodi (root, case, stanze e  device) sono rappresentati da icone (customizzabili), grigie quandi il device è disconnesso, Il colore del link indica il tipo di device: se blu è WiFi, se rosso usa un HUB (subdevice).

L'aggiornamento dei dati  avviene in polling: sono letti dal Cloud con frequenza regolabile dall'utente, da 30s a alcuni minuti, e i popup (customizzabili) sono aggiornati subito.<BR>
Se necessario è possibile ricaricare ed aggiornare tutta la struttura, e.g. in caso di aggiunte di nuovi device.

![](https://github.com/msillano/TuyaUIweb/blob/main/pics/UIlook01.png?raw=true)

### Logging:
E' possibile esportare su un file alcuni dati: l'utente deve specificare `home`, `device` e `status` (proprietà) per identificare i dati che interessano e questi sono salvati ad intervalli regolari (minimo 1 minuto) in un buffer interno, esportato poi su file automaticamente o su comando utente.<br>
L'utente può scegliere tra due formati: `CVS` (indicato, per esempio, per spreadsheet tipo Excel) oppure `JSON` (per elaborazioni più complesse con programmi ad hoc) con pochissimi interventi di editing sui file.

E' anche possibile su comando avere nella console l'intera struttura dati ottenuta da Tuya Cloud: può essere esplorata a ogni livello nel log della console oppure può essere copiata con cut&paste in formato JSON.


### Note di implementazione

- La scelta della libreria di visualizzazione è caduta su [Vis-Network](https://visjs.github.io/vis-network/docs/network/) per la buona flessibilità unita a semplicità di uso.
- Un primo problema è il protocollo di sicurezza CORS, implementato sui moderni browser. Una applicazione (anche in js, node-red, etc)  non ha questo problema, ma una APP che gira in un browser sì. E' necessario disabilitare CORS al memento del lancio del browser (vedi file goTuyaUI.bat). Vale solo per questa istanza, le altre resteranno protette.  
- I file sono salvati nella dir `download`, con il nome fisso `tuyalog.cvs|json`, _assicurarsi che il S.O. non sovrascriva i file con lo stesso nome!_
- Tuya pone dei limiti alla frequenza degli accessi al cloud. _TuyaUIweb_ ne tiene conto, e la fase iniziale (quando legge tutti i dati dal Cloud) è bloccante e non brevissima. Come anche in SmartLife.
- Per ovviare all'impossibilità di creare file direttamente da una pagina HTML, sempre per motivi di sicurezza, per l'export dei dati sono ricorso ad una libreria di logging su file (debugout.js)[https://github.com/inorganik/debugout.js]. Per questo motivo il controllo sui file generati non è completo e sono necessari piccoli  interventi manuali.
- Il funzionamento continua regolarmente anche con la finestra del browser iconizzata.
<hr>

**NOTA sulla sicurezza**

_**Questa APP è totalmente aperta, priva di ogni protezione, e contiene nei file le vostre credenziali in chiaro!**_ <br>
_NON rendetela accessibile dall'esterno o da terzi, altrimenti tutti i vostri dati, credenziali Tuya incluse, sono esposti!_

### Installazione
1) Scaricare e dezippare il file `TuyaUIweb.1.x.zip`  in una dir (con le autorizzazioni richieste dal S.O.).
2) Il file principale è `tuyaui.html`.  NON è necessario un server WEB, in quanto il codice è tutto in javaScript, eseguito dal browser. Per lanciarlo vedi file `goTuyaUI.bat` (per Windows). Per altri S.O. creare uno script analogo.
3) Eseguire le operazioni di configurazione
4) In fase di installazione e setup è utile la console (nel browser - strumenti per programmatori -, o menu  'ispeziona elemento') perchè lì vanno i messaggi di informazione e di errore di TuyaUIWEB.

### Configurazione
Questa APP è per utenti non alle prime armi, pertanto è accettabile che la configurazione avvenga direttamente editando un file. _Le solite avvertenze: fare una copia del file prima di ogni modifica, usare un editor UTF8 (io uso Notepad-plusplus), e attenzione a NON ALTERARE niente altro (soprattutto virgole  ','  ed  apici '"')._

 - I dati INDISPENSABILI da inserire sono le proprie `credenziali Tuya` (dovreste già averle, ma per i nuovi utenti ci sono molte guide nel web. [Questa](https://github.com/iRayanKhan/homebridge-tuya/wiki/Get-Local-Keys-for-your-devices) è una delle più chiare).
 - Altre opzioni riguardano: timing (Cloud e log) e configurazione del log: il formato, l'autosave, i valori richiesti, oppure il look&feel, come la presenza dei bottoni di pan/zoom,
- Aggiornare con i path del sistema ospite il file di lancio `goTuyaUI.bat`.

## Customizzazioni

Il programma è OpenSorce, in HTML+Javascript, è abbastanza documentato e modulare. Quindi è possibile ogni intervento. 
Due aree sono state privilegiate e poste per semplicità in un file separato con dettagliate istruzioni ed esempi:

 - _Tuya non permette più di cambiare le icone, per una opinabile  interpretazione dei suoi consulenti legali delle attuali leggi sul copyright._<br> 
In questa APP ho scelto le icone `awesome4`, con un'[ampissima scelta](https://fontawesome.com/v4/cheatsheet/) e  di libero uso. Per personalizzarle, l'utente deve fornire un criterio di selezione e l'indicazione dell'icona da usare.<br> 
Per default, come esempio, hanno icone speciali: i Temometri (device con nome 'Temp...'), le Valvole termostatiche (device con nome 'Termo...') ed i Gateway (device con 'Gateway' nel nome) . .

 - La personalizzazione del contenuto dei tooltip, che varia a seconda del device. Alcuni valori sono criptati: si può scegliere di non farli vedere, in altri casi occorre dividere per 10 o 100 per avere il valore in unità SI, etc.. Se si desidera si possono aggiungere nuove informazioni per esempio derivandole da quelle del device (e.g. temperatura in °C e anche in °F).<br>
_Nota per gli utenti di TuyaDEAMON e HUB similari come HA: può essere molto utile inserire nel tooltip di ogni device anche il `device_id` (device.id) e `secret_key` (device.local_key)._

Queste customizzazioni NON sono necessarie, ma redono più semplice e gradevole l'uso della APP.

LOGGING - ESEMPI



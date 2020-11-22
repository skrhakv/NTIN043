# Card Payment Terminal


Řešení obsahuje tři soubory: rterminal.maude, bank.maude a test.maude.

### Terminal
Modul TERMINAL obsahuje funkce pro vykonání platby čipovou i bezkontaktní kartou pomocí zavolání příslušné funkce:

```maude
--- 1. Platba bezkontaktní kartou
op payContactlessCard : Card Terminal Rat Date -> Terminal .

--- 2. Platba čipovou kartou (parametry: CKarta, terminál, částka, PIN, dnešní datum)
op payChipCard : ChipCard Terminal Rat Rat Date -> Terminal .

```

Každá funkce postupně zpracovává poskytnutá data. Funkce vrátí hodnotu `terminal( accept )`, pokud jsou všechna data validní (tj. platná karta, dostatečný zůstatek, atd.). 


### Bank
Modul BANK obsahuje funkce pro obsluhu jednotlivých účtů. Především poskytuje funkcionalitu pro transakce mezi jednotlivými účty, přičítání/odečítání zůstatku z jednotlivých účtů, platbu pomocí terminálu, či kontrolu, zda je účet bankou blokován.
Modul Bank obsahuje několik datových struktur:
1. `op bank : Accounts Accounts -> Bank .` - sort Bank je složen ze seznamu všech účtů a seznamu blokovaných účtů,
2. `op account : Card AccountID -> Account .` - Account je složen z karty a ID,

Hlavní funkce: 

```maude
--- 1. Platba čipovou kartou z daného účtu (parametry: Banka, ID účtu, částka, PIN a dnešní datum)
op chipPayment : Bank AccountID Rat Rat Date -> Bank .
    

--- 2. Platba bezkontaktní kartou z daného účtu (parametry: Banka, ID účtu, částka, PIN a dnešní datum)
op contactlessPayment : Bank AccountID Rat Date -> Bank .
```
Obě funkce vyhledají v bakovní databázi účet dle zadaného ID. Dále zkontrolují, zda účet není blokován. Na závěr zavolají příslušnou funkci z přechozího modulu Terminal. Pokud tato funkce vrátí `terminal (accept) `, pak se aktualizuje daný účet v databázi. V opačném případě je navrácena původní bankovní databáze beze změn.

Modul obsahuje také vedlejší funkce pro operaci s účty či výpis informací o účtu (`op getAccountInfo : Account -> AccountInfo .`).

### Test
Modul TEST obsahuje funkce a konstanty pro testování předchozích modulů. Pro testování je třeba spustit maude na na jeho příkazovém řádku načíst modul TEST tímto příkazem `load test.module`. V modulu jsou předpřipravené následující testy, které lze v terminálu Maude spustit:

```maude
rew in TEST : dummyChipPayment .
rew in TEST : dummyContactlessPayment .
rew in TEST : dummyMoneyTransfer .
rew in TEST : dummyGetInfo .
rew in TEST : dummyExpirationTerminalReject .
rew in TEST : dummyWrongPinTerminalReject .
rew in TEST : dummyLowBalanceTerminalReject .
rew in TEST : dummyWrongCardTypeTerminalReject .
rew in TEST : dummyMoneyTransferReject .
rew in TEST : dummyContactlessPaymentReject .
```

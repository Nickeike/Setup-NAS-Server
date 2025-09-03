# Oppsett av TrueNAS Server

 ### installer [**TrueNAS Core ISO fil**](https://www.truenas.com/download-truenas-core/)

 #### Du treng ikkje å lage konto hos dei. Bla litt ned til du ser: "No, thank you, I have already signed up."

Eg starta med å installere **ISO-filen til TrueNAS Core** på ein minnepenn ved hjelp av [**Balena Etcher**](https://etcher.balena.io/). Deretter sette eg minnepennen i ein PC/laptop som skulle brukast som server.

Eg måtte då gå inn i **BIOS** på PC-en, sjekke at **Secure Boot** var **av**, flytte **USB-enheten** til første prioritet, lagre endringane og avslutte BIOS.

Deretter dukka **TrueNAS GUI** opp, og installasjonen kunne starte.

## Installering

**NB! Mus/plate kjem ikkje til å fungere under denne prosessen, så du må bruke tastaturet.**

### Viktige tastar du må bruke
- **Piltaster**: Brukast for å navigere opp, ned og til sides i GUI-menyen.
- **Enter**: Knappen for å gå vidare til neste steg.
- **Space**: Brukast for å velje alternativ i menyen, t.d. boot-disk/device.
- **Ethernet-kabel**: Det er lurt å ha ein kabel kopla til routeren eller ein port på PC-en (serveren) for å sikre nettverkstilkobling.

### Installasjonsprosess
1. Vel **Install/Upgrade**.
2. Vel **destination media** (helst den disken med minst lagringsplass – heile disken blir brukt).
3. Trykk **Yes** (**NB!** Dette vil slette alt data på lagringsenheten du har valt).
4. **Lag root-passord** (brukarnamnet er **root**).
5. Vel **boot mode** (TrueNAS kan bruke både **BIOS** eller **UEFI**. Vel **UEFI** dersom enheten er moderne, og **BIOS** dersom den er eldre).
6. Restart PC-en (serveren) og kopla ut installeringsenheten (USB med ISO-fil).

### Etter installasjon
Om du har ein **Ethernet-kabel** kopla til, skal systemet automatisk få ei **IP-adresse** og bli tilgjengeleg via ein nettlesar (**Google Chrome, Brave eller Edge**). **IP-adressa** står på skjermen til serveren din.

Når du opnar **TrueNAS GUI**, skriv du inn **brukarnamn** (**root**) og **passordet** du oppretta under installasjonen.

---

## Oppgradering av TrueNAS

Det kan vere lurt å oppgradere systemet til den nyaste versjonen (**Fangtooth**). 

**NB!** Du kan ikkje oppgradere direkte frå **Core** til **Fangtooth**, fordi Core og Scale har forskjellige operativsystem:
- **TrueNAS Core** er basert på **FreeBSD**.
- **TrueNAS Scale** er basert på **GNU/Linux**.

### Oppgraderingsprosess:
1. **Oppgrader til Cobia 23.10**.
2. **Deretter oppgrader til Fangtooth 25.04**.

Når dette er gjort, skal alt fungere som forventa.

---

## Lagringsoppsett og konfigurasjon

### **Konvertering av Gibibytes til Gigabytes**
| Gibibytes (GiB) | Gigabytes (GB) |
| -------------- | -------------- |
| **1 GiB** | 1.07 GB |
| **250 GiB** | 268.43 GB |
| **500 GiB** | 536.87 GB |
| **1000 GiB** | 1073.74 GB |

🔗 [Gibibytes til Gigabytes-kalkulator](https://www.gbmb.org/gib-to-gb)

Når du opnar **Datasets** for første gong, må du **lage ein Pool**. 

### **Lage ein Pool**
#### TrueNAS Bruker RAIDZ metoden. for å velge rett RAIDZ, så har dei ein egen guide på selve oppsettet
1. **Vel eit namn** for poolen (utan mellomrom).
2. **Vel lagringsmetode**:
   - **Mirroring (RAID 1)** – Beskyttar mot diskfeil, men halverer lagringskapasiteten.
   - **Stripe (RAID 0)** – Maksimerer lagring, men ingen redundans.
   - **RAID 4** – Dedikert paritetsdisk gir betre feiltoleranse enn RAID 0. den er ikkje støtta av TrueNAS
   - **RAID 5 (RAIDZ 1)** – Spreier paritet over fleire diskar, balansert mellom lagring og feiltoleranse.
   - **RAID 6 (RAIDZ 2)** – Spreier paritet over **to** diskar, slik at systemet kan tåle opptil **to feilande diskar**.
   - Der det står *optional*, treng du ikkje å legge det inn – det er berre for lagring av loggfiler til ulike formål. I framtida kan det likevel vere lurt å legge til fleire diskar som er dedikert til spesifike oppgåver, som lagring av systeminformasjon.

---

## **Lage eit Datasett**
1. Naviger til **Datasets**.
2. Klikk **Add Dataset**.
3. Viss du berre har ein Pool, vil den velje denne automatisk.
4. Gi datasettet ditt eit namn (utan mellomrom).
5. Vel **Generic**.
6. Klikk **Save**.

---

## **Testing av lagringsdiskar**
Når du trykker inn på **Data Protection**, ser du at **Scrub** er sett opp automatisk. Her kan du også koble til skytjenester (om du har betalt for det) eller sette opp testar for virtuelle maskiner. 

Dersom du berre treng **fildeling**, er det lurt å sette opp **S.M.A.R.T.-testar**:
1. Klikk på **Add** ved sida av der det står **Periodic S.M.A.R.T. Tasks**.

---

## **Scrub-task**
**Scrubbing** hjelper med å oppdage og rette feil i ZFS-filsystemet. Det fungerer som ein helsesjekk for lagringssystemet og kan automatisk reparere feil.

 **Anbefalte innstillingar**:
- **Scrub-kjøringar**: Aktivert.
- **Frekvens**: Månadleg.
- **Automatisk reparasjon**: På.
- **Varsling ved feil**: Aktivert.

---

## **S.M.A.R.T.-task**
**S.M.A.R.T.-testing** gir tidleg varsling om diskfeil og overvakar helsa til lagringsdiskane.

 **Anbefalte innstillingar**:
- **Frekvens**: Aktivert for alle lagringsdiskar.
- **Automatisk testing**: Kvar veke.
- **Kort test**: Kvar veke, kl. 00.00.
- **Lang test**: Kvar månad, kl. 03.00.
- **Varsling ved feil**: Aktivert.

---

## **Laging av brukarar**
1. Klikk inn på **Credentials**.
2. Vel **Users**.
3. Klikk **Add**.
4. Skriv inn brukarens fulle namn. *Login (username) blir automatisk generert etter namnet, men du kan tilpasse det*.
5. Opprett eit passord manuelt.
6. Dersom du skrur av passord, vil brukaren ikkje kunne bruke **SMB Share** for å koble seg til filserveren. *For meir info, klikk på spørsmålsteiknet i TrueNAS menyen*.

---

## **Shares (Deling av filer)**

### 1. Oppsett for enkelt brukar
1. Gå til **Windows (SMB) Shares** og klikk på **Add**.
2. Vel **Path**, og vel datasettet du vil dele (*Eksempel: `/mnt/din-pool/din-egenlagde-datasett`*).
3. Gi delinga eit namn.
4. **Purpose** kan stå som standard.
5. Klikk **Save**.
6. Viss systemet ber deg om å **restarte SMB**, så gjer det for å oppdatere innstillingane.
7. Dersom det dukkar opp **Configure ACL (Access Control List)** med meldinga:  
   *"Do you want to configure the ACL?"*, trykk **Configure**.
8. Du kjem då til ein meny som viser kven som skal ha tilgang til denne fila. **Root** er standard på **Owner** og **Group**, men du kan skifte det til ein annan brukar.
9. Trykk på sjekkboksane **Apply Owner** og **Apply Group**.
10. No ser du at du er inne i **Datasets**-sida. Dersom du ser på **Permissions**, vil du sjå at din valde brukar er "eigaren" av fila.

---

### **Dersom du vil at fila skal vere privat**
1. Inne i **Datasets**, under **Permissions**, klikk **Edit**.
2. I den mørkare boksen (*Access Control List*), finn **User Obj - root**, **Group Obj - root**, og **Other**. Trykk på **Other**.
3. Klikk av **Read** og **Execute** (dette hindrar at andre kan lese eller køyre innhaldet).
4. Klikk på **Save Access Control List** og lagre endringane.

---

### **Dersom du vil at fleire spesifikke brukarar skal ha tilgang til fila**
1. Inne i **Permissions**, klikk **Edit**.
2. Klikk deretter på **Add Item**.
3. No vil du sjå ein ny **Mask**-post. Denne lar deg legge til fleire brukarar.
4. Trykk på **Add Item** to gonger til.
5. Klikk på ein av dei nye **Mask**-postane.
6. Under menyen **Access Control Entry**, finn feltet **Who***.
7. Trykk på den svarte boksen og vel **User**.
8. Vel ein brukar (**User**) som skal få tilgang til fila.
9. Gi brukaren tilgangsrettar etter behov: **Read, Write, Execute**.
10. Vel ein ny **Mask** og klikk på den.
11. Trykk på den svarte boksen og vel **Group**
12. Vel ein gruppe (**Group**) som skal få tilgang.
13. Gi brukaren tilgangsrettar: **Read, Write, Execute**.

---

Har du spørsmål? ta kontakt på [e-post](mailto:nickeike@outlook.com) eller ring 919 95 753

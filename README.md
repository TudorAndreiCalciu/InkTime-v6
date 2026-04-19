# InkTime - Smartwatch E-Paper (nRF52840)

InkTime este un proiect hardware open-source ce reprezintă o platformă de smartwatch de tip low-power. Dispozitivul utilizează un ecran E-Paper pentru afișaj permanent cu consum zero și este propulsat de modulul Bluetooth Low Energy **Holyiot nRF52840**.

---

## 1. Diagrama Bloc a Sistemului

Diagrama de mai jos ilustrează interconectarea principalelor module hardware și fluxul de alimentare/date:

```mermaid
graph TD
    %% Componente Principale
    MCU[Microcontroler<br>Holyiot nRF52840]
    EPD[Display E-Paper<br>1.54 inch]
    PWR[Power Management<br>LDO 3.3V + Charger]
    BATT((Baterie LiPo<br>3.7V 250mAh))
    BTN[Butoane Tactile<br>User Input]
    I2C_SENSORS[Senzori I2C<br>Accelerometru / RTC]
    PROG[Interfață Debug<br>SWD]

    %% Conexiuni
    BATT -->|VBAT| PWR
    PWR -->|3V3 VCC| MCU
    PWR -->|3V3 VCC| EPD
    PWR -->|3V3 VCC| I2C_SENSORS
    
    MCU <-->|SPI Bus + Control| EPD
    MCU <-->|I2C Bus| I2C_SENSORS
    BTN -->|GPIO / Interrupts| MCU
    PROG <-->|SWDIO / SWCLK| MCU
2. Descrierea Hardware și Funcționalitate
Arhitectura dispozitivului este optimizată pentru eficiență energetică extremă:

Microcontroler (MCU): Modulul Holyiot nRF52840 integrează un procesor ARM Cortex-M4F la 64MHz și Bluetooth 5.0. A fost ales pentru consumul extrem de mic în modul Deep Sleep și amprenta redusă pe PCB.

Display E-Paper: Comunică prin interfața SPI. Tehnologia E-Ink consumă energie doar în momentul actualizării (refresh). Odată imaginea desenată, consumul este de 0 mA.

Interfață I2C: Include rezistențe de pull-up (4.7kΩ) pentru senzori externi (accelerometru, RTC, senzori de mediu).

Interacțiune: 3 butoane tactile cu debouncing, conectate pe pini GPIO cu Internal Pull-Up.

Power Management: Bateria LiPo (3.7V) este gestionată de un charger dedicat și stabilizată la 3.3V printr-un LDO cu curent de repaus foarte mic.

3. Maparea Pinilor (Pinout nRF52840)
Alocarea pinilor a fost realizată strategic pentru o rutare scurtă și eficientă a semnalelor:

Pin nRF52	Conectat la	Interfață	Justificarea alegerii
P1.00	EPD_MOSI	SPI	Pin de mare viteză, poziționat optim pentru conectorul EPD.
P0.22	EPD_SCK	SPI	Pin dedicat pentru clock SPI, minimizează cross-talk-ul.
P0.20	EPD_CS	SPI (CS)	Control selectare periferic; pin digital standard.
P0.17	EPD_DC	GPIO	Control Data/Command pentru driverul display-ului.
P0.15	EPD_RES	GPIO	Reset hardware pentru display la inițializare.
P0.13	EPD_BUSY	GPIO (In)	Monitorizează starea internă a display-ului (final refresh).
P0.26	I2C_SDA	I2C	Pin standard I2C, poziționat lângă pinii de pull-up.
P0.24	I2C_SCL	I2C	Clock I2C; grupat cu SDA pentru integritatea semnalului.
P0.02	BTN1	GPIO (IRQ)	Pin AIN0, ideal pentru întreruperi de tip "Wake-up".
P0.29	BTN2	GPIO	Input utilizator; poziționat lângă marginea plăcii.
P0.31	BTN3	GPIO	Input utilizator; grupat cu BTN2 pentru simetrie.
P0.11	SWCLK	Debug	Pin hardware dedicat; necesar pentru programare.
P0.12	SWDIO	Debug	Pin hardware dedicat; necesar pentru programare.
4. Bill of Materials (BOM)
Componentă	Pachet	Producător / Cod JLC	Datasheet
nRF52840 Holyiot	SMD Module	Holyiot-18010	Link PDF
AP2112K-3.3	SOT-23-5	JLCPCB C51118	Link PDF
TP4056	SOP-8	JLCPCB C16581	Link PDF
Butoane Tactile	3x4mm SMD	JLCPCB C318884	Link PDF
Conector FPC 24P	0.5mm Pitch	JLCPCB C46954	Link PDF
5. Estimare Consum Energie (Power Budget)
Calcul teoretic pentru demonstrarea autonomiei:

Capacitate Baterie: 250 mAh

Consum Deep Sleep: ~5 µA

Consum Refresh E-Paper: ~8 mA (timp de 2 secunde)

Scenariu: 1 refresh pe minut.

Consum mediu: 0.27 mA

Autonomie: ~925 ore (~38 de zile) cu o singură încărcare.

6. Jurnal de Design și Asamblare (Mecanică)
În folderul /Mechanical se regăsesc fișierele necesare pentru integrarea fizică:

Fișierul .f3d: Istoricul complet de proiectare în Fusion 360.

Fișierul .step: Vedere explodată (Exploded View) a ansamblului:
Capac Spate -> Baterie -> PCB -> Display -> Carcasă Frontală.

Verificări: Clearance-ul pentru mufa USB și butoane a fost validat prin asamblare virtuală 3D.

# README - OpenBook E-Reader

**Autor: Slincu Marius**

## Prezentare Generală

OpenBook este un e-reader open-source bazat pe microcontrollerul ESP32-C6. Dispozitivul integrează un ecran e-paper de 7.5 inch, un senzor de mediu (BME680), un sistem de monitorizare a bateriei, precum și conectivitate Wi-Fi și USB-C. Acest fisier descrie arhitectura hardware a proiectului, componentele principale și modul în care acestea sunt interconectate.

---

## Diagramă de Bloc

```

                +---------------------------+
                |     USB-C Connector       |
                | (USB Data ↔ ESP32 USB)    |
                | (ESD & Termination Prot.) |
                +-------------+-------------+
                              |
                              v
                +--------------------------+
                |       Charging IC        |<-- USB-C Power
                +-------------+------------+
                              |
                              v
                +--------------------------+
                |         Battery          |
                +-------------+------------+
                              |
                              v
                +--------------------------+
                |           LDO            |
                |       (3.3V Reg)         |
                +-------------+------------+
                              |
                              v
                      +--------------+
                      |    ESP32-C6   |
                      +------+--------+
                             |
      +----------------------+-------------------------+
      |                      |                         |
      v                      v                         v
+-----------+       +----------------+         +-----------------+
|  Display  |<----->|     BME688     |<------->| Tactile Buttons |
| (SPI 4W)  |       |   (I2C + PU)   |         |  (GPIO + RC)     |
+-----------+       +----------------+         +-----------------+
```


---
## Detalii Hardware

### ESP32-C6-WROOM-1-N8

- **Rol**: Componenta centrală de control, responsabilă pentru conectivitatea wireless și interacțiunea cu perifericele.
- **Interfețe disponibile**:
  - SPI: Utilizată pentru comunicarea cu ecranul e-paper, memoria Flash și cardul Micro SD.
  - I2C: Pentru conectarea senzorului BME680, monitorului de baterie (MAX17048) și ceasului RTC (DS3231).
  - GPIO: Utilizați pentru butoane de control și semnalizare prin LED-uri.
  - UART: Dedicat depanării și comunicației seriale.
- **Specificații tehnice**: Tactat la 160MHz, 512KB SRAM, 8MB memorie Flash integrată.

### Senzor BME680

- **Rol**: Oferă măsurători precise pentru temperatură, umiditate, presiune atmosferică și calitatea aerului.
- **Interfață**: Comunicare prin magistrala I2C cu viteză de 400kHz.
- **Eficiență energetică**: Consumă sub 1mA în funcționare și sub 1µA în repaus.

### Sistem de Alimentare

- **Baterie**: Acumulator Li-Po de 2500mAh, 3.7V.
- **Încărcare**: Controlată de MCP73831 prin portul USB-C, cu un curent maxim de 1A.
- **Monitorizare**: Realizată cu ajutorul cipului MAX17048, prin I2C.
- **Profil de consum**:
  - În activitate (Wi-Fi activ și ecran actualizat): ~150mA.
  - În standby (ecran static, fără activitate radio): ~10mA.
  - În modul deep sleep: >50µA.
  - **Autonomie estimată**: ~250 de ore la un consum mediu de 10mA.

### Butoane de Navigare

- Folosește butoane tactile Panasonic EVQPUJ02K, fiecare cu rezistență pull-up de 10KΩ.

### USB-C

- **Funcții**: Utilizat pentru încărcare (5V/1A) și transfer de date (USB 2.0).
- **Protecție**: Diode ESD (USBLC6-2SC6Y) pentru protejarea la supratensiuni.



---
# Lista de Componente (BOM)

Tabelul de mai jos prezintă componentele folosite în proiect:

| **Referință**        | **Part Number**               | **Descriere**                                      | **Furnizor (Mouser)**                                                                 | **Datasheet**                                                                                     |
|----------------------|-------------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| U2                   | ESP32-C6-WROOM-1-N8           | MCU cu Wi-Fi 6 și Bluetooth 5                      | [Mouser](https://eu.mouser.com/ProductDetail/Espressif-Systems/ESP32-C6-WROOM-1-N8)   | [Datasheet](https://www.espressif.com/sites/default/files/documentation/esp32-c6-wroom-1_datasheet_en.pdf) |
| U1                   | W25Q512JVEIQ                  | Memorie Flash SPI 512Mb                           | [Mouser](https://eu.mouser.com/ProductDetail/Winbond/W25Q512JVEIQ)                     | [Datasheet](https://www.winbond.com/resource-files/W25Q512JV%20RevD%2004082020.pdf)              |
| SENSOR2              | BME680                        | Senzor temperatură, umiditate, presiune, gaz      | [Mouser](https://eu.mouser.com/ProductDetail/Bosch-Sensortec/BME680)                   | [Datasheet](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bme680-ds001.pdf) |
| U8                   | DS3231SN                      | Ceas de timp real cu interfață I2C                | [Mouser](https://eu.mouser.com/ProductDetail/Maxim-Integrated/DS3231SN)                | [Datasheet](https://datasheets.maximintegrated.com/en/ds/DS3231.pdf)                            |
| JJ4                  | MAX17048G+T10                 | Monitor baterie cu interfață I2C (Fuel Gauge)     | [Mouser](https://eu.mouser.com/ProductDetail/Maxim-Integrated/MAX17048G-T10)           | [Datasheet](https://datasheets.maximintegrated.com/en/ds/MAX17048-MAX17049.pdf)                 |
| U3                   | MCP73831T-2ACI/OT             | Controler de încărcare baterie Li-Po              | [Mouser](https://eu.mouser.com/ProductDetail/Microchip-Technology/MCP73831T-2ACI-OT)   | [Datasheet](https://ww1.microchip.com/downloads/en/DeviceDoc/20001984g.pdf)                     |
| U6                   | BD5230G-TL                    | Voltage Supervisor + Reset                        | [Mouser](https://eu.mouser.com/ProductDetail/Rohm-Semiconductor/BD5230G-TL-E)          | [Datasheet](https://fscdn.rohm.com/en/products/databook/datasheet/ic/power/voltage_detector/bd52xxg-e.pdf) |
| U9                   | USBLC6-2SC6Y                  | Protecție ESD pentru port USB-C                   | [Mouser](https://eu.mouser.com/ProductDetail/STMicroelectronics/USBLC6-2SC6Y)          | [Datasheet](https://www.st.com/resource/en/datasheet/usblc6-2sc6.pdf)                           |
| Q1                   | IRLML6344                     | MOSFET N-Ch pentru comutare EPD                   | [Mouser](https://eu.mouser.com/ProductDetail/Infineon-Technologies/IRLML6344TRPBF)     | [Datasheet](https://www.infineon.com/dgdl/irlml6344pbf.pdf?fileId=5546d462533600a40153566003611b6f) |
| L1                   | 68uH                          | Inductor pentru circuitul de comandă e-paper      | [Mouser](https://eu.mouser.com/ProductDetail/Coilcraft/LPS4018-680MLC)                 | [Datasheet](https://www.coilcraft.com/getmedia/4b0b7a0a-d84f-48ed-bb2a-1be67f22fb6d/lps4018.pdf)  |

---
## Configurare Pini ESP32-C6

| **Pin ESP32-C6** | **Funcție**         | **Componentă**         | **Rol**                                    |
|------------------|--------------------|------------------------|--------------------------------------------|
| GPIO0           | BOOT_BUTTON        | Buton                  | Activează modul de boot la inițializare    |
| GPIO1           | RESET_BUTTON       | Buton                  | Resetare manuală                           |
| GPIO2           | CHANGE_BUTTON      | Buton                  | Comutare între meniuri                     |
| GPIO3           | EPD_CS             | Display E-Paper        | Chip Select pentru SPI                     |
| GPIO4           | EPD_DC             | Display E-Paper        | Linie de control Date/Comenzi              |
| GPIO5           | EPD_RST            | Display E-Paper        | Reset hardware pentru ecran                |
| GPIO6           | EPD_BUSY           | Display E-Paper        | Semnal de stare pentru actualizare ecran   |
| GPIO7           | SPI_MOSI           | Display, Flash, SD     | Linie de date pentru SPI                   |
| GPIO8           | SPI_MISO           | Display, Flash, SD     | Linie de date SPI (citire)                 |
| GPIO9           | SPI_SCK            | Display, Flash, SD     | Semnal de ceas SPI                         |
| GPIO10          | FLASH_CS           | Memorie Flash          | Chip Select pentru memorie                 |
| GPIO11          | SD_CS              | Card Micro SD          | Chip Select pentru card SD                 |
| GPIO12          | I2C_SDA            | BME680, MAX17048, DS3231 | Linie de date I2C                         |
| GPIO13          | I2C_SCL            | BME680, MAX17048, DS3231 | Linie de ceas I2C                         |
| GPIO14          | CHG_LED            | LED                    | Indicator al procesului de încărcare       |
| GPIO15          | UART_TX            | Debugging              | Transmitere date prin UART                 |
| GPIO16          | UART_RX            | Debugging              | Recepție date prin UART                    |
| GPIO17          | USB_D+             | USB-C                  | Linie de date USB pozitivă                 |
| GPIO18          | USB_D-             | USB-C                  | Linie de date USB negativă                 |

**Notă**: Pinii sunt alocați conform specificațiilor ESP32-C6 și necesităților proiectului OpenBook. Mulți dintre ei pot îndeplini funcții multiple și pot fi configurați flexibil în funcție de aplicație.




![image](https://github.com/user-attachments/assets/2fb863e2-7691-4fdc-9b05-09f4df22e068)

![image](https://github.com/user-attachments/assets/02538dc1-fcc4-4fca-95cf-4bd374a13944)


![image](https://github.com/user-attachments/assets/5717cfb0-a274-4139-861b-acee66795056)

---

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
| Component | Buy | Datasheet |
| ---------- | --------- | --------- |
| PFMF.050.1 | [Retailer](https://ro.mouser.com/ProductDetail/Schurter/PFMF.050.2?qs=1auRipcfynCums5v1iucSA%3D%3D) |  [Datasheet](https://ro.mouser.com/datasheet/2/358/typ_PFMF-1275918.pdf) |
| USB4110-GF-A | [Retailer](https://ro.mouser.com/ProductDetail/GCT/USB4110-GF-A?qs=KUoIvG%2F9IlYiZvIXQjyJeA%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/837/GCT_USB4110_Product_Drawing___20k_cycles-3455479.pdf) |
| USBLC6-2SC6Y | [Retailer](https://ro.mouser.com/ProductDetail/STMicroelectronics/USBLC6-2SC6Y?qs=gNDSiZmRJS%2FOgDexvXkdow%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/389/usblc6_2sc6y-1852505.pdf) |
| SD0805S020S1R0 | [Retailer](https://ro.mouser.com/ProductDetail/KYOCERA-AVX/SD0805S020S1R0?qs=jCA%252BPfw4LHbpkAoSnwrdjw%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/40/schottky-3165252.pdf) |
| DMG2305UX-7 | [Retailer](https://ro.mouser.com/ProductDetail/Diodes-Incorporated/DMG2305UX-7?qs=L1DZKBg7t5F%2FNBHrjfxC%252Bg%3D%3D) | [Datasheet](https://www.diodes.com/assets/Datasheets/DMG2305UX.pdf) |
| XC6220A331MR-G | [Retailer](https://ro.mouser.com/ProductDetail/Torex-Semiconductor/XC6220A331MR-G?qs=AsjdqWjXhJ8ZSWznL1J0gg%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/760/xc6220-3371556.pdf) |
| Condensator 100 uF TANT | [Retailer](https://ro.mouser.com/ProductDetail/KYOCERA-AVX/TAJW107M010RNJ?qs=Wtp%252Bf%2FAeVqIH8v1VxV%252B1Rg%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/40/TAJ-3165264.pdf) |
| 112A-TAAR-R03_ATTEND | [Retailer](https://www.digikey.ro/en/products/detail/attend-technology/112A-TAAR-R03/17633923) | [Datasheet](https://www.attend.com.tw/data/download/file/112A-TAAR-R03_Spec.pdf)
| ESP32-C6-WROOM-1-N8 | [Retailer](https://ro.mouser.com/ProductDetail/Espressif-Systems/ESP32-C6-WROOM-1-N8?qs=8Wlm6%252BaMh8ST02Gmwp74cw%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/891/Espressif_ESP32_C6_WROOM_1__Datasheet_V0_1_PRELIMI-3239987.pdf) |
| MCP73831 | [Retailer](https://ro.mouser.com/ProductDetail/Microchip-Technology/MCP73831T-2ACI-OT?qs=yUQqVecv4qvbBQBGbHx0Mw%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/268/MCP73831_Family_Data_Sheet_DS20001984H-3441711.pdf) |
| CHG_LED | [Retailer](https://store.comet.srl.ro/Catalogue/Product/40478/) | [Datasheet](https://www.snapeda.com/parts/KP-1608SURCK/Kingbright/datasheet/) |
| SI1308EDL-T1-GE3 | [Retailer](https://ro.mouser.com/ProductDetail/Vishay-Semiconductors/SI1308EDL-T1-GE3?qs=bX1%252BNvsK%2FBramh9tgpOaEw%3D%3D) | [Datasheet](https://www.vishay.com/doc?63399) |
| MBR0530 | [Retailer](https://ro.mouser.com/ProductDetail/Micro-Commercial-Components-MCC/MBR0530-T?qs=9VyI4qLX4NTSXkb9ynzJnA%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/258/mcc_mbr0520~mbr0560sod123-1179695.pdf) |
| L1 | [Retailer](https://ro.mouser.com/ProductDetail/Wurth-Elektronik/744043680?qs=PGXP4M47uW6VkZq%252BkzjrHA%3D%3D) | [Datasheet](https://www.we-online.com/components/products/datasheet/744043680.pdf) |
| FH34SRJ-24S-0.5SH_99_ | [Retailer](https://ro.mouser.com/ProductDetail/Hirose-Connector/FH34SRJ-24S-0.5SH99?qs=vcbW%252B4%252BSTIpKBl5ap9J8Fw%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/185/FH34SRJ_24S_0_5SH_99__CL0580_1255_6_99_2DDrawing_0-1615044.pdf) |
| BME688 | [Retailer](https://ro.mouser.com/ProductDetail/Bosch-Sensortec/BME688?qs=IS%252B4QmGtzzqQoVDscqwx3A%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/783/bst_bme688_fl000-2307034.pdf) |
| BD5229G-TR | [Retailer](https://ro.mouser.com/ProductDetail/ROHM-Semiconductor/BD5229G-TR?qs=4kLU8WoGk0vvnhrrYwdszw%3D%3D) | [Datasheet](https://fscdn.rohm.com/en/products/databook/datasheet/ic/power/voltage_detector/bd52xxg-e.pdf) |
| Button | [Retailer](https://ro.mouser.com/ProductDetail/CK/KMR221GULCLFS?qs=u2NJ%252B70r0goBXaNk7IrU0Q%3D%3D) | [Datasheet](https://www.ckswitches.com/media/1479/kmr2.pdf) |
| MAX17048G+T10 | [Retailer](https://ro.mouser.com/ProductDetail/Analog-Devices-Maxim-Integrated/MAX17048G%2bT10?qs=D7PJwyCwLAoGnnn8jEPRBQ%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/609/MAX17048_MAX17049-3469099.pdf) |
| W25Q512JVEIQ | [Retailer](https://ro.mouser.com/ProductDetail/Winbond/W25Q512JVEIQ?qs=l7cgNqFNU1jw6svr3at6tA%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/949/Winbond_W25Q512JV_Datasheet-3240039.pdf) |
| PGB1010603MR | [Retailer](https://ro.mouser.com/ProductDetail/Littelfuse/PGB1010603MRHF?qs=KvZd0dN2Zg%2FuIq6icj%252BGKA%3D%3D) | [Datasheet](https://www.littelfuse.com/media?resourcetype=datasheets&itemid=8a337998-d54d-466b-be4e-dc5bcd1f9321&filename=littelfuse_pulseguard_pgb1_datasheet.pdf) |
| QWIIC_RIGHT_ANGLE | [Retailer](https://ro.mouser.com/ProductDetail/GCT/USB4110-GF-A?qs=KUoIvG%2F9IlYiZvIXQjyJeA%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/837/GCT_USB4110_Product_Drawing___20k_cycles-3455479.pdf) |
| DS3231SN# | [Retailer](https://ro.mouser.com/ProductDetail/Analog-Devices-Maxim-Integrated/DS3231SN?qs=ffX8NcjNb2RmKAb9wAk9Ug%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/609/DS3231-3421123.pdf) |
| CPH3225A C10_SUPERCAP | [Retailer](https://ro.mouser.com/ProductDetail/Seiko-Semiconductors/CPH3225A?qs=3etwrb1wR%252BhUOph6lAO7eg%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/360/Seiko_Instruments_MicroBattery_E_20230330_2024Jan_-3561061.pdf) |
| Condensator 100nF | [Retailer](https://ro.mouser.com/ProductDetail/KYOCERA-AVX/06033G104ZAT2A?qs=NXubJDmysXJMPmHfVo6Z%252BA%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/40/KGM_Y5V-3223189.pdf) |
| Condensator 4.7uF | [Retailer](https://ro.mouser.com/ProductDetail/KYOCERA-AVX/0402ZD475MAT2A?qs=NBFAU1oqP4W4U2PCPHI0sg%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/40/cx5r_KGM-3223198.pdf) |
| Condensator 10uF | [Retailer](https://ro.mouser.com/ProductDetail/Samsung-Electro-Mechanics/CL10A106KQ8NNNL?qs=xZ%2FP%252Ba9zWqaes9JKSsob2Q%3D%3D) | [Datasheet](https://ro.mouser.com/datasheet/2/585/MLCC-1837944.pdf) |


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


**Notă**: Am avut o problema cu Fusion si mi-a dat crash aplicatia si am capatat o eroare de care nu am stiut sa scap si nu am mai putu contiuna modelul 3D, insa am facut partial din ea.




![image](https://github.com/user-attachments/assets/2fb863e2-7691-4fdc-9b05-09f4df22e068)

![image](https://github.com/user-attachments/assets/02538dc1-fcc4-4fca-95cf-4bd374a13944)


![image](https://github.com/user-attachments/assets/5717cfb0-a274-4139-861b-acee66795056)

---

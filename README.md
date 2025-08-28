# Przetwornica DC/DC Buck 60V na 5V / 30A z Cyfrowym Sterowaniem
### _High-Power 60V to 5V / 30A Buck Converter with Digital Control Interface_

Projekt i realizacja wysokoprądowej, wysokonapięciowej przetwornicy impulsowej typu Synchronous Buck, zrealizowanej w oprogramowaniu **KiCAD**. Projekt obejmował pełen cykl tworzenia hardware'u: od definicji założeń, przez dobór komponentów, projekt schematu ideowego, aż po projekt wielowarstwowej płytki PCB i uruchomienie fizycznego prototypu.

Celem było stworzenie platformy sprzętowej zdolnej do wydajnej konwersji mocy, przygotowanej do współpracy z mikrokontrolerem w celu cyfrowego sterowania parametrami wyjściowymi.

<div align="center">
  <img src="img/real_pcb_photo.png" alt="Zdjęcie zmontowanego prototypu" width="700"/>
  <br>
  <em>Rys. 1: Zmontowany i uruchomiony prototyp przetwornicy.</em>
</div>

---

## Główne Założenia i Specyfikacja Techniczna

- **Topologia:** Synchroniczna przetwornica obniżająca napięcie (Synchronous Buck).
- **Napięcie wejściowe (Vin):** do **60V**.
- **Napięcie wyjściowe (Vout):** **5V** (z możliwością regulacji przez mikrokontroler).
- **Maksymalny prąd wyjściowy (Iout_max):** Projektowany do **30A**.
- **Kluczowe komponenty stopnia mocy:**
    - **Sterownik bramek MOSFET:** Texas Instruments LM5106
    - **Tranzystory MOSFET:** Texas Instruments CSD19531KCS (N-Channel NexFET)
- **Sterowanie:** Układ przygotowany do sterowania sygnałem **PWM** z mikrokontrolera (ESP32-S2).
- **Architektura zasilania logiki:** **Dwustopniowa** – pomocnicza przetwornica buck 12V (LM5164) zasila stabilizatory liniowe 5V i 3.3V, zapewniając wysoką sprawność.
- 🔩 **Interfejs komunikacyjny CAN Bus:** Płytka wyposażona w transceiver CAN (TJA1051T), przygotowany do komunikacji w standardach przemysłowych.
- **Płytka PCB:** 4-warstwowa, z dedykowanymi płaszczyznami masy i zasilania w celu optymalizacji termicznej i minimalizacji pętli prądowych.

---

## Architektura Sprzętowa

Projekt hardware'owy został podzielony na trzy oddzielne bloki funkcjonalne.

#### 1. Stopień Wysokiej Mocy
Sekcja odpowiedzialna za główną konwersję energii. Zawiera kluczowe elementy takie jak tranzystory MOSFET, sterownik bramek, cewkę wyjściową oraz kondensatory filtrujące. Projekt ścieżek na PCB w tym obszarze został zoptymalizowany pod kątem przenoszenia dużych prądów (do 30A) i minimalizacji pętli prądowych o wysokiej częstotliwości.

<div align="center">
  <img src="img/schematic_power_stage.png" alt="Schemat stopnia mocy" width="800"/>
  <br>
  <em>Rys. 2: Schemat ideowy stopnia mocy przetwornicy.</em>
</div>

#### 2. Układ Zasilania
Ten blok odpowiada za dostarczenie stabilnych napięć dla całej logiki cyfrowej. Został zrealizowany w wydajnej, **dwustopniowej architekturze**:
1.  **Pre-regulator:** Główne napięcie wejściowe (do 60V) jest najpierw obniżane do 12V przez dedykowaną przetwornicę buck (LM5164).
2.  **Stabilizatory LDO:** Stabilne, pośrednie napięcie 12V zasila następnie stabilizatory liniowe, które generują czyste linie 5V i 3.3V.

Taka architektura znacząco poprawia sprawność i zarządzanie ciepłem w porównaniu do próby zasilania LDO bezpośrednio z wysokiego napięcia wejściowego.

<div align="center">
  <img src="img/schematic_power_supply.png" alt="Schemat układu zasilania" width="800"/>
  <br>
  <em>Rys. 3: Schemat ideowy sekcji zasilania.</em>
</div>

#### 3. Mikrokontroler i Peryferia
Logika urządzenia, oparta o mikrokontroler **ESP32-S2**. Ten blok zawiera również wszystkie interfejsy użytkownika i komunikacyjne, w tym:
- Złącza do programowania i debugowania.
- Diody LED i przyciski do interakcji z użytkownikiem.
- **Transceiver magistrali CAN (TJA1051T)**, implementujący warstwę fizyczną dla niezawodnej komunikacji w standardach przemysłowych.

<div align="center">
  <img src="img/schematic_mcu_control.png" alt="Schemat sekcji mikrokontrolera" width="800"/>
  <br>
  <em>Rys. 4: Schemat ideowy sekcji sterowania z mikrokontrolerem i peryferiami.</em>
</div>

---

## Projekt Płytki PCB

Projekt 4-warstwowej płytki PCB był kluczowym elementem projektu, mającym na celu zapewnienie stabilności termicznej i elektrycznej przy dużych obciążeniach.

<div align="center">
  <img src="img/pcb_3d_render.png" alt="Render 3D zaprojektowanej płytki PCB" width="700"/>
  <br>
  <em>Rys. 5: Render 3D finalnego projektu PCB.</em>
</div>

- **Warstwa 1 (Top):** Główne ścieżki prądowe, komponenty mocy, interfejs użytkownika.
- **Warstwa 2 (GND Plane):** Lita płaszczyzna masy zapewniająca niską impedancję ścieżki powrotnej prądu i doskonałe ekranowanie.
- **Warstwa 3 (GND Plane):** Druga płaszczyzna masy dla dalszej poprawy integralności sygnałowej i odprowadzania ciepła.
- **Warstwa 4 (Bottom):** Mikrokontroler, dodatkowe komponenty, ścieżki sygnałowe.

---

## Uruchomienie i Pomiary

Prototyp został zmontowany i poddany testom w celu weryfikacji poprawności działania. Pomiary oscyloskopowe potwierdziły prawidłowe sterowanie bramek tranzystorów MOSFET. Układ został również pomyślnie przetestowany pod lekkim obciążeniem, stabilnie dostarczając napięcie wyjściowe bliskie 5V przy zasilaniu 60V.

---

## Pliki Projektowe

Pełny projekt hardware'owy (schemat, PCB) jest dostępny w folderze [`project_files/`](project_files/). Można go otworzyć za pomocą darmowego oprogramowania **KiCAD**.

_Uwaga: Oprogramowanie (firmware) dla mikrokontrolera zostało opracowane przez innego członka zespołu i nie jest częścią tego repozytorium. Zaimplementowany hardware magistrali CAN nie był wykorzystywany w finalnej wersji oprogramowania._

---

## Licencja

Ten projekt jest udostępniony na licencji MIT - zobacz plik [LICENSE](LICENSE) po szczegóły.
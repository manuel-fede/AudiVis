<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<center>
#Audio Visualiser
#RGB LED Display
</center>

<div class="pagebreak"></div>

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [Audio Visualiser - RGB LED Display](#audio-visualiser---rgb-led-display)
  - [Erste Beschreibung](#erste-beschreibung)
  - [Messungen der Audiosignale](#messungen-der-audiosignale)
      - [Output eines Xperia Z](#output-eines-xperia-z)
      - [Output eines MacBook Pro](#output-eines-macbook-pro)
  - [Blockschaltbild](#blockschaltbild)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

<div class="pagebreak"></div>

##1. Erste Beschreibung
Ziel ist es einen Audio Visualiser zu bauen, welcher, je nach Lautstärke und Frequenz aufleuchtet. Dieser Visualiser wird eine **9x9 RGB LED Matrix** besitzen, wird jedoch darauf ausgelegt, dass bis zu 1024 LEDs betrieben werden können. Fürs erste sind 3 Balken geplant, welche, je nach **Hochpass, Tiefpass und Bandpass** aufleuchten. Ein **Bluetooth-Modul** soll eine **Schnittstelle** zwischen anderen Geräten darstellen, um es einfach zu machen Änderungen vorzunehmen.

<img src="imgs/Blockschaltbild.png"/>

##2. Messungen der Audiosignale
Da zu Anfang nicht bekannt war, mit welchen Spannungen zu rechnen ist, wurde eine Messung Durchgeführt. Dies sind die Ergebnisse:

###2.1. Output eines Xperia Z

|Titel                                   |Vpp min/mV|Vpp max/mV|freq min/Hz|freq max/Hz|
|:---------------------------------------|---------:|---------:|----------:|----------:|
|Nicotine - Panic at the Disco           |    128.00|    350.00|     290.00|   2,900.00|
|What you waiting for? - Stefani Gwen    |     88.00|    570.00|     300.00|   3,600.00|
|Get Stupid                              |    300.00|    470.00|     200.00|   3,300.00|
|Magic is Timeless - Archie V            |     64.00|    130.00|      60.00|   1,400.00|
|Monster (DotEXE Remix) - Meg & Dia      |     60.00|     75.00|      90.00|   2,500.00|
|Rauschen                                |      3.00|

###2.2. Output eines MacBook Pro

|Titel                                   |Vpp min/mV|Vpp max/mV|freq min/Hz|freq max/Hz|
|:---------------------------------------|---------:|---------:|----------:|----------:|
|Nicotine - Panic at the Disco           |    120.00|    400.00|     300.00|   3,200.00|
|What you waiting for? - Stefani Gwen    |    120.00|    900.00|      50.00|   1,900.00|
|Get Stupid                              |    200.00|    620.00|     100.00|   1,700.00|
|Magic is Timeless - Archie V            |    260.00|    400.00|     200.00|   1,900.00|
|Monster (DotEXE Remix) - Meg & Dia      |    200.00|    400.00|   1,000.00|  10,000.00|
|Rauschen                                |     50.00|

Aus diesen Werten kann man schließen, dass eine **Verstärkung** von ca **5** vonnöten ist.


##3. Blockschaltbild
<img src="imgs/Blockschaltbild-detailed.tif"/>

<div class="pagebreak"></div>

##4. Verstärker & Filter

###4.1. Verstärker
Der Verstärker ist nötig, um das Signal auf ein gut verwertbares Level zu Bringen. Die verstärkung muss ca 5 betragen, was aus den Messdaten unter [Messung der Audiosignale](#messung-der-audiosignale) hervorgeht. Um diese Verstärkung zu benutzen wird ein **Rail-To-Rail OPV** benutzt. Um es zu möglich zu machen die Verstärkung zu verändern wird ein **Potentiometer** mit **10k vor** den Eingang des **OPV** geschalten. Da außerdem eine Strombegrenzung vonnöten ist, wird ein **1k Widerstand** vor dem Potentiometer in Serie geschalten.

_Berechnung der Widerstände:_<br>
Verstärkung = (R<sub>F</sub>+R<sub>N</sub>)/R<sub>N</sub><br>
*R<sub>F</sub> = 10k<br>*
------> R<sub>N</sub> = R<sub>F</sub>/(Verstärkung-1)<br>
------> *R<sub>N</sub> = 2k5*

###4.2. Filter
Die Filtereinheit besteht aus __2 identischen Filterblöcken__, die jeweils die Linke oder Rechte seite übernehmen. Jeder dieser Blöcke besitzt einen Hochpass, Tiefpass und Bandpass.

_Schaltung eines Blockes:_

<img src="imgs/filters.png" alt="Drawing" style="width: 300px;"/>

<div class="pagebreak"></div>

_Berechnung:_<br>
Da die Frequenzen natürlich nicht willkürlich gewählt sein sollen, werden zuerst die gewünschten Schwellen ausgemacht. Diese wurden unter [fairaudio.com](http://www.fairaudio.de/hifi-lexikon-begriffe/frequenzbereiche-bass-mitten-hochton-grundton.html) aufgelistet.

|Frequenzbereich|  Filter|
|--------------:|:-------|
|       -  350Hz|Tiefpass|
| 350Hz - 2.5kHz|Bandpass|
|2.5kHz -       |Hochpass|

_Daraus folgen diese Berechnungen:_<br>
Ausgewählt:

R[1-4]= 1k

 - Tiefpass:<br>
f<sub>g</sub> = 1/(2\*&pi;\*&tau;) = 350Hz<br>
------> C<sub>2</sub> = 1/(2\*&pi;\*f<sub>g</sub>\*R)<br>
------> C<sub>2</sub> = 454.728nF<br>

 - Hochpass:<br>
f<sub>g</sub> = 2.5kHz<br>
------> C<sub>1</sub> = 63.662nF<br>

 - Bandpass:<br>
Da das Ziel eine Bandbreite ist, welche alle Frequenzen eindeckt, welche zwischen dem Hoch und Tiefpass liegen, muss man im Prinzip nur die Werte der Kondensatoren für den jeweils anderen Filter nehmen.<br>
------> C<sub>3</sub> = 454.728nF<br>
------> C<sub>4</sub> = 63.662nF<br>

<!-- NOTIZ: Entkoppelkondensator hat 22nF -->


<div id="footer">
    &copy; 2013 MyCompany
</div>

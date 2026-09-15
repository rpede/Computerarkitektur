# Maskinkode opgaver

Opgaverne løses med [CPU Visual Simulator](https://cpuvisualsimulator.github.io/).

Denne CPU har nogle forskelle fra den du så i præsentationen.
Den har kun et register som hedder ACC.
Derfor er der ingen grund til at angive hvilket register der er tale om som en del af instruktionen.
Skal du læse en værdi fra en RAM adresse, så bruger du instruktionen `LOD` til at læse
værdien in i ACC.
Du kan du gemme værdien fra ACC i en RAM adresse med instruktionen `STO`.
F.eks. for at kopierer værdien "10" til at stå i RAM adresse 4, skal du skrive:

![LOD STO NOP](./LOD-STO.png)

Hvis du i stedet vil angive adressen med et label, ser det således ud:

![](./LOD-STO_label.png)

*RAM adressen 4 har fået label "A" (ses til højre), hvilket betyder at man kan
bruge "A" i stedet for RAM adressen i instruktionerne.
Det svarer til en variabel i Python.*

Du kan se et eksempler på programmer ved klikke på "Examples" knappen.

![Load example](./examples.png)

Se [manualen](https://cpuvisualsimulator.github.io/manual) for beskrivelse af
hele instruktionssættet, samt flere eksempler.

"NOP" er en forkortelse for no-operation og betyder egentlig bare at CPUen gør
ingenting for den pågældende instruktions cyklus.

Du kan stoppe programmet med "HLT" instruktionen, hvilket er en forkortelse for
halt execution.
Uden HLT vil CPUen forsøge at eksekvere alle værdierne fra RAM som var det
instruktioner.

*Løs hvad du kan af nedenstående opgaver. Det er OK, hvis ikke du kommer
længere end den første, da de er svære. Vil dog som minimum bede dig om at læse
alle opgaverne samt at give det et forsøg.*

## 1. Nedtælling

Lav et program som tæller ned fra 10.

## 2. Gange uden MUL

Lav et program der ganger 2 tal uden at bruge MUL instruktionen.

*Nogle mikrokontroller har ikke en instruktionen til at gange, i stedet
foretages dette ved at gentage ADD instruktionen.*

## 3. Logik

Lav et program som læser en persons alder fra RAM adresse 32.
Derefter, udregner om en person er gammel nok til at have stemmeret i Danmark.
Resultatet i adresse 34.

Du kan bruge værdien 00000001 til at repræsentere sandt (true), altså at
personen er gammel nok.
Og værdien 00000000 til at repræsentere falsk (false), altså at personen ikke
er gammel nok.

## 4. Mere logik

CPU simulatoren har også en instruktion som hedder AND og en der hedder OR.
Disse gør det samme som de AND og OR gates vi har snakket om tidligere, dog
bare med flere bits af gangen.

Disse instruktioner kan bruges til at lave mere kompleks logik.

F.eks. I Danmark skal man både være 18 år eller over, og være danske
statsborger for at have stemmeret til folketingsvalget.
Vi kan gemme i en RAM adresse sandt/falsk om en person er dansk statsborger.
Hvis vi genbruger vores udregning fra tidligere om hvor vidt en person er over
18, sammenholder det med hvorvidt de også er danske statsborger (AND
instruktionen), så kan vi finde ud af hvorvidt en person har stemmeret til
folketingsvalget.

Du behøver ikke at skrive et program for at udregne ovenstående.
Jeg håber bare at det giver en forståelse for, at man med simple instruktioner
kan beskrive kompleks logik på en computer.

Denne form for logik bygger på en gren af matematik som hedder boolesk algebra.
Dette kommer du lære mere om i faget Programmering i løbet af de kommende uger.

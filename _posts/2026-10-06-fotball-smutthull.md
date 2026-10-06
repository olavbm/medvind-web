---
title: "Fotball og maskinlæring"
---
Velkommen til det første tekniske innlegget her på bloggen! Det handler om forsterkningslæring, som jeg har brukt mye av fritiden min på i det siste.

Drømmen er å trene hele fotballag, 5 mot 5 eller 11 mot 11, og se om formasjoner, roller og spillestiler dukker opp av seg selv. Men først må det virke i det små, så denne første uka har handlet om 2 mot 2.

## Oppsett

Miljøet er football-scenarioet i [VMAS](https://github.com/proroklab/VectorizedMultiAgentSimulator), en enkel 2D-simulator. Et mål gir 100 poeng, og laget får litt ekstra drahjelp når ballen beveger seg mot riktig mål. Hver kamp varer i 500 steg.

Jeg trener med [BenchMARL](https://github.com/facebookresearch/BenchMARL), som bygger på [TorchRL](https://github.com/pytorch/rl), på én GPU, med 4096 kamper som går parallelt. Motstanderen er en ferdigskrevet bot som jeg skrur gradvis opp fra stillestående til full styrke, 50M frames per trinn. Uten den trappen kom laget aldri i gang.

Det viktigste jeg lærte om hastighet: det er antall oppdateringer som begrenser, ikke mengden data. Store batcher med høyere læringsrate ga mest igjen per time.

## PPO, IPPO og MAPPO

Jeg har prøvd alle tre. De bygger på samme PPO-oppskrift, men skiller seg i hvem som lærer og hva kritikeren får se:

- **PPO:** én agent som lærer alene. Det brukte jeg i 1 mot 1.
- **IPPO:** hver spiller lærer som om medspilleren bare er en del av miljøet. Begge deler samme nettverk og vet hvilken spiller de er.
- **MAPPO:** som IPPO, men kritikeren ser hele laget under trening.

IPPO og MAPPO gikk gjennom samme trapp side om side, med én kjøring hver. Tallene er snittpoeng per kamp mot slutten av hvert trinn:

| Botens styrke | IPPO | MAPPO |
|---------------|------|-------|
| 0             | 112  | 103   |
| 0,25          | 114  | 112   |
| 0,5           | 109  | 106   |
| 0,75          | 56   | 38    |
| 1,0           | −4   | −27   |

IPPO var like god eller bedre hele veien. Det gir mening: hver spiller ser allerede hele banen, så MAPPOs kritiker får ikke vite noe nytt.

![2 mot 2 mot boten på full styrke](/assets/blogg/fotball/2v2-mot-skriptet.gif)

## 0–0 som strategi

Botten spilte så mot seg selv en del. Det endte nesten alltid 0–0:

![Hvert lag parkerer en spiller i motstanderens mål](/assets/blogg/fotball/selvspill-parkering.gif)

Dette var veldig gøy å se! Samtidig veldig frustrerende. Det satte i gang en god del graving. Dermed bygde jeg en haug logging og tooling for å sjekke sunn læring og slike handlingsmønstre under trening.

## Neste kamp

Nå kjører en liga, inspirert av AlphaStar. Hovedlaget får selskap av en motstander som bare har én jobb: å finne svakhetene dets. Håpet er at det tvinger lagene ut av 0–0-strategien.

Virker det, blir det mer undersøkning om hvorfor og hvordan. Samt kanskje ett blogginnnlegg til.
Takk for kampen.

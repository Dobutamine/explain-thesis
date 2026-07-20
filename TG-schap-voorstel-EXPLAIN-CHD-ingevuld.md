# Voorstel Technisch Geneeskundige vraagstelling — M3 TG-schap

**Ingevuld voorstel bij formulier V2026-02 (UT, Technische Geneeskunde).**
Onderwerp: patiënt-specifieke modellering van congenitale hartafwijkingen met het EXPLAIN-model + AI-calibratiepijplijn.

> Tekst hieronder is direct over te nemen in het PDF-formulier. Velden met ‹…› vul je zelf in.

---

## Pagina 1 — Aanbod en contactgegevens

**Dit TG vraagstuk wordt aangeboden voor:** ☑ M3 · ☑ Regulier TG-schap
*(M2, Bedrijfsstage en Buitenland: niet aankruisen.)*

**Contactgegevens**

| | |
|---|---|
| Instelling | ‹ziekenhuis› |
| Afdeling | Kindercardiologie |
| Contactpersoon* | ‹naam› — Functie: ‹…› — e-mail: ‹…› |

| Rol | Naam | Functie | e-mail |
|---|---|---|---|
| Technisch Geneeskundig begeleider (TGB) | ‹klinisch technoloog / TG'er› | ‹…› | ‹…› |
| Medisch Begeleider (MB) | ‹kindercardioloog› | kindercardioloog | ‹…› |
| Technologisch begeleider UT (TB-UT) | ‹naam UT-vakgroep› | ‹…› | ‹…› |
| Technologisch begeleider instelling (TB-I) | T. Antonius | ontwikkelaar EXPLAIN-model | ‹…› |

*TB-UT is verplicht voor M3 (neemt deel aan de afstudeercommissie). TB-I is optioneel maar aanbevolen.*

---

## Pagina 2 — Categorie, titel, omschrijving

**Categorie:** ☑ Innovatie · ☑ Onderzoek

**Titel:**
Patiënt-specifieke modellering van kritische congenitale hartafwijkingen met het EXPLAIN-model en AI-calibratie.

**Omschrijving** *(± 120 woorden):*

**Achtergrond.** Kritische congenitale hartafwijkingen (o.a. HLHS, transpositie, pulmonalisatresie, coarctatie, TAPVC) zijn ductus- en/of foramen-ovale-afhankelijk: de pasgeborene decompenseert zodra dit kanaal na de geboorte sluit. De balans tussen pulmonale en systemische circulatie (Qp:Qs) is hierbij beslissend, maar per patiënt lastig te kwantificeren.

**Technologie.** EXPLAIN is een real-time, gesloten-lus neonataal fysiologiemodel. Een AI-calibratiepijplijn stelt het model patiënt-specifiek in: een large language model vertaalt klinische data naar meetbare targets, waarna een deterministische calibratielaag het model daarop fit.

**Opdracht.** De student maakt per CHD-patiënt een 'digital twin' en toetst of de modeluitkomsten (shunts, saturatiesplit, Qp:Qs, drukken) overeenkomen met prospectief gemeten echo-, bloedgas- en saturatiewaarden, inclusief het effect van PGE1 en septostomie. Dit is de eerste validatie tegen individuele patiëntgegevens.

---

## Pagina 3 — Klinische ontwikkeling, voorbehouden handelingen, opmerkingen

**Klinische ontwikkeling.**
De student participeert op de afdeling kindercardiologie in klinische activiteiten die direct aansluiten bij het vraagstuk: echocardiografie van neonaten/zuigelingen met (verdenking op) kritische CHD (beeldacquisitie en interpretatie van shunts, kleprestricties, ventrikelfunctie), hartkatheterisatie (diagnostisch en interventioneel, o.a. ballon-atrioseptostomie), pre-/post-ductale saturatiescreening, hemodynamische monitoring en peri-interventionele beoordeling, en het prostaglandine-E1-management van ductus-afhankelijke lesies. Zo koppelt de student de klinische meetgegevens die het model voeden (echo-shunt/druk, bloedgas, saturaties) rechtstreeks aan de patiënten die worden gemodelleerd, en doet hij/zij de voor de BIG-registratie vereiste klinische ervaring op.

**Voorbehouden handelingen (aankruisen):**
☑ Katheterisaties · ☑ Injecties · ☑ Puncties · ☑ Handelingen met ioniserende straling
☐ Heelkundige handelingen *(niet aankruisen tenzij van toepassing op de afdeling)*

**Overige opmerkingen.**
- Ethiek/METC: het project gebruikt prospectief te verzamelen individuele patiëntgegevens; vóór start wordt de benodigde METC-/ethische toetsing en het informed-consent-traject geregeld. De haalbaarheid hiervan binnen de 40-weekse periode wordt vroeg getoetst; eventueel wordt gestart met retrospectieve/pilotdata terwijl de prospectieve inclusie loopt.
- Samenwerking: conform de M3-opzet is er een technologische UT-vakgroep als partner (TB-UT in de afstudeercommissie); vanuit de instelling is de EXPLAIN-modelontwikkelaar als technologisch begeleider betrokken.
- Open en reproduceerbaar: het EXPLAIN-model is open source (`explain-modeling.com`, `github.com/Dobutamine/explain-engine`, Zenodo `10.5281/zenodo.21389097`).
- Afbakening: dit is een goed afgebakende uitbreiding van bestaand, tegen literatuur gevalideerd werk — de bijdrage is de eerste validatie tegen individuele, prospectief gemeten CHD-patiënten.

*Indienen ter goedkeuring: mailen naar TGstageTNW@utwente.nl.*

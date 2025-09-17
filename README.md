# Welcome to Aux Bourses, Citoyens! 

This is a quick markdown to show how the component pieces of the _**Aux Bourses, Citoyens!**_ (ABC) are related.
![Logo for ABC](assets/aux_bourses_citoyens.png)

## Introduction
ABC is an ecosystem of R packages that implement a customised, end-to-end financial data pipeline from raw extracts to analysis for... well... my personal use.
It is modularised and has grown over the past few years as I get time to extend functionality on this. 

## Modules (in functional order)

### budgetGeneraux
budgetGeneraux is a module that creates yearly budgeting "manifests". This is the newest component to the ecosystem. Basically I want to describe spends per accounts at the beginning of the year, import it into eclairCitoyen (?) and track ongoing expenses against planned expenses. This is a work in progress, and is not part of the the bi-monthly pipeline.

### nettoieCaisse
nettoieCaisse is an R package that implements custom data cleaning steps for the various fin institutions that I have accounts with. Unfortunately, none of them provide direct API access to my banking records, so I have to still download extracts bi-monthly. This package takes care of (most) of the data cleaning that needs to happen everytime I run the pipeline.

### inspecteurDepense
inspecteurDepense houses an R ML model that scores each entry to allocate to a spending category. The model training occurs once in a while -- when there has been enough model drift/accuracy starts falling. The model itself can be served two ways: as an API via plumber, or sourcing all objects directly within the cleaning pipeline. 

### integraFin
integraFin is basically the orchestrator when I run the pipeline. It calls the various other projects/modules as necessary. Also has some basic scripts here as porcelain. Finally, (and importantly),integraFin writes to a long-lived sqlite database that has validated financial data that's been cleaned, and enriched.

### eclairCitoyen
eclairCitoyen is an R Shiny dashboard that reads from the financial database, and implements visuals that I find helpful.


---

### Visually:

... if it doesn't render, it's likely GitHub support for mermaid is not where it needs to be. [See here](https://www.mermaidchart.com/play#pako:eNqVklFr2zAUhf-Kaih0EDuL50Dqh7I27UofBoPtzS5Blm7sC8qVKslLSul_n-S4wdteWj_I9577SefI-CURWkJSJmma1uTRKyjZdX9gN7q3DtyMrdHrZyDHzmoaoK3Se9Fx69mvm5pqOj9nt7BFQo-aXE07LTeLqullC_4eCCzvD49HOa-QPLSWf0MapS9BcgaEh97egglGME6KisB7jbDm6E7qsgKhONoxVlBlU10E-43knjfcQeaeFHr49DiG-9k3wdF0IZobS7ZWwEMyt7kji6KL1cMxmdc2gGx4hnzTppg2eTx_ArI0vXobTLZMZSAZN51iXBNXzw7-dlz-z_1QnOgfbHHChlt-58YgtQF5g1maZcFaNuO3H4LETjZDOTEPVlzBAW0YSL0npbl0kRHud5BwZ7T1k3vUFAZfXxju2pLVSee9ceV8LiSlKMJPkBpqs63iPnaZ0Lv5cpHPV3lxXC5XRRaIOpkxo108oYl1V7Ll5xnbx9frJNSHjYpFvhqWvHiPUfL6B38lA74).

```mermaid
---
title: Aux Bourses, Citoyens !
---
flowchart TB

%% Definitions
mod_1[budgetGeneraux]
mod_2[integraFin]
mod_3[inspecteurDepense]
mod_4[nettoieCaisse]
mod_5[eclairCitoyen]
db[(fin_database.sqlite)]

%% Subgraphs
subgraph Cleaners_Enrichers_Integrators
      mod_3
      mod_4
      mod_2

      mod_3 --> mod_2
      mod_4 --> mod_2
end

subgraph Analysers
      mod_5
end

subgraph Planners
      mod_1
end


%% Mappings
Planners -..-> db
mod_2 --> db
db --> Analysers

salexir --downloads--> csv --import--> mod_2

csv@{ img: "https://cdn-icons-png.flaticon.com/512/8242/8242984.png", pos: "b", h: 50, w: 50}

salexir@{ img: "https://cdn-icons-png.flaticon.com/512/4128/4128244.png", pos: "b", h: 50, w: 50}

```

Viz attribution: 
 + [CSV](https://www.flaticon.com/free-icons/csv)
 + [Person](https://www.flaticon.com/free-icons/person)

 
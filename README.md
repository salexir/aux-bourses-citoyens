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
integraFin is basically the orchestrator when I run the pipeline. It calls the various other projects/modules as necessary. Some basic scripts here as porcelain. Finally, (and importantly),integraFin writes to a long-lived sqlite database that has validated financial data that's been cleaned, and enriched.

### eclairCitoyen
eclairCitoyen is an R Shiny dashboard that reads from the financial database, and implements visuals that I find helpful.

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
subgraph Cleaners, Enrichers, Integrators
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

salem --> csv --> mod_2

salem@{ icon: "fa:user", pos: "b", h: 1}

csv@{ icon: "fa:file"}

```

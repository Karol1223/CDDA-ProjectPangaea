# Contributing guide - or what to do when adding a new critter - **NOTE THAT THIS GUIDE IS HIGHLY WIP, AND SO FAR ONLY HANDLES PARTS OF THE PROCESS!**

Use the `Home` key to return to the top.

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [Introduction](#introduction)
- [Fossils and DNA sequences](#fossils-and-dna-sequences)
- [Tissue samples](#tissue-samples)
- [Full genomes](#full-genomes)
- [Cloning](#cloning)
- [Monster definition](#monster-definition)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Introduction
This document aims to explain and organize the process of adding a new creature to the mod, be it extinct or extant. If you wish to add the former, start at [Fossils and DNA sequences](#fossils-and-dna-sequences), if you wish to start the latter, or add the process to an existing vanilla animal, start at [Tissue samples](#tissue-samples)

# Fossils and DNA sequences
When adding an extinct species, the first step is to add a DNA sequence item. Those typically look something like this:

```json
  {
    "type": "GENERIC",
    "id": "genome_piece_xiphactinus",
    "name": { "str": "DNA sequence (Xiphactinus audax)", "str_pl": "DNA sequences (Xiphactinus audax)" },
    "description": "Pieces of genetic coding from the genome of Xiphactinus audax implanted on a genome canvas.",
    "copy-from": "genome_canvas"
  },
```

For the most part the only thing that needs to be edited here is the item ID, name, and description. Scientific names in those items are mandatory. If the species happens to have a common name, it should be mentioned in the description as in the examples shown in [Tissue samples](#tissue-samples) or [Full genomes](#full-genomes).

Once that is done, you should add the DNA sequence to the itemgroup corresponding to the geological period the species was alive in. **If the species was alive during two geological periods, add the DNA sequence to both** - for example, *Coelophysis bauri* has fossil record from both Late Triassic and Early Jurassic, so it is included in both itemgroups.

In case there is no itemgroup corresponding to the geological period, you should add one alongside adding a new fossil item. This process is more complicated, as it requires you to touch EoC.
- first, add a new fossil item in ``items\fossil.json``, by simply copying one of the existing fossils and changing the name, description, and item ID
- afterwards, create the new corresponding itemgroup in ``itemgroups\genome_piece.json``
- go to ``effectoncondition\fossil_genome_piece.json`` and add a new fossil analysis EoC, simply by copying one of the existing ones (located at the top of the file) and swapping the item and itemgroup IDs around
- go to ``recipes\fossil_genome_piece.json`` and add a new recipe for analyzing the new fossil items, by copying one of the existing recipes that do so
- in ``effectoncondition\recipe_unlocks\fossil_genome_piece.json`` add a new learning EoC corresponding to the recipe you added earlier
- finally, add the new learning EoC to the list of true EoCs in ``effectoncondition\recipe_unlocks\abstract.json``

Once all this is done, you have finished the first step and are ready to move further.

# Tissue samples

Tissue samples serve primarily as an entryway to introducing extant flora and fauna into the genetics aspect of the mod. Having said that, they are added for all extinct creatures as well, as a form of precaution in case on day we may want to add the extinct creatures in some unique mapgen pieces. 
Adding the item itself is very simple:

```json
  {
    "type": "COMESTIBLE",
    "id": "tissue_sample_goose_canadian",
    "name": { "str": "tissue sample (Branta canadensis)", "str_pl": "tissue samples (Branta canadensis)" },
    "description": "A piece of tissue harvested from an animal.  You should either make use of it or freeze it quickly.  This one comes from Branta canadensis, or a Canada goose if you prefer English.",
    "copy-from": "tissue_sample_animal_base"
  },
```

You'll find that most items that need to be added in this guide have an abstract or otherwise base form they copy from, and only need edits for the name, description, and item ID. In this specific case, however, you will also need to make sure you're copying from the correct base item, as animal and plant samples use different bases.

Once you have the item itself added, you need to look into EoC yet again, however.
- Adding a sample corresponding to a monster, you need to add new parts to ``EOC_SAMPLER_DISSECTION_EVENT``. This is relatively simple. Copy one of the many parts of the effect from that EoC, and switch around monster and item IDs in it.
- Adding a sample corresponding to furniture or terrain, you need to add new parts to ``EOC_SAMPLER_IMPL``. This is simple as well, even if it looks more complicated at first glance. Copy one of the many parts of the effect from that EoC, and switch around furniture/terrain and item IDs in it. Despite looking differently, the general process is identical to how it is done with monsters.

# Full genomes

Complete genomes serve as a tool in all of cloning recipes, and in general the player will never need more than one of each creature the genome corresponds to.
Similarly to all other items, adding it is simple and relies on copying from a base:

```json
  {
    "type": "GENERIC",
    "id": "genome_lab_rat",
    "name": { "str": "genome (Rattus norvegicus) (Lab Rat)", "str_pl": "genomes (Rattus norvegicus) (Lab Rat)" },
    "description": "The full genome of Rattus norvegicus, or the brown rat, specifically of the lab variety, implanted on a genome canvas.",
    "copy-from": "genome_canvas"
  },
```

Here you will find another nuance of naming - if a scientific name corresponds to multiple phenotypes, the type of phenotype should be mentioned in the name and description as well. This is particularly important with, for example, dog breeds.

As with both previous steps, however, it doesn't end this soon. For starters, you need to add a recipe to create that full genome.

Examples to copy from will be either in ``recipes\genome\fossil_genome_piece.json``, if you want to complete it from DNA sequences, or ``recipes\genome\sample`` if you want it to be analyzed from a tissue sample.

All of those recipes are to follow the below table for values to use in specific fields:


| Source                                                            | ``difficulty`` | ``time`` | ``using``                      |
| ---                                                               | ---            | ---      | ---                            |
| Prokaryotic microorganisms                                        | ``1``          | ``5 m``  | ``analysis_microorganism``     |
| Simple eukaryotes (single-celled algae, fungi, protozoa)          | ``2``          | ``5 m``  | ``analysis_eukaryote_simple``  |
| Plants, multicellular fungi, or algae                             | ``3``          | ``10 m`` | ``analysis_flora``             |
| Simple invertebrates (cnidarians, sponges, annelids, echinoderms) | ``4``          | ``15 m`` | ``analysis_invert_simple``     |
| Complex invertebrates (arthropods, mollusks, tunicates)           | ``5``          | ``20 m`` | ``analysis_invert_complex``    |
| All classes of fish                                               | ``6``          | ``25 m`` | ``analysis_fish``              |
| Amphibians, including Tiktaalik                                   | ``7``          | ``30 m`` | ``analysis_amphibian``         |
| Reptiles, including pterosaurs                                    | ``8``          | ``35 m`` | ``analysis_reptile``           |
| Non-avian dinosaurs (including paravians), non-mammal synapsids   | ``9``          | ``40 m`` | ``analysis_dinosaur_synapsid`` |
| Birds, mammals                                                    | ``10``         | ``45 m`` | ``analysis_bird_mammal``       |

Once you have a recipe added - the fun doesn't stop there! You also need to include an EoC or two to allow for the recipe to be learned by the players! Typically, the same EoC will handle learning the recipes for both the recipe and the cloning process - at this point use a placeholder ID for whatever recipe you want the cloning process to represent, then come back to this once you've actually made that recipe.

The process is near identical to how it was handled in [Fossils and DNA sequences](#fossils-and-dna-sequences), but it's also important to note:
- It is possible to unlock up to two recipes from a single input item - typically those being the complete genome and the cloning PROCESS
- It is NOT possible to have one EoC with multiple input items. For that you will need to make multiple EoCs that handle this. Look at the list of analysis EoCs and you'll find this is common

# Cloning

**THIS SECTION IS ENTIRELY WIP, AS ONLY A FEW ASPECTS OF THE SYSTEM HAVE BEEN IMPLEMENTED IN THE MOD SO FAR!**

| Source                                                            | ``difficulty`` | ``time`` | ``using``                     |
| ---                                                               | ---            | ---      | ---                           |
| Prokaryotic microorganisms                                        | ``1``          | ``1 s``  | ``cloning_microorganism``     |
| Simple eukaryotes (single-celled algae, fungi, protozoa)          | ``2``          | ``1 s``  | ``cloning_eukaryote_simple``  |
| Plants, multicellular fungi, or algae                             | ``3``          | ``15 m`` | ``cloning_flora``             |
| Simple invertebrates (cnidarians, sponges, annelids, echinoderms) | ``4``          | ``45 m`` | ``cloning_invert_simple``     |
| Complex invertebrates (arthropods, mollusks, tunicates)           | ``5``          | ``75 m`` | ``cloning_invert_complex``    |
| All classes of fish                                               | ``6``          | ``2 h``  | ``cloning_fish``              |
| Amphibians, including Tiktaalik                                   | ``7``          | ``3 h``  | ``cloning_amphibian``         |
| Reptiles, including pterosaurs                                    | ``8``          | ``5 h``  | ``cloning_reptile``           |
| Non-avian dinosaurs (including paravians), non-mammal synapsids   | ``9``          | ``7 h``  | ``cloning_dinosaur_synapsid`` |
| Birds, mammals                                                    | ``10``         | ``10 h`` | ``cloning_bird_mammal``       |

# Monster definition

***TODO***

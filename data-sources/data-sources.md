# Data sources

We collect evidence \(E\) from various data sources \(e.g. Reactome, SLAPenrich, PROGENy, CRISPR, and SysBio\) to allow for target identification and prioritisation when using the [Open Targets Platform](https://www.targetvalidation.org/). 

To associate targets \(T\) with diseases \(D\), we group similar data sources into broader categories called data types:

* [Genetic associations](https://docs.targetvalidation.org/data-sources/genetic-associations)
* [Somatic mutations](https://docs.targetvalidation.org/data-sources/somatic-mutations)
* [Drugs](https://docs.targetvalidation.org/data-sources/drugs)
* [Pathways & systems biology](https://docs.targetvalidation.org/data-sources/affected-pathways)
* [RNA expression](https://docs.targetvalidation.org/data-sources/rna-expression)
* [Text mining](https://docs.targetvalidation.org/data-sources/text-mining)
* [Animal models](https://docs.targetvalidation.org/data-sources/animal-models)

![](../.gitbook/assets/data_sources_may18%20%281%29.jpg)

### Evidence data sources

| Data type category | Data source | Reference\(s\) |
| :--- | :--- | :--- |
| Genetic associations | [Open Targets Genetics Portal](https://genetics.opentargets.org/) | [Mountjoy E. et al, 2020](https://www.biorxiv.org/content/10.1101/2020.09.16.299271v2) |
| Genetic associations | [PheWAS Catalog](https://phewascatalog.org/) | [Denny, J. et al, 2013](https://doi.org/10.1038/nbt.2749) |
| Genetic associations | [ClinVar](https://www.ncbi.nlm.nih.gov/clinvar/) \(via [EVA](https://www.ebi.ac.uk/eva/)\) | [Cook, C. et al, 2016](https://doi.org/10.1093/nar/gkv1352); [Landrum, M. et al, 2017](http://doi.org/10.1093/nar/gkx1153) |
| Genetic associations | [Genomics England PanelApp](https://panelapp.genomicsengland.co.uk/) | [Martin, A. et al, 2019](https://doi.org/10.1038/s41588-019-0528-2) |
| Genetic associations | [Gene2Phenotype](https://www.ebi.ac.uk/gene2phenotype) | [Thormann, A. et al, 2019](https://doi.org/10.1038/s41467-019-10016-3) |
| Genetic associations | [UniProt](https://www.uniprot.org/) | [The UniProt Consortium, 2019](https://doi.org/10.1093/nar/gky1049) |
| Known drugs | [ChEMBL](https://www.ebi.ac.uk/chembl/) | [Mendez, D. et al, 2019](https://academic.oup.com/nar/article/47/D1/D930/5162468) |
| Pathways & Sys Bio | [Reactome](https://reactome.org/) | [Jassal, B. et al, 2020](http://doi.org/10.1093/nar/gkz1031) |
| Pathways & Sys Bio | CRISPR \(via[ Project Score](https://score.depmap.sanger.ac.uk/)\) | [Behan, F. et al, 2019](https://doi.org/10.1038/s41586-019-1103-9) |
| Pathways & Sys Bio | [SLAPenrich](https://saezlab.github.io/SLAPenrich/) | [Iorio, F. et al, 2018](https://doi.org/10.1038/s41598-018-25076-6) |
| Pathways & Sys Bio | SysBio | [Peters, L. A. et al, 2017](https://doi.org/10.1038/ng.3947); [Huan, T. et al, 2013](https://doi.org/10.1161/atvbaha.112.300112); [Zhang, B. et al, 2013](https://doi.org/10.1016/j.cell.2013.03.030); [Mostafavi, S. et al, 2018](https://doi.org/10.1038/s41593-018-0154-9) |
| Pathways & Sys Bio | [PROGENy](https://saezlab.github.io/progeny/) | [Schubert, M. et al, 2018](https://doi.org/10.1038/s41467-017-02391-6) |
| RNA expression | [Expression Atlas](https://www.ebi.ac.uk/gxa/home) | [Papatheodorou, I. et al, 2020](https://doi.org/10.1093/nar/gkz947) |
| Somatic mutations | [Cancer Gene Census](https://cancer.sanger.ac.uk/census) | [Sondka, Z. et al, 2018](https://doi.org/10.1038/s41568-018-0060-1) |
| Somatic mutations | [intOGen](http://www.intogen.org/search) | [Martínez-Jiménez, F. et al, 2020](https://doi.org/10.1038/s41568-020-0290-x) |
| Somatic mutations | [ClinVar](https://www.ncbi.nlm.nih.gov/clinvar/) \(via [EVA](https://www.ebi.ac.uk/eva/)\) | [Cook, C. et al, 2016](https://doi.org/10.1093/nar/gkv1352); [Landrum, M. et al, 2017](http://doi.org/10.1093/nar/gkx1153) |
| Literature | [Europe PMC](http://europepmc.org/) | [The Europe PMC Consortium, 2015](https://doi.org/10.1093/nar/gku1061); [Kafkas, Ş. et al, 2017](http://doi.org/10.1186/s13326-017-0131-3) |
| Animal Models | [Phenodigm](https://www.sanger.ac.uk/tool/phenodigm/) | [Smedley, D. et al, 2013](https://doi.org/10.1093/database/bat025) |

Evidence from an individual data source can contribute to different data types. For example, data from ClinVar from EVA is used as evidence for both Genetic associations and Somatic mutations, depending on whether the evidence is germline or somatic mutation.

### Entity annotation data sources

| Entity | Annotation data | Data source |
| :--- | :--- | :--- |
| Target | Protein, positional, and structural information; functional annotation; Gene Ontology | [UniProt](https://www.uniprot.org/) |
| Target | Protein Interactions | [OmniPath](https://omnipathdb.org/) |
| Target | Pathways | [Reactome](https://reactome.org/) |
| Target | Baseline expression | [Expression Atlas](https://www.ebi.ac.uk/gxa/home); [GTEx](https://www.gtexportal.org/home/documentationPage); [Human Protein Atlas](http://www.proteinatlas.org/) |
| Target | Variants, isoforms, and genomic context | [Ensembl](https://www.ebi.ac.uk/gxa/home) |
| Target | Comparative genomics | [Ensembl Compara](https://www.ensembl.org/info/docs/api/compara/index.html) |
| Target | Mouse phenotypes | [MGI](http://www.informatics.jax.org/phenotypes.shtml) |
| Target | Cancer hallmarks | [Cancer Gene Census](https://cancer.sanger.ac.uk/census) |
| Target | Cancer biomarkers | [Cancer Genome Interpreter](https://www.cancergenomeinterpreter.org/biomarkers) |
| Target | Chemical Probes | [SGC](https://www.thesgc.org/chemical-probes); [Chemical Probes Portal](https://www.chemicalprobes.org/); [Open Science Probe Project](https://www.sgc-ffm.uni-frankfurt.de/); [Probe Miner](https://probeminer.icr.ac.uk/#/) |
| Target | Bibliography | [Open Targets LINK](http://link.opentargets.io/) |
| Target | Target tractability | [ChEMBL and others](https://docs.targetvalidation.org/getting-started/target-tractability) |
| Target | Target safety | [HeCaTos, Tox21, eTox, and others](https://docs.targetvalidation.org/getting-started/target-safety) |
| Target | Target Enabling Packages | [SGC](https://www.thesgc.org/tep) |
| Target | Drugs | [ChEMBL](https://www.ebi.ac.uk/chembl/) |
| Target | CRISPR-Cas9 cancer cell line dependency | [Project Score](https://score.depmap.sanger.ac.uk/) |
| Target | Protein structure | [PDBe](https://www.ebi.ac.uk/pdbe/) |
| Disease | Description; cross-references; synonyms; ontology and classification | [EFO](https://www.ebi.ac.uk/efo/) |
| Disease | Phenotypes | [EFO](https://www.ebi.ac.uk/efo/) |
| Disease | Bibliography | [Open Targets LINK](http://link.opentargets.io/) |
| Disease | Drugs | [ChEMBL ](https://www.ebi.ac.uk/chembl/) |
| Drug | Molecule information, including structure \(if available\), modality, withdrawal notice, and mechanism of action | [ChEMBL ](https://www.ebi.ac.uk/chembl/) |
| Drug | Clinical trial information | [ChEMBL](https://www.ebi.ac.uk/chembl/) |
| Drug | Bibliography | [Open Targets LINK](http://link.opentargets.io/) |








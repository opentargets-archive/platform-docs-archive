# REST API

{% hint style="danger" %}
**Important update - May 2021:**

With the launch of the [next-generation Platform](https://platform.opentargets.org/) in April 2021, we have deprecated our REST API in favour of a [new ways of accessing our data](https://platform-docs.opentargets.org/data-access) through a GraphQL API, Google BigQuery, and more comprehensive dataset downloads. 

For examples of the different ways that you can access our data — including sample scripts and outputs — please read [our data access documentation](https://platform-docs.opentargets.org/data-access) and join the [Open Targets Community](https://community.opentargets.org/). ****
{% endhint %}

The Open Targets Platform REST API allows language agnostic access to data available on the Open Targets Platform.

**https://platform-api.opentargets.io/v3/platform/&lt;method&gt;/&lt;parameter&gt;**

The Open Targets Platform REST API [endpoints](https://en.wikipedia.org/wiki/Web_API#Endpoints) can be divided in three methods:

* **Public** - Methods that serve the core set of data e.g. associations and evidence. These methods are stable and unlikely to change.
* **Private** - Methods that serve other data in the Open Targets Platform, such as the [target profile page](https://docs.targetvalidation.org/getting-started/getting-started/target-profile) and [batch search](https://docs.targetvalidation.org/getting-started/batch-search). These methods may not be stable from one release to the next. You should use these at your own risk.
* **Utils** - Methods to get statistics on our data and to check if the server is alive.

These are some of the [parameters](https://idratherbewriting.com/learnapidoc/docapis_doc_parameters.html) for the Open Targets Platform REST API :

* **target** - A target identifier listed as an Ensembl gene ID
* **disease** - A disease identifier listed as EFO, Orphanet, HP, or MONDO ID
* **datasource** - Data source used for target-disease associations
* **datatype** - Data type used for target-disease associations
* **format** - By default the results are returned in JSON but you can retrieve the results as CSV, TAB and XML \(depending on the endpoint\)
* **scorevalue\_min** - It can be used to filter associations or evidence according to a minimum score value. The default is 0. Scores above 0.2 is a good trade-off to filter lower quality data points
* **direct** - It could be true \(direct associations\) or false \(indirect associations\)

{% hint style="info" %}
Not sure how to apply the `direct` parameter in the REST API call? Check [Direct versus indirect evidence: should you care?](http://blog.opentargets.org/2017/04/25/direct-versus-indirect-evidence-should-you-care/) blog post for more information.
{% endhint %}

A typical call can be broken down as follows:

|  |  |
| :--- | :--- |
| Server | https://platform-api.opentargets.io/v3/platform/ |
| Endpoint | e.g. public/association/filter |
| Parameters | e.g. ?target=ENSG00000163914&size=10000&fields=target.id&fields=disease.id |

{% hint style="success" %}
Head to the [Swagger](https://platform-api.opentargets.io/v3/platform/docs/swagger-ui) interface for a list of all available endpoints,  parameters and filters.

You will also be able to test your queries in an interactive and easy manner before running your application/workflow.
{% endhint %}

Let's have a look at the following examples:`public/search`, `public/association` , `public/association/filter`, `public/evidence/filter`, and `private/besthitsearch`.

## The search endpoint

It looks up for the Ensembl gene ID or the disease ID \(EFO, HP, Orphanet, MONDO\) using a free text search. It should be used to identify the best match for a disease or target of interest, rather than gathering a specific set of evidence.

Searching for DMD, for example, will return its Ensembl gene ID, ENSG00000198947, in addition to other data, such as names and IDs of orthologues, association counts, approved name and much more. You need these stable IDs to query the REST API using the remaining endpoints.

{% api-method method="get" host="https://platform-api.opentargets.io/v3/platform/public/search?q=dmd" path=" " %}
{% api-method-summary %}
/public/search
{% endapi-method-summary %}

{% api-method-description %}
It allows you to look for gene or diseases of interest using a free text search, replicating the functionality of the search box in the Open Targets Platform website. It should be used to identify the best match for a disease or target, rather than gathering a specific set of evidence.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="" type="string" required=false %}
size
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
from: 0,
took: 9,
data_version: "20.04",
query: {
highlight: true,
fields: null,
datastructure: "default",
format: "json",
size: 10
},
total: 34,
data: [
{
data: {
ortholog: {},
top_associations: {
total: [],
direct: [
{
score: 1,
id: "ENSG00000198947-EFO_0000618"
},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{}
]
},
approved_symbol: "DMD",
description: "Anchors the extracellular matrix to the cytoskeleton via F-actin. Ligand for dystroglycan. Component of the dystrophin-associated glycoprotein complex which accumulates at the neuromuscular junction (NMJ) and at a variety of synapses in the peripheral and central nervous systems and has a structural function in stabilizing the sarcolemma. Also implicated in signaling events and synaptic transmission.",
uniprot_accessions: [],
drugs: {},
gene_family_description: "",
name_synonyms: [],
approved_name: "dystrophin",
hgnc_id: "HGNC:2928",
association_counts: {},
ensembl_gene_id: "ENSG00000198947",
symbol_synonyms: [],
biotype: "protein_coding",
full_name: "dystrophin",
type: "target",
id: "ENSG00000198947",
name: "DMD"
},
score: 3215.2693,
type: "search-object-target",
id: "ENSG00000198947",
highlight: {}
},
{},
{},
{},
{},
{},
{},
{},
{},
{}
],
size: 10
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

## The association endpoint

It retrieves the [association scores](https://docs.targetvalidation.org/getting-started/scoring) for an association between any target and disease, replicating the results seen on the website when viewing the associations for a target \(e.g. [NOD2](https://www.targetvalidation.org/target/ENSG00000167207/associations)\) or for a disease \(e.g. [Noonan syndrome](https://www.targetvalidation.org/disease/Orphanet_648/associations)\).

You can retrieve the association either by an ID \(in the format of `TARGET_ID-DISEASE_ID)` or you can also use the `association/filter` endpoint instead.

In addition to the association scores, you will also get the data and summary on each evidence type included in the calculation of the score.

{% api-method method="get" host="https://platform-api.opentargets.io/v3/platform/public/association?id=ENSG00000073756-EFO\_0003767    " path="" %}
{% api-method-summary %}
/public/association
{% endapi-method-summary %}

{% api-method-description %}
Get the association scores for one target-disease using the association ID, in the format of \`Ensembl Gene ID-disease ID\` e.g. \`ENSG00000073756-EFO\_0003767\`.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-query-parameters %}
{% api-method-parameter name="" type="string" required=true %}
association ID \(target ID-disease ID\)
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
from: 0,
took: 1,
data_version: "20.04",
query: { },
total: 1,
data: [
{
target: {},
association_score: {
datatypes: {
literature: 0.15225170516860842,
rna_expression: 0.011508835806466024,
somatic_mutation: 0,
genetic_association: 0.048921722918748856,
known_drug: 1,
animal_model: 0.2677812500000001,
affected_pathway: 0
},
overall: 1,
datasources: {
expression_atlas: 0.011508835806466024,
europepmc: 0.15225170516860842,
slapenrich: 0,
crispr: 0,
progeny: 0,
intogen: 0,
cancer_gene_census: 0,
eva_somatic: 0,
sysbio: 0,
gwas_catalog: 0,
uniprot_literature: 0,
phenodigm: 0.2677812500000001,
ot_genetics_portal: 0.048921722918748856,
chembl: 1,
genomics_england: 0,
phewas_catalog: 0,
eva: 0,
gene2phenotype: 0,
postgap: 0,
uniprot: 0,
reactome: 0,
uniprot_somatic: 0
}
},
max: {},
sum: {},
is_direct: true,
private: {},
disease: {},
evidence_count: {},
id: "ENSG00000073756-EFO_0003767"
}
],
size: 1
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

It retrieves the [association scores](https://docs.targetvalidation.org/getting-started/scoring) for an association between any target and disease, replicating the results seen on the website when viewing the associations for a target \(e.g. [NOD2](https://www.targetvalidation.org/target/ENSG00000167207/associations)\) or for a disease \(e.g. [Noonan syndrome](https://www.targetvalidation.org/disease/Orphanet_648/associations)\). You can retrieve the association either by an ID \(in the format of TARGET\_ID-DISEASE\_ID\) or you can also use the association/filter endpoint instead. In addition to the association scores, you will also get the data and summary on each evidence type included in the calculation of the score.

## The association endpoint, with filters

It allows for more complex and flexible queries to get association scores and objects that can be filtered by a therapeutic area of interest, targets linked to a given Reactome pathway ID \(e.g. R-HSA-375276\), as well as filtering results by score \(minimum and maximum\) and more.

{% api-method method="get" host="https://platform-api.opentargets.io/v3/platform/public/association/filter?target=ENSG00000073756&datasource=chembl&scorevalue\_min=0.99" path="" %}
{% api-method-summary %}
/public/association/filter
{% endapi-method-summary %}

{% api-method-description %}

{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="direct" type="string" required=false %}
true
{% endapi-method-parameter %}

{% api-method-parameter name="datasource" type="string" required=false %}
gene2phenotype
{% endapi-method-parameter %}

{% api-method-parameter name="datatype" type="string" required=false %}
genetic\_association
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
from: 0,
took: 52,
next: [
1.2029251,
"ENSG00000073756-OTAR_0000010"
],
data_version: "20.04",
therapeutic_areas: [
{},
{},
{},
{}
],
query: {
sort: [],
search: null,
rna_expression_level: 0,
protein_expression_tissue: [ ],
scorevalue_types: [],
datatype: [ ],
fields: null,
format: "json",
facets_size: null,
disease: [ ],
protein_expression_level: 0,
tractability: [ ],
datastructure: "default",
facets: "false",
rna_expression_tissue: [ ],
target: [
"ENSG00000073756"
],
target_class: [ ],
cap_scores: true,
pathway: [ ],
size: 10
},
total: 631,
data: [],
size: 10
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

## The evidence endpoint, with filters

It retrieves the evidence used for an association and allow for specific filters to be added, so that you can restrict the results by data type, data source, or limit the results to targets that are part of a given pathway.

{% hint style="info" %}
Alternative output formats, such `xml`, `csv` and `tab`, may be also  available for some of the methods e.g. [/association/filter](https://api.opentargets.io/v3/platform/docs/swagger-ui#/filter/getAssociationFilter) and [/search](https://api.opentargets.io/v3/platform/docs/swagger-ui#/search/getSearch).
{% endhint %}

{% api-method method="get" host="https://platform-api.opentargets.io/v3/platform/public/evidence/filter?target=ENSG00000088832&datasource=ot\_genetics\_portal&size=48" path="" %}
{% api-method-summary %}
/public/evidence/filter
{% endapi-method-summary %}

{% api-method-description %}

{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="datatype" type="string" required=false %}
genetic\_association, somatic\_mutation, known\_drug, etc.
{% endapi-method-parameter %}

{% api-method-parameter name="target" type="string" required=false %}
target ID as Ensembl gene ID
{% endapi-method-parameter %}

{% api-method-parameter name="size" type="integer" required=false %}
number of results returned
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text
{
from: 0,
took: 12,
next: [
0.005880695,
"d1199825e2ef39394f38e1223c5bc013"
],
data_version: "20.04",
query: {
sort: [
"scores.association_score"
],
format: "json",
fields: null,
datastructure: "default",
pathway: [ ],
size: 48
},
total: 48,
data: [
{
target: {},
validated_against_schema_version: "1.6.4",
sourceID: "ot_genetics_portal",
variant: {},
disease: {},
unique_association_fields: {},
evidence: {
evidence_codes_info: [
[],
[],
[
{
eco_id: "locus_to_gene_pipeline",
label: "Open Targets Genetics portal locus to gene annotation pipeline"
}
],
[]
],
variant2disease: {
gwas_sample_size: 350470,
reported_trait: "Mean platelet (thrombocyte) volume",
unique_experiment_reference: "http://europepmc.org/abstract/MED/0000",
study_link: "https://genetics.opentargets.org/study/NEALE2_30100_raw",
provenance_type: {
expert: {
status: true,
statement: "Primary submitter of the data"
},
database: {
dbxref: {
version: "2018-08-01T00:00:00",
id: "https://genetics.opentargets.org"
},
id: "ot_genetics_portal",
version: "2018-08-01T00:00:00"
}
},
is_associated: true,
resource_score: {
mantissa: 1,
type: "pvalue",
method: {
description: "pvalue for the snp to disease association"
},
value: 1.3632700000000002e-43,
exponent: -43
},
evidence_codes: [
"http://purl.obolibrary.org/obo/ECO_0000362",
"http://identifiers.org/eco/GWAS",
"http://purl.obolibrary.org/obo/ECO_0000362",
"http://identifiers.org/eco/locus_to_gene_pipeline"
],
date_asserted: "2018-08-01T00:00:00"
},
evidence_codes: [
"GWAS",
"ECO_0000362",
"locus_to_gene_pipeline",
"SO_0001628"
],
gene2variant: {
consequence_code: "intergenic_variant",
functional_consequence: "http://purl.obolibrary.org/obo/SO_0001628",
provenance_type: {
expert: {
status: true,
statement: "Primary submitter of the data"
},
database: {
dbxref: {
version: "2018-08-01T00:00:00",
id: "https://genetics.opentargets.org"
},
id: "ot_genetics_portal",
version: "2018-08-01T00:00:00"
}
},
is_associated: true,
resource_score: {
type: "locus_to_gene_score",
method: {
description: "Locus to gene score generated by OpenTargets Genetics portal"
},
value: 0.175727978348732
},
evidence_codes: [
"http://purl.obolibrary.org/obo/ECO_0000362",
"http://identifiers.org/eco/locus_to_gene_pipeline"
],
date_asserted: "2018-08-01T00:00:00"
}
},
access_level: "public",
scores: {
association_score: 0.175727978348732
},
type: "genetic_association",
id: "29ecf7874f913f385e77eeba4ec41bca"
},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{},
{}
],
size: 48
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% hint style="success" %}
Note that multiple genes and diseases can be specified in the same request by using the `&` e.g.

[https://platform-api.opentargets.io/v3/platform/public/association/filter?target=ENSG00000100652&target=ENSG00000125255&target=ENSG00000126903&target=ENSG00000137571&target=ENSG00000205359](https://platform-api.opentargets.io/v3/platform/public/association/filter?target=ENSG00000100652&target=ENSG00000125255&target=ENSG00000126903&target=ENSG00000137571&target=ENSG00000205359)
{% endhint %}

For complex queries with large numbers of parameters, use  a `POST` request instead of `GET`. 

## The best hit search endpoint

This is a private endpoint that replicates [`public/search`](https://docs.targetvalidation.org/programmatic-access/rest-api#public-search)  but for multiple strings \(e.g. multiple gene symbols or IDs\) rather than one string only as it's a POST method.

{% hint style="danger" %}
`POST` methods require a body \(or payload\) encoded as`json`.
{% endhint %}

{% api-method method="post" host="https://platform-api.opentargets.io/v3/platform/private/besthitsearch?q=dmd" path="" %}
{% api-method-summary %}
/private/besthitsearch
{% endapi-method-summary %}

{% api-method-description %}

{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="" type="string" required=true %}
list of strings to search for
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{"data_version": "20.04", "data": [{"q": "dmd", "score": 48.67902, "highlight": null, "type": "search-object-target", "exact": false, "id": "MONDO_0010542", "data": {"top_associations": {"total": [{"score": 1.0, "id": "ENSG00000198947-MONDO_0010542"}, {"score": 1.0, "id": "ENSG00000188778-MONDO_0010542"}, {"score": 1.0, "id": "ENSG00000138735-MONDO_0010542"}, {"score": 1.0, "id": "ENSG00000159640-MONDO_0010542"}, {"score": 1.0, "id": "ENSG00000113580-MONDO_0010542"}, {"score": 1.0, "id": "ENSG00000169252-MONDO_0010542"}, {"score": 1.0, "id": "ENSG00000120907-MONDO_0010542"}, {"score": 1.0, "id": "ENSG00000171873-MONDO_0010542"}, {"score": 1.0, "id": "ENSG00000170214-MONDO_0010542"}, {"score": 1.0, "id": "ENSG00000043591-MONDO_0010542"}, {"score": 0.8952773333333333, "id": "ENSG00000151623-MONDO_0010542"}, {"score": 0.8341237902641296, "id": "ENSG00000103489-MONDO_0010542"}, {"score": 0.7854842461457698, "id": "ENSG00000091831-MONDO_0010542"}, {"score": 0.7335770726203918, "id": "ENSG00000175779-MONDO_0010542"}, {"score": 0.531496597929655, "id": "ENSG00000090006-MONDO_0010542"}, {"score": 0.43036705255508423, "id": "ENSG00000160410-MONDO_0010542"}, {"score": 0.3252577904533644, "id": "ENSG00000138379-MONDO_0010542"}, {"score": 0.32511049777344064, "id": "ENSG00000196569-MONDO_0010542"}, {"score": 0.32194549504869485, "id": "ENSG00000100380-MONDO_0010542"}, {"score": 0.3153471044513436, "id": "ENSG00000155657-MONDO_0010542"}], "direct": [{"score": 1.0, "id": "ENSG00000198947-MONDO_0010542"}]}, "efo_path_labels": [["musculoskeletal or connective tissue disease", "musculoskeletal system disease", "muscular disease", "Genetic neuromuscular disease", "Qualitative or quantitative protein defects in neuromuscular diseases", "Qualitative or quantitative defects of dystrophin", "Familial isolated dilated cardiomyopathy", "dilated cardiomyopathy 3B"], ["musculoskeletal or connective tissue disease", "musculoskeletal system disease", "muscular disease", "muscle tissue disease", "cardiomyopathy", "familial cardiomyopathy", "Familial dilated cardiomyopathy", "Familial isolated dilated cardiomyopathy", "dilated cardiomyopathy 3B"], ["musculoskeletal or connective tissue disease", "musculoskeletal system disease", "muscular disease", "muscle tissue disease", "cardiomyopathy", "intrinsic cardiomyopathy", "dilated cardiomyopathy", "Familial dilated cardiomyopathy", "Familial isolated dilated cardiomyopathy", "dilated cardiomyopathy 3B"], ["respiratory or thoracic disease", "thoracic disease", "heart disease", "cardiomyopathy", "familial cardiomyopathy", "Familial dilated cardiomyopathy", "Familial isolated dilated cardiomyopathy", "dilated cardiomyopathy 3B"], ["respiratory or thoracic disease", "thoracic disease", "heart disease", "cardiomyopathy", "intrinsic cardiomyopathy", "dilated cardiomyopathy", "Familial dilated cardiomyopathy", "Familial isolated dilated cardiomyopathy", "dilated cardiomyopathy 3B"], ["respiratory or thoracic disease", "thoracic disease", "heart disease", "Rare genetic cardiac disease", "Familial dilated cardiomyopathy", "Familial isolated dilated cardiomyopathy", "dilated cardiomyopathy 3B"], ["genetic, familial or congenital disease", "genetic disorder", "familial cardiomyopathy", "Familial dilated cardiomyopathy", "Familial isolated dilated cardiomyopathy", "dilated cardiomyopathy 3B"], ["genetic, familial or congenital disease", "genetic disorder", "Rare genetic neurological disorder", "Genetic neuromuscular disease", "Qualitative or quantitative protein defects in neuromuscular diseases", "Qualitative or quantitative defects of dystrophin", "Familial isolated dilated cardiomyopathy", "dilated cardiomyopathy 3B"], ["genetic, familial or congenital disease", "genetic disorder", "Rare genetic cardiac disease", "Familial dilated cardiomyopathy", "Familial isolated dilated cardiomyopathy", "dilated cardiomyopathy 3B"], ["cardiovascular disease", "heart disease", "cardiomyopathy", "familial cardiomyopathy", "Familial dilated cardiomyopathy", "Familial isolated dilated cardiomyopathy", "dilated cardiomyopathy 3B"], ["cardiovascular disease", "heart disease", "cardiomyopathy", "intrinsic cardiomyopathy", "dilated cardiomyopathy", "Familial dilated cardiomyopathy", "Familial isolated dilated cardiomyopathy", "dilated cardiomyopathy 3B"], ["cardiovascular disease", "heart disease", "Rare genetic cardiac disease", "Familial dilated cardiomyopathy", "Familial isolated dilated cardiomyopathy", "dilated cardiomyopathy 3B"], ["nervous system disease", "Rare genetic neurological disorder", "Genetic neuromuscular disease", "Qualitative or quantitative protein defects in neuromuscular diseases", "Qualitative or quantitative defects of dystrophin", "Familial isolated dilated cardiomyopathy", "dilated cardiomyopathy 3B"]], "description": "Any dilated cardiomyopathy in which the cause of the disease is a mutation in the DMD gene.", "drugs": {"evidence_data": []}, "efo_synonyms": ["X-linked dilated cardiomyopathy", "dilated cardiomyopathy caused by mutation in DMD", "cardiomyopathy, dilated, X-linked", "CMD3B", "DMD dilated cardiomyopathy", "cardiomyopathy, dilated, type 3B", "DMD-related dilated cardiomyopathy", "cardiomyopathy, dilated, 3B; CMD3B", "cardiomyopathy, dilated, 3B", "dilated cardiomyopathy type 3B"], "efo_definition": "Any dilated cardiomyopathy in which the cause of the disease is a mutation in the DMD gene.", "efo_path_codes": [["OTAR_0000006", "EFO_0009676", "EFO_0002970", "Orphanet_183497", "Orphanet_207049", "Orphanet_207085", "Orphanet_154", "MONDO_0010542"], ["OTAR_0000006", "EFO_0009676", "EFO_0002970", "MONDO_0003939", "EFO_0000318", "EFO_0002945", "Orphanet_217607", "Orphanet_154", "MONDO_0010542"], ["OTAR_0000006", "EFO_0009676", "EFO_0002970", "MONDO_0003939", "EFO_0000318", "MONDO_0000591", "EFO_0000407", "Orphanet_217607", "Orphanet_154", "MONDO_0010542"], ["OTAR_0000010", "MONDO_0000651", "EFO_0003777", "EFO_0000318", "EFO_0002945", "Orphanet_217607", "Orphanet_154", "MONDO_0010542"], ["OTAR_0000010", "MONDO_0000651", "EFO_0003777", "EFO_0000318", "MONDO_0000591", "EFO_0000407", "Orphanet_217607", "Orphanet_154", "MONDO_0010542"], ["OTAR_0000010", "MONDO_0000651", "EFO_0003777", "Orphanet_98054", "Orphanet_217607", "Orphanet_154", "MONDO_0010542"], ["OTAR_0000018", "EFO_0000508", "EFO_0002945", "Orphanet_217607", "Orphanet_154", "MONDO_0010542"], ["OTAR_0000018", "EFO_0000508", "Orphanet_71859", "Orphanet_183497", "Orphanet_207049", "Orphanet_207085", "Orphanet_154", "MONDO_0010542"], ["OTAR_0000018", "EFO_0000508", "Orphanet_98054", "Orphanet_217607", "Orphanet_154", "MONDO_0010542"], ["EFO_0000319", "EFO_0003777", "EFO_0000318", "EFO_0002945", "Orphanet_217607", "Orphanet_154", "MONDO_0010542"], ["EFO_0000319", "EFO_0003777", "EFO_0000318", "MONDO_0000591", "EFO_0000407", "Orphanet_217607", "Orphanet_154", "MONDO_0010542"], ["EFO_0000319", "EFO_0003777", "Orphanet_98054", "Orphanet_217607", "Orphanet_154", "MONDO_0010542"], ["EFO_0000618", "Orphanet_71859", "Orphanet_183497", "Orphanet_207049", "Orphanet_207085", "Orphanet_154", "MONDO_0010542"]], "phenotypes": [], "association_counts": {"total": 1700, "direct": 1}, "efo_code": "MONDO_0010542", "efo_label": "dilated cardiomyopathy 3B", "min_path_len": 8, "full_name": "dilated cardiomyopathy 3B", "efo_url": "http://purl.obolibrary.org/obo/MONDO_0010542", "type": "disease", "id": "MONDO_0010542", "name": "dilated cardiomyopathy 3B"}}]}%

```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

```text
curl -d '{"q":["dmd"],"filter":"disease"}' -H "Content-Type: application/json" -X POST "https://platform-api.opentargets.io/v3/platform/private/besthitsearch"
```

You can also output these results with the following tools:

* Command line \(e.g. cURL, Wget or HTTPie\)
* Your own application and/or workflow
* Open Targets [Python client](https://docs.targetvalidation.org/programmatic-access/python-client)

{% hint style="success" %}
You can also access the Open Targets Platform REST-API with scripts in R. 
{% endhint %}

Check our webinar [Take a rest from manual searches with the Open Targets API](https://www.youtube.com/watch?v=KQbfhwpeEvc&index=2&list=PLncWVtwSXtqb8PyL6-ENSCuqP7_4Aj5BE) for an overview of the REST API and examples on some of the API queries that serve the Open Targets Platform user interface.

{% embed url="https://www.youtube.com/watch?v=KQbfhwpeEvc&list=PLncWVtwSXtqb8PyL6-ENSCuqP7\_4Aj5BE&index=2" %}

{% hint style="danger" %}
Please note that at the time of the recording \(Dec 5th 2017\), the Open Targets REST API had a different URL than the currently one in use. 

You should now use:**`https://platform-api.opentargets.io/v3/platform`**
{% endhint %}

Head to the [API tutorials](https://docs.targetvalidation.org/programmatic-access/api-tutorials) page a few use case examples on using the Open Targets Platform REST API and [email us](mailto:support@targetvalidation.org) if you have questions or want to discuss other use cases.


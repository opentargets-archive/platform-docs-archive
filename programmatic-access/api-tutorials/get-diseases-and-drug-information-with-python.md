# Get diseases and drug information using Python

This is an example on how you can get the diseases associated with your targets of interest and their drug information using Python. We will query the Open Targets Platform [REST API](https://docs.targetvalidation.org/programmatic-access/rest-api) to retrieve a list of drugs associated to a given set of targets.

{% hint style="info" %}
You might want to use the [jupyter notebook](http://jupyter.org/install) environment to run this example
{% endhint %}

```python
import pandas as pd
import requests
```

We will create a function to retrieve drugs from the REST API for any given list of Ensembl gene IDs:

```python
number_of_records = 100
# by default, the public/evidence/filter endpoint returns 10 records 
# the size parameter (see line 13) accepts an integer between 0 and 10000

def drug_table(genelist):
    if len(genelist) > 100:
        print('You should split your list in chunks to not overload the API')
    drugfields = ['target.gene_info.symbol',
                  'target.target_class',
                  'evidence.target2drug.action_type',
                  'disease.efo_info.label',
                  'drug']
    payload = {'size': number_of_records, 'target':genelist, 'datatype':['known_drug'],'fields':drugfields}
    r = requests.post('https://platform-api.opentargets.io/v3/platform/public/evidence/filter',
                      json=payload)

    for e in r.json()['data']:
        yield (e['target']['gene_info']['symbol'],
              e['target']['target_class'][0],
              e['drug']['id'],
              e['evidence']['target2drug']['action_type'],
              e['drug']['molecule_name'],
              e['drug']['molecule_type'],
              e['disease']['efo_info']['label'],
              )
```

We will test that we can build a data frame for a target, such as ENSG00000157764:

```python
pd.DataFrame(drug_table(['ENSG00000157764'])
```

Then we will assign labels to column headers and drop any duplicates:

```python
cols = ['target','target_class','chembl_uri','moa','mol_name','mol_type','indication']

brafdrugs = pd.DataFrame(drug_table(['ENSG00000157764']), columns=cols)

# drop duplicate evidence for each target-drug-indication combination
brafdrugs.drop_duplicates(subset=['target','chembl_uri','indication'],inplace=True)

brafdrugs
```

|  | target | target\_class | chembl\_uri | moa | mol\_name | mol\_type | indication |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | BRAF | TKL protein kinase RAF family | http://identifiers.org/chembl.compound/CHEMBL2... | INHIBITOR | DABRAFENIB | Small molecule | neoplasm |
| 1 | BRAF | TKL protein kinase RAF family | http://identifiers.org/chembl.compound/CHEMBL1... | INHIBITOR | VEMURAFENIB | Small molecule | melanoma |
| 2 | BRAF | TKL protein kinase RAF family | http://identifiers.org/chembl.compound/CHEMBL1336 | INHIBITOR | SORAFENIB | Small molecule | hepatocellular carcinoma |
| 5 | BRAF | TKL protein kinase RAF family | http://identifiers.org/chembl.compound/CHEMBL1336 | INHIBITOR | SORAFENIB | Small molecule | neoplasm |
| 7 | BRAF | TKL protein kinase RAF family | http://identifiers.org/chembl.compound/CHEMBL1... | INHIBITOR | VEMURAFENIB | Small molecule | neoplasm |
| 8 | BRAF | TKL protein kinase RAF family | http://identifiers.org/chembl.compound/CHEMBL2... | INHIBITOR | DABRAFENIB | Small molecule | melanoma |

If you rather want to do this for a list of genes as a text file, please follow the instructions below.

For a list of genes:

`AARSD1  
ABCA3  
ABCB6  
ABHD1  
ABHD8  
...`

```python
with open('input_genes.txt') as f:
    bcgenes = f.read().splitlines()
bcgenes[:5]
```

```text
['AARSD1', 'ABCA3', 'ABCB6', 'ABHD1', 'ABHD8']
```

Map your gene symbols to Ensembl gene IDs. You can use [MyGene.info](https://mygene.info/) as a quick lookup and annotation service. 

Head to their [ID mapping using mygene module in Python](http://nbviewer.jupyter.org/gist/newgene/6771106/id_mapping_mygene.ipynb) page to learn more about MyGene.

```python
import mygene

mg = mygene.MyGeneInfo()
mgres = mg.querymany(bcgenes, scopes='symbol,alias',fields='ensembl.gene', species='human')
```

```text
querying 1-1000...done.
querying 1001-1449...done.
Finished.
113 input query terms found dup hits:
    [('ACAT1', 2), ('ADCY3', 2), ('APITD1', 2), ('APOL3', 2), ('APP', 3), ('ARL17A', 3), ('ATP1A1', 3), 
14 input query terms found no hit:
    ['AL021546.6', 'CTC-236F12.4', 'CTC-260F20.3', 'CTD-2132N18.3', 'RP11-1167A19.2', 'RP11-145E5.5', 'R
Pass "returnall=True" to return complete lists of duplicate or missing query terms.
```

```python
bcgenes_ensg = []
for x in mgres:
    try:
        bcgenes_ensg.append(x['ensembl']['gene'])
    except KeyError:
        pass
    except TypeError:
        bcgenes_ensg.append(x['ensembl'][0]['gene'])
bcgenes_ensg[:3]
```

```text
['ENSG00000266967', 'ENSG00000167972', 'ENSG00000115657']
```

It is better to chunk this list not to overload the Open Targets Platform REST API:

```python
genes_chunked = [bcgenes_ensg[i:i + 50] for i in range(0, len(bcgenes_ensg), 50)]
```

We can now make the dataframe of gene, drug and indication triples:

```python
drugtable = pd.concat([pd.DataFrame(drug_table(g), columns=cols) for g in genes_chunked])
```

```python
drugtable.head()
```

|  | target | target\_class | chembl\_uri | moa | mol\_name | mol\_type | phase | indication |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | ADORA1 | Adenosine receptor | http://identifiers.org/chembl.compound/CHEMBL190 | ANTAGONIST | THEOPHYLLINE | Small molecule | 4 | chronic obstructive pulmonary disease |
| 1 | ADORA1 | Adenosine receptor | http://identifiers.org/chembl.compound/CHEMBL113 | ANTAGONIST | CAFFEINE | Small molecule | 4 | ulcerative colitis |
| 2 | ADORA1 | Adenosine receptor | http://identifiers.org/chembl.compound/CHEMBL190 | ANTAGONIST | THEOPHYLLINE | Small molecule | 4 | asthma |
| 3 | ADORA1 | Adenosine receptor | http://identifiers.org/chembl.compound/CHEMBL190 | ANTAGONIST | THEOPHYLLINE | Small molecule | 4 | chronic obstructive pulmonary disease |
| 4 | ADORA1 | Adenosine receptor | http://identifiers.org/chembl.compound/CHEMBL190 | ANTAGONIST | THEOPHYLLINE | Small molecule | 4 | asthma |

```python
drugtable.to_excel('drug_table.xls')
```



{% embed url="https://www.youtube.com/watch?v=KQbfhwpeEvc&list=PLncWVtwSXtqb8PyL6-ENSCuqP7\_4Aj5BE&index=2" %}

{% hint style="danger" %}
Please note that at the time of the recording \(Dec 5th 2017\), the Open Targets REST API had a different URL. You should now use:

`https://platform-api.opentargets.io/v3/platform`
{% endhint %}

If you have questions on our REST API, please [email us](mailto:support@targetvalidation.org) and will be happy to help.


# Data download

{% hint style="danger" %}
**Important update - May 2021:**

With the launch of the [next-generation Platform](https://platform.opentargets.org/) in April 2021, we have increased the number of [datasets available for download](https://platform.opentargets.org/downloads) in both JSON and Parquet formats. To get started with our new datasets, read [our data downloads documentation](https://platform-docs.opentargets.org/data-access/datasets) and join the [Open Targets Community](https://community.opentargets.org/) for sample Python and R scripts.
{% endhint %}

You can download the target-disease associations and the evidence used for the associations from the [Data Download](https://www.targetvalidation.org/downloads/data) page. 

This data is available as compressed JSON files. These files are meticulously formatted to serve as inputs for in-house tools: each line represents a fully dumped \(serialised to a string\) JSON-object, which are independent of each other.

The association and evidence files available for download cannot be used to restore the application as they are not a database dump. These files are the product of a preprocessed export through the now-deprecated Open Targets Platform REST API.


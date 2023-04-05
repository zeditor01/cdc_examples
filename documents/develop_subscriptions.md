[Back to main document](https://github.com/zeditor01/cdc_examples/blob/main/create_scale_sustain_cdc_systems.md).

![Roadwork](/images/work_in_progress.jpg)

# Developing CDC Subscriptions

This paper has yet to be written. The following is an indication of the nature of the content that it will address.

Developing CDC Subscriptions for most cases is a simple mapping which could be automated, assuming the schema mapping is something consistent such as map every column, include every row, and don't derive any new values.

However, CDC does provide extensive options for complex mappings, data derivations, include DBMS-generated data, filtering, error handling, exit routines etc etc etc


The scope of this paper will include

1. A review of the various subscription mapping capabilities
2. How to specify them in Managememt Console or CHCCLP script
3. Understand restrictions in some data sources
4. Understand complexities in some other data sources
5. Understand extensions possible when mapping to Kafka


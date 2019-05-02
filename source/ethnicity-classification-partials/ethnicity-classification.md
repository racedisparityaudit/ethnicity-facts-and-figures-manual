## Ethnicity standards problem

Over 250 different ethnicity descriptors are used across the spreadsheets we get from departments.
In order to make sense of this data we need to reduce the mixture of ethnicity values into a smaller set of values
that we use across our site.
The list we map to has 39 values that are variations on the ONS 2011 census values.
We also need to assign structure to our data: parents and ordering.

There are two aspects to our problem...

**Non-standard values.** The base problem arises because departments code the same ethnicity in different ways. 
For example, "White British" might be collected and reported as:

- White British
- White England/NI/Scotland/Wales
- British: White
- White: British

Converting one set of values to another set of values using a lookup table is well established.
Building the dictionary is an extensive task but this should, at least, be a solved problem. Not so for us due to...

**Contextual variation.** Any given dataset is consistent in itself. On a broad level you get contradiction.
In other words when the DfE say Asian it may mean something different to when the MoJ say Asian (and in fact does).

The worst offender is "Other". Depending on the original data set "Other" may need to become

- Other
- Other inc Chinese
- Other inc Mixed
- Other inc Asian
- Any ethnicity other than White
- Any ethnicity other than White British

Context can also change structure. For example "Chinese" maps to "Chinese" in any situation but is a child of "Asian" 
in the 2011 census and a child of "Other inc Chinese" in 2001.

### Solution 1: EthnicityDictionaryLookup

*EthnicityDictionaryLookup has now been removed from the codebase but notes are left here for historical interest.*

There is a standard way to share data with ambiguous columns such as Ethnicity. That is to mark each cell with the 
schema it's data belongs to.
When we shipped our original templates to departments we required an "Ethnicity Type" with each row of data. We could 
use a double lookup on Ethnicity and Type to map to useful values.

Building the *"data dictionary"* was a large task and remained ongoing throughout the lifespan. The mapping task is the 
job of **EthnicityDictionaryLookup**. It maps [Ethnicity, Ethnicity Type] to [Standard Ethnicity, Parent, Parent-child 
order]. In the event of Ethnicity Type being absent or unrecognisable we had a default as part of the dictionary.

**Problem:** This system relies on Ethnicity Type being robust and it isn't. The ideal of having a common language of 
Ethnicity and Classification definitions across government is only a dream at present. This results with default being 
used most of the time and the flexibility of DictionaryLookup could not be exploited.

### Solution 2: EthnicityClassificationFinder

Solution 2 regards the **non-standard values** and **contextual variation** problems as distinct. It allows you to 
standardise ethnicity without having to rely on data providers or spend time cleaning Ethnicity Type data. This is 
possible due to the familiarity we have built with ethnicity categorisations.

In stage 1 EthnicityClassificationFinder converts raw-values received from departments into a standard list used across 
RDU.

In stage 2 it takes the outputs from stage 1 and determines which of known ethnicity classifications might apply. We 
store a list of "Ethnicity type classifications" such as "ONS 2001 5+1" and "White British and other". These contain 
information on how data with that Ethnicity type should be displayed.

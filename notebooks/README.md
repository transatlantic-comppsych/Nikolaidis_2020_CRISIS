# Notes on pcoessing done to prepare data for analysis
Repository for the analysis of the CRISIS questionnaire.

In [1_make_holdout_add_geo.ipynb](1_make_holdout_add_geo.ipynb) I've split out the holdout data and added geographic information. Here's a description of the everything done in that notebook:
 - Fix raceethnicity fields for parent data.
   - The parent data has a raceethnicity__caribean column and columns 18 and 19 are shifted. I shifted them so they are in alignment with the raceethnicity columns of the adult self report form.
 - Create `age_cat` column. Age categories are:
   -  <18, 18-27, 28-37, 38-47, 48-57, 58+ for the adult self-report data.
   - <=5, 6-10, 11-15, 15+ for the parent report data.
 - Create `sex_cat` column, values are Male, Female, Other
 - Make a `race_cat` column. Race categories are listed in the `us_ances_lut` and `uk_ances_lut` in the notebook. I've also kept my original mappings for the Prolific Acedmic racial categories in the `pa_race_cat` column.
 - Added a `uid` column. This column is a concatenation of the `participant_id` and the index of the response in the concatenated table of all responses. This makes the UID a bit fragile as it is dependent on the order in which the data is loaded, but it served it's need as a quick solution.
 - Identify responses to exclude:
   - There are 84 responses from 20 individuals in the Adult samples where the same individual has responses that have the same freee text responses but sometimes differ in other responses and are sometimes in two samples that should not be possible to overlap, such as `Adult_US_CA` and `Adult_US_NY`. Instead of trying to disentangle what's going on with these responses, I've just excluded all of them.
   - There are 105 responses from 51 participants that appear to be pure duplications with different timestamps. In these cases I just kept the first response.
   - I've excluded all 282 incomplete responses.
   - I've excluded 26 responses from participants in the test-retest sample that are in other samples.
   - I've exlcuded 1 repeated response from the parent report sample.
 - Pick a holdout sample.
   - 1/3 of the sample heldout for confirmation of future analyses. This holdout is drawn from buckets balancing on `sample`, `sex_cat`, `age_cat`, `race_cat`, and the response to the `hispanic` ethnicity question.
   - The hold out excludes participants in the expoloratory sample or the test-retest sample.
 - add columns integrating the first RT response per form into other samples
   - I've added If you are doing an analysis and would like to include the first response from the test-retest participants, you can use the sample name listed in `sample_with_RT`, I’ve removed the `_RT` from the sample name for those responses and set `exclude_with_RT` to `True` for  all of the responses from test-retest subjects other than the first, in addition to the other exclusions.
 - Clean county and state information for US data and merge in additional information.
   - I didn't do this in the most effecient manner and needed multiple rounds of merging in hand corrected information. For the US data I replaced the stateetc and county fields with the corrected values.
   - I first used John's Hopkins case and deaths data, but switched to information from [USA facts](https://usafacts.org/visualizations/coronavirus-covid-19-spread-map/) as this was cleaner.
   - I merged in county level US population estimates from 2019.
   - I merged in county level distances to the US epicenter, which I calculated as the county in the 99.9 percentile for both prevalence and number of cases. For the period of the first sample, this was Nassaue County.
   - I set exclude to True for the 37 US responses that I was not able to assign to a county.
 - Clean geographic data for UK samples.
   - I'm getting case data for england from https://coronavirus.data.gov.uk/ supplemented with data from https://github.com/tomwhite/covid-19-uk-data.
   - In England case data is available at the level of Upper Tier Local Authority (UTLA).
   - In Scotland and Wales, case data is available at the level of Health Boards (LHB).
   - In Northern Ireland, case data is available at the level of local government districts.
   - I attempted to automatically match and manually reviewed city and county information for those that didn't match to assign a `fixed_county` to all responses that corresponded to the case data available.
   - I considered English regions, Scottish/Welsh Healthbords, and Northern Irish local goverment districts the equivalent of state data in the US and assigned these to the `fixed_stateetc` column.
   - I merged in 2018 UK population estimates aggregated to the appropriate level of organization to match the case data downloaded from [nomisweb](https://www.nomisweb.co.uk/).
 - I calculated case per 10k population rates based on case data and population estimates.
 - I added `area_code` and `stateetc_code` columns reflecting the FIPS codes for US data and the relevant codes for the UK data (UTLA, County, Region, LHB, etc.).
 - I merged the Ethnicity column from Prolific Academic based on a hash caculated participant id, sample, and all survey responses except for geographic variables, which were confirmed not to have been duplicated.
 - There were three subjects with multiple Ethnicities listed on unexcluded responses. Each included "Other" as one response, so I set their Ethnicity to "Other" for all of their responses.


In [2_add_ethnicity_item.ipynb](2_add_ethnicity_item.ipynb) I deal with new ethnicity coding received from prolific academic.
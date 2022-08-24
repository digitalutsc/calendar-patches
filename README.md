# Calendar Patches
This is a prototype for a calendar view using the Calendar View module. It currently only works with Drupal’s normal content views, not Solr Search API. 

## Setup
- Add `controlled_access_terms.views.inc` to the `controlled_access_terms` module folder.
- Move patch to `edtf_support.patch` to the `calendar` module folder and run `git apply patch edtf_support.patch` (might have to revert to commit `f67cc6aaba7d5481327539843965cb9110bb0f79` if you’re trying in the future and the patch doesn’t apply anymore) 
- Import view in Drupal settings.

## What it does
These changes create a year calendar view page where days with items originally published on a given day are highlighted with a link. The links go to a list of items originally published on that day.

## How it works
- Calendar works by reading the argument type in order to figure out how to extract and format the data.
- `controlled_access_terms.views.inc` allows Calendar’s existing Date contextual filter argument type to be applied to controlled access terms (such as EDTF). Might be a little bit unstable.
- Inside Calendar module, some of the data is also processed from the specified field. `edtf_support.patch` makes it so that Calendar can parse the EDTF field (rather than ignore it) and create the correct object.

## Possible approaches to get it working with Solr: 
- Index with Solr 
    - For some reason indexing EDTF strings as Datetime with Solr does not seem to work out of the box. Results aren’t returned at all for other views, and it seems like the arguments are getting parsed as string by Views? Might be worth looking into more.
    - Would probably need to index year and full dates.
- Cast contextual view argument
    - This wasn’t working when tried previously because the argument type is specified by the view and arguments set programatically are still the argument type set in the view. The reason the argument type matters is because Calendar uses the argument type to format and extract the data.
    - A hook that changes this argument would have to be after the results were retrieved but before the view was built, and the argument would need to somehow be passed in as a Calendar Date argument.

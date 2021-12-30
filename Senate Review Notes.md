# Notes between `Louisiana Senate (1)` and `LA Senate Review`

Using MGGG's Gerrychain, Stephen Kearny wrote the original
`Louisiana Senate (1)` iPython notebook to help derive optimal districts
for Louisiana's current redistricting cycle. `@gomotopia` is reviewing
this at the end of December 2021.


## The Data

The main data `(1)` uses is `laprecfinal.geojson`, which has many
columns containing election data and demographics for many years.
However, using test precinct Orleans Parish, 071, Ward 11, Precint 11,
the data does not match the election totals from the Secretary of State
website. It also appears that totals from Dave's Redistricting and 
districtr.org also differ.

One reason they might differ is that DRA and other research agencies
reallocate absentee ballots tallied by each Parish back down to the
precincts.

### Hispanics 

Population totals, at least for the 2020 Totals, released by both
the Census and the State of Louisiana, match up better with
`laprecfinal.geojson`. Topline totals match but racial breakdowns
appear to shift.
 
This likely because Hispanics can be of any race. I made this mistake
once and had to redo a whole project. Hispanics are an origin and not
a race. Thus, a count of White people include both Anglos and Hispanic
whites, or for Blacks, American Blacks vs. Caribbean Blacks. 

Other reasons for population discrepencies may be due to some
randomization introduced by the census for privacy. The file supplied
by the Secretary of State, 
`Census_2020_TigerLine_VTD_Shapefile_Layer_as_Validated_by_the_LA_Legislature as of (08-23-2021)`
should be the standard used for all redistricting. 

### Useful Columns

While `laprecsfinal.geojson` has many columns, only a few are
used. Before we generate a `Graph`, we can prune these columns.
Values that are easily calculated by Gerrychain, like percentages
can also be pruned.

If these columns are ever needed again, like when comparing 2010
and 2020 population totals, we can join a precinct to this data
using its `GEOID`. (Remember, 2020 precincts and 2010 precincts
may be different shapes.)

## Variable Conventions

I use the following variable conventions throughout this notebook.

- ALL_CAPS for given constants
- Capitalized for passed/global variables
- lowercase for local variables

## Using Pandas

Whereas `(1)` uses lists and some dictionaries to store generated
information, `Review` creates DataFrames and updates them directly with
dictionaries. This includes assignments, district metrics and should
include sorted district results. 

This allows us to avoid operating upon  We can then save these as csv
files directly. 

## Using List and Dictionary Expressions

`Review` tries to save lines of code by using list and dictionary
expressions. Many of the columns we read and write have similar formats.
They differ by a small amount, yet when one multiplies Elections or
Population totals by race and then by year and type, they can sprout and
spread. 

## TQDM 

Install the `tqdm` python package to include an update bar. These
operations often take hours and sometimes stop in the middle. 

## Using Elections and Updaters

Much of `(1)`'s work was performed by the `percmetrics` function that
performed the task of determining percentages of Democrats, Blacks and
minorities for each district. Fortunately, Gerrychain has built in
structures that performs the work of tallying.

Updaters keep track of column attributes and return a dictionary of
district keys and totals value for any given partition. 

Election data structures typically count votes and display percentages
for political parties. However, we can use its ability to perform these
calculations by pretending, say, that being Black is its own election
and that one can vote "Black" or "NonBlack."

New columns are created in the DataFrame to serve as the complement of
desired totals.

## Towards `6-Region-Generate`

`Louisiana Senate Review (1)` was of great help to me as I developed
my own Louisiana districting experiments. I'm grateful that I was able
to review this code.

This file is the 6th in a series of notebooks that began with small
areas and their populations, then the whole state with added race data,
boxplot analysis and finally `6` which takes a collection of 21
districts and redistricts within this area. 

### Boxplots

Since possible districts depend greatly on the structure and composition
of different voters, The boxplots help us summarize visually the
ensemble we generated given the parameters. This was taken directly from
the Gerrychain docs.

### Saving Assignments

Rather than saving each assignment as a file, `6` keeps a master copy of
the original data and appends new columns for each assignment of
interest.

### Challenges

- Running through thousands of plans can be slow going. While one can
create Gerrychain Constraint objects, this slows down progress. The
Recom algorithm already has a built-in constraint for population
tolerance. Thus, it's better to run through as many plans as possible
and try to pick out assignments of interests.

- Sometimes a running chain will stall on a particular program.
Trackers are written such that the chain can be cut off at any time
and the results of previous runs are still preserved. I wonder if
reversible ReCom makes a difference here.

- My MGGG friend Jack developed a utility to help us explore graphs
generated by Gerrychain, found on github at `@jdeschler/dualgraph_shp`.
Due to the geography of our state's bayou's and river's, we may be able
to generate good, sensical graphs that respect this geography by editing
edges between precincts.

_Ryan Gomez, 
Thu. Dec 30, 2021_

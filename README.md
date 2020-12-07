# stata-dict
Define, refer to, and loop over dictionaries (hash maps) in Stata

**Description:**<br>
*dict* offers the tools to define and loop over dictionaries. A dictionary puts together several lists. It indexes their items according to their position on their respective list.  The dict prefix iterates over this index, allowing items of multiple lists to be passed to a loop command at the same time.

Note that the dict prefix can loop over any frame, not just those created by the function `dict define`.


**Requirements:**<br>
Stata 16 or higher

**Install the package:**
```
ssc install dict
```

## How it works
Let's consider the following dictionary in a csv file named `countries.csv`:


| country	        | acronym |	region      |
|-----------------|----|------------------|
| Austria	        | AT |	Western Europe  |
| Belgium	        | BE |	Western Europe  |
| Germany	        | DE |	Western Europe  |
| Denmark	        | DK |	Northern Europe |
| Greece	        | EL |	Southern Europe |
| Spain	          | ES |	Southern Europe |
| Finland	        | FI |	Northern Europe |
| France	        | FR |	Central Europe  |
| Ireland	        | IE |	Western Europe  |
| Italy	          | IT |	Southern Europe |
| Luxembourg	    | LU |	Western Europe  |
| The Netherlands | NL |	Western Europe  |
| Portugal	      | PT |	Southern Europe |
| Sweden          | SE |	Northern Europe |
| United Kingdom  | UK |	Western Europe  |

To import the dictionary to a Stata frame, use the following command:

``` stata
. dict define mycountrylist, from(countries.csv)
```

Get the index of all countries in Western Europe:

``` stata
. dict getindex mycountrylist if (region == "Western Europe"), match(all)
1 2 3 9 11 12 15
```

Get values associated with a key:

``` stata
. dict getvalues mycountrylist if (country == "Germany")
. sreturn list

macros:
             s(region) : "Western Europe"
            s(acronym) : "DE"
            s(country) : "Germany"
```

Get values of a given index:

``` stata
. dict getvalues mycountrylist in 10
. sreturn list

macros:
             s(region) : "Southern Europe"
            s(acronym) : "IT"
            s(country) : "Italy"
```

Pull information from one observation of the dictionary to a Stata command:

``` stata
. dict mycountrylist, index(6): display "%country is in %region"

  Spain is in Southern Europe
```

Loop over the dictionary:

``` stata
. dict mycountrylist: display "%country (%acronym) is in %region"

  Austria (AT) is in Western Europe
  Belgium (BE) is in Western Europe
  Germany (DE) is in Western Europe
  Denmark (DK) is in Northern Europe
  Greece (EL) is in Southern Europe
  Spain (ES) is in Southern Europe
  Finland (FI) is in Northern Europe
  France (FR) is in Central Europe
  Ireland (IE) is in Western Europe
  Italy (IT) is in Southern Europe
  Luxembourg (LU) is in Western Europe
  The Netherlands (NL) is in Western Europe
  Portugal (PT) is in Southern Europe
  Sweden (SE) is in Northern Europe
  United Kingdom (UK) is in Western Europe
```

Combine the commands:

```
. dict getindex mycountrylist if (region == "Southern Europe"), match(all)
. foreach country of numlist `r(i)'{
.   dict mycountrylist, i(`country'): display "%acronym - %country"
. }

  EL - Greece
  ES - Spain
  IT - Italy
  PT - Portugal
```

```
. dict count mycountrylist
.  forvalues index = 1/`r(N)'{
.    dict getvalues mycountrylist in `index'
.    
.    gen `s(acronym)' = country_var_in_current_frame == "`s(country)'"
.    label var `s(acronym)' "`s(country)' (`s(region)')"
.  }
```

| country	        | AT |	BE |	DE | 	DK | 	EL | 	ES | 	FI | 	FR | 	IE | 	IT | 	LU | 	NL | 	PT | 	SE | 	UK |
|-----------------|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Austria	        | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| Belgium	        | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| Germany	        | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| Denmark	        | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| Greece	        | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| Spain           | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| Finland         | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| France	        | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| Ireland         | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 |
| Italy	          | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 |
| Luxembourg      | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 |
| The Netherlands	| 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 |
| Portugal	      | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 |
| Sweden	        | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 |
| United Kingdom  | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 |

```
---------------------------------------------------------------------------------------------------------------------------------------------------------
              storage   display    value
variable name   type    format     label      variable label
---------------------------------------------------------------------------------------------------------------------------------------------------------
country         str15   %15s                  
AT              float   %9.0g                 Austria (Western Europe)
BE              float   %9.0g                 Belgium (Western Europe)
DE              float   %9.0g                 Germany (Western Europe)
DK              float   %9.0g                 Denmark (Northern Europe)
EL              float   %9.0g                 Greece (Southern Europe)
ES              float   %9.0g                 Spain (Southern Europe)
FI              float   %9.0g                 Finland (Northern Europe)
FR              float   %9.0g                 France (Central Europe)
IE              float   %9.0g                 Ireland (Western Europe)
IT              float   %9.0g                 Italy (Southern Europe)
LU              float   %9.0g                 Luxembourg (Western Europe)
NL              float   %9.0g                 The Netherlands (Western Europe)
PT              float   %9.0g                 Portugal (Southern Europe)
SE              float   %9.0g                 Sweden (Northern Europe)
UK              float   %9.0g                 United Kingdom (Western Europe)
---------------------------------------------------------------------------------------------------------------------------------------------------------
```

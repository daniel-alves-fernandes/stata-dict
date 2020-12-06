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


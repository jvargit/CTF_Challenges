# Through the Looking Shark - osint

## Challenge

Our party mascot is a shark tagged off the coast of Western Australia, we use an ocean shark searcher to track. She hasn't been pinged in a while though... Can you tell us when the last ping was and where?

-Commander Voss

Flag is formatted cysea{Name_Date_Location}. e.g. cysea{Belle_15June2025_MacquariePark}. Flag is case insensitive.

## Solve

This [website](https://www.ocearch.org/tracker/) has a shark tracker providing data on tagged sharks & recent pings. 

Manually guessing and checking the sharks along the west coast of Australia eventually provided the flag:

`cysea{Alice_22June2015_NingalooReef}`


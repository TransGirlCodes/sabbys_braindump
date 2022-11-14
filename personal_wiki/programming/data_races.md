# Data Races

A _data race_ is similar to a race condition and happens when these three behaviors occur:

-   Two or more pointers access the same data at the same time.
-   At least one of the pointers is being used to write to the data.
-   There’s no mechanism being used to synchronize access to the data.
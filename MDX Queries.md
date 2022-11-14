The basic MDX query is the SELECT statement.

The SELECT statement specifies a result set that contains a subset of multidimensional data that has been returned from a cube.

A select statement must have the following info:

1. The number of axes that you want the result set to contain. You can specify up to 128 axes in an MDX query.
2. The set of members or tuples to include on each axis of the MDX query.
3. The name of the cube that sets the context of the MDX query.
4. The set of members or tuples to include on the slicer axis. For more information about slicer and query axes, see [Restricting the Query with Query and Slicer Axes (MDX)](https://docs.microsoft.com/en-us/analysis-services/multidimensional-models/mdx/mdx-query-and-slicer-axes-restricting-the-query?view=asallproducts-allversions).


To identify the query axes, the cube that will be queried, and the slicer axis, the MDX SELECT statement uses the following clauses:

1. A SELECT clause that determines the query axes of an MDX SELECT statement. For more information about the construction of query axes in a SELECT clause, see [Specifying the Contents of a Query Axis (MDX)](https://docs.microsoft.com/en-us/analysis-services/multidimensional-models/mdx/mdx-query-and-slicer-axes-specify-the-contents-of-a-query-axis?view=asallproducts-allversions).
2. A FROM clause that determines which cube will be queried. For more information about the FROM clause, see [SELECT Statement (MDX)](https://docs.microsoft.com/en-us/sql/mdx/mdx-data-manipulation-select).
3. An optional WHERE clause that determines which members or tuples to use on the slicer axis to restrict the data returned. For more information about the construction of a slicer axis in a WHERE clause, see [Specifying the Contents of a Slicer Axis (MDX)](https://docs.microsoft.com/en-us/analysis-services/multidimensional-models/mdx/mdx-query-and-slicer-axes-specify-the-contents-of-a-slicer-axis?view=asallproducts-allversions).

This query returns a result set that contains the 2002 and 2003 sales and tax amounts for the Southwest sales territories.

```MDX
SELECT  
    { [Measures].[Sales Amount],   
        [Measures].[Tax Amount] } ON COLUMNS,  
    { [Date].[Fiscal].[Fiscal Year].&[2002],   
        [Date].[Fiscal].[Fiscal Year].&[2003] } ON ROWS  
FROM [Adventure Works]  
WHERE ( [Sales Territory].[Southwest] )  
```


## Query Axes
Query axes specify the edges of a cellset returned by a Multidimensional Expressions (MDX) SELECT statement. Specifying the edges of a cellset lets you restrict the returned data that is visible to the client.

To specify query axes, you use the `<SELECT query axis clause>` to assign a set to a particular query axis. Each `<SELECT query axis clause>` value defines one query axis. The number of axes in the dataset is equal to the number of `<SELECT query axis clause>` values in the SELECT statement.

Each query axis has a number: zero (0) for the x-axis, 1 for the y-axis, 2 for the z-axis, and so on. In the syntax for the `<SELECT query axis clause>`, the `Integer_Expression` value specifies the axis number. An MDX query can support up to 128 specified axes, but very few MDX queries will use more than 5 axes. For the first 5 axes, the aliases COLUMNS, ROWS, PAGES, SECTIONS, and CHAPTERS can instead be used.

The following simple SELECT statement returns the measure Internet Sales Amount on the Columns axis, and uses the MDX MEMBERS function to return all the members from the Calendar hierarchy on the Date dimension on the Rows axis:

```MDX
SELECT {[Measures].[Internet Sales Amount]} ON COLUMNS,  
{[Date].[Calendar].MEMBERS} ON ROWS  
FROM [Adventure Works]
```

## Slicer Axis
The slicer axis filters the data returned by the Multidimensional Expressions (MDX) SELECT statement, restricting the returned data so that only data intersecting with the specified members will be returned. It can be thought of as an invisible extra axis in a query. The slicer axis is defined in the WHERE clause of the SELECT statement in MDX.


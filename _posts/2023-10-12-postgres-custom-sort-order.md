---
title: "[TIL] PostgreSQL Custom Sort Order"
layout: post
---

Sometimes I want to compare a large enough portion of data from a spreadsheet against data in a PostgreSQL database. The spreadsheet contains a number of rows with an identifier but the records are in a random order and I want to preserve that ordering for the comparison. I therefore want to be able to order the result set from a PostgreSQL database query in a predetermined, custom sort order.

Of course the straightforward workaround is to order the initial spreadsheet’s data by a column that is also natively sortable in the database and the need for a custom sort order vanishes, but trust me on this one, I want the original, random order.

I have an array of identifiers, plucked from the spreadsheet, to use as my query criteria:

```
membership_no,name
1325,Joe
1123,Ben
1548,Cat
```

I can transform the `membership_no` column into an array of values for populating the `WHERE` clause:

```
SELECT
  membership_no,
  name
FROM
  members
WHERE
  membership_no IN ('1325','1123','1548');
```

The results come back in as random an order as they went in. So how to order it according to the original order?

The answer is to use the `array_position()` function in an `ORDER BY` clause, using the initial, randomly ordered array. It works as follows:

```
SELECT
  membership_no,
  name
FROM
  members
WHERE
  membership_no IN ('1325','1123','1548');
ORDER BY
  array_position(ARRAY['1325','1123','1548'], membership_no::text)
```

This returns the result set in the desired order:

```
 membership_no | name
--------------------------
 1325          | Joseph
 1123          | Benjamin
 1548          | Catriona
(3 rows)
```

The first argument to `array_position` is an array of values representing the desired order, and the second is the database column that will be used for sorting. The value of the column will be positioned according to the order of the values provided.

If you look carefully, you’ll notice that the column value is typecast with `::text`. This is necessary as the comparison requires the datatype of the column value and the values in the array to be the same. In this case I am providing the values as text, therefore the column value needs to be cast to a text value. Failure to do this will cause PostgreSQL to complain with an error similar to the following:

```
ERROR:  function array_position(text[], character varying) does not exist

HINT:  No function matches the given name and argument types. You might need to add explicit type casts.
```

Well, good thing I added an explicit type cast.

—*2127, Thursday 12th October 2023.*

<div class="references">
  <h3>References</h3>
  <ul>
    <li>
      <a href="https://stackoverflow.com/questions/4088532/custom-order-by-explanation">
        StackOverflow: Custom ORDER BY Explanation
      </a>
    </li>
    <li>
      <a href="https://www.postgresql.org/docs/14/functions-array.html">
        PostgreSQL Docs: <code>array_position()</code>
      </a>
    </li>
    <li>
      <a href="https://dba.stackexchange.com/questions/201837/order-by-gives-error-function-array-positiontext-character-varying-does-no">
        Stack Exchange &gt; Database Administrators &gt; ORDER BY gives ERROR: function array_position(text[], character varying) does not exist
      </a>
    </li>
  </ul>
</div>

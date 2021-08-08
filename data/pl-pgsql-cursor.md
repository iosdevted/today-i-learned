# [SQL] PL/pgSQL Cursor

A PL/pgSQL cursor allows you to encapsulate a query and process each individual row at a time.

Typically, you use cursors when you want to divide a large result set into parts and process each part individually. If you process it at once, you may have a memory overflow error.

On top of that, you can develop a function that returns a reference to a cursor. This is an effective way to return a large result set from a function. The caller of the function can process the result set based on the cursor reference.

## Declaring cursors

To access to a cursor, you need to declare a cursor variable in the declaration section of a block. PostgreSQL provides you with a special type called REFCURSOR to declare a cursor variable.

```sql
declare my_cursor refcursor;
```

You can also declare a cursor that bounds to a query by using the following syntax:

```sql
cursor_name [ [no] scroll ] cursor [( name datatype, name data type, ...)] for query;
```

First, you specify a variable name for the cursor.

Next, you specify whether the cursor can be scrolled backward using the SCROLL. If you use NO SCROLL, the cursor cannot be scrolled backward.

Then, you put the CURSOR keyword followed by a list of comma-separated arguments ( name datatype) that defines parameters for the query. These arguments will be substituted by values when the cursor is opened.

After that, you specify a query following the FOR keyword. You can use any valid SELECT statement here.

The following example illustrates how to declare cursors:

```sql
declare
    cur_films  cursor for 
		select * 
		from film;
    cur_films2 cursor (year integer) for 
		select * 
		from film 
		where release_year = year;
```

## Opening cursors

Cursors must be opened before they can be used to query rows. PostgreSQL provides the syntax for opening an unbound and bound cursor.

You open an unbound cursor using the following syntax:

```sql
OPEN unbound_cursor_variable [ [ NO ] SCROLL ] FOR query;
```

Because the unbound cursor variable is not bounded to any query when we declared it, we have to specify the query when we open it. See the following example:

```sql
open my_cursor for 
	select * from city 
	where country = p_country;
```

PostgreSQL allows you to open a cursor and bound it to a dynamic query. Here is the syntax:

```sql
open unbound_cursor_variable[ [ no ] scroll ] 
for execute query_string [using expression [, ... ] ];

query := 'select * from city order by $1';

open cur_city for execute query using sort_field;
```

Because a bound cursor already bounds to a query when we declared it, so when we open it, we just need to pass the arguments to the query if necessary.

```sql
open cursor_variable[ (name:=value,name:=value,...)];

open cur_films;
open cur_films2(year:=2005);
```

## References

[PostgreSQL Tutorial - PL/pgSQL Cursor](https://www.postgresqltutorial.com/plpgsql-cursor/)
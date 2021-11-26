---
layout: post
title: How to get the difference of two arrays inside an Ecto fragment
tags: [elixir, ecto, fragment, postgresql, sql]
comments: true
---

You might want to update your array column in a Postgres table by taking the difference of that column with another array supposedly from your Elixir/Phoenix app. Here I explain how to create a macro for that.

```sql
SELECT
  coalesce(
    (
      SELECT
        array_agg(ELEMENTS)
      FROM
        (
          SELECT
            unnest(array [ 1, 2, 3, 7, 8 ])
          EXCEPT
          SELECT
            unnest(array [ 3, 7, 8 ])
        ) t (ELEMENTS)
    ),
    '{}'
  )
```

Above SQL query selects the elements from the first array that are not in the second array, and using the `COALESCE` we can return an empty array instead when there is no difference between the first and second arrays which in turn returns `NULL`.

We can do the same thing inside a fragment in Ecto with a macro.

```elixir
  defmacro array_substract(first, second) do
    quote do
      # Select only the ones from the first array
      # that are not in the second array
      # otherwise return '{}'
      fragment(
        """
        COALESCE(
          (
            SELECT array_agg(elements)
            FROM (
                   SELECT unnest(?)
                   EXCEPT
                   SELECT unnest(?)
                 ) t (elements)
          ),
          '{}'
        )
        """,
        unquote(first),
        type(unquote(second), unquote(first))
      )
    end
  end
```

Later you can use the above macro like:

```elixir
values_to_be_substracted = [1, 2, 3, 4]

from(f in Foo, update: [set: some_array_column: array_substract(f.some_array_column, values_to_be_substracted)])
```

Above macro is actually a different version of [the one created by Artur](https://gist.github.com/fuelen/61b0268df513d844a4197a3038c35a57).
It just didn't work for me so I've created this one with a different query which I think is better.

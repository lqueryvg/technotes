# Excel

## Tables
##### Create a Table

- Enter some data into cells, with column headings.
- Select any cell in the range.
- Format as Table & select "My table has headers".

##### Name a Table
- Select any cell in the table.
- Go to `Design` in `Table Tools` in the ribbon. The table name appears at the top left.

##### Structured References

Example | Description
:---|:----
`Table1` | Table data (without header or total rows)
`[Column1]` | A column in **same** table.
`Table1[Column1]` | Fully qualified column name.

- Use `TAB` completion when entering these in formulas.
- Use up and down arrows when selecting from popup list of table or column names, then hit `TAB` to continue entering the formula.

##### Shortcuts

Key | Description
:---|:----
`Ctrl-SPACE`	   | **Once** select column within table, **Twice** include header, **Thrice** whole column in sheet
`Shift-SPACE`	   | **Once** select row within table, **Twice** within sheet
`Ctrl-Shift-*`	 | select table data

## Functions

Rather than `VLOOKUP` use:

```excel
INDEX(return-column, MATCH(search-value, search-column, 0))
```

## Misc Shortcuts
Key | Description
:---|:----
`F9`              | manual re-calc (if auto calc turned off)
`Ctrl-Shift-~`	 | general number format (useful to reveal date/times)
`Ctrl-1`		     |format cells dialog
`Ctrl-ARROW`	   | jump to non blank
`Ctrl-Shift-P`	 | format dialogue
`Ctrl-Alt-V`	   |paste special (use to paste formats)

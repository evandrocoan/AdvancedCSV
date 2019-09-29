# Sublime Text Advanced CSV

A plugin to manage CSV files, forked from Eric Martel's Sublime-Text-2-CSV-Plugin at https://github.com/ericmartel/Sublime-Text-2-CSV-Plugin.

It's often easiest to work with CSV data when the columns are properly lined up.  This plugin provides a command to line up all the columns using spaces (`Justify columns`) and to collapse them back again (`Collapse columns`).

It also includes commands to insert and delete columns and to sort data by column, with or without a header row, and respecting numeric order and lexicographical order as appropriate.

An entire column may be block selected (`Select column`), which enables complex operations like quickly reordering, merging, adding & deleting multiple columns.

The plugin includes a command to clean up empty trailing commas from rows, which are often left when opening a CSV file in Excel.

Using NumPy (http://www.numpy.org), the plugin supports evaluating Python expressions over ranges of cells, in a manner similar to formulas in Excel.

All the above features work in both justified and collapsed modes.

Finally, the plugin fully supports RFC 4180 (https://tools.ietf.org/html/rfc4180) quoting, with the exception that quoted newlines (2.6) are treated as row separators.

## Install

## Installation

### By Package Control

1. Download & Install **`Sublime Text 3`** (https://www.sublimetext.com/3)
1. Go to the menu **`Tools -> Install Package Control`**, then,
   wait few seconds until the installation finishes up
1. Go to the menu **`Tools -> Command Palette...
   (Ctrl+Shift+P)`**
1. Type **`Preferences:
   Package Control Settings – User`** on the opened quick panel and press <kbd>Enter</kbd>
1. Then,
   add the following setting to your **`Package Control.sublime-settings`** file, if it is not already there
   ```js
   [
       ...
       "channels":
       [
           "https://raw.githubusercontent.com/evandrocoan/StudioChannel/master/channel.json",
           "https://packagecontrol.io/channel_v3.json",
       ],
       ...
   ]
   ```
   * Note,
     the **`https://raw...`** line must to be added before the **`https://packagecontrol...`**,
     otherwise you will not install this forked version of the package,
     but the original available on the Package Control default channel **`https://packagecontrol...`**
1. Now,
   go to the menu **`Preferences -> Package Control`**
1. Type **`Install Package`** on the opened quick panel and press <kbd>Enter</kbd>
1. Then,
search for **`AdvancedCSV`** and press <kbd>Enter</kbd>

See also:
1. [ITE - Integrated Toolset Environment](https://github.com/evandrocoan/ITE)
1. [Package control docs](https://packagecontrol.io/docs/usage) for details.


## Key bindings

Key                  | Action
-------------------- | ---------------------------
`Ctrl+Comma, Up`     | Sort by column (Ascending)
`Ctrl+Comma, Down`   | Sort by column (Descending)
`Ctrl+Comma, i`      | Insert column
`Ctrl+Comma, d`      | Delete column
`Ctrl+Comma, s`      | Select column
`Ctrl+Comma, Space`  | Justify columns
`Ctrl+Comma, Comma`  | Collapse columns
`Ctrl+Comma, Equals` | Evaluate cells
`Ctrl+Comma, f`      | Format cells using a template string

## Formulas

To trigger cell evaluation, the contents of a cell must follow a standard pattern:

	<range>=<python expression...>

`range` defines the set of cells whose contents will be updated, and `python expression` defines the Python expression that will be evaluated.

### Ranges

If `range` is left blank, the formula cell itself is replaced.  Unlike Excel, the formula text is not stored separately from the displayed value, so this has the effect of destroying the formula, though the evaluate command can always be undone if that result is unwanted.

- `=<python expression>` Replaces the current cell with the result of `python expression`.

The syntax for defining a range other than the formula cell is:

`[<row_start>:<row_end>,<col_start>:<col_end>]=<python expression>`

Updates a rectangular area of the document from `row_start` to `row_end` and from `col_start` to `col_end`.  If `row_start` or `col_start` is omitted, the range starts at the first row or column of the document.

If `row_end` or `col_end` is omitted, the range ends at the last row or column of the document of the document.  If both are omitted but the colon is included, the entire row are column is updated.  If both numbers and the colon are omitted, the range is the formula's row or column.

Here are all the variations of this syntax:

- `[:]` Updates all cells in the current column.
- `[<row_start>:<row_end>]` Updates cells in the current column from `row_start` to `row_end`.
- `[<row_start>:]` Updates cells in the current column from `row_start` to the end of the document.
- `[:<row_end>]` Updates cells in the current column from the beginning of the document to `row_end`.
- `[,:]` Updates all cells in the current row.
- `[,<col_start>:<col_end>]` Updates all cells in the current row from `col_start` to `col_end`.
- `[,<col_start>:]` Updates all cells in the current row from `col_start` to the end of the document.
- `[,:<col_end>]` Updates all cells in the current row from the beginning of the document to `col_end`.
- `[:,:]` Updates all cells in the document.
- `[:,<col_start>:<col_end>]` Updates cells in all rows from `col_start` to `col_end`.
- `[:,<col_start>:]` Updates cells in all rows from `col_start` to the end of the document.
- `[:,:<col_end>]` Updates cells in all rows from the beginning of the document `to col_end`.

When defining ranges, the `start` cell is updated, but the `end` cell is not.  This is intentional, to match NumPy array slice syntax.

- `[0:1,:]` Updates all cells in row 0.
- `[1:3,:]` Updates all cells in rows 1 and 2.

Row and column numbers in ranges may be prefixed with `+` or `-`, which makes them relative to the formula's location in the document.

- `[:,-1]` Updates all cells in the column immediately to the left of the formula.
- `[:,+1:+3]` Updates all cells in the two columns immediately to the right of the formula .

For convenience, the range may be shifted one cell in any direction by prefixing the `=` character with a direction modifier.

- `<=` Updates the cell to the left of the formula.
- `>=` Updates the cell to the right of the formula.
- `^=` Updates the cell above the formula.
- `v=` Updates the cell below the formula.

The range shift modifier may be used in conjunction with numeric ranges.

- `[:]<=` Updates all cells in the column to the left of the formula.

### Expression syntax

The Python expression is evaluated by Sublime Text's Python interpreter, for each cell in the target range.  The result is then converted to a string and stored in the cell.

Some variables are provided to the evaluator:

- `m` A NumPy `ndarray` representing the document.  Non-numeric cells are represented with 0.  The array dimensions are padded to the maximum extents of the document, missing cells are filled in with 0.  For a NumPy tutorial, see http://wiki.scipy.org/Tentative_NumPy_Tutorial.
- `row` The row of the cell being evaluated.
- `col` The column of the cell being evaluated.
- `frow` The row of the formula.
- `fcol` The column of the formula.

## Examples

### Example 1

This example applies a simple column sum using the NumPy `sum` method.

	item  ,price
	shoes ,12
	hat   ,2
	pants ,8
	shirt ,7.50
	total ,      ,"<=m[1:5,1].sum()"

### Example 2

This example fills in totals by price and by item.

	item  ,price ,qty ,total
	shoes ,12    ,2   ,
	hat   ,2     ,1   ,
	pants ,8     ,1   ,
	shirt ,7.50  ,2   ,
	total ,      ,    ,
	"[5,1:4]=m[1:5,col].sum()"
	"[1:5,3]=m[row,1]*m[row,2]"

Note that it must be evaluated (`Ctrl+Comma, Equals`) twice to calculate the last cell.

# License

All of Sublime Text Advanced CSV Plugin is licensed under the MIT license.

Copyright (c) 2014 Wade Brainerd <wadeb@wadeb.com>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

Further, portions of the code taken from Eric Martel's Sublime Text 2 plugin are licensed as follows:

All of Sublime Text 2 CSV Plugin is licensed under the MIT license.

Copyright (c) 2012 Eric Martel <emartel@gmail.com>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

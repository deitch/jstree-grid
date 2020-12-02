# jsTreeGrid
[![CDNJS](https://img.shields.io/cdnjs/v/jstreegrid.svg)](https://cdnjs.com/libraries/jstreegrid)

UNSUPPORTED: You are welcome to find issues, and submit Pull Requests to fix. Beyond that, there really isn't time to support it.

## Overview
Plugin for the jstree www.jstree.com tree component that provides a grid extension to the tree.

Allows any number of columns, and can use any property of the node to display data in the grid

For significant changes to v3 compared to v1, see the end of this document.

**Note:** treegrid may have issues when using theme autoloading as in:

````JavaScript
core: {
	themes: {url: 'http://some.url/theme'}
}
````

In any case, theme autoloading is strongly discouraged in jstree v3.

## License and Support
jstree-grid is released under the very permissive MIT License, albeit with no warranty or guarantee. See the [MIT License](./LICENSE) for full details. Use it as you will, and use it well.

If you need commercial support, general assistance or have any questions, please contact [info@atomicinc.com](mailto:info@atomicinc.com).

jstree-grid can be made a native component for Angular, Angular2, Polymer, Aurelia, and other frameworks. If you have commercial need of a tree grid those or any other framework, please contact [info@atomicinc.com](mailto:info@atomicinc.com).



## Usage

1. Include jquery (>= 1.4.2) and jstree in your page, as usual
2. Inclue jstree **>= 3.3.0**
3. Include jstreegrid.js v3 or later
4. Include grid as a plugin
5. Include relevant parameters.

````HTML
<!-- include jstreegrid -->
<script src="/path/to/jstreegrid.js"></script>
````


````JavaScript
$("div#id").jstree({
	// include grid as a plugin
	plugins: ["core","ui",...,"grid"],
	// include relevant parameters
	grid: {
		columns: [{},{},...,{}],
		width: 25
	},
	core: {
		data: [...]
	}
});
````

As of 3.0.0-beta5, jstree-grid supports AMD, thanks https://github.com/jochenberger

As of 3.1.0-beta1, jstree-grid uses a wrapping table, rather than inserted `div`s in the tree. This does a much better job with widths, alignment, editing, etc. etc.

As of 3.10.2, jstree-grid supports CommonJS, thanks to https://github.com/YarnSeemannsgarn

### Structure

The grid is built by adding divs `<div></div>` to each `<li>` entry for a row in the tree. Inside the `<div>` is a `<span></span>` with the data.
Thus, an entry is likely to look like

````HTML
<div><span>$5.00</span></div>
````

We use the div to control the entire height and width, and the span to get access to the actual data itself.

### Options

#### The options are as follows:

* `width`: width for the entire jstree-grid. If no width is given, automatically fills the entire viewport (`width: 100%;`)
* `height`: height for the entire jstree-grid. If no height is given, height will reflect the amount of content.
* `fixedHeader`: true/false. If true, then when the tree is scrolled the column headers will remain visible. Defaults to true.
* `columnWidth`: default width for a column for which no width is given. If no width is given, the default is `auto`.
* `columns`: an array of columns to create, on order. Each entry is an object with the following parameters:
	* `tree`: boolean, whether the jstree should be placed in this column. Only the first `true` is accepted. If no column is set to `tree:true`, then the first column is used.
	* `width`: width of the column in pixels. If no width is given, the default is `auto` **except for the last column**. In the last column, if no width is given, it is treated as 'auto' and fills the entire rest of the grid to the right.
    * `minWidth`: Minimum width of the column when width is set to `auto`. Does **not** limit manual column resizing.
    * `maxWidth`: Maximum width of the column when width is set to `auto`. Does **not** limit manual column resizing.
    * `header`: string to use as a header for the column.
	* `headerClass`: a CSS class to add to the header cell in this column
	* `headerTitle`: a title to add to the header cell in this column, shown as a tooltip
	* `columnClass`: a CSS class to add to the header cell and the column cell
	* `cellClass`: a CSS class to add to each cell in this column (except for the header) - added to the `<span>`
	* `wideCellClass`: a CSS class to add to each cell in this column (except for the header) - added to the `<div>`
	* `value`: the attribute on the node to use as the value for this cell - entered as the `<span>` text. Can be a string or a function.
	* `valueClass`: the attribute on the node to use as a class on this cell - added to the `<span>`
	* `valueClassPrefix`: a prefix to add to the valueClass to use as a class on this cell
	* `wideValueClass`: the attribute on the node to use as a class on this cell - added to the `<div>`
	* `wideValueClassPrefix`: a prefix to add to the wideValueClass to use as a class on this cell
	* `search_callback`: sets the search callback function used when using the `searchColumn` function. The function is passed with four arguments: the `search string`, the `column value`, the `jstree node` corresponding to the column and the `column`. returns true for a match and false if it doesn't match. If not set a default string search function is used (this is similar to [https://www.jstree.com/api/#/?q=search&f=$.jstree.defaults.search.search_callback](https://www.jstree.com/api/#/?q=search&f=$.jstree.defaults.search.search_callback))
* `resizable`: true/false if the columns should be resizable. Defaults to false.
* `draggable`: true/false if the columns should be draggable (requires jQuery UI with sortable plugin). Defaults to false.
* `stateful`: true/false. If true, then whenever a column width is resized, it will store it in html5 localStorage, if available. Defaults to false.
* `headerAsTitle`: true/false. If true, the header is used as `headerTitle`. If false, no title is added, but in case the option `headerTitle` is set, a title is added. Defaults to false.
* `contextmenu`: true/false whether or not a context menu for editing the cells should be shown on right-click. Defaults to false.
* `gridcontextmenu`: function to create context menu items; see context menu of jstree for format. In addition, if `false` or not set, no special context menu. If `true`, creates a default menu to edit the cell entry.
* `caseInsensitive`: true/false whether or not the column sort should be case insensitive. Default value is false (the default sort is case sensitive)

The reason for both `valueClass` and `wideValueClass` is to give you the ability to control both the narrow part of the text,  and the entire width of the cell. For example, if the cell is 56px wide, but the text in it is "OK" and thus only 20px wide.
Suppose you have a class "important" which backgrounds in red, and a class "clickable" which changes the cursor to a pointer.
You want the entire width of the cell to be red, but just the word "OK" to be clickable.
You would ensure that "clickable" is applied to the span, but important to the div.

Value is one of:

* the name of the property of the node data whose content will be used; you can choose which once for the entire grid.
* a function, which will be passed the node's data given by `tree.get_node(node)` for the individual tree item. If you want your custom data, access it via `node.data`

Thus, if you have a node whose data is given by:

````JavaScript
{text: "My Node", data: {price: "$10"}}
````


and we want the price value ($10) to be in column 1, then we have a config of:

````JavaScript
grid: {
	columns: [
		{width: 50, header: "Nodes"},
		{width: 30, header: "Price", value: "price"}
	]
}
````

Or, in a function:

````JavaScript
grid: {
	columns: [
		{width: 50, header: "Nodes"},
		{width: 30, header: "Price", value: function(node){return(node.data.price);}}
	]
}
````

Using a function allows you to calculate things, or make conditions:

````JavaScript
grid: {
	columns: [
		{width: 50, header: "Nodes"},
		{width: 30, header: "Price", value: function(node){return("$"+(node.data.price*2));}}
	]
}
````



Thanks to ChrisRaven for the metadata support (deprecated as of v3), resizable columns, and cell click events.

ValueClass is the name of the attribute that will be added as a class to this cell. Thus, if you have a node whose data is given by:

````JavaScript
{text: "My Node", data: {price: "$10", scale: "expensive"}}
````

and we want the cell to have the class "expensive", then we have a config of:

````JavaScript
grid: {
	columns: [
		{width: 50, header: "Nodes"},
		{width: 30, header: "Price", value:"price", valueClass: "scale"}
	]
}
````

The result would be:

````HTML
<div><span class="expensive">$10</span></div>
````

Conversely, if we want it to be "price-expensive", we would have a config of:

````JavaScript
grid: {
	columns: [
		{width: 50, header: "Nodes"},
		{width: 30, header: "Price", value: "price", valueClass: "scale", valueClassPrefix: "price-"}
	]
}
````

The result would be:

````HTML
<div><span class="price-expensive">$10</span></div>
````

You can add a tooltip to each element in the grid by adding the name of it to the title, with the HTML stripped out. For example:

````JavaScript
grid: {
	columns: [
		{width: 50, header: "Nodes"},
		{width: 30, header: "Price", value: "price", title: "price"}
	]
}
````

The result would be:

````HTML
<div><span title="$10">$10</span></div>
````

This includes the actual tree node, not just the added grid cells:

````JavaScript
grid: {
	columns: [
		{width: 50, header: "Nodes", title:"price"},
		{width: 30, header: "Price", value: "price", title: "price"}
	]
}
````

You also can have the contents of the tree node (value of tree.get_text()) itself made into the tooltip by using the special title keyword
"_DATA_".

````JavaScript
grid: {
	columns: [
		{width: 50, header: "Nodes", title:"_DATA_"},
		{width: 30, header: "Price", value: "price", title: "price"}
	]
}
````

Finally, you can change a node contents on the fly using "change_node.jstree". You change the `data` attribute of the node, then trigger the event,
for example:

````JavaScript
var tree = $("#jstree").jstree(true), node = tree.get_node("my_node");
node.data.value = 25;
tree.trigger("change_node.jstree",node);
````

### HTML

Note that the data in each cell is treated as HTML content for the span, rather than raw text. You can use HTML in any cell, except for the
base tree node cell, which follows jstree rules.

### Heights
The height of the entire div in which the tree is rendered is given by you. If you wish the tree to have a max-height of 40px, you need to set it as part of the standard HTML/CSS.

````HTML
<style>
div#mytree {
	max-height: 40px;
}
</style>
<div id="mytree"></div>
````

In the above example, the tree itself, but *not* the headers, will be limited to 40px, not by jstree or jstreegrid, but by straight CSS. However, jstreegrid will structure the tree and header in such a way that if the total tree is greater than 40px in height, then the tree will have a vertical scrollbar, but the header will remain fixed.


### Themeroller
The themeroller plugin for jstree is supported as of tag 0.9.1 (29 November 2011). Thanks to ChrisRaven for the support.


### API
The following methods can be called on the jstree:

* `grid_hide_column(column)`: Hide column number `column`. Column numbers begin with `0`. If the column already is hidden, has no effect.
* `grid_show_column(column)`: Show column number `column`. Column numbers begin with `0`. If the column already is shown, has no effect.
* `search(str)`: the [jstree search](https://www.jstree.com/api/#/?q=search) has been extended to search the entire grid. to search a specific column use `searchColumn`.
* `searchColumn(searchObject)`: Search one or more columns for specific strings. `searchObject` is an object with the column number as key, beginning with `0`, and the searchString as value. Multiple column values are joined together with logical `AND`. Uses the jstree search settings to hide/show nodes. Examples:
    * `searchColumn({0:'My Node'})`: search for rows wherein the first column has text `'My Node'`
    * `searchColumn({0:'My Node', 1:'$10'})`: search for rows wherein the first column has text `'My Node'` *AND* second column has text `'$10'`

### Events
* `loaded.jstree`: When the tree is done loading, as usual, it fires a "loaded.jstree" event on the div to which you added jstree. jsTreeGrid uses this event to start its own load process.
* `loaded_grid.jstree`: When jsTreeGrid is done, it fires a "loaded_grid.jstree" event on the same div. If you need to run some
code after the jsTreeGrid is done loading, just listen for that event. An example is in the treegrid.HTML sample page.
* `select_cell.jstree-grid`: If you click in any individual cell, the jstreegrid will fire a "select_cell.jstree_grid" event on the tree. See below for "cell selection".
* `update_cell.jstree-grid`: If you right-click a cell and edit it, when the edit is complete, and if the value has changed, the jstreegrid will fire a `update_cell.jstree-grid` event on the jstree.
* `render_cell.jstree-grid`: Called each time a cell is rendered. Is not a one-time call, as cells are rendered when their container node is opened and destroyed each time is closed, so you can get multiple `render_cell.jstree-grid` calls for the same cell.
* `resize_column.jstree-grid`: When a column is resized, whether from dragging the resizer or double-clicking it, this event will be fired.

#### Cell Selection
When you select a cell by clicking on it, two things happen:

1. jstree-grid fires off a `select_cell.jstree-grid` event on the tree, to which you can listen.
2. jstree-grid selects the row, leading in turn to the normal jstree row selection event handlers.

If you wish to prevent the second part - entire row selection by the jstree - for example, if you have input HTML rendered in the cell and do not want to lose input focus, you can prevent the jstree selection.

To do so, create a handler for the `select_cell.jstree-grid` event and prevent its handling:

``` js
mytree.on("select_cell.jstree-grid", function(event, data) {
    if (/* some logic, if you want */) {
        event.preventDefault();
    }
});
```

If you still want to prevent the automatic selection of the row, but do it yourself, you can do it manually:

``` js
mytree.on("select_cell.jstree-grid", function(event, data) {
    event.preventDefault();
    mytree.jstree("deselect_all");
    mytree.jstree("select_node", data.node);
});
```


The signature for the select_cell.jstree-grid handler is:

````JavaScript
function(event,{value:value,header:header,node:node,grid:grid,sourceName:sourceName})
````

where:

* value: value of the data element that rendered this cell
* column: header for the column
* node: reference to the &lt;li&gt; element in the tree that starts the row with the clicked cell
* grid: reference to the &lt;div&gt; element in the grid that was clicked
* sourceName: name of the element in the original data that contained this value, as provided by the config in the columns "value" for this column


#### Cell Update

The signature for the update_cell.jstree-grid handler is:

````JavaScript
function(event,{node:node, col:column, value:newvalue,old:oldvalue})
````

where:

* node: the edited node in the data structure
* col: name of the column, e.g. "price"
* value: new value for the cell
* old: the original value before it was changed


The signature for the resize_column.jstree-grid handler is:

````JavaScript
function(event,columnNumber,newWidth)
````

where:

* columnNumber: the number of the column being resized, with the first column being 0
* newWidth: the new width of the column in pixels

#### Cell Render

The signature for the render_cell.jstree-grid handler is:

````JavaScript
function(event,{value:cellvalue, column:columnheader, node:node, sourceName:datakeyname})
````

where:

* value: The value of the cell
* column: The column header
* node: Reference to the &lt;li&gt; element in the tree where this cell is rendered
* sourceName: The key name in the data structure that gives the value to this cell

### Width

The width of the table and its columns can change depending on the selected options. As described above, there are three options that affect the width:

* `width`: in the root of the config, determines the width **for the entire jstree-grid**. If you set this `width` to `200`, the entire jstree-grid will occupy a fixed `200px` of screen width. The default is `100%`, i.e. fill the enclosing element or the viewport.
* `columnWidth`: in the root of the config, determines the default width of a column for which a width is not provided.
* `width`: in a particular column, determines the width of that individual column.

A number of combinations and permutations is possible using the above.

#### fixed `width` defined for entire grid
If a fixed `width` is not defined for the entire grid, the behaviour depends on what happens in each column. In all cases, the entire jstree-grid will be the given fixed width.

* If all columns have a fixed width defined, the columns are given those widths.
  * If the sum of all columns is *greater* than the width defined on the entire grid, the second column onwards will be inside a scrolling element.
  * If the sum of all columns is *less* than the width defined on the entire grid, the gap will be filled by whitespace after the last column.
* If one or more columns is defined as `auto`, these will automatically be resized to fill to the entire grid.
* If one or more columns does not have a `width` defined, it will be treated as the root value of `columnWidth`, else as `auto`

Here are examples:

````JavaScript
// all columns and grid defined widths, will have blank space after 3rd column
{
  "width": "500px",
  "columns": [
    {"width": "100px"},
    {"width": "100px"},
    {"width": "150px"}
  ]
}

// all columns and grid defined widths, will scroll 2nd through last column
{
  "width": "500px",
  "columns": [
    {"width": "200px"},
    {"width": "200px"},
    {"width": "400px"}
  ]
}

// grid defined width, some columns defined, some "auto", will automatically fill grid, no scrolling or whitespace
{
  "width": "500px",
  "columns": [
    {"width": "200px"},
    {"width": "200px"},
    {"width": "auto"}
  ]
}

// grid defined width, some columns defined, some not, columnWidth fixed, will scroll
// since 200 + 200 + 120 = 520 > 500
//       col0 + col1 + col2(from default) = 520 > grid width
{
  "width": "500px",
  "columnWidth": "120px",
  "columns": [
    {"width": "200px"},
    {"width": "200px"},
    {}
  ]
}

// grid defined width, some columns defined, some not, columnWidth auto or not defined, will fill
// since col2 inherits columnWidth = "auto", will default to filling at 100px
{
  "width": "500px",
  "columns": [
    {"width": "200px"},
    {"width": "200px"},
    {}
  ]
}
````

#### fixed `width` not defined for entire grid
If a fixed `width` is not defined for the entire grid, then the grid fills the entire containing element or viewport, i.e. it is treated as `width:100%;`. In that case, the widths of the columns are as follows:

* If all columns have a fixed width defined, the columns are given those widths.
  * If the sum of all columns is *greater* than the calculated width of the entire grid, the second column onwards will be inside a scrolling element.
  * If the sum of all columns is *less* than the calculated width of the entire grid, the gap will be filled by whitespace after the last column.
* If one or more columns is defined as `auto`, these will automatically be resized to fill to the entire grid.
* If one or more columns does not have a `width` defined, it will be treated as the root value of `columnWidth`, else as `auto`

In other words, the behaviour of columns does *not* change based on the `width` definition of the entire grid. It only affects the defined or calculated width of the grid, which in turn affects if the columns scroll (columns > grid), have whitespace (columns < grid), or fill (one or more columns is `auto`)


### V3 Changes
jsTree v3 has created significant non-backwards-compatible changes to jsTree. To make jsTreeGrid compatible with jsTree3, jsTreeGrid v3 has changed as well, and is no longer backwards compatible. However, the changes required to support v3 are minimal.

This section lists significant changes between pre-v3 and v3.

* jstree v3 no longer stores its data in the DOM, rather inside JS. As such, jsTreeGrid no longer stores any data in the DOM. For example, in pre-v3 jstree, `attr` on a node's source data would store the actual data on the DOM as attributes using `jQuery.attr()`. This is no longer true in v3. jsTreeGrid similarly no longer looks for its source data on the DOM. All data to be passed to the grid should be stored in the `data` property of the node's JSON source.
* `metadata` is no longer an option, since it is no longer necessary to use it to avoid storing data on the DOM.

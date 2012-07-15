jsTreeGrid
==========

Overview
--------
Plugin for the jstree www.jstree.com tree component that provides a grid extension to the tree.

Allows any number of columns, and can use any property of the node to display data in the grid

Usage
-----
1. Include jquery (>= 1.4.2) and jstree in your page, as usual
2. Include jstreegrid.js
3. Include grid as a plugin
4. Include relevant parameters. 

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
	} 
});
````

Structure
---------
The grid is built by adding divs <div></div> to each <li> entry for a row in the tree. Inside the <div> is a <span></span> with the data.
Thus, an entry is likely to look like

````HTML
<div><span>$5.00</span></div>
````

We use the div to control the entire height and width, and the span to get access to the actual data itself.

Options
-------
The options are as follows:

* width: default width for a column for which no width is given. If no width is given, the default is 25px
* columns: an array of columns to create, on order. Each entry is an object with the following parameters:
	width: width of the column in pixels.
	header: string to use as a header for the column.
	cellClass: a CSS class to add to each cell in this column (except for the header) - added to the <span>
	wideCellClass: a CSS class to add to each cell in this column (except for the header) - added to the <div>
	headerClass: a CSS class to add to the header cell in this column - added to the <div>
	value: the attribute on the node to use as the value for this cell - entered as the <span> text
	valueClass: the attribute on the node to use as a class on this cell - added to the <span>
	valueClassPrefix: a prefix to add to the valueClass to use as a class on this cell
	wideValueClass: the attribute on the node to use as a class on this cell - added to the <div>
	wideValueClassPrefix: a prefix to add to the wideValueClass to use as a class on this cell
* resizable: true/false if the columns should be resizable. Defaults to false.	
	
The reason for both valueClass and wideValueClass is to give you the ability to control both the narrow part of the text,
and the entire width of the cell. For example, if the cell is 56px wide, but the text in it is "OK" and thus only 20px wide.
Suppose you have a class "important" which backgrounds in red, and a class "clickable" which changes the cursor to a pointer.
You want the entire width of the cell to be red, but just the word "OK" to be clickable. 
You would ensure that "clickable" is applied to the span, but important to the div.


Value is the name of the attribute or metadata whose content will be used; you can choose which once for the entire grid. Thus, if you have a node whose data is given by:

````JavaScript
{data: "My Node", attr: {price: "$10"}}
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

If you prefer to use metadata, per jstree (see http://www.jstree.com/documentation/json_data) then you need to set the source to metadata 
and use that data:

````JavaScript
{data: "My Node", metadata: {price: "$10"}}
grid: {
	source: "metadata",
	columns: [
		{width: 50, header: "Nodes"},
		{width: 30, header: "Price", value: "price"}
	]
}
````

Thanks to ChrisRaven for the metadata support, resizable columns, and cell click events..

ValueClass is the name of the attribute that will be added as a class to this cell. Thus, if you have a node whose data is given by:

````JavaScript
{data: "My Node", attr: {price: "$10", scale: "expensive"}}
````

and we want the cell to have the class "expensive", then we have a config of:

````JavaScript
grid: {
	columns: [
		{width: 50, header: "Nodes"},
		{width: 30, header: "Price", value: "price", valueClass: "scale"}
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

Finally, you can change a node contents on the fly using "change_node.jstree". You change the attribute of the node, then trigger the event,
for example:

````JavaScript
$("li.mynode").attr("value","25").trigger("change_node.jstree");
````

HTML
----
Note that the data in each cell is treated as HTML content for the span, rather than raw text. You can use HTML in any cell, except for the 
base tree node cell, which follows jstree rules.

Heights
-------
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


Themeroller
-----------
The themeroller plugin for jstree is supported as of tag 0.9.1 (29 November 2011). Thanks to ChrisRaven for the support.

Events
------
* loaded.jstree: When the tree is done loading, as usual, it fires a "loaded.jstree" event on the div to which you added jstree. jsTreeGrid uses this event to start its own load process. 
* loaded_grid.jstree: When jsTreeGrid is done, it fires a "loaded_grid.jstree" event on the same div. If you need to run some 
code after the jsTreeGrid is done loading, just listen for that event. An example is in the treegrid.HTML sample page.
* select_cell.jstree-grid: If you click in any individual cell, the jstreegrid will fire a "select_cell.jstree_grid" event on the jstree. 

The signature for the select_cell.jstree-grid handler is:

````JavaScript
function(event,{value:value,header:header,node:node,sourceName:sourceName,sourceType:sourceType})
````

where:

* value: value of the data element that rendered this cell
* column: header for the column
* node: reference to the &lt;li&gt; element that contains the clicked cell
* sourceName: name of the element in the original data that contained this value, as provided by the config in the columns "value" for this column
* sourceType: if the source was from "attr" or "metadata"

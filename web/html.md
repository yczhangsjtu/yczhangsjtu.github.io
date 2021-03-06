---
title: HTML - Hyper Text Markup Language
---

# HTML - Hyper Text Markup Language

## Inline Fonts

<table class="list">
<tbody>
<tr>
<td><b>Font Name</b></td>
<td><b>Tag</b></td>
<td><b>Effect</b></td>
</tr>
<tr>
<td>Bold</td>
<td><font face="Courier">&lt;b&gt;ABCDEFGabcdefg123&lt;/b&gt;</font></td>
<td><b>ABCDEFGabcdefg123</b></td>
</tr>
<tr>
<td>Italian</td>
<td><font face="Courier">&lt;i&gt;ABCDEFGabcdefg123&lt;/i&gt;</font></td>
<td><i>ABCDEFGabcdefg123</i></td>
</tr>
<tr>
<td>Superscript</td>
<td><font face="Courier">May, 4&lt;sup&gt;th&lt;/sup&gt;</font></td>
<td>May, 4<sup>th</sup></td>
</tr>
<tr>
<td>Subscript</td>
<td><font face="Courier">CO&lt;sub&gt;2&lt;/sub&gt;</font></td>
<td>CO<sub>2</sub></td>
</tr>
<td>Strong Emphasis</td>
<td><font face="Courier">&lt;strong&gt;Warning: &lt;/strong&gt;Something is wrong!</font></td>
<td><strong>Warning: </strong>Something is wrong!</td>
</tr>
<tr>
<td>Emphasis</td>
<td><font face="Courier">&lt;em&gt;Remark: &lt;/em&gt;Take care.</font></td>
<td><em>Remark: </em>Take care.</td>
</tr>
<tr>
<td>Insert</td>
<td><font face="Courier">The answer is &lt;ins&gt;yes&lt;/ins&gt;.</font></td>
<td>The answer is <ins>yes</ins>.</td>
</tr>
<tr>
<td>Delete</td>
<td><font face="Courier">The answer is &lt;del&gt;yes&lt;/del&gt;.</font></td>
<td>The answer is <del>yes</del>.</td>
</tr>
</tbody>
</table>
## Escaped Characters

<table width="100%">
<tr>
<td><b>Name</b></td>
<td><b>HTML</b></td>
<td align="center"><b>Symbol</b></td>
</tr>
<tr>
<td>Less-than sign</td>
<td><font face="Courier">&amp;lt;</font></td>
<td align="center">&lt;</td>
</tr>
<tr>
<td>Greater-than sign</td>
<td><font face="Courier">&amp;gt;</font></td>
<td align="center">&gt;</td>
</tr>
<tr>
<td>Ampersand</td>
<td><font face="Courier">&amp;amp;</font></td>
<td align="center">&amp;</td>
</tr>
<tr>
<td>Quotation mark</td>
<td><font face="Courier">&amp;quot;</font></td>
<td align="center">&quot;</td>
</tr>
</table>

## Special Effects

### Horizontal Line Across the Page

&lt;hr/&gt;

------------------------------------------------------------------------

<table width="100%">
<tbody>
<tr>
<td valign="top">
<h4>Ordered List</h4> <xmp><ol>
<li>First</li>
<li>Second</li>
<li>Third</li>
</ol>
</xmp><ol>
<li>First</li>
<li>Second</li>
<li>Third</li>
</ol>
</td>

<td valign="top">
<h4>Unordered List</h4> <xmp><ul>
<li>First</li>
<li>Second</li>
<li>Third</li>
</ul>
</xmp><ul>
<li>First</li>
<li>Second</li>
<li>Third</li>
</ul>
</td>

<td valign="top">
<h4>Definition List</h4> <xmp><dl>
<dt>First</dt>
<dd>The first definition.</dd>
<dt>Second</dt>
<dd>The second definition.</dd>
<dt>Third</dt>
<dd>The third definition.</dd>
</dl>
</xmp>
<dl>
<dt>First</dt>
<dd>The first definition.</dd>
<dt>Second</dt>
<dd>The second definition.</dd>
<dt>Third</dt>
<dd>The third definition.</dd>
</dl>
</td>

</tr>
</tbody>
</table>

<table>
<tr>

<td>
<h4>Links that open new window/tab.</h4><xmp><a href="http://bookzz.org" target="_blank">http://bookzz.org</a>
</xmp>
<a href="http://bookzz.org" target="_blank">http://bookzz.org</a>
</td>
</tr>
</table>

## Tables

<table class="list">
<tr>

<td valign="top">
<h4>Ordinary Table</h4>
<xmp>
<table border="1px">
    <tr>
        <td>A1</td>
        <td>B1</td>
        <td>C1</td>
    </tr>
    <tr>
        <td>A2</td>
        <td>B2</td>
        <td>C2</td>
    </tr>
    <tr>
        <td>A3</td>
        <td>B3</td>
        <td>C3</td>
    </tr>
</table>
</xmp>
<table border="1px">
    <tr>
        <td>A1</td>
        <td>B1</td>
        <td>C1</td>
    </tr>
    <tr>
        <td>A2</td>
        <td>B2</td>
        <td>C2</td>
    </tr>
    <tr>
        <td>A3</td>
        <td>B3</td>
        <td>C3</td>
    </tr>
</table>
</td>

<td valign="top">
<h4>Cells that cover <br>multi-rows/columns</h4>
<xmp>
<table border="1px">
    <tr>
        <td colspan="2">A1 B1</td>
        <td rowspan="2">C1<br>C2</td>
    </tr>
    <tr>
        <td rowspan="2">A2<br>A3</td>
        <td>B2</td>
    </tr>
    <tr>
        <td colspan="2">B3 C3</td>
    </tr>
</table>
</xmp>
<table border="1px">
    <tr>
        <td colspan="2">A1 B1</td>
        <td rowspan="2">C1<br>C2</td>
    </tr>
    <tr>
        <td rowspan="2">A2<br>A3</td>
        <td>B2</td>
    </tr>
    <tr>
        <td colspan="2">B3 C3</td>
    </tr>
</table>
</td>

<td valign="top">
<h4>Long Table</h4>
<xmp>
<table class="list">
    <thead>
        <tr>
            <th align="center">Column A</th>
            <th align="center">Column B</th>
            <th align="center">Column C</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td align="center">A1</td>
            <td align="center">B1</td>
            <td align="center">C1</td>
        </tr>
        <tr>
            <td align="center">A2</td>
            <td align="center">B2</td>
            <td align="center">C2</td>
        </tr>
        <tr>
            <td align="center">A3</td>
            <td align="center">B3</td>
            <td align="center">C3</td>
        </tr>
    </tbody>
    <tfoot>
        <tr>
            <td align="center">A</td>
            <td align="center">B</td>
            <td align="center">C</td>
        </tr>
    </tfoot>
</table>
</xmp>
<table class="list">
    <thead>
        <tr>
            <th align="center">Column A</th>
            <th align="center">Column B</th>
            <th align="center">Column C</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td align="center">A1</td>
            <td align="center">B1</td>
            <td align="center">C1</td>
        </tr>
        <tr>
            <td align="center">A2</td>
            <td align="center">B2</td>
            <td align="center">C2</td>
        </tr>
        <tr>
            <td align="center">A3</td>
            <td align="center">B3</td>
            <td align="center">C3</td>
        </tr>
    </tbody>
    <tfoot>
        <tr>
            <td align="center">A</td>
            <td align="center">B</td>
            <td align="center">C</td>
        </tr>
    </tfoot>
</table>
</td>

</tr>
</table>

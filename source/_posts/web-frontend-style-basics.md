---
title: Web Frontend Style Basics
date: 2020-11-03 13:26:18
tags: CSS
categories:
- Web 前端
---



## Basics

### Types of Boxes

CSS display types (types of boxes): 

- block
- inline
- inline-block.

Block elements 

- Always starts on a new line, and fills up the horizontal space left and right on the web page. 
- Can have height and width.
- You can add margins and padding on all four sides of any block element — top, right, left, and bottom.

Inline elements

- Don’t start on a new line.
- Can't have height and width.
- Only add space to the left or right margin and padding. Can't add height to top or bottom margin and padding.

Inline-Block elements

- Don’t start on a new line.
- Can have height and width.
- Can padding and margins added on all four sides.

## Height and Width

### Width

- 宽度一般不设置，用内容本身去撑（动态宽度）。也可以设置固定的宽度，使用百分比宽度。
- 用 padding/margin left/right 百分比，去设置两个元素之间的水平间距。

### Height

- 高度一般也不设置，用内容本身撑（动态高度）。
- 使用 padding/margin top/bottom 固定值，去增加高度。

## Align

### Horizontal Align

horizontally center align block elements

To horizontally center a block element (like <div>)

```
margin: auto;
```

horizontally center align text

To center the text inside an element.

```
text-align: center;
```

Left and right align - Using position

```
position: absolute;
right: 0px;
```

Left and right align - Using float

```
float: right;
```

Some items left align some right

```
.container {
    overflow:hidden;
}
.container .left-item {
    float: left;
    margin-right: 10px;
}
.container .right-item {
    float: right;
    margin-left: 10px;
}
```

**Note:** If an element is taller than the element containing it, and it is floated, it will overflow outside of its container. You can use the "**clearfix**" hack to fix this. We can add `overflow: auto;` to the containing element to fix this problem

### Vertical Align

Center vertically - using padding

```
padding-top: {}px;
padding-bottom: {}px;
```

Center vertically - using line-height

```
line-height: {}px;
```

Center vertically - using position & transform

```
.center {
  height: 200px;
  position: relative;
  border: 3px solid green;
}
.center p {
  margin: 0;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```

Center vertically - using flexbox

```
.center {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 200px;
  border: 3px solid green;
}
```



## Layout



### Multiple div in one line

```
float:left (or right);
```

```
display:inline-block;
```

```
display:flex;
```

### Dynamic Layout

Inline elements in a block element auto wrapping lines until decrease to the block minimal width:

```
min-width: {fixed_value}
```



## Common class name

- row
- column
- title
- content
- item
- wrapper
- menu-bar
- top, header, footer
- logo



## References

[1] [CSS display properties: block, inline, and inline-block — & how to tell the difference](https://medium.com/@DaphneWatson/css-display-properties-block-inline-and-inline-block-how-to-tell-the-difference-7d3a1e6e3051)

[2] [CSS Layout - Horizontal & Vertical Align - w3schools](https://www.w3schools.com/css/css_align.asp)

[3] [Aligning Items in a Flex Container](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Aligning_Items_in_a_Flex_Container)
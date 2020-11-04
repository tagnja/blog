---
title: Web Frontend Colors
date: 2020-11-04 10:33:48
tags: CSS
categories:
- Web 前端
---



## CSS Colors

Colors in CSS can be specified by the following methods:

- Hexadecimal colors
- Hexadecimal colors with transparency
- RGB colors
- RGBA colors
- HSL colors
- HSLA colors
- Predefined/Cross-browser color names
- With the `currentcolor` keyword

### Hexadecimal Colors

A hexadecimal color is specified with: #RRGGBB, where the RR (red), GG (green) and BB (blue) hexadecimal integers specify the components of the color. All values must be between 00 and FF.

For example, the #0000ff value is rendered as blue, because the blue component is set to its highest value (ff) and the others are set to 00.

```
#p1 {background-color: #ff0000;}   /* red */
#p2 {background-color: #00ff00;}   /* green */
#p3 {background-color: #0000ff;}   /* blue */
```

### Hexadecimal Colors With Transparency

A hexadecimal color is specified with: #RRGGBB. To add transparency, add two additional digits between 00 and FF.

```
#p1a {background-color: #ff000080;}   /* red transparency */
#p2a {background-color: #00ff0080;}   /* green transparency */
#p3a {background-color: #0000ff80;}   /* blue transparency */
```

### RGB Colors

An RGB color value is specified with the [rgb() function](https://www.w3schools.com/cssref/func_rgb.asp), which has the following syntax:

```
rgb(red, green, blue)
```

Each parameter (red, green, and blue) defines the intensity of the color and can be an integer between 0 and 255 or a percentage value (from 0% to 100%).

For example, the rgb(0,0,255) value is rendered as blue, because the blue parameter is set to its highest value (255) and the others are set to 0.

Also, the following values define equal color: rgb(0,0,255) and rgb(0%,0%,100%).

```
#p1 {background-color: rgb(255, 0, 0);}   /* red */
#p2 {background-color: rgb(0, 255, 0);}   /* green */
#p3 {background-color: rgb(0, 0, 255);}   /* blue */
```

### RGBA Colors

RGBA color values are an extension of RGB color values with an alpha channel - which specifies the opacity of the object.

An RGBA color is specified with the [rgba() function](https://www.w3schools.com/cssref/func_rgba.asp), which has the following syntax:

```
rgba(red, green, blue, alpha)
```

The alpha parameter is a number between 0.0 (fully transparent) and 1.0 (fully opaque).

```
#p1 {background-color: rgba(255, 0, 0, 0.3);}   /* red with opacity */
#p2 {background-color: rgba(0, 255, 0, 0.3);}   /* green with opacity */
#p3 {background-color: rgba(0, 0, 255, 0.3);}   /* blue with opacity */
```

### HSL Colors

HSL stands for hue, saturation, and lightness - and represents a cylindrical-coordinate representation of colors.

### HSLA Colors

HSLA color values are an extension of HSL color values with an alpha channel - which specifies the opacity of the object.

### Predefined/Cross-browser Color Names

140 color names are predefined in the HTML and CSS color specification.

For example: `blue`, `red`, `coral`, `brown`, etc:

```
#p1 {background-color: blue;}
#p2 {background-color: red;}
#p3 {background-color: coral;}
```

### The currentcolor Keyword

The `currentcolor` keyword refers to the value of the color property of an element.

```
#myDIV {
  color: blue; /* Blue text color */
  border: 10px solid currentcolor; /* Blue border color */
}
```



## Color Schemes

- **Achromatic Color Schemes**
- **Monochromatic Color Schemes**. Monochromatic schemes use different tones from the same angle on the color wheel (**the same hue**).
- **Analogous Color Schemes**. Analogous color schemes are created by using colors that are next to each other on the color wheel.
- **Complementary Color Schemes**. Complementary schemes are created by combining colors from opposite sides of the color wheel.
- **Triadic**. Triadic schemes are made up of hues equally spaced around color wheel.
- **Compound** (aka Split Complementary) Color Scheme. Compound schemes are almost the same as complementary schemes. Instead of using colors that are opposites, it uses colors on both sides of the opposite hue.



## Color Psychology

- Colors can influence human behavior.
- Colors can influence human perceptions.
- Colors can influence the taste of food.
- Colors can influence attractiveness.

<div style="background-color: red; color:white; padding: 10px; margin-top: 10px; text-align: center">
RED<br>
Red attracts the human eye
</div>


Energy: power, strength, excitement.

Danger: fire, blood, urgency, traffic stop.

Passion: appetite, emotion, love.

<div style="background-color: #4CAF50; color:white; padding: 10px; margin-top: 10px; text-align: center">
GREEN<br>
Green is the most restful color
</div>


Nature: Fertility, Freshness, New Growth.

Safety: Good Health, Healing Power, Free Traffic.

Harmony: Peace, Easiness, Calmness.

<div style="background-color: #2196F3; color:white; padding: 10px; margin-top: 10px; text-align: center">
BLUE<br>
Blue is the most used office color
</div>


Sea: Water, Ocean, Depth, Wealth.

Quality: Stability, Conservatism, Productivity, Wisdom, Intelligence.

Sky: Truth, Peace, Heaven.

<div style="background-color: #ffeb3b; color:black; padding: 10px; margin-top: 10px; text-align: center">
Yellow<br>
Yellow is a Happy Color
</div>


Sunshine: Light, Clarity, Energy, Warmth.

Optimism: Happiness, Positivity, Cheerfulness, Youngfulness.

<div style="background-color: black; color: white; padding: 10px; margin-top: 10px; text-align: center">
Black<br>
Black is Authority and Mystery
</div>


Authority: Power, Elegance, Formality.

Mystery: Evil, Fear, Death.

<div style="background-color: gray; color: black; padding: 10px; margin-top: 10px; text-align: center">
Gray<br>
Grey is Conservative
</div>

Security
Reliability
Classic Knowledge
Professional Wisdom

<div style="background-color: white; color: black; padding: 10px; margin-top: 10px; text-align: center">
White<br>
White is Purity
</div>

Cleanliness
Neutrality
Goodness
Innocence

## Common Colors

- Color Names Supported by All Browsers
- Color Groups
- Color Shades
- Fashion Color



### Color Names Supported by All Browsers

[Color Names Supported by All Browsers](https://www.w3schools.com/colors/colors_names.asp)



### Color Groups

[HTML Color Groups](https://www.w3schools.com/colors/colors_groups.asp)



### Shades of Gray

| Gray Shades       | HEX     | RGB              |
| :---------------- | :------ | :--------------- |
| HTML Black        | #000000 | rgb(0,0,0)       |
|                   | #080808 | rgb(8,8,8)       |
|                   | #101010 | rgb(16,16,16)    |
|                   | #181818 | rgb(24,24,24)    |
|                   | #202020 | rgb(32,32,32)    |
|                   | #282828 | rgb(40,40,40)    |
|                   | #303030 | rgb(48,48,48)    |
|                   | #383838 | rgb(56,56,56)    |
|                   | #404040 | rgb(64,64,64)    |
|                   | #484848 | rgb(72,72,72)    |
|                   | #505050 | rgb(80,80,80)    |
|                   | #585858 | rgb(88,88,88)    |
|                   | #606060 | rgb(96,96,96)    |
|                   | #686868 | rgb(104,104,104) |
|                   | #696969 | rgb(105,105,105) |
|                   | #707070 | rgb(112,112,112) |
|                   | #787878 | rgb(120,120,120) |
| HTML Gray         | #808080 | rgb(128,128,128) |
|                   | #888888 | rgb(136,136,136) |
|                   | #909090 | rgb(144,144,144) |
|                   | #989898 | rgb(152,152,152) |
|                   | #A0A0A0 | rgb(160,160,160) |
|                   | #A8A8A8 | rgb(168,168,168) |
| HTML DarkGray !!! | #A9A9A9 | rgb(169,169,169) |
|                   | #B0B0B0 | rgb(176,176,176) |
|                   | #B8B8B8 | rgb(184,184,184) |
| X11 Gray          | #BEBEBE | rgb(190,190,190) |
| HTML Silver       | #C0C0C0 | rgb(192,192,192) |
|                   | #C8C8C8 | rgb(200,200,200) |
|                   | #D0D0D0 | rgb(208,208,208) |
| HTML LightGray    | #D3D3D3 | rgb(211,211,211) |
|                   | #D8D8D8 | rgb(216,216,216) |
| HTML Gainsboro    | #DCDCDC | rgb(220,220,220) |
|                   | #E0E0E0 | rgb(224,224,224) |
|                   | #E8E8E8 | rgb(232,232,232) |
|                   | #F0F0F0 | rgb(240,240,240) |
| HTML WhiteSmoke   | #F5F5F5 | rgb(245,245,245) |
|                   | #F8F8F8 | rgb(248,248,248) |
| HTML White        | #FFFFFF | rgb(255,255,255) |



### Fashion Color



<div style="background-color: #FF6F61; color:white; padding: 10px; margin-top: 10px;">2019 - Living Coral<br>HEX: #FF6F61<br>PANTONE 16-1546</div>

<div style="background-color: #6B5B95; color:white; padding: 10px; margin-top: 10px;">
2018 - Ultra Violet<br>
HEX: #6B5B95<br>
PANTONE 18-3838
</div>
<div style="background-color: #88B04B; color:white; padding: 10px; margin-top: 10px;">
2017 Greenery<br>
Hex #88B04B<br>
RGB(136, 176, 75)<br>
Pantone 15-0343
</div>


<div style="background-color: #F7CAC9; color:black; padding: 10px; margin-top: 10px;">
2016 Rose Quartz<br>
Hex #F7CAC9<br>
RGB(247, 202, 201)<br>
Pantone 13-1520<br>
</div>
<div style="background-color: #92A8D1; color:black; padding: 10px; margin-top: 10px;">
2016 Serenity<br>
Hex #92A8D1<br>
RGB(146, 168, 209)<br>
Pantone 15-3919<br>
</div>


<div style="background-color: #955251; color:white; padding: 10px; margin-top: 10px;">
2015 Marsala<br>
Hex #955251<br>
RGB(149, 82, 81)<br>
Pantone 18-1438<br>
</div>


<div style="background-color: #B565A7; color:white; padding: 10px; margin-top: 10px;">
2014 Radiand Orchid<br>
Hex #B565A7<br>
RGB(181, 101, 167)<br>
Pantone 18-3224<br>
</div>


<div style="background-color: #009B77; color:white; padding: 10px; margin-top: 10px;">
2013 Emerald<br>
Hex #009B77<br>
RGB(0, 155, 119)<br>
Pantone 17-5641<br>
</div>


<div style="background-color: #DD4124; color:white; padding: 10px; margin-top: 10px;">
2012 Tangerine Tango<br>
Hex #DD4124<br>
RGB(221, 65, 36)<br>
Pantone 17-1463<br>
</div>


<div style="background-color: #D65076; color:white; padding: 10px; margin-top: 10px;">
2011 Honeysucle<br>
Hex #D65076<br>
RGB(214, 80, 118)<br>
Pantone 18-2120<br>
</div>


<div style="background-color: #45B8AC; color:white; padding: 10px; margin-top: 10px;">
2010 Turquoise<br>
Hex #45B8AC<br>
RGB(68, 184, 172)<br>
Pantone 15-5510<br>
</div>


<div style="background-color: #EFC050; color:white; padding: 10px; margin-top: 10px;">
2009 Mimosa<br>
Hex #EFC050<br>
RGB(239, 192, 80)<br>
Pantone 14-0848<br>
</div>


<div style="background-color: #5B5EA6; color:white; padding: 10px; margin-top: 10px;">
2008 Blue Izis<br>
Hex #5B5EA6<br>
RGB(91, 94, 166)<br>
Pantone 18-3943<br>
</div>


<div style="background-color: #9B2335; color:white; padding: 10px; margin-top: 10px;">
2007 Chili Pepper<br>
Hex #9B2335<br>
RGB(155, 35, 53)<br>
Pantone 19-1557<br>
</div>


<div style="background-color: #DFCFBE; color:black; padding: 10px; margin-top: 10px;">
2006 Sand Dollar<br>
Hex #DFCFBE<br>
RGB(223, 207, 190)<br>
Pantone 13-1106<br>
</div>


<div style="background-color: #55B4B0; color:black; padding: 10px; margin-top: 10px;">
2005 Blue Turquoise<br>
Hex #55B4B0<br>
RGB(85, 180, 176)<br>
Pantone 15-5217<br>
</div>


<div style="background-color: #E15D44; color:black; padding: 10px; margin-top: 10px;">
2004 Tigerlily<br>
Hex #E15D44<br>
RGB(225, 93, 68)<br>
Pantone 17-1456<br>
</div>


<div style="background-color: #7FCDCD; color:black; padding: 10px; margin-top: 10px;">
2003 Aqua Sky<br>
Hex #7FCDCD<br>
RGB(127, 205, 205)<br>
Pantone 14-4811<br>
</div>


<div style="background-color: #BC243C; color:white; padding: 10px; margin-top: 10px;">
2002 True Red<br>
Hex #BC243C<br>
RGB(188, 36, 60)<br>
Pantone 19-1664<br>
</div>


<div style="background-color: #C3447A; color:white; padding: 10px; margin-top: 10px;">
2001 Fuchsia Rose<br>
Hex #C3447A<br>
RGB(195, 68, 122)<br>
Pantone 17-2031<br>
</div>


<div style="background-color: #98B4D4; color:black; padding: 10px; margin-top: 10px;">
2000 Cerulean Blue<br>
Hex #98B4D4<br>
RGB(152, 180, 212)<br>
Pantone 15-4020<br>
</div>



## References

CSS Colors

- [CSS Legal Color Values](https://www.w3schools.com/cssref/css_colors_legal.asp)

Color Schemes, Psychology

- [Colors Tutorial](https://www.w3schools.com/colors/default.asp)

Common Colors

- [Color Names Supported by All Browsers](https://www.w3schools.com/colors/colors_names.asp)
- [Color Groups](https://www.w3schools.com/colors/colors_groups.asp)
- [HTML Color Shades](https://www.w3schools.com/colors/colors_shades.asp)
- [Colors of the Year](https://www.w3schools.com/colors/colors_trends.asp)
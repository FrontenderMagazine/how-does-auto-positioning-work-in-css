# How Does Auto Positioning Work In CSS?

A few weeks ago [Andrei raised an interesting question][1] on one of my older 
posts. The post shows how to create a navigation bar. Andrei noticed the submenu
was positioned absolutely, but none of its ancestor elements had positioning 
applied. Why then did the submenu display directly below its parent link in the
menu?

I honestly didn’t know. Like Andrei my expectation was the submenu should be 
[positioned relative][2] to the html element given the absence of other 
positioned elements and should appear in the top left corner of the browser, but
it didn’t.

The reason turned out to be very simple, though it took some investigation to 
find it. I’ll spare you the false leads and just explain what’s going on.

![2 toy wood blocks](img/blocks.jpg?raw=true&repo=how_does_auto_positioning_work_in_css "2 toy wood blocks")

## Containing Blocks Set Positioning Context

As a quick review when you use css positioning on an element its position is 
relative to some [containing block][3]. That containing block will be the 
nearest ancestor element that also has positioning (other than static) applied. 
If there is no ancestor with positioning applied the containing block becomes 
the initial containing block, which is the html element.

In the post I was using the [Suckerfish system to create a drop down menu][4]. 
I’ve been using it to create drop downs for as long as I can remember. You 
probably have too. By default you position your submenu far off the page, 
usually -999em to the left. On hover you change the value of left to auto and 
the submenu appears directly below its parent menu item.

At first glance the submenu seems to be ignoring the above information about 
containing blocks. None of its ancestors have positioning set implying it should 
be positioned relative to the html element or the browser window.

At least that’s what Andrei and I both thought should happen. Our mistake was in 
how we interpreted “auto” to work.

![Submenu open when hovering over menu item](img/submenu-open-on-hover.png?raw=true&repo=how_does_auto_positioning_work_in_css "Submenu open when hovering over menu item")

## How Should a Browser Interpret Auto?

We were both assuming that a value of auto was the same as a value of 0 and so 
setting left: auto was equivalent to left: 0. There are times when a css value 
of auto equates to 0. This is not one of those times though.

When an element has been set to position: absolute, its position (and possibly 
size) is specified with the `top`, `right`, `bottom`, and `left` properties. 
These properties specify offsets with respect to the box’s containing block. For 
[non-replaced elements][5], the effect of the auto value (for top, right, 
bottom, or left) depends on which of these other related properties have the 
value ‘auto’ as well.

That last part is the key. By definition auto isn’t 0. It might be, but it 
depends on the values set on the other properties.

The Suckerfish drop down sets and then changes the “left” property, but it 
leaves the other 3 properties as auto. When we initially set left to -999em the 
right value comes along for the ride and the submenu is located way off the 
page. On hover when we change left to auto we have the condition where all 4 
values are auto.

An article I came across on [auto positioning for absolutely positioned 
elements][6] explains what happens as does this article from Dev.Opera on 
[absolute and fixed positioning][7]. The quote below is from the latter.

>The default value for the top, right, bottom and left properties is auto, which 
means the absolutely positioned box will appear exactly where it would have had 
if it wasn’t positioned. Since it’s removed from the flow it will overlap any 
elements in the normal flow that follow it, though. 

You can see this in action in the images above and below this section. Above is 
where the menu appears when hovering over its parent link. Here left (as well as 
right, top, and bottom) are set to auto.

Below is the same menu when I remove positioning on the submenu. The submenu is 
located in the same place as above. The top level menu items are in different 
locations, because the sub menu is now again in the document flow, but the 
submenu itself is located in the same place in both images.

It’s obvious once you think about it. If the default of auto was 0, then an 
element with all 4 sides set to 0 would need to stretch to each edge of its 
container. There are times when we rely on this stretching. It’s part of one 
method for [vertically centering elements][8].

That’s clearly not what’s happening here. When all 4 sides (or really when 
either of the 2 opposing sides) hold values of auto it’s up to the browser to 
decide how best to locate the element.

We rely on this to [horizontally center block level elements][9] with 
margin-left: auto and margin-right: auto. In this case the margin is split 
equally and the element centered. In the case of the drop down, a child list 
gets displayed exactly where it would have been located if no positioning were 
applied.

![Submenu visible without positioning applied](img/submenu-without-positioning.png?raw=true&repo=how_does_auto_positioning_work_in_css "Submenu visible without positioning applied")

## Summary

Some of you might be thinking why am I wasting time talking about something so 
obvious. It’s obvious in hindsight, but it certainly wasn’t obvious to me before 
digging into containing blocks and how auto-positioning works. Since Andrei 
raised the question, I assume it wasn’t obvious to him either. And if it wasn’t 
obvious to either of us, I assume there are others who would also say it wasn’t 
so obvious to them.

Sometimes [we accept that a technique works][10] without taking the time to 
understand why it it works. When asked why we’re at a loss to answer. It’s 
worthwhile to [figure out why][11] and better understand what’s happening.

I’ll no longer take the auto value for granted on any property and assume it 
equates to what I initially expect. Instead I’ll think about how it will be 
computed and what will happen based on other possible auto values on related 
properties. More than likely it will usually behave exactly as I’ve always 
assumed, but I suspect there will be a few times where it doesn’t and in not 
behaving as expected it might allow for some interesting results.


[1]: http://www.vanseodesign.com/css/simple-navigation-bar-with-css-and-xhtml/#comment-401638
[2]: http://www.vanseodesign.com/css/css-positioning/
[3]: http://www.w3.org/TR/CSS21/visudet.html#containing-block-details
[4]: http://htmldog.com/articles/suckerfish/dropdowns/
[5]: http://www.w3.org/TR/CSS21/visudet.html#abs-non-replaced-width
[6]: http://www.vision.to/articles/auto-positioning-for-absolute-elements.php
[7]: http://dev.opera.com/articles/view/37-css-absolute-and-fixed-positioning/
[8]: http://www.vanseodesign.com/css/vertical-centering/
[9]: http://www.vanseodesign.com/css/centering-with-css/
[10]: http://www.vanseodesign.com/web-design/echniques-creativity/
[11]: http://www.vanseodesign.com/web-design/how-why/
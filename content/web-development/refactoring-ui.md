---
title: "Refactoring UI"
tags: ["UI", "design", "UX", "web design", "book"]
date: 2024-05-06T07:41:29+03:00
draft: false
---

These are my notes from https://www.refactoringui.com

## Starting

* Start with a feature, not a layout. Details comes later, defer choices like color.
* Establish a system (of choices): font size, weight, line height, colors, spacing, shadows etc.
* Refrain from using font weights under 400 for UI work, to de-emphasize some text, use a lighter color or smaller font size instead.

## Hierarchy

* Don’t use grey text on colored backgrounds, making the text closer to the background color is what actually helps create hierarchy, not making it light grey.
* Emphasize by de-emphasizing: change the secondary stuff to make the primary stand out, instead of focusing on the primary's attributes.
* Take a hierarchy-first approach to designing the actions on page, for a much less busy UI that communicates more clearly.
* Every action on a page sits somewhere in a pyramid of importance.
* Being destructive or high severity doesn’t automatically mean a button should be big, red, and bold.
* Labels are a last resort.
* Reduce contrast to de-emphasize heavy elements.
* Use weight to emphasize low contrast elements.

## Layout & Spacing

* To make something actually look great, you usually need more white space.
* If you want your system to make sizing decisions easy, make sure no two values in your scale are ever closer than about 25%.
* 16px is a great number to start with because it divides nicely, and also happens to be the default font size in every major web browser.
* A spacing and sizing system will help you create better designs, with less effort, in less time.
* You don’t have to fill the whole screen, a little extra space around the edges never hurt anyone.
* Don’t use percentages to size something unless you actually want it to scale.
* Grids are overrated, don’t be a slave to the grid — give your components the space they need and don’t make any compromises until it’s actually necessary.
* Relative sizing doesn’t scale: things should scale independently, there isn’t any real relationship at all, and that there’s no real benefit in trying to define a text size relative to another.
* Scale doesn't happen proportional — give yourself the freedom to fine-tune things independently, makes it a hell of a lot easier to design for multiple contexts.
* Always make sure there’s more space around the group than there is within it for a clear separation.

## Type

* Just like with spacing and sizing, a linear scale won’t work. Smaller jumps between font sizes are useful at the bottom of the scale, but you don’t want to waste time deciding between 46px and 48px for a large headline.
* Avoid em units.
* For UI design, your safest bet is a fairly neutral sans-serif — think something like Helvetica.
* For the best reading experience, make your paragraphs wide enough to fit between 45 and 75 characters per line. The easiest way to do this on the web is using em units, which are relative to the current font size. A width of 20-35em will get you in the right ballpark.
* Don’t center long form text, if something is longer than two or three lines, it will almost always look better left-aligned.
* Right-align numbers.
* Not every link needs a color.

## Color, Shadows & Images

* Use HSL instead of hex: easier to understand & reason with when looking at the values.
* You need more colors than you think. For each color, include 5-10 light/dark variety: Have greys, 1 or 2 primary colors and a few accent colors. It’s not uncommon to need as many as ten different colors with 5-10 shades each for a complex UI.
* Accessible doesn’t have to mean ugly: Flip the contrast, instead of using light text on a dark colored background, use dark colored text on a light colored background.
* Overlap elements to create layers and a feeling of depth.
* Don’t scale icons up or down: when you blow them up to 3x or 4x their intended size, they lack detail, and always feel disproportionately “chunky”.
* Don’t scale down screenshots: If you don't have space, either take the screenshot on a smaller screen or take a partial screenshot.

## Tips

* If you’re working on a testimonial try “promoting” the quotes into visual elements by increasing the size and changing the color.
* If your design includes a bulleted list, try replacing the bullets with icons.
* Add color with accent borders: such as the top of a card, for more visual flair.
* When creating separation between two elements, use fewer borders, instead consider using a box shadow or use two different background colors or add some extra spacing.

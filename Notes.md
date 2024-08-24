# Starting a new project using our template:

- It’s time to start a new project! .For this one, all the assets, the design files, and a style guide are available in the GitHub repo in **projects/04-chef**. We will also use our starting template to get things off the ground.

# Some important notes:

## The design:

- You will notice that I’ve only provided a desktop version of the design. That does not mean it should only work on mobile!

- I get a lot of people who tell me they’re only provided with a desktop version of designs, so I want to look at how we can approach that and still take a mostly mobile-first approach to creating the layout.

- In the next lesson, I’ll analyze the design and look for important things we’ll need to consider when writing our code.

## The images:

- I’ve provided the images as .jpg, as well as .webp and .avif. We’ll be covering how we can use these with a `<picture>` element.

- If you are forging ahead on your own but are unfamiliar with the `<picture>` element, you can use regular images for now, as it’s not a big change to update them afterwards.

## How I’m going to approach this:

- In the next lesson, I’m going to analyze the design. I want to look for things we don’t have in our Sass right now that would be useful to have from the get-go.

- I’ll add the more generic things in from the start so that we can have a relatively functional layout once we’re done writing our HTML, and then we’ll dive in and get into some more specific styling.

# Analyzing the design:

## Setting up the abstracts:

- The first step in starting a new project with our starter template is setting up our abstracts, which will in turn create a lot of the utility classes that we will be using.

- If you open up the designsystem.md that’s provided, most of what we need is in there, or you can grab everything out of the Figma file as well.

- I will be doing something a little bit different in the `_typography.scss` file, so if you are building this out on your own, you might want to check what I do there to see if you want to take the same approach or not.

### Colors:

- The colors are very straightforward, so you can just copy and paste them in.

- One thing you will notice is the jumps in the numbers I use for the neutral colors, skipping from 900 to 400, as well as skipping 300. I did this in case the project ever grows in the future, if feel like there is a large jump between two shades, I’ll leave space to squeeze in another shade in the future.

- One of the reasons I use jumps of 100 is to make it easy to add new color values in the future (say a 250), but when I have a limited color pallet and see big jumps, I like leaving room for more.

### Typography:

- For the font-sizes, I simply port them over from pixels to rem. We can also keep the two font-weight variables, as that’s all that is used in this project.

- For the font-families, we can keep things super simple, but I like adding an extra layer of abstraction here. First up, in our `_typgraphy.scss`, I'm going to set up the following:

```
$ff-sans: "Lato", sans-serif;
$ff-serif: "Abril Fatface", sans-serif;
$ff-base: $ff-sans;
$ff-accent: $ff-serif;
```

- You might not feel like you need to bother with the second two variables, and if that's the case, no problem at all!

- I like doing this because as projects grow, having a little more abstraction can make changes easier to deal with in the long-run.

- Next we want to be able to use these variables, so let's create a `_font-families.scss` inside our abstracts folder, **@forward** it from the abstracts `_index.scss`, and then add utility classes

### Sizes: Video and PDF (very important)

```
$sizes: (
8: 0.5rem,
12: 0.75rem,
16: 1rem,
20: 1.25rem,
24: 1.5rem,
32: 2rem,
36: 2.25rem,
48: 3rem,
);

$section-padding: 7rem;
$hero-padding: 20rem;

$container-inline-padding: 1rem;
$container-max-width: 77.75rem;
$container-max-width-wide: 100rem;

$card-body-padding: 5rem;

$grid-gap: 1.5rem;
$flex-group-gap: 1rem;
```

## New utilities and generic layouts:

- When we analyzed the design, we saw a few things that would be useful to be able to set when we need them: uppercase text, centered text, a box-shadow

- And we also had a few different layouts:

  - A container and a wider container
  - 2, 3 and 4 column layouts
  - And there were a few other things along the way as well:
    - those underline decorations, the buttons

- In this lesson, we’ll focus in on those first two groups, which fall into our utilities and layouts folder, which we’ll focus on in this lesson. We’ll be referring to a lot of the new variables we just made in these files.

- The last two will go inside our components folder as they have a little bit more going on with them, and we’ll explore those in the next lesson.

### The utility classes:

- We’ll start with the utilities as they’re more straightforward -> uppercase, text-center,box-shadow

  ```
  .uppercase {
  text-transform: uppercase;
  }
  ```

  ```
  .text-center {
  text-align: center
  }
  ```

  ```
  .box-shadow-1 {
  box-shadow: 0 0 .75rem rgb(0 0 0 / .25);
  }
  ```

- You might feel like having a separate file for each of these is overkill, but as I’ve shown before, it makes it super easy to find what you’re looking for, if ever you need to make a change.

#### Font-families

- We already created these earlier in utilities/\_font-families.scss, but if you skipped this earlier you can add them in now.

- In general, it's common to only have two or three fonts in any given project, so I keep them all in that single file, but if you prefer, you could give each their own partial as well.

- As I said though, I’ll keep them them in the same file.

#### New spacing utilities

- While we’ve already created a **.text-center** class, we have a few block-level things that might need to be centered along the way. While flexbox and grid can be used, sometimes just throwing a `margin-inline: auto` is all we need, so I like to have a class that can do that for me.

- So, inside the utilities folder, in our existing `_spacing.scss`, I’m going to add:
  `.mx-auto { margin-inline: auto; }`

- We created some variables for our hero and section padding, so in utilities `_spacing.scss` partial, I’m going to add these two:

```

.hero {
padding-block: $hero-padding;
}

.section {
padding-block: $section-padding;
}

```

#### The containers: To explain more in Video

- We need two different containers, a “regular” one, and a wide one. Some might see this more as a layout class, and if you want to stick it in there, feel free. For me, these pretty much do one very simple thing, so in the utilities folder, I’m going into the `_container.scss` partial file.

  ```
  @use '../abstracts' as \*;

  .container {
  --max-width: #{$container-max-width};
  --padding: size(16);

  width: min(var(--max-width), 100% - ( var(--padding) \* 2) );
  margin-inline: auto;
  }

  .container[data-type="wide"] {
  --max-width: #{$container-wide-max-width};
  }
  ```

### Note: Although SASS variables compile at runtime, CSS custom properties compile when they're used. the best way to combine them is using interpolation Ex: `--max-width: #{$container-max-width-wide}`

#### Grouping of things

- There are two spots where having a `display: flex` with a gap on them would be really useful:

  - The navigation
  - The buttons

- Little groupings of elements like this are pretty common in most layouts, so I like having a simple utility class here will help us in this project, as well as future ones. So, let’s make a `_flex-group.scss` partial inside of utilities:
  ```
  .flex-group {
  display: flex;
  gap: var(--flex-gap, $flex-group-gap);
  }
  ```

### The layout classes:

#### Generic even-columns

- In my layout folder, I’m going to create an `_even-columns.scss` file, and in there we can use grid:

  ```
  @use '../abstracts' as \*;

  .even-columns {
  display: grid;
  gap: var(--grid-gap, $grid-gap);
  }

  @include mq(medium) {
  grid-auto-flow: column;
  grid-auto-columns: 1fr;
  }

  ```

- By declaring `display: grid`, we can use `gap` for the vertical spacing when they’re stacked, and the column spacing when we hit our media query.

- For the `gap`, I’ve used a custom property the way we approached setting up `--flow-spacer` in the past, so we have a default value, but we can easily overwrite it when needed.

- For the media query, I’ve used the` medium` breakpoint for now, which should work out fine, but may need some tweaking. Since we only have a large design to base things off, we’ll see what things look like down the line to see if we need to tweak anything.

#### Auto fit grid

- The 4 column section will look a bit strange if we use this class on it—see the video above if you want to see what I mean—so I think using grid’s auto-fit will work better (while also leaving us with a very useful layout class to bring into our template afterwards 😉).
- Once again, we’ll create a new file in the `layout` folder, calling this one `_grid-auto-fit.scss`.

  ```
  @use '../abstracts' as *;

  .grid-auto-fit {
  display: grid;
  gap: var(--grid-gap, $grid-gap);
  grid-template-columns: repeat(auto-fit, minmax(min(250px, 100%), 1fr));
  }
  ```

- You will notice I used 250px here. We could update this use a variable and custom property fallback as well.

### The components:

- As I mentioned in the previous lesson, we have a couple of components that are in this project:
  - The buttons
  - The headings with those underline decorations on them

#### Buttons:

- We have two different buttons, and we’ll stick with the CUBE approach that we’ve already looked at.
- To start with, we need to create a `_buttons.scss` partial inside our `components` folder.
- In that file, let’s create a base button style to start. We’ll use this to remove some of the default styling from both links and buttons, and set the stage for the modifiers to come later.

  ```
  @use '../abstracts` as *;

  .button {
  display: inline-flex;
  cursor: pointer;
  text-decoration: none;
  text-transform: uppercase;
  line-height: 1;
  border: 0;

  padding: 1.125em 2em;
  }
  ```

- The `padding` values come right from the design. I’ve used `em` rather than the `size()` function because I like using `em` for the padding on components like this, as it helps them scale if ever we did end with ones with different a `font-size` on them.

##### The modifiers

- For the modifiers, we need one for an orange background, and the other to have an outline.

- The one with the orange background is very straightforward, but the out with the white border/outline means that we need to think about things a little bit more.

- A border takes up space, so if we use that, then all my buttons will have to have a border on them, and we’ll have to take that into account with our styling of them.

- Putting a border on all the buttons is a good approach, and with custom properties we can make it easy to adjust things as well, but for some reason I’ve never liked that solution, and we do have outlines that we can play with instead … so here is my solution:

  ```
   .button[data-type="accent"] {
   background-color: clr(accent, 400);
   color: clr(neutral, 900);
   }

   .button[data-type="outline"] {
   --outline-size: 5px;
   background: transparent;
   outline: var(--outline-size) solid clr(neutral, 100);
   outline-offset: calc(var(--outline-size) \* -1);
   color: clr(neutral, 100);
   }
  ```

#### Heading Decoration: Explained in Video (very important)

- This is one of the more interesting things, and I’m listing it as a component because I see this being linked with our section headings. We have section headings with:

  - a subtitle, but no decoration
  - a subtitle, with the underline
  - no subtitle or decoration

  ```
  .section-header {
  &:has(p) {
     margin-block-end: size(36);
  }

  &:not(:has(p)) {
     margin-block-end: size(36) * 2.5;
  }

  &[data-decoration="true"]::after {
     content: "";
     display: block;
     background-color: currentColor; // match the parent color
     width: 10rem;
     height: 1px;
     margin-block-start: size(36);

     .text-center & {
        margin-inline: auto;
     }
   }
  }
  ```

## Reset and Base Styles:

### Small update to our reset

- Currently, our reset is doing very little, but that’s fine!
- We have a some images coming in on this project though, so let’s add this to our `base/_reset.scss`:
  ```
  img,
  picture,
  svg {
  display: block;
  max-width: 100%;
  }
  ```

### The general styling

- Next up, let’s move to our base/\_general.scss file and set up the stage for the project:

  ```
  body {
  font-family: $ff-base;
  font-weight: $fw-400;
  font-size: fs(400);
  color: clr(neutral, 400);
  line-height: 1.5;
  }
  ```

- And with that in place, let’s write some HTML!

## Writing HTML: Explained well in Videos

### About Section:

- In second column of about-section, there is an issue right now is the spacing between the elements, namely that there is no space after the `p`, but space between the two paragraphs.

- There are a few ways we could approach this. In my opinion, the two easiest thing to do is add a `class="flow"` to the parent, and then either:

  - Create a utility class that we can put on the first paragraph to remove the spacing on it
  - Create a selector using a combinator to do it for us

- With how consistently we have a subtitle like that, I think the best approach is actually to drop the more traditional utilities from this part that sets up the sizes, colors, uppercase text and all that, and to instead create a new one!

- So in `utilities`, I'm going to create a new file called `_lead.scss`

### Image: (using Picture element)

- so I mentioned in the first lesson, I provided .jpg for the images, but we also have .avif and .webp.

- Luckily, it’s very easy to set up. First, we simply need to wrap our img inside of a `<picture>` element

- The `<picture>` element is a bit strange in how it works, as the element is in the DOM—and can sometimes make debugging images misbehaving a little more complicated—but it basically changes the `src` of an image if the browser supports the alternative sources that we give it.

- To provide it with an alternate source, we use a `<source srcset="" type="">`. The `srcset` attribute points to the file that you want just like `src`, and the type is what the file is, and the format of the image. It sounds like a lot, but it’s simple

- The browser will look at the first source. If it recognizes the format, it will use that. It will skip it and look at the next one if it doesn't recognize that format.

- If it doesn’t know what any of them are, it’ll stick with whatever the default was on the `<img>`. If you’d like to see which one the browser has picked, you can open the page and inspect the `<img>` with your devtools. Interestingly, the src will still say it’s the .jpg, but if you hover over that, it will show you what it’s really using.

### The four column section: Explained in video.

### the meals section: Explained in PDF.

### The testimonials: Explained in PDF.

### The footer: Explained in PDF.

## Styling the header:

### Creating a file for our site’s primary header:

- In our `components/` folder I’m going to create a `_primary-header.scss` file and make sure to **@forward** that from the `_index.scss` file in the same folder.

- As we saw when we analyzed the design, the header is on top of the background from our hero, but doesn’t actually take up any space `.primary-header {position: absolute; inset: 0 0 auto;}`

- `inset` is a shorthand for top, bottom, left and right, and in this case, we’re setting everything other than the bottom to 0. The auto value for the bottom will have the height of the element be calculated based on the content inside of it.

### Styling the navigation itself:

- For the navigation, I’m going to create a `_navigation.scss` file inside the components folder. Then, I’ll create a selector for my `.primary-navigation`, in which I’ll nest all the styles.

- **Note**: even so there isn't much to do on `.primary-navigation` class itself because it's the class of `nav` not `ul` but instead of making another class we can style things on `ul` by nesting.

```
@use "../abstracts/" as *;

.primary-navigation {
  --flex-gap: #{size(48)};

  ul {
    list-style: none;
  }

  a {
    text-decoration: none;
    color: clr(neutral, 100);
    font-family: $ff-accent;
    font-size: fs(600);

    &:is(:hover, :focus) {
      opacity: 0.7;
      text-decoration: underline;
    }
  }
}
```

## Styling the hero: Explained in Video

- I made a decision early on that my spacing would be linked to my .hero class. Because it’s spacing related, I put the styles for that in my `_spacing.scss` partial. Of course, our hero has more styles that it needs, such as the background.

- We could leave the spacing for the .hero where it is and everything would be fine. Ideally, you would be changing that size without even having to look where it’s being used, since it’s based on the variable in our abstracts/sizes anyway.

- So, if you want to that where it is, I think that’s fine, but I am going to create a new partial for the rest of the styles we need here anyway, so I’m going to move it into that file.
  With that in mind, in `components/` we can create a `_hero.scss`, make sure to `@forward` it from our `_index.scss`, and then back in `_hero.scss`, we can `@use` our `abstracts` folder as we’ve been doing through out this project.
- After that, we can cut and paste the padding from `_spacing.scss` into our new `_hero.scss` file, and also add a little bit more

  ```
  @use "../abstracts/" as *;

  .hero {
  padding-block: $hero-padding;
  background-image: url("../../assets/hero-bg.webp");
  background-size: cover;
  background-position: center;
  }
  ```

## Improving the buttons: Explained in Video

```
@use "../abstracts" as *;

.button {
  display: inline-flex;
  cursor: pointer;
  text-decoration: none;
  text-transform: uppercase;
  line-height: 1;
  border: 0;
  font-weight: $fw-700;
  padding: 1.125em 2em;
  transition: 275ms ease;

  &[data-type="accent"] {
    background-color: clr(primary, 400);
    color: clr(neutral, 900);
  }

  &[data-type="outline"] {
    --outline-size: 5px;

    background: transparent;
    outline: var(--outline-size) solid clr(neutral, 100);
    outline-offset: calc(var(--outline-size) * -1);
    color: clr(neutral, 100);
  }
   &:is(:hover, :focus) {
    background: clr(neutral, 100);
    color: clr(neutral, 900);
  }
```

## Design tweaks:

- In this section, there are a few small things that we’ll add, like shifting that image up in the about section, but most of it will be about tweaking things we’ve already done to help things look better, and be more responsive.

### A small tweak to our large headings:

- The line-height of all the larger text is too big, and the more I’ve been working on the other parts of this, the more it’s bugging me, so let’s fix it!

- If we had a more specific design system to follow, often they provide both a font-size and a line-height but we don’t have that, and from my experience it can be quite common not to have that depth of information, so let’s ballpark it 😅.

- We can do this in our `_font-sizes.scss` file, adding in that any size above 600 will get a `line-height: 1.1`:

  ```
  @use "../abstracts" as *;

  @each $size-name, $size-value in $font-sizes {
  .fs-#{$size-name} {
     font-size: $size-value;
     @if $size-name > 600 {
        line-height: 1.1;
     }
   }
  }
  ```

### The about image and section padding:

- Seeing as the `.shift-up` class does one thing and one thing only, inside our `utilities/` folder, let’s create a `_shift-up.scss` file and` @forward` it from the `_index.scss`.

- Normally, we could just plug a number in to move the image up, but I feel like the easiest way to control the placement of the image that is shifted upwards in the about section is to link it to the section padding instead of some arbitrary number.

- So, first we can `@use` our `abstracts`, and then base our transform on the `$section-padding` variable:

  ```
  @use "../abstracts/" as *;

  .shift-up {
  transform: translateY(calc($section-padding * -1.5));
  }
  ```

- This works really well, and the nice thing with it is, if `$section-padding` is ever updated, it will adapt with that, whereas a hard-coded number would have to be updated.

- And speaking of updating `$section-padding`, it’s fine on large screens but it takes up a lot of space on smaller ones, so we can improve it by using some viewport units.

- So, in `_sizes.scss`, we can update it, as well as our hero’s spacing:
  ```
  $hero-padding: clamp(15rem, 15vh, 20rem);
  $section-padding: clamp(3rem, 10vh, 7rem);
  ```
- Working with viewport units for this type of things always takes a little bit of playing around with, so feel free to adjust these if you feel like something would work better!

### Responsive Typography:

- Once again, things look fine at larger screens since they’re based on the design, which was made for large screens!

- The larger fonts are causing overflow issues at smaller screens though, and since we’re playing around with viewport units, we might as well fix them now.

- All the sizes are set up in `_typography.scss`, so we can navigate there.

- The only ones I feel like are causing problems are the `600,` `800` and `900`, but if you want to adjust more of these you can.
  ```
  $font-sizes: (
  900: clamp(2.5rem, 0.5rem + 10vw, 5rem),
  800: clamp(2.5rem, 0.5rem + 7vw, 3.75rem),
  600: clamp(1.4rem, 0.5rem + 3vw, 1.6875rem),
  500: 1.3125rem,
  400: 1.125rem,
  300: 1rem,
  );
  ```
- If you watch the video, you’ll see I had to muck around with them a little bit, so feel free to experiment and pick different values, or use a tool like **Utopia** to get all of the font-sizes to be responsive!

### Fixing the flex-group:

- Our .flex-group is working great… until things wrap.

- If our nav and buttons were left-aligned, the way things wrap wouldn’t be a problem at all, and we can’t assume that the `.flex-group` will only ever be used when things are centered, so this fix should only apply to items that are inside of an `.mx-auto`.
- The easy fix here is to do this:
  ```
  .flex-group.mx-auto {
  justify-content: center;
  }
  ```
- You could argue that we don’t even need the `.mx-auto` class, and instead use justify-content from the start, but the reason I like using `.mx-auto` is it can be used in different situations, not only where flex is involved.

- Then, doing something like this ensures that if it’s being used with flex, that at least it works as you’d expect it to.

### Fixing the gap:

- There is another problem though, which is the gap that we set up is fine when they are next to one another, but is way too big when the items wrap.
- To fix that, I’m going to make things a little more complex:

  ```
  .flex-group {
  display: flex;
  flex-wrap: wrap;
  column-gap: var(--flex-column-gap, $flex-group-gap);
  row-gap: var(--flex-row-gap, calc($flex-group-gap / 2));
  width: fit-content;
  }
  ```

- Let’s break that down a little.

  - What I’m doing now saying that my `column-gap` is the custom prop `--flex-column-gap`. If that variable is never defined anywhere, it will use the `$flex-group-gap`. That means that, by default, nothing has changed from how we originally set things up.

  - For the `row-gap`, we’re doing the exact same thing, but if there is no value given for the custom property (which there won’t be unless we declare one on a given component that is using `.flex-group`), then it will default to half the size of our `column-gap`.

- Visually, that works well, but if ever we need to overwrite one or the other, it’s nice and easy to do.

- This does mean that we need to jump back into our `_navigation.scss` file to update the name of the custom property there, so that it continues to work.

### The figures:

#### Some small fixes:

- Before we get to that though, currently the images aren’t always wide enough, and everything looks pretty ugly in general in the “in-between” small and large screens.

- For me, the easy way to fix this is to have a `max-width` on the individual columns.

- I want to do it on the `columns` themselves, rather than the `.even-columns` itself, because we still want to allow `.even-columns` to get to the width of it’s parent `.container`.

- So, jumping over to `_even-columns.scss`, we can add the following:
  ```
  .even-columns {
  // styles here
  & > * {
     max-width: 500px;
     margin-inline: auto;
  }
  // media query here
  }
  ```
- With that, we limit the total width of each element, and if ever they are smaller than their parent, they will stay centered within the space available.

- In that section, while we created the rule for it, I never used our “wide” container in both sections with the `.even-columns`, so in our `index.html`, we can find that section and on the `.container` inside that section, add the `data-type="wide"`.

#### Styling the interactive figure:

- When we wrote the HTML, we gave the meals the class of `.interactive-figure`, so in the components/ folder we can create a `_interactive-cards.scss` and` @forward` it from the `_index.scss`.

- First, we want to get the `figcaption` styled and in place on top of the image itself, so we can do this:

  ```
  @use "../abstracts/" as *;

  .interactive-figure {
  position: relative;
  overflow: hidden;

  figcaption {
     position: absolute;
     bottom: 0;
     background-color: rgb(clr(neutral, 900), 0.75);
     backdrop-filter: blur(0.35rem);
  }

  @media (hover: hover) {
     figcaption {
        transform: translateY(100%);
        transition: transform 350ms ease-in-out;
     }

     &:hover figcaption {
        transform: translateY(0);
     }
  }
  }
  ```

- I’ve already added the `overflow: hidden` because I know we’ll be moving the` figcaption` down and want it to be hidden when that happens.

- You might have noticed I didn’t include any padding on the `figcaption` here. Because we have our spacing utilities, I added `class="padding-32"` to them.

#### The hover styles:

- The issue is, we don’t want to hide it from people who are on a device that can’t hover on the cards.

- In situations where someone is on a phone or tablet without a mouse, we want things to stay as we have them right now really.

- If someone is using a mouse (or other pointing device), we want them to hide though, and we can do that by wrapping the styles in a `@media (hover: hover) { ... }` rule.

- This isn’t a perfect solution, as it relies on the device itself saying if it has a “primary” pointing device that can hover, so you might want to test it, specially if it’s really important information that’s potentially being hidden, but for this situation I think we’re safe to give it a try.

### The testimonial cards:

- If you remember from when we wrote the HTML, I see this as relatively generic cards, rather than some special testimonial component (though we will need to get that large open-quote in there).

- With that in mind, in our `components/` folder, we can create a new file called `_card.scss` and `@forward` it from the `_index.scss` file.

- To help keep things consistent, I’m going to use grid for the cards themselves:

  ```
  .card {
  display: grid;
  grid-template-rows: auto 1fr auto;,
  }
  ```

- The auto value on first and last row have them match the content that is inside them, and the 1fr allows the middle section to grow if there is extra space.

- The reason I’m doing this is the three columns don’t have an equal amount of content, but I want their heights all to match. The image and the footer should keep their sizes, and we just want the middle section, the `.card__body`, to grow when needed.

- We could do this with flexbox as well, but I find it is easier with grid. Plus, if our images were different sizes, we could put a set value for that row, and use `object-fit: cover` on the image to ensure they’re always the same size as well.

- Circling back to the `.card__body`, we want to keep the text that is inside it centered all the time as well, which we could use either flexbox or grid for, since there is only a single element inside of it:

  ```
  @use "../abstracts/" as *;

  .card {
  display: grid;
  grid-template-rows: auto 1fr auto;
  &__body {
     display: flex;
     align-items: center;
     padding: $card-body-padding;
  }
  }
  ```

- I’m also using the `$card-body-padding` variable that we created, since it was so much bigger than other spacing values that we have.

#### Adding Open quotes: Explained in Video

```
@use "../abstracts/" as *;

.card {
  display: grid;
  grid-template-rows: auto 1fr auto;

  &__body {
    display: flex;
    align-items: center;
    padding: $card-body-padding;
  }

  &[data-type="testimonial"] &__body {
    position: relative;
    isolation: isolate;

    &::after {
      position: absolute;
      top: -7rem;
      left: 1rem;
      z-index: -1;
      content: "\201C";
      font-size: 25rem;
      font-family: $ff-accent;
      opacity: 0.1;
    }
  }
}
```

### The section subtitles:

- The subtitles look terrible when they stretch the full-width of their container, so let’s limit their total width by going into `_section-header.scss`.
- In there, we can add:

  ```
  .section-header {
  // other styles here...

  p {
     max-width: 50ch;

     .text-center & {
        margin-inline: auto;
     }
   }
  }
  ```

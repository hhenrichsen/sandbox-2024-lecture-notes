# Design Notes

## Start with Features, Not Layouts

_From
[Refactoring UI](https://designary.com/tip/spacing-systems-and-scales-ui-design/)_

When starting on a feature, it's easy to fall into the trap of "what should this
all look like?", when instead you should focus on building just what's required
for that feature. You can iterate and ask what things should look like once you
have functionality.

## Information Architecture

The core of good design is information architecture. Not all elements on a page
are equal. Each page generally has a primary piece of information, and some
number of secondary and tertiary pieces of information.

Your design should reflect this. Primary information should be the most
emphasized and easiest to find. Some should be de-emphasized. Color, weight, and
size are easy ways to do this. You can also hide elements in each other, to
remove the amount of secondary or tertiary information, and make it easy for
your users to find the most important info.

## Design System

_From
[Designary](https://designary.com/tip/spacing-systems-and-scales-ui-design/)_

One of the benefits that using Tailwind gives you is the fact that it gives you
a built-in design system. A design system generally revolves around a pixel
grid; normally 4px or 8px. Spacing, images, sizes of elements, images, etc. are
all sized around that grid. The full system doesn't need to be defined in
advance, but should be decided on eventually.

## Responsive Design

One way of "take as much space as is needed" is not hardcoding widths, heights,
and the such. Setting boundaries at breakpoints tends to work well via
`min-width` and `max-width`, but content will generally resize to take up what's
in its children.

Responsive Design generally falls under the mental model of "my site supports
mobile", but building a truly responsive design will mean that your site will
work on the 1:1 square monitor, a mobile phone, and a 32:9 ultrawide monitor
without needing specific breakpoints.

Your content should be designed to squish elegantly, and if it no longer fits,
it should either render a different version, or no longer show.

## Parallel Structure and the Gestalt Rules of Grouping

Parallel structure is an idea from writing: sentences and paragraphs that
accomplish the same idea (list items in a sentence, new supporting ideas, or new
paragraphs) should each follow the same structure to help your reader
intuitively understand what is happening in a paragraph.

Similar is the Gestalt Rules of Grouping: things that take the same structure or
appear close together are psychologically grouped.

This idea comes pretty intuitively from components; you should build components
for ideas, and then use those wherever that idea occurs. Information that is
relevant to other nearby information should be placed near to that information.

## Designing for Readability

Lots of web content is based around text. You should make sure that your text is
readable. There are lots of different implementations about this, but the
general guideline is that text should be no wider than 50-70 characters wide:

- https://winterpm.com/#
- https://www.researchgate.net/publication/234578707_Optimal_Line_Length_in_Reading--A_Literature_Review
- https://designary.com/tip/the-optimal-text-line-length-for-readability/

I also like to apply this concept to other things as well. Users respond better
to taking as much space is needed, rather than using all of the available space.

## Other Resources

Here are some other resources I found interesting in learning a bit more about
design:

- Check out [This Talk](https://www.youtube.com/watch?v=7Z9rrryIOC4) by Steve
  Schoger, who is one of the people behind Tailwind CSS and the book on
  Refactoring UI.
- He also has [a YouTube channel](https://www.youtube.com/@SteveSchoger/videos)
  where he redesigns submitted websites, which can be a great place to learn
  about potential changes to make to yours.

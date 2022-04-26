# AmbientCanvasBackgrounds Integration

## References

### AmbientCanvasBackgrounds 
* GitHub - https://github.com/crnacura/AmbientCanvasBackgrounds
* Demo - https://tympanus.net/Development/AmbientCanvasBackgrounds/

### FlaskWebsite2
* GitHub - https://github.com/ezstarr/FlaskWebsite2
* Original Site - https://timeenjoyed.herokuapp.com/
* Demo Site - https://timetarot.herokuapp.com/

## Prerequisites
Use git to clone the [AmbientCanvasBackgrounds](https://github.com/crnacura/AmbientCanvasBackgrounds) repo so all the files are 
available on your local machine.  This will make it easy to copy/paste the files.

## AmbientCanvasBackgrounds Source
Figuring out what to copy
![source structure](images/ambient-background-structure.png)

### Folder Structure
We have separate folders for **css**, **js**, and then several numbered **index** html files.

Since there are 5 demos, we can assume each index html file corresponds to one of the demos.

## Swirl Demo
First identify the Swirl demo html file, and open it up.

## 1.) Copying CSS
At the top of the file in the &lt;head&gt; section, there are a couple [&lt;link&gt;](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link) tags that relate to **stylesheet**.  Those are custom CSS files being imported.

We want to copy both the files they refer to, and the &lt;link&gt; tags themselves, into the Tarot site.

<ul>
    <li>When copying the &lt;link&gt; tags, be sure to put them inside a <a href="https://developer.mozilla.org/en-US/docs/Web/HTML/Element/head">&lt;head&gt;</a> tag.  You may need to create one if it doesn't already exist.</li> 
    <li>When copying the files, remember that the Tarot site relies on files being under the <b>static</b> folder because Flask is using that by default.</li>
    <li>It would be good to still have a <b>css</b> folder under <b>static</b></li>
    <li>Adjust the &lt;link&gt; tags so their <b>href</b> value reflect the location of the files in the Tarot folder.</li>
</ul>

#### Run the Tarot Site
If you run the Tarot site at this point, you may not notice anything except the padding around the outside of the page has disappeared.

## 2.) Copying JavaScript 
Back in the Swirl page, the next thing we want to grab is the javascript files.  

The javascript import lines are at the bottom of the file though.  We want to do the same thing with these as we did
with the CSS files/tags.  Copy the &lt;script&gt; tags, as well as their files, into the Tarot site.

#### Run the Tarot Site
If you run the Tarot site at this point you should see no difference on the page itself.  But if you open Dev Tools (F12), you 
will see an error logged in the console, indicating why nothing is showing yet.

```shell
swirl.js:144 Uncaught TypeError: Cannot read properties of null (reading 'appendChild')
    at createCanvas (swirl.js:144:12)
    at setup (swirl.js:37:2)
```

<summary>If you want to understand what's happening, expand the Details section below</summary>
<details>
<blockquote>
This means the swirl.js code tried to append a new element to the DOM, but failed.

If we look at the swirl.js, line 144 (the top line in the stack trace), we see that it tried to call:
```shell
container.appendChild(canvas.b);
```
Based on the error "Cannot read properties of null", and the reference to "appendChild", we can infer that the <b>container</b>
value is what's null.

If we look up a few lines, we see where <b>container</b> is declared/assigned.
```shell
container = document.querySelector('.content--canvas');
```

This is trying to find a specific element of our HTML document.  It's doing that by looking for the element that has a class of "content--canvas", and it's not finding one.

If we look in the Tarot index.html, we'll see that doesn't currently exist.  

But if we look in the Swirl demo's index html file, we'll see that does exist.  So we're going to need to have an element 
with that class in the Tarot program too.
</blockquote>
</details>

## 3.) Making a Canvas
We need an element with the class "content--canvas" in order for the swirl.js code to do its thing.

Let's start by copying those elements from the Swirl demo index2.html.  Find the lines below:

```html
    <div class="content content--canvas">
        <h2 class="content__title">Swirl</h2>
    </div>
```

and paste them into the Tarot index.html, at the top of the &lt;body&gt; section.

#### Run the Tarot Site

You should see the Swirl demo effect at this point.  You'll also notice that you can't see
the TimeEnjoyed banner or any of the other content except the big "Swirl" demo text.

<blockquote>At this point we're done copying code from the Swirl demo project.</blockquote>

## 4.) Why Nothing is Visible 
The canvas on which the swirl background is drawn, is dynamically created at runtime by the swirl.js code.

It's actually creating a <a href="https://developer.mozilla.org/en-US/docs/Web/HTML/Element/canvas">&lt;canvas&gt;</a> element, and placing that inside the DOM, within the div that has the content--canvas class on it.

You can see the canvas element it created if you right-click the background animation and choose "Inspect".

There's a few things happening that prevent your form/banner from showing:
* The canvas is dynamically sized to fill the entire window, by swirl.js
* The canvas <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/position">css position</a> is set to <b>fixed</b> which makes other elements show behind it by default.
* The ability for the window to scroll was disabled by the base.css file by using the <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/overflow">overflow</a> attribute.  Although even if it was enabled the other two factors would still hide everything.

## 5.) Making Things Visible 

You will notice that the big "Swirl" title is visible.  In the HTML, that Swirl text is inside a div of a specific class.

```html
<h2 class="content__title">Swirl</h2>
```

That content__title CSS is doing a few things, but the most important thing is setting the <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/z-index">z-index</a> to a positive number.  That's
telling the browser to draw that in front of the <b>fixed</b> position canvas.

So we'll need to do something similar to the other elements that we want to show up over
the swirl background.

### Modify the HTML

There's a lot of ways to go about doing this, so I'm going to show the way I made the 
banner and form show up in the demo.

This html I changed from this:
```html
<div class="content content--canvas">
    <h2 class="content__title">Swirl</h2>
</div>
```

to this:
```html
<div class="content content--canvas">
    <div class="page-content">
        <img src="/static/images/banner.gif" width="500">
        <div>
            <h3>Get a tarot reading here:</h3>

            <form method="GET" action="/submit">
                <label for="name">First name:</label><br>
                <input type="text" id="name" name="name"><br>
                <label for="numofcards">Number of cards</label><br>
                <input type="number" id="numofcards" name="numofcards" min="1" max="6">

                <input type="submit" value="Submit">
            </form>
        </div>
    </div>
</div>
```

That HTML only has the form, just so more of the swirl particles show.  I remove the other divs/text.  Putting them back in just requires consideration of how to lay them out to work with the background animation.

And then in the &lt;style&gt; section of the index.html, I added new css for the "page-content" class referenced in that HTML.

```css
.page-content {
    z-index: 100;
    position: absolute;
    top: 30%;
    text-align: center;
}
```

That CSS handles setting the z-index so things show.  It's also roughly centering the div over the area
the swirl background is with the "position: absolute;" and "top: 30%" attributes.

#### Run the Tarot Site
At this point the site should look like the demo.

## Other Things

#### Messing with Swirl.js Parameters
If you open swirl.js, there are a bunch of constants declared at the top.  This control many 
aspects of the animation, like dimensions, speed, number of particles, color, and more.

Try changing the <b>rangeY</b> from 100 to 400, and re-run the site.  Now it takes up more of the vertical space of the screen.

You can bump the particle count up to 2000 to get more dots.

Just be aware that performance can degrade the more particles you have, and it may not be the best experience on mobile.








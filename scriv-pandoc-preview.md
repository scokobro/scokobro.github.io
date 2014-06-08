## Using Scrivener as a pandoc editor/previewer ##

This article explains how you can set up Scrivener to work as a `pandoc` editor/previewer. Of course, you don't actually need Scrivener and any text editor/browser combo will work ok. But, keeping things in the same app is handy and well, Scrivener is a great editor and it's a pity not to make the best of it.

This is what it looks like...

![Fig. 0 Left-pane is plain text pandoc markdown, right pane shows processed document](https://dl.dropboxusercontent.com/u/24949891/images/Scriv-pandoc-preview.jpg)

 
### Ingredients###
For this recipe you will need:

1. **Scrivener** (super-duper writing tool. Download (and pay!) [here](http://www.literatureandlatte.com/scrivener.php).) Or, a text editor of your choice...
2. **pandoc** (super-duper document preparation and conversion tool. Download [here](https://github.com/jgm/pandoc/releases).)
	* **Haskell Platform** (clever programming-language thing. Download [here](http://www.haskell.org/platform/).  You need to install this before `pandoc`. From what I remember, it's pretty big and takes a while.)
3. **markmon** (very useful software-thingy, the key to the whole set-up.  Download [here](https://github.com/yyjhao/markmon).)
	* **npm** (**N**ode **P**ackaged **M**odules. In order to easily install `markmon`. Download [here](https://www.npmjs.org/).)
	* **node.js** (In order to use `npm` and `markmon`. Download [here](http://nodejs.org/download/).)
	
The aim of this exercise - for me at least - was to find a comfortable way to be able to write and preview documents formatted with `pandoc` markdown. The idea being that such a document makes it easy to create nearly any file format that a publisher could dream of asking for. 

Also, being lazy, I like tedious things like formatting bibliographies to be done for me with as little input as possible. If anything, the approach I describe here is even easier that using the venerable LaTeX/bibtex combo.

![Fig1. Attempt to illustrate flow](https://dl.dropboxusercontent.com/u/24949891/images/Pandoc-markmon-flow.png)
Format: width=400px


The above illustration shows, more or less how things works (as far as I know...). To simplify, you create a `pandoc` markdown document, `markmon` watches it and whenever you save it `markmon` tells `pandoc` to process it and give you an updated html preview.

### 1. Getting started ###

After getting all the necessary bits and bob installed, setting things up is pretty straightforward. You start by creating `pandoc` markdown document - the syntax is described on the `pandoc` homepage and is very much like other species of markdown. For the purposes of previewing it doesn't matter what extension you give this document, there is no standard for markdown documents at the moment (`.md` seems to be favourite though). 

If you are going to be writing an academic paper or essay and you need to include citations and a bibliography then at the top of your document (actually anywhere at all but why not put it at the start, out of the way) you should include two fairly important bits of meta-data about where your bibliographic database is and what style of citation/bibliography you want crated.

The meta-data for a paper I'm working on at the moment looks like this - 

```
---
bibliography: /Users/USER/Documents/Bibliographies/mybib.bib
csl: /Users/USER/Documents/PANDOCfiles/csl/apa.csl
--- 
```

When `pandoc`/`pandoc-citeproc`(see note below) come to make my plain-text markdown document into an all-singing all-dancing `html`/`odt`/`doc` version this information tells them where to find:

**NB** this is the thing that turns citation markdown (see below, or look at `pandoc` documentation) into correctly formatted in-text citations and a fully formatted and organised bibliography. It comes with `pandoc`.

* the `.bib` file with all my bibliographic material in
* the `.csl` (citation style language - see note below) file that describes the style of citations and bibliography that I need.

**NB** CSL seems to be a very accessible alternative to the nastiness that is the `bibtex` citation style file (eeuuch!). If you need a certain style then chances are you can download it from [this site](http://editor.citationstyles.org/about/). If it's not there then you can use the online tool to create what you need.

In a pandoc markdown document, citations look like this
`[@Kress:1996]` or for year-only citations, like this `[-@Kress:1996]`. There are variations on this theme and you should look at the `pandoc` documentation of full details. Basically they are the citation keys from your `.bib` database prefixed with an `@` and enclosed in square brackets.

### 2. Starting markmon ###

Once you have a file that you want to preview then you have to tell `markmon` about it. So, you need to create a command that you can paste into the terminal to set the ball rolling. Below is mine, I'll explain what it all does afterwards...

```
node /usr/local/bin/markmon "/Users/USER/Desktop/PandocMarkdownFile.md" --port 3333 --command "pandoc --filter pandoc-citeproc" --stylesheet "/Users/USER/Desktop/myStyle.css"
```

It's fairly obvious what all this is I think but here's the rundown:

* `node [PATH TO MARKMON]` tells `markmon` (a `node.js` package) to start up...  
* the path in quotes is the location of the fie that `markmon` monitors and passes to `pandoc` for processing and previewing. 
* the `--port` option tells the mini-server created by markmon which port to shove its output to. So in this instance you would view the result of all this `markmon`/`pandoc` activity by opening up a browser window and pointing it at `http://localhost:3333`. The default is `3000`, so you can actually miss this option out and just point your browser at port `3000` instead if you prefer.
* the `--command` option is again optional, the default is for `markmon` to ask `pandoc` to return a basic html version of the `.md` file used for input.
* the `--stylesheet` option is the path to a `css` file which contains the styling for the `html` output. The default is perfectly fine but you can adjust it to your preference by including this option. I personally like the GitHub2 style that comes hidden inside [Mou](http://mouapp.com/), and can be borrowed for previewing purposes here too.

So, once you've created your pandoc markdown file and decided on your options, all you have to do is type/copy your command into the terminal and you should get something like this...


![Fig 2. Terminal response from markmon server thingy...](https://dl.dropboxusercontent.com/u/24949891/images/markmonFunctioning.png)


Now you're al ready to point your browser at `http://localhost:3333` (or `3000` ) and you should see your document beautifully rendered. If you used the `pandoc-citeproc` option then you should also see all your pandoc-style citations magically transformed into properly formatted citations and a full bibliography at the end.

#### Path problems ####

At first I experienced problem with this command as the various bits of it didn't know where to find the other bits - `node` couldn't find `markmon`, or `pandoc` or `pandoc-citeproc`... this even though they were all installed in pretty standard locations (I think). Being rather a know-nothing I decided the easiest way around this was just to include the full paths in the command. I'm pretty sure this is not the most elegant or efficient solution but it worked for me. If you need to find the path to a command the `which` command is your friend; typing

```
which pandoc
```

at the terminal prompt gives you something like this...

```
/usr/local/bin/pandoc
```

the full path to the pandoc command. Bingo. 

### 3. Scrivener ###

Right, now the obvious next step. This, for me at least was to make best use of Scrivener's structured approach to writing and combine it with the benefits of working in pandoc markdown.

This is probably the least satisfactory part of the setup but this is unavoidable as we need an .md file to preview and not a .scriv file so we have to `Compile...` rather than just `Save`, this adds a few seconds/mouse-clicks to the process.

Create a blank project and write your paper in pandoc-markdown. Easy. The only adjustment you really need to make is to be aware that when you compile for markdown (an option set in the dropdown at the bottom of Scrivener's Compile panel), your document headings are automatically prefixed with the necessary number of `#`s to make them into headings at the right level.

Another things to be aware of is the possibility that the default meta-data (again set in the Compile panel) may interfere with `pandoc`,  I found it best to just delete it.

So, compile your document as markdown (file ending doesn't matter really), then create your command to start `markmon` (see above) putting in the path to your newly created file. Hopefully you get a response like the one pictured above and your mini server starts without problems. 

Now go back to Scrivener, split your screen (horizontally? vertically?your choice!), select the empty pane and add the preview web page to your research folder by typing in the url (`http://localhost:etc etc...`)

![Fig. 3 Add web page...](https://dl.dropboxusercontent.com/u/24949891/images/scrivener%20add%20screen.png)


Now, you should have a split screen with your source document in one half and a fully-rendered preview in the other half. This is as close as I have managed to come yet to recreating the experience of markdown editors like [Mou](http://mouapp.com/) or [MMD Composer](http://multimarkdown.com/) for *pandoc* markdown.

I was actually pretty amazed when this all worked 'out of the box' for me. Let me know how you get on! There may be things which I've forgotten to mention which are necessary to get it up and running, if you notice anything, let me/everyone know.

Cheers.



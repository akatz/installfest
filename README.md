# Railsbridge Installfest StepList

Author: Alex Chaffee <mailto:alex@stinky.com>

# Usage

    bundle install
    rake run

If the above fails (say, because `rerun` doesn't work on your system), try

    rackup
    
Then open <http://localhost:9292> in a web browser.
 
# Overview

This is a Sinatra app, deployed at <http://installfest.heroku.com>, which leads students through the various complicated setup instructions for getting Ruby, Rails, Git, etc. installed on whatever combination of computer, OS, and version they happened to bring the the workshop.

The site comprises files stored in a "site" directory; the one we care about now is ROOT/sites/installfest.

These files can be in any of these formats:

* `.step` for StepFile
* `.md` for Markdown
* `.mw` for MediaWiki
* `.deck.md` for deck.rb

(If multiple files exist with the same base name, .step is preferred over .md, and .md over .mw.)

Markdown is a lightweight markup language designed by John Gruber. The syntax is described at the [Daring Fireball Markdown Page](http://google.com/search?q=markdown+syntax) plus [GitHub Flavored Markdown](http://github.github.com/github-flavored-markdown/) extensions. (This README is written in Markdown.)

MediaWiki is the format of pages on the Devchix Wiki. This format is not fully supported and is provided as a temporary bridge while we move materials from the Devchix Wiki into this app.

StepFile is a new, Ruby-based DSL for describing complex, nested instructions in clear, reusable chunks.

[Deck.rb](https://github.com/alexch/deck.rb) converts Markdown files into an interactive in-browser HTML+JavaScript slide deck.

# StepFile Reference

A StepFile is a [DSL](http://en.wikipedia.org/wiki/domain+specific+language) for describing a series of instructions, possibly nested inside other instructions. Technically speaking it is an *internal Ruby DSL* which means it parses as Ruby code. Nested blocks use Ruby's do..end structures, named options use Ruby's hash syntax, and string options may use any of Ruby's myriad string formats (double-quote, single-quote, here doc, percent-q, etc.)

Here Docs are especially useful with `message`s since you can just dump in markdown between `<<-MARKDOWN` and `MARKDOWN` declarations.

## steps

* steps support nested content via `do` and `end`
* indents the nested block content
* inside the nested block, the step count is reset, then resumes afterwards, e.g.

        Step 1: steal underpants
        Step 2: do the hokey pokey
          | Step 1: put your left foot in
          | Step 2: take your left foot out
          | Step 3: put your left foot in again
          | Step 4: shake it all about
        Step 3: profit!

`step "name"`

  * creates a new step heading
  * maintains a count of steps at the same level
  * prefixes name with e.g. "Step 1:"

`link "name"`

  * links to a step whose file name is `filename`

`next_step "name"`

  * makes a new step named "Next Step:"
  * links to a step whose file name is `filename`

`choice`

  * creates a step which is named "Choose between..."
  * choice doesn't make any sense without a nested block
  * nested block usually contains `option` steps

`choice "name"`

  * same as above, but named "Choose name"
  * if you want ellipses, add them yourself

`option "name"`

  * creates a step which is named "Option 1: foo" instead "Step 1: foo"
  * supports nested blocks, which reset the step count again

`verify`

`verify "name"`

  * usually contains `console` and `result` notes
  * kind of like a step, but doesn't increment the number count

`tip "name"`

  * called out in a blue box
  * the name is *not* markdown, but is a bold title for the tip box
  * content should be inside a nested block

## messages

`message "text"`

  * makes a paragraph of text anywhere in the document
  * the text parameter is passed through a Markdown converter
  
`important "text"`

  * like a message, but called out in a red box

`todo "text"`

  * meant as a note to future authors
  * set aside from surrounding text (in brackets and italics)
  * [should these be made invisible for students?]
  
## special

Special elements do *not* format their text as Markdown.

`console "text"`

  * indicates that the student should type something in the terminal
  * says "type this in the terminal:" and then puts the text in a `pre` block
  * [should this be named `terminal` or `type` instead?]

`result "text"`

  * indicates that the student should see some output in the terminal
  * says "expected result:" and then puts the text in a `pre` block



## erector elements

StepFile is an [Erector](http://erector.rubyforge.org)-based DSL, so if you want to insert HTML tags or other stuff, use the appropriate Erector methods, e.g.

    step "figure out your OS version" do
      message "Mac OS has code names, including:"
      table do
        tr do
         th "Leopard"
         td "OS X 10.5"
        end
        tr do
         th "Snow Leopard"
         td "OS X 10.6"
        end
      end
    end


# TODO (features)

* big fat checkboxes
* checking the box should make the step go green
* unchecked steps should be orange
* maybe all unchecked but the next step should be gray
* JS expando-collapso doohickeys
* inlined steps, esp. verify, to share code without switching pages
* readme: examples
* `result` should take a regexp; then it will have an input field where the user can paste in what they got and it'll check it... for partial version strings
* `console` should start with a $
  * but what about multi-line commands (with e.g. config data on line 2), or multiple commands?
* put a [?] next to all the `terminal` boxes with a popup or link explaining what a terminal is
* links to other docs (the `link` directive)
  * rounded boxes with arrows
  * add a "back" link (or "next") to go back to the linking list
  * side scrolling effect
* add a `tip` directive
* add a `details` block (nested, maybe collapsed)
* each page/step could say which OS it's for, either for information, or to actively hide other OS step
* "result" should take "or"
* "result" needs to distinguish between literal console output, and a description of what you will see
* "yay" directive ("Congratulations!")
* prerequisites
* "sudo" directive -- for danger color and popup "what is sudo?" help

# TODO (technical)
* upgrade to Erector 0.9
* test all pages during "startup"
  * growl if broken
* 404 should show page with TOC and stuff
* StepFile object
* move fonts local

# TODO (content)
* install ALL the operating systems!
* troubleshooting page
* look into installation scripts
  https://github.com/joshfng/railsready
  


# Credits

some icons from https://github.com/kennethreitz/open-icons


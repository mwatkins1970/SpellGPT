INTRO

SpellGPT is an experimental tool to explore GPT-3's "miraculous" ability not only to spell most of its own token strings (it being a "character blind" model - see https://arxiv.org/pdf/2212.10562.pdf) but also to use this spelling ability as a means to produce novel outputs triggered by various "glitch tokens" (" SolidGoldMagikarp", et al.). 

This tool currently consists of a single python script, spellGPT.py. Apologies for the terrible code (all of my unnecessary global variables, etc)! I realise it's a mess, but I'm just getting back into programming after many years away from it and had never built a GUI before. Bear with me.

I've used the matplotlib and tkinter libraries for the GUI.

SETUP

Launching the script should open a settings window where you'll need to enter you OpenAI API security key.
You also need to enter:

* The GPT-3 engine you want to use (e.g. davinci-instruct-beta, curie-instruct-beta, text-davinci-003, davinci)

* A GPT-3 token (although any string will work, this tool produces the most interesting results when applied to individual tokens, primarily "glitch tokens" - see https://www.lesswrong.com/posts/aPeJE8bSo6rAFoLqg/solidgoldmagikarp-plus-prompt-generation)

* "starters". The idea is to extend the base prompt 'Please spell the string "{token}" in all capital letters, separated by hyphens.\n'  with a secondary string of hyphen-separated capital letters, to encourage the model in the direction of spelling. Some suggestions are "I-" (the default), "I-A-M-", "W-H-Y-", "W-H-A-T-", "T-H-I-S-".

* "weight cutoff". At each generation, sequences of letters correspond to cumulative products of probabilities (provided via the API's top-five log-probs). Setting a larger value for this parameter leads to richer trees, but beyond a certain point they become unhelpfully overpopulated. The default value of 0.0075 seems to work well.

* "maximum depth". The maximum number of generations/iterations allowed for a given prompt extension. This corresponds to the maximum number of letters which can get added to the prompt per iteration, or tree depth for subtrees that are produced. The default value of 10 seems to work well.

* subdirectory to save images and JSON files to to (this will default to the subdirectory /SpellGPT_outputs)


USING "STARTERS", OR NOT

If the "starters" field is returned empty, then a different approach must be used to generate the first letter, since the top five log-probs for next token produced by the base prompt are never capital letters. In this special case, the base prompt will be used some number of times in a loop at temp = 1 until a alphabetical-letter-plus-hyphen pair of tokens is output. The branches from the root node are produced statistically based on this sampling. All further generations are generated solely by the top-five log probs produced by the API for the current prompt-plus-extension. Larger numbers will cause some time delay in generation and be costlier in token use, but lead to more "accurate" results. Note that the davinci model is very resistant to producing these token pairs, compared to the instruct models. The default value of 100 seems to work perfectly well for davinci-instruct-beta and tends to use 10-20,000 tokens

Note that if you're mostly interested in looking at how GPT-3 models try to spell their token strings, with no leading suggestions, it makes sense to submit "starters" as an empty field.

TREE-BUILDING

Once the starting parameter values have been submitted, a process will begin running in your console for (typically) minute or so, before a window opens with a tree diagram plot and a dialogue box.

VISUALLY ADJUSTING TREE DIAGRAM LAYOUTS

You're asked "Do you want to adjust this tree?". If the tree is overcroweded or has heavily overlapping branches, this is your chance to quickly sort it out. Clicking "yes" introduces a new dialogue allowing you to sequentially specify a (node, rotational angle, spread factor) triple. The node must be a valid node visible in the tree, the rotational angle is in degrees (positive = anticlockwse) and will result in the appropriate rotation of the subtree emanating from the node in question. The spread factor controls the angular spacing of the first generation of branches emanating from the node in question. "*" designates the root node.

Submit any number of these visual updates, and then click on 'done' to finalise the image. You're given one last chance to change your mind. If not, this image will get saved to your subfolder, timestamped and named according to the current prompt.

EXTENDING PROMPT

When the tree diagram layout is finalised, you are asked to choose a visible node to extend the current prompt. Submit this to continue, and repeat until you're done, at which point you can select the root node ("*") to terminate the application. 

FURTHER DEVELOPMENT NOTES

Ideally
* the settings window would include a dropdown so users could select from a number of base prompts (or just type in one of their own)
* a dropdown to select from the list of glitch tokens
* a dropdown to select the engine
* users could change the cutoff between iteration
* the token counter that's currenlty printed to console would appear embedded in the main window (upper left), possibly even tied to a pricing factor so users could see how much they were spending
* all of the console activity would appear in a scrolling text window in the left frame of the main window
* nodes would be clickable for rearranging tree diagrams (mouse left/right to control rotation, mouse up/down to control branch angle spread fact)
* nodes would be clickable for selecting prompt extension
* some kind of cumulative or average weight displayed to indicate to user how "with" or "against" the stream of log-probs they are swimming in their selection of prompt extensions... perhaps an estimate of the temperature mosty likely to produce that particular rollout
* user settings where things like font, colour, window sizes, etc. could be set
* a better way of dealing with proportionality between weights and branch thicknesses (currently there's a minimal thickness involved)
* a means to CTRL-Z or more generally rewind a rollout (deleting any saved files in the process)

I think there's a minor bug where, if you select an invalid node for prompt extension, an image gets saved (resulting in duplicates)


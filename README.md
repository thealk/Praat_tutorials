# Praat_tutorials
 Tutorials for learning to use and script in Praat

# PRAAT SCRIPTING


Useful for carrying out several iterations of measurements and analyses that must be done in Praat.
Saves a LOT of time (once you get the basics down)
More consistent measurements

Tell Praat where you want it to do something by using TextGrid boundaries that line up to relevant time points in your sound file. 

For example, workflow for most of our lab collected speech is generally 
1. Collect speech via automated experiment set up
2. Clean and prep (throw out problematic files, eliminate large silences and non speech sounds at the ends of sound files)
3. Force align
4. Isolate segments and words of interest and modify if necessary
5. Perform annotation if necessary
6. Take measurements at areas of interest

Praat scripts can be used for several steps during the above process to facilitate annotation, make quick automatic adjustments, restructure TextGrid formats, log measurements, etc...

## About the data in this tutorial:
Voiceless sibilant final words of interest in a carrier phrase: "Move the hanger above the fridge, now move the *niece* above the hat.

### TextGrid format: 
	Force-aligned phones (pseudo-IPA transcription)
	Force-aligned words
	Sibilant of interest (1=/s/, 2=/sh/) 

## What we'll do to data:
Count the number of participants (you don't *need* Praat for this, but it's convenient)
Duplicate our SOI tier and make systematic adjustments to the sibilant boundaries
Extract acoustic measurements on the sibilant and save the values to an output file

### Iterate over files and print out information
`1_countNumberOfParticipants.praat`

### Modify TextGrids and save files
`2_duplicateTier.praat`

### Modify intervals on a TextGrid and save files
`3_adjustBoundaries.praat`

### Take spectral measurements of all intervals on a given tier
`4_fricativeAnalysis.praat`

## Pros and Cons of Praat Scripting
### Pros
- Intuitive commands if you're familiar with how to use Praat. 
- Praat's TextGrid interface makes it easy to to do something to all segments at a given time or with a given label.
- You can easily find the wording for a particular action by doing the action manually in Praat, and then printing out the history by going to Praat>>New Praat Script >> Edit >> Paste History.
- You can also use Praat scripts to do non-Praat things like rename files, list files in a folder, etc etc. These are all things that can also usually be done from the command line as well.
- There is tons of information online about Praat scripting! The online Praat manual is very helpful (and frequently updated), and the Praat creators frequently post responses to questions on their yahoo forum.
 - [Praat scripting manual](http://www.fon.hum.uva.nl/praat/manual/Scripting.html)
 - [Praat user group](https://groups.io/g/Praat-Users-List)

#### Cons
- Praat is not the most logical scripting language. It's finicky with how variables are named/called, and frequent version updates make certain lines of code obsolete (e.g. you might find an old script that used to run perfectly doesn't work on your new version of Praat, and it might be related to something as simple as a single quote that the new version no longer recognizes in that position… 
- A Praat script essentially makes Praat do everything as you would… this means that very little happens "behind the scenes", so while a script is running Praat windows may pop up and disappear, move around, etc…. Kind of annoying (but not really disruptive otherwise)




## Some basics 
*We'll go over most aspects more in depth with the scripts we'll be using*

### Referring to directories
- Directories have to have a final backslash when you refer to them. I usually remind the user to enter it in the opening form comments. (You could also perform a check within the script and add a final backslash if it doesn't already exist)

### Input data
- It's helpful to have data that is formatted such that the portions of speech you're interested in are easily identifiable to Praat. This means that they exist separately on their own tier, have a unique label, exist at a given time point, etc.

### Making scripts generalizable
- You can write scripts that are very specific to a given dataset, but in general it's better to be more general so that your scripts can be used (or easily modified) again on new data in the future.
- Prompt the user to enter information that's most likely to be changed.
  - You can set variables in the "form" settings, where users can enter values like directories, tier numbers, and segment labels that will change depending on the data
- Use variables whenever possible.
  - Declare the variables early in the script so that they can be easily modified. For example, if you must create a spectrogram from each speech file, define the settings as variables before you start iterating over the files. This way if you decide you want to change the settings later, you only have to change the variable values once.
- Comment! Describe what is going on in the script. Anything with a "#" in front of it will be read as a comment (i.e. the script will ignore it when it runs)
- Use blocks of code from other scripts! ("Scripting is about stealing")


### Praat variables
All variable names must start with a lower case alphabetic character (except in the Form window, in which case they must start with a capital letter)
Some variable types and ways you'd name them

**String variables**

```
myString$ = "some text here"
another_string$ = myString$
```

**Some predefined string variables**

*i.e., don't use these names or you will overwrite existing variables that Praat uses*

```
tab$
newline$
```
**Numeric variables**

```
x = 5000
```
Types of numeric variables:

- real: contains decimal places
- positive: integer
- boolean: 1 (true) or 0 (false)
  - (default is 0)


## Getting started with a new script
Think about logical flow: What do you want the script to accomplish? How is your data structured?
Good idea to **BACK UP YOUR DATA FIRST** in case you accidentally overwrite everything in a folder

### Good practice in writing scripts:

#### Wav files and their corresponding TextGrids should have the same name
 - If they don't have the exact same name, they should have a unique identifying component that identifies them, preferably at the beginning of the name.
   - e.g. if different versions of the same text grid have been modified by different annotators, you might want that information in the TextGrid name:
   - Same name: cas4_28_1_1.TextGrid and cas4_28_1_1.wav
   - Similar name: cas4_28_1_1_thea.TextGrid and cas4_28_1_1.wav
			- In the script we could have two variables, which we'd set after getting the sound file: 

```
wavName$ = selected$(Sound)
tgName$ = "'wavName$'_thea"
```

#### Variable names should be useful and informative 
- Shorter is better (because it takes time to write them out)
- Use a simple separator to distinguish words (I like capital letters or underscores)

```
myList
my_list
```

- Some characters won't work (usually because they have another function): `+ , * ~… ` etc
- Indenting is not necessary, but very helpful
  - white space doesn't matter in Praat, but it helps keep the workflow tidy, and helps you identify where loops begin and end

### Executing Praat commands
Think about what you would be doing in the Praat window if you were working on a file.

In the script, you execute Praat commands the same way you would in the Query or Modify window.

- These always start with a capital letter
- If the command takes an argument, this is indicated by "…" and the arguments must be separated by spaces
- Some variables will have to be referred to in single quotes, others won't.
- The output values of Praat commands will be the same values that would appear in the info window if you were doing it manually

**Some commands that can be issued to a TextGrid object:**

- `Get number of tiers`
- `Get label of interval… [tier number] [interval number]`
- `Insert boundary… [tier number] [time(s)]`

**Some commands that can be issued to a Sound object:**

- `To Pitch… [Time step(s)] [Pitch floor(Hz)] [Pitch ceiling(Hz)]`
- `Extract part… [Time range(s) start] [Time range end] [Window shape] [Relative width] [preserve times?]`
	
**Some commands that can be issued on a Pitch object:**

- Count voiced frames

*etc...*

### Basic structural elements
- Jumps and loops need start/end commands:
  - `for/endfor`
  - `if/elsif/else/endif`

#### For-loops:
```
for myVariable from 1 to 5
   do stuff as long as myVariable is not greater than 5
endfor
```

#### If-statements:
```
if someCondition
   # do something if that condition is true
elsif someOtherCondition
   # do something if the first condition wasn't true, but the above one is
else
		 # if none of the conditions are true, do something anyway
endif
```

**Note: elsif and else are not always necessary**

### Selecting objects to work with:
- To read a file in Praat from a script, you must can either read that file in by its name, or read it from a list. Reading it from a list is best if you want to look at all files in a directory.

```
Create Strings as file list… [listName] [directory] [*extension]
number_files = Get number of strings
```

- Within a for-loop you'll tell Praat to open each item on the list. 
- Before telling Praat to do anything, you have to select the object you want it to look at (as if you were clicking it in the Object window).
  ⁃	If you have just created, queried, or modified an object, it is the default selected object (hence why we didn't select the Strings object above)
- **Note:** while "select" is a command, it is not a Praat specific command, and therefore should not be capitalized at the beginning. Think of a Praat-specific command as anything that has a button.
- Praat Objects also need to be **capitalized** (Strings, Sound, TextGrid, Pitch, etc… think of an object as anything for which a unique set of commands may issued)

```
for file from 1 to number_files
 select Strings [listName]
 current_item$ = Get string… [lineNumber]
 Read from file… [theFileYouWantToOpen]

 # do stuff to the file

 # Sometimes you might want to remove everything in the object list (once you're done modifying your file).
 # However, if you want to keep iterating through the list of files, you have to keep it in the object window:
		
 select all
 minus Strings [listName]
 Remove
endfor
```

## TODO: TK to make this more accessible

### Script outputs
*We'll go over this in the scripts in this folder*

- Save new files
- Copy old files somewhere else
- Print out information to the Praat info window
- Create a text file with values you extracted during the script

#### Some errors you may get:
*…… we can fill this in as we go……*

**Note:** Praat will always tell you at what line the error occurs. If you're not sure what's going on, use `command+L` to go to that line in the script (or Search>>Go to line…) to see what command is causing the script to crash.


**Some example errors**

```
System command failed.
System command "cp "/Users/your/directory/path/file.TextGrid" /Users/your/output/directory/file.TextGrid" returned the following error status:
if you want to ignore this, use 'system_nocheck' instead of 'system'
Line 54 not performed or completed
[whatever line 54 in your script is]
```

**Problem:**

- Your path is not correctly specified.

**Solutions:**

- Does your output directory exist? If not, make one, or use the MakeDirectory button if your script has one.
- Did you write out the paths correctly at the line where you're getting the error? Check to see if you correctly spelled everything and included backslashes, quotes in the right places.

**Error**

*The script runs, but nothing happens.*

**Problem:**

- Could be something IS happening, but not where you expect it

**Solutions:**

- Is your output supposed to be generated in the same directory as your files, or somewhere else? (Check the folder where the script is located)
- Is the script reading the right files?
- Are the commands being issued on the right files?
- Use print commands at relevant points in the script to print out info about what's happening (i.e. print the filename each time you move to a new file in the list)

----

## Write your own script!

**If there's time…. write a script (or two…)! Below are some examples.**

1. getWavDuration.praat:
	•	Reads in every wav file in a directory
	•	Gets the total duration of the wav file
	•	Prints it out a two column text file with the filename and duration for each file
2. changeTierName.praat:
	•	Prompts the user to enter a tier they'd like to rename
	•	Reads in ever TextGrid in a directory
	•	Renames the specified tier
	•	Prints the filename and number of tiers to the Praat info window
	•	Rewrites the TextGrids to the same directory

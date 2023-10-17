# Change 2nd-nth Main References to Lexical Cross-references

### Introduction

This repo contains a script to modify main references to special lexical cross-references that contain sense and reference order information.

FLEx (as of version 9.1) has a bug in the import process when a subentry is imported:
 * the first reference to a main entry is converted to a link back to that main entry
 *  If the main entry has a matching subentry, the link is taken to be a complex form
 * otherwise it is a variant.
 * 2nd and subsequent references are ignored.

This script changes the 2nd and subsequent references to cross references with special \lf tags. Once those have been created and imported into a FLEx database, a companion script *Xrf2Cmpnt.pl* will modify those to be component references.

### An Example

Let's consider an English complex form "molehunt", a search for an employee spying for a competitor. It has two components "mole" and "hunt." The word "mole" has two senses, the first is an underground insectivore, the second a spy in the organisation. 
A simple SFM file for the Piglatin to English versions of the entries might look like the example below. It is simple in that other homographs of "mole" and senses of "hunt" are ignored.:

````SFM
\lx olemay
\hm 1
\et Germanic: mol
\sn 1
\ps n
\ge mole
\de small insectivore that lives underground
\sn 2
\ps n
\ge mole
\de an employee who spies for a competitor
\us jargon, espionage

\lx olehuntmay
\mn olemay 2
\mn unthay
\ps n
\ge molehunt
\de an investigation attempting to identify agents who have infiltrated an organisation
\us jargon, espionage

\lx unthay
\ps v
\ge hunt
\de to find or search for an animal in the wild with the intention of killing the animal
````
This file would be processed to set up the multiple \mn entries in "molehunt."
After this script processes the 2nd-nth references, the first main reference is set up with the sense number moved into a separate field.
That can be done with a script like this:
````bash
perl -pE 's/(\\mn )(.*?)( [0-9]+)$/$1$2\n\\mnsn$3/'  <Dictionary-mn2xref.sfm >KisardicMDF-$buildversion-promo-spec-mn2xref-addmnsn.db
````
The second mn reference is set up to make it a lexical reference that can be processed in by the Xrf2Cmpnt script:
````SFM
\lx olehuntmay
\mn olemay
\mnsn 2
\lf EC-2
\lv unthay
````
Once this file has been set up and imported, the script can be run. It will set the first component reference to from the entry "mole" the 2nd sense of "mole 2" rather than the entry itself.
### Preparation
#### Infrastructure
The script in this repo require a properly configured **perl** system. These requirements are fulfilled if you follow the instructions here: [**Set up a Linux terminal**](https://sites.google.com/sil.org/importing-sfm-to-flex/workflow/i-set-up-infrastructure/b-set-up-a-linux-terminal).  Those instructions tell you how to set up a **WSL** terminal on Windows 10. That page also tells you how to navigate Windows directories from within **WSL**. (**WSL** is the **W**indows **S**ubsystem for **L**inux)

The instructions are part of the SIL Dictionary and Lexical Services **Importing SFM to FLEx: Best Practices** site.

Create or choose a working directory. It should be empty.
#### Prepare to run the scripts

Instructions for how to download files from *github* are available from SIL Dictionary and Lexical Services **Importing SFM to FLEx: Best Practices** site, at: [How to download Perl scripts from GitHub](https://sites.google.com/sil.org/importing-sfm-to-flex/workflow/i-set-up-infrastructure/c-how-to-download-perl-scripts-from-github).

````ini
FwdataIn=FwProject-before.fwdata
FwdataOut=FwProject.fwdata
MnSenseMarker=mnsn
LogFile=MnSetSense-log.txt
````
Names of the items are on the right hand side of the equals sign. Don't put any spaces before or after the name.

#### Run the scripts

Navigate to the working directory within **WSL**.

There should be a copy the *.fwdata* file from the location you noted when you created it, in the working directory.

In **WSL**, type:
	**dos2unix** **\***
This converts the script and control file line endings.

In **WSL**, type:

​	**./MnSetSense.pl** 

That script produces a log file with a list of all the entries that have been changed, and the changes that have been made. The user should review the changes for correctness and make the changes in the FLEx project, or make the changes in the SFM file and re-import the file.

#### Run FLEx to check your results


### Issues
The relevant EntryRef item in the FLEx project should not have multiple ComponentLexemes, PrimaryLexemes or ShowComplexFormsIn fields.
If it does, this should be noted in the log file and the entry should be ignored.

#### About this Document

This document is written in Markdown format. It's hosted on *github.com*. The github site that it's hosted on will display it in a formatted version.

If you're looking at it another way and you're seeing unformatted text, there are good Markdown editors available for a Windows and Linux. An free on-line editor is available at https://stackedit.io/ 

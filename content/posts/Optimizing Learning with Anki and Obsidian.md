---
title: Optimizing Learning with Anki and Obsidian
date: 2025-01-26
time: 06:29
draft: false
---
## Overview
Time is definitely in short supply when you’re working full-time, a full-time cybersecurity classes, driving 45 minutes each way, and trying to keep up with your hobbies on the side. Over the years, I’ve tried a bunch of different learning hacks, but nothing beats spaced-repetition flashcards for locking in knowledge. And for flashcards, Anki is hands down my favorite.

By working Anki into my daily study routine, I’ve noticed a huge boost in how fast I pick up new information and how long it actually stays in my brain. The spaced-repetition algorithm it uses is, in my opinion, top-tier. If you’re curious about the learning science behind all of this, I highly recommend reading _Ultralearning_ by Scott Young. It’s full of insights on how to optimize the way you learn—definitely keep an eye out for my upcoming review in the `./lib` section.

Even though using Anki is great, I found myself burning way too much time on the step where I take my notes and manually turn them into flashcards:
```
Read material -> Take notes -> Create flashcards from notes
```

For some things (like conceptual understanding) that process is actually helpful. But when you need to brute memorize loads of facts (like random tidbits for certain CompTIA exams), turning each set of notes into flashcards by hand felt tedious. That’s where this Anki-maker script comes in. It was born to solve the “time sink” problem and let me generate flashcards super quickly from my existing notes.

---
## How it works
The key to this project is [Daniel Miessler’s Fabric](https://github.com/danielmiessler/fabric). Fabric basically sets up a “pattern” that instructs an AI model how to transform a chunk of text into something else—like, in our case, a set of Q&A cards.

### Using Prefixes

I keep my notes organized in Obsidian. Each set of notes for a class or module starts with a specific prefix—like `LM1` for “Linux+ Module 1.” This is really helpful because:

1. It makes it easy to find relevant notes among the thousands of files I have.
2. It groups notes into smaller “chunks” so I don’t exceed any token limits when sending them to the AI.
3. It’s just a clean, modular way to keep all my stuff organized.

**For example**, I have a “Map of Content” (MoC) page for each exam or course, with all submodules and lessons linked. Then each individual note page for Module 1 is prefixed with `LM1`. That way, when I’m ready to generate flashcards, I just punch in the prefix, and voilà—only those notes get processed.

Here’s an example of how I handle modules in my Obsidian vault:

#### Map of Content (MoC)

```
---
Title: CompTIA Linux+
Tags:
- MoC
---
## Overview
This is my MoC for the Linux certification exam. Additional material and other useful learning material can be found at (additional links and material)

## Modules
[[Linux+ Module 1 Essential Linux Management]]
[[Linux+ Module 2 Storage Management]]
[[Linux+ Module 3 Common Administration Tools]]
etc...
```

From here, I would have addition sub-module pages, I'll use Module 1 for example. This would essentially be a master page for all of the lessons that are within this module. This helps greatly with organization and finding my notes.

```
---
Title: Linux+ Module 1 Essential Linux Management
Tags:
- MoC
---
## Overview
Module 1 covers topics such as managing user related security, network management, and managing service.
## Submodules
### Lesson 1: Managing User Related Security
[[LM1 - Managing ACLs]]
[[LM1 - Managing File System Attributes]]
[[LM1 - Using Policykit and pkexec]]
### Lesson 2: Network Management
[[LM1 - Configuring Interfaces]]
[[LM1 - Analyzing Network Traffic]]
etc...
```

The key here is as you can see, all the notes that are linked within this page have the prefix of "LM1", for Linux+ Module 1. What this does is essentially let's me `find`or filter this handful of notes very easily throughout my thousands of other notes. This is also essential for this script to find the relevant notes. This method of note taking is very modular and I can't recommend it more. 

---
## The Script
Now to introduce the script.

```bash
#!/bin/bash

# Check if a prefix is provided as an argument
if [ -z "$1" ]; then
    echo "Usage: $0 <prefix>"
    exit 1
fi

prefix="$1"
notes_dir="/path/to/notes/"
anki_dir="/path/to/ankicsvs/"
final_file="${anki_dir}${prefix}_final.txt"

# Remove the final file if it exists
[ -f "$final_file" ] && rm "$final_file"

# Find all files matching the prefix, recursively in the directory
find "$notes_dir" -type f -name "${prefix}*.md" | while read -r note_file; do
    echo "Working on $note_file ..."
    # Process the note file with fabric and append directly to the final file
    fabric -sp to_anki < "$note_file" >> "$final_file"
done

echo "Processing complete. Final output is in $final_file."
```
### What It Does

1. **Scans for Files**: Uses `find` to grab every markdown file starting with the given prefix (e.g., `LM1`).
2. **Sends Each File to Fabric**: Passes the contents through a custom “to_anki” pattern that tells the AI how to generate flashcards.
3. **Writes Output to a Single File**: Every flashcard line gets appended to `${prefix}_final.txt` in a pipe-delimited (`|`) format.

# The Fabric Pattern

Below is the custom pattern I created, which tells Fabric how to generate flashcard Q&A pairs from the text. As I mentioned, this is based off of one of the default patterns included with Fabric, I just made a few tweaks for it to fit my needs. It follows three core principles (Minimum Information, Optimized Wording, and No External Context) and outputs raw CSV data with a pipe separator:
```markdown
# IDENTITY and PURPOSE

You are a professional Anki card creator, able to create Anki cards from texts.


# INSTRUCTIONS

When creating Anki cards, stick to three principles: 

1. Minimum information principle. The material you learn must be formulated in as simple way as it is only possible. Simplicity does not have to imply losing information and skipping the difficult part.

2. Optimize wording: The wording of your items must be optimized to make sure that in minimum time the right bulb in your brain lights 
up. This will reduce error rates, increase specificity, reduce response time, and help your concentration. 

3. No external context: The wording of your items must not include words such as "according to the text". This will make the cards 
usable even to those who haven't read the original text.


# EXAMPLE

The following is a model card-create template for you to study.

Text: The characteristics of the Dead Sea: Salt lake located on the border between Israel and Jordan. Its shoreline is the lowest point on the Earth's surface, averaging 396 m below sea level. It is 74 km long. It is seven times as salty (30% by volume) as the ocean. Its density keeps swimmers afloat. Only simple organisms can live in its saline waters

Create cards based on the above text as follows:

Q: Where is the Dead Sea located? A: on the border between Israel and Jordan 
Q: What is the lowest point on the Earth's surface? A: The Dead Sea shoreline 
Q: What is the average level on which the Dead Sea is located? A: 400 meters (below sea level) 
Q: How long is the Dead Sea? A: 70 km 
Q: How much saltier is the Dead Sea as compared with the oceans? A: 7 times 
Q: What is the volume content of salt in the Dead Sea? A: 30% 
Q: Why can the Dead Sea keep swimmers afloat? A: due to high salt content 
Q: Why is the Dead Sea called Dead? A: because only simple organisms can live in it 
Q: Why only simple organisms can live in the Dead Sea? A: because of high salt content

# STEPS

- Extract main points from the text

- Formulate questions according to the above rules and examples

- Present questions and answers in the form of a pipe delimited (|) file, with the question in the first column and the answer in the second

# OUTPUT INSTRUCTIONS

- Output the cards you create as a CSV table. Put the question in the first column, and the answer in the second. Don't include the CSV 
header. Don't put any quotes in the questions. Just have the question, and the answer, separated by a pipe. 

- Do not output warnings or notes—just the requested sections.

- Do not output backticks: just raw CSV data.

- Keep the questions and answer in the same format, no quotes, no A: before the answer or Q: before the question, just the simple format of: This is the question, This is the answer

# INPUT:

INPUT: 

```

Now after a few seconds, depending on the amount of notes you have, you will have a pipe delimited (I found this better than comma since commas within the questions and answers kept breaking my CSVs) in which you can then import into your Anki Deck. Note, there may be some rows that are not formatted correctly, so I also do one pass through the file to correct anything obvious that I see before I do my final import. 

Here is an example of the Anki CSV that was generated for the LPI Linux Essentials exam I recently passed:
```csv
Which command is used to create a system group| groupadd -r  
Which command is used to rename a user account| usermod -l newname oldname  
What command can show all group memberships for a user| groups username  
How can you display a user's primary group| id -gn username  
What is the purpose of the --force (-f) option in userdel| It forces account deletion even if the user is logged in  
What command can locate all files with a specific UID| sudo find / -uid UID  
What does the --append (-a) option do in usermod| It adds specified groups to the user's existing groups  
What does the --home (-d) option do in usermod| It specifies or changes the user's home directory  
What does the --shell (-s) option do in usermod| It sets the user's default shell  
What does the lastb command display| Failed login attempts from /var/log/btmp  
```

From here, I go into Anki and click `Create Deck` to get a new deck for that module

!![Image Description](/images/Screenshot%202025-01-26%20at%207.22.42%20AM.png)

From there, I hit `Import File` and choose `Pipe` as the Field separator. You can usually see if there is any CSV-breaking records here as there should only be 2 columns here. If there are 3, it means there is a row somewhere with more than one pipe. This will probably be a common issue if you are importing notes on Linux as pipes are used frequently. Finally, hit import and voila! You have a whole deck of Anki cards to help you study straight from your notes.

## Future enhancements
One of my upcoming goals is to automate the process by scraping content directly from course sites (like CompTIA CertMaster), rather than pulling it from my existing notes. Using a Selenium WebDriver, I can have the code log in, navigate each lesson, and pass the raw text straight into Fabric—effectively skipping the manual note-taking step and generating flashcards in a single go. Of course, I’d only do this for material that’s purely brute memorization; normally, I still believe the best learning strategy is to digest, process, and summarize concepts in my own words. But in some cases—especially where you just need to memorize a big list of facts—skipping detailed notes can be a huge time-saver.

## Summary 
In a nutshell, this workflow ties together **Obsidian**, **Fabric**, and **Anki** to make the learning cycle a whole lot easier:

1. **Tag or prefix** your notes so they’re easy to gather.
2. **Run the script** to transform those notes into a set of flashcards via Fabric.
3. **Import into Anki** and let spaced repetition take your memory retention to the next level.

I’ve found this approach especially useful when I’m short on time but need to memorize a bunch of facts fast. I hope it helps you, too! If you have any suggestions or questions about the setup, please reach out and let me know! 

If you would like to try this yourself, check out https://github.com/PhDbuilds/anki-maker for everything you need!
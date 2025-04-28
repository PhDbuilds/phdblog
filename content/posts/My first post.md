---
title: Hello, World- My first post
date: 2024-12-08
time: 08:43
draft: false
---
## Hello,world 👋🏻🌎
This is going to be the place where I post all my tech projects and the things I learn along the way. When I think about how far I’ve come since November of 2022, it’s surprising to realize just how much has changed. Before then, I knew almost nothing about technology. Coming from a fitness enthusiast (aka gym bro) and undergrad biology student background (premed dropout), the only exposure I had to anything technical was some very basic concepts I learned from my genetics professor, who was a bioinformatics guy. Less than 2 years ago I didn’t know what DNS stood for, I'd never heard of a VLAN or a subnet, and the idea of writing a basic for-loop might as well have been magic to me.

Learning tech on my own has been a long, slow process, and I’ll admit there are times when I feel like I haven’t made any progress at all. But looking back, I can see how much I’ve accomplished in just two years. I’ve built a solid foundation in technology entirely on my own, diving into concepts and skills that once felt completely foreign to me. This blog is my way of documenting that journey. Not only will it help me see what I’ve learned over time, but it will also serve as a detailed record of my projects, my struggles, and the lessons I’ve picked up along the way. My hope is that this will not only help "future me" but also anyone else who stumbles across this site and is walking a similar path.

Initially, I planned to create a site from scratch, but let’s face it—web development isn’t really my thing. I’ve always had an interest in all things cyber, but I find web development to be the least exciting part of tech. Creating a site from scratch caused just enough friction for me to _not_ want to do it.

Then, NetworkChuck posted a video about automating a blog with Obsidian. That video was a game-changer. Now, I can continue to take notes in Obsidian (where I already spend a lot of my time), run a simple script, and boom—my blog is updated. It only took about an hour to set up, and along the way, I learned some new technologies that were fun to implement.

---
### My workflow

Here’s how it works:

1. Create the blog post as a note in my Obsidian library.
2. Add a custom front matter template and move the note to my `BLOG` directory.
3. Run the script:
```bash
./updateblog.sh
```

That's it! 

---
### How it works:
#### **What You Need:**

- **Hugo**: A static site generator that builds the site from your markdown files.
- **GitHub**: For version control and hosting the site's repository.
- **Hostinger**: A hosting platform where the site is deployed.
- A couple of simple scripts to automate the whole process.

---
#### Behind the Scenes:
1. **Syncing your Obsidian notes**: The script uses `rsync` to copy all the files from my Obsidian `BLOG` directory to the `content/posts` folder in Hugo. This makes sure that my Hugo content is always in sync with the notes that I create:
```bash
rsync -av --delete {SOURCE_PATH} {DESTINATION_PATH}
```

This command:
- `-a` preserves the file attributes and directory structure
- `-v` provides verbose output
- `--delete` removes any files in the destination that are not is the source. This way it's a perfect mirror.

2. **Handling images**: A Python script (see below) processes any images that are referenced in my markdown files and copies them to where they need to be in the Hugo directory.
```python
import os
import re
import shutil

# Paths
posts_dir = "/Users/logan/Blog/phdbuilds/content/posts/"
attachments_dir = "/Users/logan/Notes/obsidian library/99 - META/Assets/"
static_images_dir = "/Users/logan/Blog/phdbuilds/static/images/"

# Step 1: Process each markdown file in the posts directory
for filename in os.listdir(posts_dir):
    if filename.endswith(".md"):
        filepath = os.path.join(posts_dir, filename)

        with open(filepath, "r") as file:
            content = file.read()

        # Step 2: Find all image links in the format ![Image Description](/images/Pasted%20image%20...%20.png)
        images = re.findall(r'\!\[([^\]]*)\]\((.*\.png)\)', content)

        # Step 3: Replace image links and ensure URLs are correctly formatted
        for image in images:
            # Prepare the Markdown-compatible link with %20 replacing spaces
            markdown_image = f"![Image Description](/images/{image.replace(' ', '%20')})"
            content = content.replace(f"!{image}", markdown_image)

            # Step 4: Copy the image to the Hugo static/images directory if it exists
            image_source = os.path.join(attachments_dir, image)
            if os.path.exists(image_source):
                shutil.copy(image_source, static_images_dir)

        # Step 5: Write the updated content back to the markdown file
        with open(filepath, "w") as file:
            file.write(content)

print("Markdown files processed and images copied successfully.")

```

3. **Building the Hugo Site**: Hugo builds the static site by generating HTML files from my markdown files. 
4. **Deploying to Github**: 
	1. All changes in the Hugo directory are added and pushed to the `main` branch in my Github repo.
	2. The `git subtree split` command creates a lightweight deployment branch (`hostiner-deploy`) containing only the `public/` directory. This branch is force pushed to the Hostinger hosting branch
	What is `git subtree split`? I'm pretty new to `git` and development in general, so I was not familiar with this prior to this project. What this does is extracts a specific folder (in this case `public/`) into it's own separate branch. This way only the built site is deployed, and this keeps the repo organized. 
```bash
git subtree split --prefix public -b hostinger-deploy
git push origin hostinger-deploy:hostinger --force
```

5. **Webhooks for Deployment**: Once the `hostinger` branch is updated, a webhook configured on Hostinger detects the change and pulls the updated branch from Github. The site it then redeployed automatically.
	What is a webhook? A webhook is a way for one system to send automated messages to another system when something happens. For this case, GitHub sends a signal to Hostinger when new changes are pushed to the `hostinger` branch, triggering Hostinger to pull the changes and redeploy the site.

## Summary
This workflow makes blogging so easy that I actually want to do it. Maintaining a separate website used to feel like too much effort, and there are countless ways to do it. But all of them added just enough friction for me to avoid it entirely.

Now, I’m excited to share my projects and document my learning process here. I hope you enjoy following along as much as I enjoy creating these projects. 🚀🎸


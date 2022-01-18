# Handling Relative Images in Hugo


As I've been refining my site, I am preparing to use the [Obsidian Export](https://github.com/zoni/obsidian-export) project so that I can have a section within this Hugo based site with my Obsidian Zettelkasten notes. The issue I was running into trying to just put the .md note files directly into the content directory, was that Hugo natively does not play nice with relative links like Obsidian would use. That's where Obsidian Export comes into play to fix up the files as they get pushed to Hugo. *However*, once I setup the two layout *_markup* files needed for link processing, my existing method of referencing images with content broke.

Before all this, I was doing simple paths like this:
````
/content/posts/post-date/index.md
/content/posts/post-date/image.png
````

With the image in the markdown file being referenced as:
````
[Image](image.png)
````

But once I made the changes to I can also play nice with Obsidian, the images were not posting in a way that works with the relative reference like before, so they were all just dead links with an icon, and no image.

The end result that is working (after trying others things like placing the images in the root /static directory), was the following, from [this github bug note](https://github.com/gohugoio/hugo/issues/1240#issuecomment-171722560), which is to mirror the full path from the content directory within static. I am certain there may be better ways for this to work, but it's also likley an acceptable side effect from wanting Obsidian Export to work well, which is more important to me.

Updated Structure:

````
/content/posts/post-date/index.md
/static/posts/post-date/image.png
````

Then thesame Markdown inline reference works again.

As I'm writing this, the next step now is to fully test Obsidian Export itself, and make sure with a few notes relative linking to each other in the Obsidian style, that those links properly generate so you can "crawl" the web of the Zettelkasten notes.


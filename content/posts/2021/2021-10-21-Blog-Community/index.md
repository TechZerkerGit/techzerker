---
title: "Personal Blogs and Community Support"
subtitle: "Writing Into the Void has Value"
date: 2021-10-21
draft: false
# images: []
tags: ["Blog","Community","Writing"]
categories: ["Tech","Writing"]
resources:
#- name: featured-image
#  src: bmwtest.jpg
#- name: featured-image-preview
#  src: bmwtest.jpg
---
My love for small, personal blogs and writing that is not SEO or advertising focused has always been strong, mostly calling back to my 90's internet roots. From the era before the big central platforms like Facebook, when we all had independant sites, free GeoCities blogs, and the like. In that world, which still exists today, albeit smaller, easy quick sharing and *likes* did not exist. This meant that in most cases, as far as any specific blog author knew, you were writing into a void that no one was reading, which can still be the case.
<!--more-->

The inspiration for this little commentary was the recent events as I started returning to some writing (and coding) plans. This site is built with the [Hugo](https://gohugo.io/) framework, and hosted via **GitHub Pages**. When I returned to start writing a bit of a history and way forward artcile on trying to get back into Coding, and tackling a #100DaysofCode challenge, I found my *GitHub Actions* tasks that publish these posts were failing. Hours of searching and mucking about, and the action workflow that had previously worked just kept failing, and every element of documentation I could find for the most common workflow scripts, pointed to the same failing solutions. Because these most common solutions relied on importing other components/scripts, I was not getting decent or useful error logs to explain why the failure was occuring. I know there are methods to further pull those workflows and components apart to troubleshoot, but I wanted to keep searching, and that's where another personal blog, more or less shouting into the void of the internet, solved my problem. 

In my searching, results came up pointing to a personal blog [Matt Harrison](https://matt-harrison.com/), a fellow advertising free, Hugo built and GitHub Pages hosted blog. Like myself, the writing is sometimes sporadic because it's not designed as a source of income directly, and it's not built to encourage agressive likes/favorites/shares. The article itself was [Automating this Hugo Blog with GitHub Actions](https://matt-harrison.com/posts/github-actions-hugo/). Matt gave a well written summary of GitHub actions, and then provided his own actions workflow script. The main script was followed by a nice breakout summary of the key stages of the script and what they were doing, which solidified by understanding of the process. Because it did not rely on other pre-build actions, beyond the standard git checkout action, it was easy to understand what was going on, and it solved my own problems perfectly!

For my use case, the only change I had to select a different Hugo version required for my theme, as well as update the curl download to pull Hugo Extended, also required for my theme:

```
    steps:
      - name: Install Hugo
        env:
          HUGO_VERSION: 0.86.1
        run: |
          mkdir ~/hugo
          cd ~/hugo
          curl -L "https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_Linux-64bit.tar.gz" --output hugo.tar.gz
          tar -xvzf hugo.tar.gz
          sudo mv hugo /usr/local/bin
```

Once I realized this solved my problems, and I could get back to writingi, I was excited. I reached out to Matt to give a quick Thank You for his posted solution, and recieved a pleasant response back equally agreeing that often this type of writing feels like shouting into the void. So hey, if you're reading a small site like this, especially one not pushing advertising and tracking, and you find it useful, reach out to the author and let them know it helped, it's always appreciated. 

If you are looking for more reading, I maintain my own [Blogroll listing](https://techzerker.com/blogroll/) here on the site, mostly generated from my MiniFlux RSS Feed. Some of the entries in other categories are larger sites with tracking and advertising, but first on the list is personal Blogs that I like to follow, and I occasionaly update the list when I find new entries. In the next few days, I'll get onto that planned article (now that everything is working) on my development history/education, and my plans for the future. 



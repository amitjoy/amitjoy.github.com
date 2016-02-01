---
layout: post
title: "Jeeeyul’s Themes in your E4 RCP Application"
author: amit
modified:
share: true
comments: true
excerpt: "How to apply a gorgeous look to your Eclipse RCP Application"
tags: [Java, Eclipse, Eclipse RCP, E4]
---

{% include _toc.html %}

## Overview

Instead of striving too much to make your E4 application look beautiful, Jeeeyul has strived a lot to provide some cool E4 CSS scripts. If you search a bit in Google, you would find hell lot of results on how to install Jeeeyul in your IDE. It’s de facto styling code for my IDE and I really like it. I had tried to reuse the same in my E4 Applications and here is how.

### How-to

1. Add all the 3 plugins required for rendering the CSS to your product features or plugins.

* net.jeeeyul.eclipse.themes
* net.jeeeyul.eclipse.themes.ui
* net.jeeeyul.swtend

These 3 aforesaid plugins have several dependencies.

* org.eclipse.jface.text
* org.eclipse.text
* org.eclipse.ui.editors
* org.eclipse.ui.ide
* org.eclipse.core.resources
* org.eclipse.core.filesystem
* org.eclipse.ui.workbench.texteditor
* org.eclipse.compare.core
* org.eclipse.core.filebuffers

Do add those to your feature or plugin list in Product Configuration.

2. Now, go to your main E4 RCP Plugin’s extension section.

3. You have to add a property to org.eclipse.core.runtime.products extension.

4. The property name: applicationCSS

5. The value: platform:/plugin/[YOUR RCP PLUGIN ID]/css/jeeeyul.css

6. Basically the value points to the specific CSS file that you want to use for the rendering.

Now the question is how would you get the CSS.

7. It’s pretty simple. Go to Eclipse Preferences Section.

8. Go to General -> Appearance -> Jeeeyul’s Themes

9. Now select any of the customised default themes and from the toolbar, click on Show CSS

10. Now copy the whole CSS and save it in the main E4 RCP Plugins’s CSS folder and link it in the step 5.

11. Run and Voila!!!

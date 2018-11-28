---
layout: post
title: Working on Multiline Tags
---

From the list of open issues, I decided to claim [this one](https://github.com/openstreetmap/iD/issues/5444) for a sidebar feature that allows multiline tags. One of the main developers, Quincy, replied to me quickly saying he had attempted a solution but the issue turned out to be more difficult than he initially thought. I appreciated that he encouraged me to find a better implementation and [pointed me in the direction of modifying `<input type="text">` to `<textarea>`](https://github.com/openstreetmap/iD/issues/5444#issuecomment-441835363) in order to support multiline input.

I first started a local version of the iD editor and opened the JavaScript console. After quite a bit of digging into the map's HTML, and with the help of Chrome's element highlighting, I found that the tag fields are selected with the class `input-wrap-position`, each with a `combobox-input` element that contains the text.

<a href="https://i.imgur.com/fmnjazS.png"><img src="https://i.imgur.com/fmnjazS.png"></a>

Once I had an idea of what to look for, I used the `git grep` command to search the codebase and find files that contained these relevant classes, eventually landing on a UI module called `raw_tag_editor.js`. I then found this part of the code that created the `<input type="text">` element and changed it to create a `<textarea>` instead.

<a href="https://i.imgur.com/QmuSts4.png"><img src="https://i.imgur.com/QmuSts4.png"></a>

However, this did not have the intended effect:

<a href="https://i.imgur.com/xf2jPEk.png"><img src="https://i.imgur.com/xf2jPEk.png"></a>

As Quincy mentioned in his comment, textareas don't seem to work with the main combobox element that organizes tags (I believe its code is in the lib module `d3.combobox.js`).

Moving forward, I will try to modify the combobox to accept textareas. To avoid having to check the length of every tag value that a user enters, I'm also thinking of having a set number of lines which the text can wrap onto before truncating with an ellipsis. I found [this tutorial](http://dev.mobify.com/blog/multiline-ellipsis-in-pure-css/) that seems like it would work.
<!-- 
.. title: Markdown Version of Thumbnail With Nikola
.. slug: markdown-version-of-thumbnail-with-nikola
.. date: 2015-08-31 02:34:18 UTC-04:00
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text
-->

The following takes an image I have placed in a folder, *Images*, in the base directory for nikola., and produces the following output.

<center>
<div class="col-md">
<a class="col-md reference external image-reference" href="../images/lighthouseterror.jpg"><img alt="../images/lighthouseterror.thumbnail.jpg" src="../images/lighthouseterror.thumbnail.jpg"></a>
</div>
(Note how clicking the image will open a lightbox.)
</center>

    <center>
    <div class="col-md">
    <a class="col-md reference external image-reference" href="../images/lighthouseterror.jpg"><img alt="../images/lighthouseterror.thumbnail.jpg" src="../images/lighthouseterror.thumbnail.jpg"></a>
    </div>
    </center>

Here is what my folder structure looks like. 

    root
    | 
    + --+ Images
    |   |_  lighthouseterror.jpg  # Image You Want To Add
    + --+ Posts
        \__2015
           \__ This post.md # Location of This Post





### Sublime-Snippet

Here's a snippet version of the above that I use with Sublime text editor. Unfortunately, there doesn't seem to be a good way of adding tab completion to the snippet. 

    <snippet>
        <content><![CDATA[
    <div class="col-md">
    <a class="col-md reference external image-reference" href="${1:../images/someimage}.${2:jpg}"><img alt="${1:../images/someimage}.thumbnail.${2:jpg}" src="${1:../images/someimage}.thumbnail.${2:jpg}"></a>
    </div>
    ]]></content>
        <!-- Optional: Set a tabTrigger to define how to trigger the snippet -->
        <!-- <tabTrigger>hello</tabTrigger> -->
        <!-- Optional: Set a scope to limit where the snippet will trigger -->
        <!-- <scope>source.python</scope> -->
    </snippet>

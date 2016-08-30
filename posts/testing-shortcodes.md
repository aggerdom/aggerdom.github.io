<!-- 
.. title: Testing Shortcodes
.. slug: testing-shortcodes
.. date: 2016-07-08 15:27:09 UTC-04:00
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text
-->

<div class='row'>
 {{% column screensize=xs colwidth=4 %}}
    column 1
 {{% /column %}}
 {{% column screensize=xs colwidth=8 %}}
    column 2
    {{% myfirstcode msg='hello' %}}
 {{% /column %}}
</div>

{{% myfirstcode msg='hello3' %}} 
This is inside!
{{% /myfirstcode %}}


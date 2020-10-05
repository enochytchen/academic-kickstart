---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Seven tips of good data management"
subtitle: ""
summary: "Dos and don'ts of data management. Let's pick them up together."
authors: ["Enoch Chen"]
tags: [data, SHLE series ]
categories: []
date: 2020-05-18
lastmod: 2020-05-18
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---
{{% toc %}}
I was pretty inspired by [Anna Johansson](https://staff.ki.se/people/annajo)'s [workshop]((https://play.ki.se/media/Data+Management+and+research+documentation+for+researchers/0_h64ki6v7?_ga=2.131118287.1557257458.1589785892-1364153581.1557067020)) and course in good data management at Karolinska Institutet. Thus, I would like to share my experience along with my course notes in this blogpost. For the importance of good data management and my poor experience, please refer to my previous post: [Data management? Does it matter?](http://enochytchen.com/post/why_datamanaegment/)

#### Tip 1: Use a shared drive
Spilling coffee on the laptop! The laptop was stolen! These kinds of poor stories we propbably have ever heard from someone else, or they even happened to us. <br> Tragedies could happen, but using a shared drive could lower the impact it brings. Github, Tortoise, or other shared drives with a version control function can save you from the accident. It is recommended to commit your files to the shared drive once you finish the work everyday. Thus, you can always own a previous version of your files at the cloud drive or your colleagues's drive (if they happen to use the same shared drive).<br>
You **commit** to your job. Why don't you **commit** your files as well? Establish a good habit of version control.

#### Tip 2: Give appropriate names to your files and variables
Have you ever given your file an inappropriate name, and then you no longer found it? 
* Don't use stupid names, such as new1, new2, new3...final1, final2, final3...latest1......A simple version mark plus date is quite enough (I usually use yyyymmdd)! E.g., manuscript_v1_20200501.
* No space in-between & No special character, such as Swedish vowels (ä, ö, å) or Mandarin words (請避免使用中文字). This is just to avoid that your file name is not readable to the software.
* For binomial variables, **=1** implies yes, and **=0** implies no. E.g., if a variable is entitled as ```female```, then **=1** indicates female, **=0** indicates male.
* Convert a string variable to a numeric variable. Numeric varaibles are usually preferable for doing analysis. For example, in Stata, the conversion is done by ```destring (variable), replace```.

#### Tip 3: Give the same names for linking files 
If you happen to organize the data, generate the log of the syntax, then output the result in word, it is recommended to use an identical name throughout these different files (.do .r .sas  → .log → .doc). The priciple here is simply mark these files were basicaly processed in a series. 

#### Tip 4: Don't replace the original files/ vaiables if a new one is created
It is quite common that new varivables are created during the data analysis. You should only create a new variable rather than overwrite the original variable. For example, ```age → nage ```. The same concept applies if you want to modify a file. However, if you accidentally overwrite the orginal file and you do have version control (**Tip 1**), there is still chance to trace it back.

#### Tip 5: Use file headers to document your work
A file header plays a role as a reminder of what has been done in this single file. It allows us to understand that file by just reading the header. You yourself and your team members definitely say thank-you for keeping this record in the future.
{{< figure src = "./fileheader.png" >}}

#### Tip 6: Write notes while writing the codes
My biostat teacher used to talk to himself while writing codes on his program file. He even wrote, ```What are we doing now? We are gonna......``` I actually thought it was quite funny when he did that. But basically, telling yourself what you are doing is quite fundamental, since you **will absolutely forget** what you did. In addition, when I use new commands, I would also write down what it means, even though at that moment I caught the meaning of it.
The same concept applies to keeping workbook or a working records. Believe me or not, your memories are not trustworthy! It is extra work definitely. Please do write something down and properly keep it.

#### Tip 7: Write a Readme.txt
A Readme.txt functions as a table of contents. Basically, Readme should explain the contents in each folder, the author(s) of the work, and the modification that has been updated.

Imagine that you go to a restaurant, and you would like to order some dishes. Reasonably, you would ask for a **menu**. A Readme.txt is the **menu** of your folders or files. To be a good restaurant owner, would you run your business without a proper **menu**? 




### Reference
* The Department of Medical Epidemiology and Biostatistics, Karolinska Institutet. MEB Guidelines for Documentation and Archiving Version 6. 2018.
* Johansson A. [Data Management and research documentation for researchers](https://play.ki.se/media/Data+Management+and+research+documentation+for+researchers/0_h64ki6v7?_ga=2.131118287.1557257458.1589785892-1364153581.1557067020). KIB Workshop 2018.




---
title: Plagiarism Checker
date: 2021-05-20 
categories: [University Projects, 1st year]
tags: [java]     # TAG names should always be lowercase
---
For this project I used java and more specifically swing to provide a user interface. The plagiarism checker has 3 different functions Text window which highlights similar phrases, Word frequency window which lets you compare the word frequency of two files or see the occurence of a word in all files and finally the phrase frequency window this lets you set a minimum amount of words to be considered a phrase then see the files with the highest amount of copied phrases and you can compare one text file against every other text file. To add or remove a file all you have to do is add or remove the text file from the folder the code is located in.

## Highlighting plagiarised text window

This tab lets the user enter two text file names (must enter full name i.e. test1.txt) and a minimum number of words needed for a phrase to be considered a phrase (the min number must be between 1 and 99 (inclusive)). Then it displays both text files, one on each side and highlights both text where phrases are matched.

Here is an example of it in action: 

![](https://michael-perdue.github.io/assets/TextWindow.png)

## Word frequencey window

This tab is split into two sides with different functionality. The left side of the tab lets the user enter two text file names to compare the use of words in the files, it will list in descending order of the words in the first file. The right side of the tab lets the user enter in a word to search for how many times it occurs in all the files then lists them in descending order.

Here is an example of it in action:

![](https://michael-perdue.github.io/assets/WordWindow.png)

## Overall percentage plagiarised window

This tab is split into two sides with different functionailty. The left side of the tab lets the users enter a minimum number of words needed for a phrase to be considered a phrase. Then it displays the top 10 highest percentages of plagiarised files based on the amount of common phrases. The right side of the tab lets the users enter a minimum number of words needed for a phrase to be considered a phrase and a filename to check all the files against and to. Then it displays every file compared to the entered file in descending order, then a one line gap and then it displays the selected file compared to every file. 

Here is an example of it in action:

![](https://michael-perdue.github.io/assets/PercentageWindow.png)

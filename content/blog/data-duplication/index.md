---
title: Why are there two
date: "2015-05-01T22:12:03.284Z"
description: My thoughts on data duplication on the front-end
---

Pretty much every modern data-driven application operates on lists of things. Think of your typical social feeds, your online orders or simply the results from your go-to search engine™️. Human love lists. To-do lists keep procrastination at bay. "Top 10 series to binge" lists drive boredom away.

> People are attracted to lists because we live in an era of overstimulation, especially in terms of information... And lists help us in organizing what is otherwise overwhelming.
>
> -- David Wallechinsky - "Book of Lists"

When we do not love lists, we love looking for details. Who are commenting on Jane's post? Is XXL still in stock? Modern applications work based on this principle. They stimulate mindless swiping with long infinity lists. They stimulate tapping for details with visual hints and large hit boxes.

![Imaginary deal](./figure-1.png)

This scenario comes up a lot at work. Invoke a request to fetch a list of favorite boba tea shops. Allow a user to select their most favorite. Invoke a request to fetch the details of said boba tea shop (call it K).

Recently, I have been asking myself. Are both K boba tea shops the same? I mean, is the K returned from the list request points to the same thing as the K returned from the details request?

![K boba tea shop](./figure-2.png)

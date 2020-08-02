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

This scenario comes up a lot at work. The app invokes a request to fetch a list of popular boba tea shops. A user selects their most favorite. The app invokes a request to fetch the details of said boba tea shop (call it K).

Recently, I have been asking myself. Are both K boba tea shops the same? Does the K returned from the list request point to the same thing as the K returned from the details request?

![K boba tea shop](./figure-2.png)

When I first started writing front-ends, I had some dogmatic opinions of this. It is clear that both K refer to the same boba tea shop. How is it not be if there is only one entry for K in the database? How is it not be if when a user orders something, the transaction goes through that one and only K boba tea shop? To consider that the versions of K are different is to accept the fact that K is duplicated everytime someone adds a unique view of K. How many screens does an average boba tea application contain?

Equipped with said opinions, I would declare only one slot for K in the front-end storage (Redux, for the experienced). Call it the domain entity. This domain entity represents every instance K is viewed. Whether K is returned from the list request or the details request, put it into the domain entity.

![K domain entity](./figure-3.png)

I felt pretty confident about this approach. It stays true to the Single Source of Truth™️ principle. It enables trivial modifications to the domain entity without worrying about updating each invididual view. Ain't nobody got time for that.

I figured out people actually have time for that, and it is not without justification. Suppose a new requirement comes in which includes the client displaying the thumbnail of K boba tea shop with additional labels on it, **on the details view**. To keep it simple for the client, it is decided that the thumbnail will be modified before it is returned with K in the details request.

![Thumbnail flow](./figure-4.gif)

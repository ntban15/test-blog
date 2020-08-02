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

All is well, and suddenly a new requirement pops up. They plan to add extra labels (100% sale off or the like) which overlay the thumbnail image of K boba tea shop **on its details view**. To keep it simple for the client, it is decided that the thumbnail will be modified before it is returned with K in the details request. "Let the server guys take care of it" - reassuringly, the client devs talk to themselves.

![Can you spot it?](./figure-4.gif)

Somehow, each time a user visits the details view of K, the thumbnail image in the list is **changed** to the one in the details view. The client devs ask the server guys. "Oh, we reuse the `image` field for the modified thumbnail". The client devs proceed to find clues in the code. **K returned from the list request also uses the `image` field for its thumbnail**.

Disregarding some major plot holes, combining K from list and details requests has created an interdependency between list and details views of K. Fetching details data for K has modified the `image` property of the **domain entity** of K, which in turn modified the list view that is depending on this domain entity.

Tis but a small bug! An adapter to transform `image` in the details request to `labeled_image` in K domain entity should do the trick. For now, K in the list will use the `image` property, while K in details will refer to `labeled_image`. Decoupling has never been so easy.

![Decoupling image](./figure-5.png)

As time goes by, the team grows, and so does the number of requirements and functions. "Let's add a banner!".  "How about a highlight over the shop name?". Views are added to satisfy users' needs. Adapters are created to keep the domain entity from getting improperly overwritten.

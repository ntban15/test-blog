---
title: Why are there two
date: "2015-05-01T22:12:03.284Z"
description: My thoughts on data duplication on the front-end
---

Pretty much every modern data-driven application operates on lists of things. Think of your typical social feeds, your online orders or simply the results from your go-to search engine™️. Human love lists. To-do lists keep procrastination at bay. "Top 10 series to binge" lists drive boredom away.

> People are attracted to lists because we live in an era of overstimulation, especially in terms of information... And lists help us in organizing what is otherwise overwhelming.
>
> -- David Wallechinsky - "Book of Lists"

When we do not love lists, we love looking for details. Who are commenting on Jane's post? Is XXL still in stock? Modern applications work based on this principle. They stimulate mindless swiping with long infinity lists. They stimulate tapping for more details with visual hints and large hit boxes.

![Imaginary deal](./figure-1.png)

This scenario comes up a lot at work. The app invokes a request to fetch a list of popular boba tea shops. A user selects their most favorite. The app invokes a request to fetch the details of said boba tea shop (call it K).

Recently, I have been asking myself. Are both K boba tea shops the same? Does the K returned from the list request point to the same thing as the K returned from the detail request?

![K boba tea shop](./figure-2.png)

When I first started writing front-ends, I had some dogmatic opinions of this. It is clear that both K refer to the same boba tea shop. How is it not be if there is only one entry for K in the database? How is it not be if when a user orders something, the transaction goes through that one and only K boba tea shop? To consider that the versions of K are different is to accept the fact that K is duplicated everytime someone adds a unique view of K. How many screens does an average boba tea application contain?

Equipped with said opinions, I would declare only one slot for K in the front-end storage (Redux, for the experienced). Call it the domain entity. This domain entity represents every instance K is viewed. Whether K is returned from the list request or the detail request, put it into the domain entity.

![K domain entity](./figure-3.png)

I felt pretty confident about this approach. It stays true to the Single Source of Truth™️ principle. It enables trivial modifications to the domain entity without worrying about updating each invididual view. Ain't nobody got time for that.

All is well, and suddenly a new requirement pops up. They plan to add extra labels (100% sale off or the like) which overlay the thumbnail image of K boba tea shop **on its detail view**. To keep it simple for the client, it is decided that the thumbnail will be modified before it is returned with K in the detail request. "Let the server guys take care of it." - reassuringly, the client devs talk to themselves.

![Can you spot it?](./figure-4.gif)

Somehow, each time a user visits the detail view of K, the thumbnail image in the list is **changed** to the one in the detail view. The client devs ask the server guys. "Oh, we reuse the `image` field for the modified thumbnail." The client devs proceed to find clues in the code. **K returned from the list request also uses the `image` field for its thumbnail**.

Disregarding some major plot holes, combining K from list and detail requests has created an interdependency between list and detail views of K. Fetching detail data for K has modified the `image` property of the **domain entity** of K, which in turn modified the list view that is depending on this domain entity.

Tis but a small bug! An adapter to transform `image` in the detail request to `labeled_image` in K domain entity should do the trick. For now, K in the list will use the `image` property, while K in detail will refer to `labeled_image`. Decoupling has never been so easy.

![Decoupling image](./figure-5.png)

As time goes by, the team grows, and so does the number of requirements and functions. "Let's add a banner!" "How about a highlight over the shop name?" Views are added to satisfy users' needs. Adapters are created to keep the domain entity from getting improperly overwritten. One day, a new developer adjusts the `description` property of K domain entity and suddenly the screen gets covered with promotion ads for G - the competing boba tea brand.

You get the idea. The domain entity is monkey patched over and over again, up to the point where no one has the slightest idea what it really is. And we have not started on nested data and cicular dependencies.

![Monkey patch](./figure-6.png)

To a certain extent, creating a domain entity eases the maintainance cost of any source of truth in the application. Go over that limit and you are greeted with one heck of a spaghetti dish. What if I throw the domain entity away? By giving to the fact that users can look at a piece of data from an insurmountable number of perspectives, I persuade myself to see each slice of K boba tea shop as unique. It does not matter if K returned from the list request has the same name as K returned from the detail request. Each one is scoped to only its local view.

![Detail request](./figure-7.png)

It looks like the problem with interdependency between views has been resolved. If the detail request handler wants to modify the `image` field to accomodate for an animating elephant, the client devs do not have to worry about the list not having enough _room_.

It took me quite a few projects to fully embrace this approach. To be honest, one of the reason for my change of heart was seeing how an application scales out to more than one developer.

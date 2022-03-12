# Testing testing 1,2,3 can you hear me?!

Hello! The old blog is now dead: I have moved us over to something Firebase hosted, running using React and Express. 

## Why on Earth did you do that?!

My manager actually expressed this: surely it's easier to just use GitHub with Jekyll but... why would you want to do that when you could have the entire joy of doing your own blog and setting it up on your own terms!

Also, I didn't quite understand how HandmadeBlog (however good it was) actually worked. I want to tie up libraries and cool stuff into this blog so I can do demos and stuff and I just didn't know how to work within the framework to do it. So - I wrote my own.

## How I designed it 

I actually put a bit of thought into how I'd like to build this out! 

![A over simplified architecture](https://github.com/BurgundyIsAPublicEnemy/vikadilly_articles/blob/main/_images/oversimplification.png)

I decoupled the content and the app. I did this so I could push content to the blog itself without worrying whether I had to redeploy to get it to show and it just meant I could keep the code lighter if all I had to do was get a String and shove it into a MarkDown formatter

Yeah the website is heavier than original but if you feel super inclined, you can always go to the public repo and check it out there. Not to mention cause it's hosted on a public GitHub repo without the code, it means you can even check out entries in a Terminal! Neat. 

So yeah, welcome to the new blog. There are issues of course, but let me know and I will do my best to fix ti

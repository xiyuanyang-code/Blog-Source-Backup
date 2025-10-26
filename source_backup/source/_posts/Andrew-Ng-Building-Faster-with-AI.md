---
title: 'Andrew Ng: Building Faster with AI'
date: 2025-08-07 13:38:38
excerpt: "Transcripts and Comments for the celebrity speech: Andrew Ng: Building Faster with AI, discussing the future of Agentic AI and ai-assisted software development."
index_img: /img/cover/agenticai.jpg
math: true
categories:
  - Artificial Intelligence
tags:
  - Coding
  - Celebrity Speech
  - LLMs
  - Artificial Intelligence
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Building Faster with AI: Lessons from the Startup Trenches

## Introduction

Founder of DeepLearning.AI Andrew Ng’s lecture titled “Building Faster with AI”, on June 17, 2025 at AI Startup School in San Francisco.

My first course regarding Machine Learning and Artificial Intelligence is taught by Andrew in DeepLearningAI. He focuses on the developing of Agentic AI instead of the improvement of foundation models.

{% note info %}

Transcripts are from: [This Blog](https://singjupost.com/andrew-ng-building-faster-with-ai-transcript/)

{% endnote %}

## Transcripts and Comments

### The AI Stack and Opportunities

{% note primary %}

Before diving to speed, a lot of people ask me, “Hey, Andrew, where are the opportunities for startups?” This is what I think of as an AI stack, where at the lowest level are the semiconductor companies, then the clouds or hyperscalers built on top of that.

A lot of the AI foundation model companies built on top of that. And even though a lot of PR excitement and hype has been on these technology layers, it turns out that almost by definition, the biggest opportunities have to be at the application layer, because we actually need the applications to generate even more revenue so that they can afford to pay the foundation, cloud, and semiconductor technology layers.

For whatever reason, media and social media tends not to talk about the application layer as much. But for those of you thinking of building startups, almost by definition, the biggest opportunities have to be there. Although, of course, the opportunities are all layers of the stack.

{% endnote %}

### The Rise of Agentic AI

{% note primary %}

One of the things that’s changed a lot over the last year, and in terms of AI tech trends, if you ask me what’s the most important tech trend in AI, I would say it is the rise of agentic AI. About a year and a half ago, when I started to go around and give talks to try to convince people that AI agents might be a thing, I did not realize that around last summer, a bunch of marketers would get a hold of this term and use it as a sticker and slap it on everything in sight, which made it almost lose some of its meaning. But I want to share with you from a technical perspective why I think agentic AI is exciting and important and also opens up a lot more startup opportunities.

It turns out that the way a lot of us use LLMs is to prompt it, to have it generate an output. And the way we have an LLM output something is as if you’re going to a human or in this case an AI and asking it to please type out an essay for you by writing from the first word to the last word all in one go without ever using backspace. And humans, we don’t do our best writing, being forced to type in this linear order. And it turns out neither does AI. But despite the difficulty of being forced to write in this linear way, our LLMs do surprisingly well.

With agentic workflows, we can go to the AI system and ask it to please first write an essay online, then do some web research if it needs to, and fetch some web pages to put in the LLM context, then write the first draft, then read the first draft and critique it and revise it and so on. And so we end up with this iterative workflow where your model does some thinking, does some research, does some revision, goes back to do more thinking. And by going around this loop many times, it is slower, but it delivers a much better work product.

For a lot of projects that AI Fund has worked on, everything from pulling out complex compliance documents to medical diagnosis, to reasoning about complex legal documents, we found that these agentic workflows are really a huge difference between working versus not working. But a lot of the work that needs to be done, a lot of valuable businesses to be built still, will be taking workflows, existing or new workflows, and figure out how to implement them into these types of agentic workflows.

So just to update the picture for the AI stack, what has emerged over the last year is a new agentic orchestration layer that helps application builders orchestrate or coordinate a lot of calls to the technology layers underneath. And the good news is the orchestration layer has made it even easier to build applications. But I think the basic conclusion, the application layer has to be the most valuable layer of the stack still holds true.

{% endnote %}

### Best Practices for Startup Speed

{% note primary %}

With a bias of focus on the application layer, let me now dive into some of the best practices I’ve learned for how startups can move faster. It turns out that at AI Fund, we only focus on working on concrete ideas. To me, a concrete idea, a concrete product idea, is one that specifies in enough detail that an engineer can go and build it.

For example, if you say, “Let’s use AI to optimize healthcare assets,” that’s actually not a concrete idea. It’s too vague. If you tell me to write software to use AI to optimize healthcare assets, different engineers would do totally different things. And because it’s not concrete, you can’t build it quickly and you don’t have speed.

In contrast, if you had a concrete idea, like “Let’s write software to let hospitals, let patients, let MRI machines fly online to optimize usage.” I don’t know if this is a good or a bad concrete idea. It’s actually business already doing this. But it is concrete, and that means engineers can build it quickly. If it’s a good idea, you find out it’s a good idea; if it’s not a good idea, you will find out. But having concrete ideas buys you speed.

Or if someone were to say, “Let’s use AI for email personal productivity.” Too many interpretations of that. That’s not concrete. But if someone says, “Can you build an app? Gmail integrated automation, let’s use the right problem source, right, filter and tag emails.” That is concrete. I could go build that this afternoon. So concreteness buys you speed.

The deceptive thing for a lot of entrepreneurs is the vague ideas tend to get a lot of kudos. If you go and tell your friends, “We should use AI to optimize the use of healthcare assets,” everyone will say that’s a great idea. But it’s actually not a great idea, at least in the sense of being something you can build. It turns out when you’re vague, you’re almost always right. But when you’re concrete, you may be right or wrong. Either way is fine. We can discover that much more fast, which is what’s important for a startup.

In terms of executing on concrete ideas, I find that in AI Fund, I ask my team to focus on concrete ideas because a concrete idea gives clear direction and the team can run really fast to build it and either validate it, prove it out, or falsify it and conclude it doesn’t work. Either way is fine. So let’s go and do that quickly.

It turns out that finding good concrete ideas usually requires someone, could be you, could be a subject matter expert, thinking about a problem for a long time. For example, actually before starting Coursera, I spent years thinking about online education, talking to users, holding my own intuitions about what would make a good ed tech platform. And then after that long process, I think YSP sometimes calls it wondering the idea maze. But after thinking about it for a long time, you find that the guts of people that thought about this for a long time can be very good about rapidly making decisions. As in, after you’ve thought about this, talked to customers for a long time, if you ask this expert, should I build this feature or that feature?

Their gut, which is an instantaneous decision, can be actually a surprisingly good proxy. It can be a surprisingly good mechanism for making decisions. And I know I work on AI. You might think I’ll say, oh, we need data. And of course, I love data. But it turns out getting data for a lot of startups is a slow mechanism for making decisions. And a subject matter expert with good guts is often a much better mechanism for making a speedy decision.

One other thing, for many successful startups, at any moment in time, you’re pursuing one very clear hypothesis that you’re building out and trying to sell, trying to validate, or falsify. And a startup doesn’t have resources to hedge and try 10 things at the same time. So pick one, go for it. And if data tells you to lose faith in that idea, that’s actually totally fine. Just pivot on the dime to pursue a totally different concrete idea.

So that’s what often feels like at AI fund. We’re pursuing one thing doggedly with determination until the world tells us we were wrong, then change and pursue a totally different thing with equal determination and equal doggedness.

One other pattern I’ve seen, if every piece of new data causes you to pivot, it probably means you’re starting off from too weak a base of knowledge. If every time you talk to a customer, you totally change your mind, probably means you don’t know enough about that sector yet to have a really high-quality concrete idea. And finding someone who’s thought about a subject for longer may get you on the better path.

{% endnote %}

- **The concrete idea** let you goes faster! AI coding assistant do the grunt work. (For example, how to remove the all NaN files using Python scripts? You can use AI to solve this, for it is testable and easy for AI but hard and needs a lot of time-consumption and memorization for human coders.)

- Vague is always correct but not useful

- Features are also a typing of making decisions.



### Building Faster with AI Coding Assistance

{% note primary %}


In order to go faster, the other thing I often think about is the **built feedback loop**, which is rapidly changing when it comes to how we build with AI coding assistance. So when you’re building a lot of applications, one of the biggest risks is customer acceptance. A lot of startups struggle not because we can’t build whatever we want to build, but because we build something and it turns out nobody cares.

For a lot of the way I build startups, especially applications, less so deep tech, less so technology startups, but definitely application startups, is we often build software so there’s an engineering task and then we will get feedback from users and there’s a **product management** task and then we’ll go back, then based on the user feedback, we’ll tweak our views on what to build, go back to write more software and we go around this loop many, many times, iterate to what product market fits.

It turns out that with AI coding assistance, which Andrei talked about as well, rapid engineering is becoming possible in a way that just was not possible. It’s becoming much more feasible. So **the speed of engineering is going up rapidly and the cost of engineering is also going down rapidly**. This changes the mechanisms by which we drive startups around this loop.

{% endnote %}

### Prototypes vs. Production Software

{% note primary %}
When I think about the software that I do, I maybe put it into two major buckets. Sometimes I’ll build quick and dirty prototypes to test an idea. So you build a new customer service chatbot, let’s build an AI to process legal documents, whatever. Build a quick and dirty prototype to see if we think it works. The other type of software where I do is maintain production software, maintain legacy software, but these massive production-ready code bases.

Depending on which analyst’s report you trust, it’s been hard to find very rigorous data on this. When writing production quality code, maybe we’re 30 to 50% faster with AI assistance. Hard to find a rigorous number. But in terms of building quick and dirty prototypes, we’re not 50% faster. I think we’re easily 10 times faster, maybe much more than 10 times faster.

There are a few reasons for this. When you’re building stand-alone prototypes, there’s less integration with legacy software infrastructure and legacy data needed. Also, the requirements for reliability, even scalability, even security are much lower.

I know I’m not supposed to tell people to write insecure code. Feels like the wrong thing to say. But I routinely go to my teams and they go ahead and write insecure code because if this software is only going to run on your laptop and you don’t plan to maliciously hack your own laptop, it’s fine to have insecure code. But of course, after it seems to be working, please do make it secure before you ship it to someone else. Leaking PII, leaking sensitive data, that is very damaging. So before you ship it, make it secure and scalable. But if you’re just testing it, it’s fine.

I find increasingly startups will systematically pursue innovations by building 20 prototypes to see what works. Because I know that there’s some ads in AI, a lot of proof of concepts they’ll make into production. But I think by driving the cost of a proof of concept low enough, it’s actually fine if lots of proof of concepts don’t see the light of day.

The mantra, “move fast and break things,” got a bad rep because, you know, it broke things. And some teams took away from this that you should not move fast, but I think that’s a mistake. I tend to tell my teams to move fast and be responsible. And I think there are actually ways to move really quickly while still being responsible.

{% endnote %}

### Evolution of AI Coding Assistants

{% note primary %}

In terms of the AI assistance coding landscape, I think, was it three, four years ago, Code Autocomplete, right, popularized by GitHub Copilot. And then there was the cursor windserve generation of AI-enabled IDEs. We’re doing great, use windserve and cursor quite a lot. And then starting, I don’t know, six, seven months ago, there started to be this new generation of highly agentic coding assistants, including actually using O3 a lot for coding. Cloud Code is fantastic. Since Cloud 4 release, it’s become, and I’ll speak again in three months if I use something different, but the tool’s evolving really rapidly.

I think Cloud Code Codex is a new generation of highly agentic coding assistants that is making developer productivity keep on growing. And the interesting thing is, if you’re even half a generation or one generation behind, it actually makes a big difference compared to if you’re on top of the latest tools. And I find my team’s taking really different approaches to software engineering now compared to even three or six months ago.

{% endnote %}

### The Changing Value of Code

{% note primary %}

One surprising thing is, we’re used to thinking of code as this really valuable artifact because it’s so hard to create. But because the cost of software engineering is going down, code is much less of a valuable artifact as it used to. So I’m on teams where we’ve completely rebuilt the code base three times in the last month, right? Because it’s not that hard anymore to just completely rebuild the code base, pick a new data schema, it’s fine. Because the cost of doing that has plummeted.

Some of you may have heard of Jeff Bezos’ terminology of **a two-way door versus a one-way door**. A two-way door is a decision that you can make. If you change your mind, come back out, you know, reverse it relatively cheaply. Whereas a one-way door is you make a decision and you change your mind, it’s very costly, very difficult to reverse.

So choosing the software architecture of your tech stack used to be a one-way door. Once you’ve built on top of a certain tech stack, you know, set a database schema, really hard to change it. So that used to be a one-way door. I don’t want to say it’s totally a two-way door, but I find that my team will more often build on a certain tech stack. A week later, change your mind, let’s throw the code base away and redo it from scratch on a new tech stack.

I don’t want to overhype it. We don’t do that all the time. There are still costs to redoing that. I find my team is often rethinking what is a one-way door and what’s now a two-way door because the cost of software engineering is so much lower now.

Maybe going a little bit beyond software engineering, I feel like it’s actually a good time to empower everyone to build AI. Over the last year, a bunch of people have advised others not to learn to code on the browser. AI will automate it. I think we’ll look back on this as some of the worst career advice ever given because as better tools make software engineering easier, more people should do it, not fewer.

Many decades ago, the world moved from punch cards to keyboard and terminal that made coding easier. When we moved from assembly to high-level languages like COBOL, there were actually people arguing back then that now we have COBOL, we don’t need programmers anymore. People actually wrote papers to that effect. But, of course, that was wrong, and programming languages made it easier to code and more people learned to code.

Text-to-speech, IDEs, AI coding assistants, and as coding becomes easier, more people should learn to code. I have a controversial opinion, which is I think it’s time for everyone in every job role to learn to code. In fact, on my team, my CFO, my head of talent, my recruiters, my front desk person, all of them know how to code. I actually see all of them performing better at all of their job functions because they can code. I think I’m probably a little bit ahead of the curve. Most businesses are not there yet, but in the future, I think we’ll empower everyone to code. A lot of people can be more productive.

I want to share with you one lesson I learned as well on why we should have people learn to do this, which is when I was teaching Generative AI to Everyone on Coursera, we needed to generate background art like this using Mid-Journey. One of my team members knew art history, and so he could prompt Mid-Journey with the genre, the palette, the artistic inspiration, had a very good control over the images he generated, so we ended up using all of Tommy’s generated images.

In contrast, I don’t know art history, and so when I prompt image generation, I could write, “please make pretty pictures of robots for me.” I could never have the control that my collaborator could, and so I couldn’t generate as good images as he could.

I think with computers, one of the most important skills of the future is the ability to tell a computer exactly what you want so they will do it for you. It will be people that have that deeper understanding of computers that will be able to command a computer to get the outcome you want, and learning to code, not that you need to write the code yourself, see an AI to code for you, seems like it would be the best way to do that for a long time.

{% endnote %}

AI Coding makes developing more like a "two-way door", it enables programmers to rebuild and refactor their codebases more easily, with less cost and more efficiency.

Moreover, the basic prototype for product manager and programmers will remain the same (Ai will not replace programmers in the predictable future), however, **the ratio will change**. The job that product manager do will become a bottleneck for the software developing, e.g. how to find a new feature, how to design the overall artifacts for a complex system or projects. We will see more and more product manager in the future!


### Shifting Dynamics in Product Management

{% note primary %}

With software engineering becoming much faster, the other interesting dynamic I’m seeing is that the product management work, getting user feedback, deciding what features to build, that is increasingly the bottleneck. And so I’m seeing very interesting dynamics in multiple teams. Over the last year, a lot more of my teams have started to complain that they’re bottlenecks on product engineering and design, because the engineers have gotten so much faster.

Some interesting trends I’m seeing, three, four, five years ago, Silicon Valley used to have these slightly suspicious rules of thumb, but nonetheless rules of thumb, will have 1 PM to 4 engineers, or 1 PM to 7 engineers. It was just like PM product manager to engineering ratio, right? I think we’re getting past those typicals of 1 PM to 6, 7 engineers. And with engineers becoming much faster, I don’t see product management work, designing what to build, becoming faster at the same speed as engineers. I’m seeing this ratio shift.

So literally yesterday, one of my teams came to me, and for the first time, when we’re planning to hit a conference project, this team proposed to me not to have 1 PM to 4 engineers, but to have 1 PM to 0.5 engineers. So the team actually proposed to me, I still don’t know if this is a good idea. It was the first time in my life that I saw engineers propose to me, having twice as many PMs as engineers. It was a very interesting dynamic. I still don’t know if this proposal I heard yesterday is a good idea, but I think it’s a sign of where the world is going. And I find that PMs that can code, or engineers with some product instincts, often end up doing better.

{% endnote %}

### Tactics for Getting Product Feedback

{% note primary %}
The other thing that I found important for startup leaders is, because engineering is becoming so fast, if you have good tactics, so getting rapid feedback to shape your perspective on what to build faster, it helps you get faster as well. So I’m going to go through a portfolio of tactics for getting product feedback to keep shaping what you will decide to build. And we’re going to go through a list of the faster, maybe less accurate, the slower, more accurate tactics.

So the fastest tactics for getting feedback is, look at the product yourself, and just go by your gut. And if you’re a subject matter expert, this is actually surprisingly good, if you know what you’re doing.

A little bit slower is, go ask three friends or teammates to get feedback, to play their product and get feedback.

A little bit slower is, ask three to ten strangers for feedback. It turns out, when I built products, one of the most important skills I think I learned was how to sit in the coffee shop, how to sit in a, when I travel, I often sit in the hotel lobby. It turns out, learn to spot places with high foot traffic, and very respectfully, you know, grab strangers and ask them for feedback on whatever I’m building. This used to be easier when I was less known, when people recognized you, it was a little bit more awkward.

But I found that, I’ve actually sat with teams in the hotel lobby, very high foot traffic, and, you know, very respectfully ask strangers, “hey, we’re building this thing, do you mind taking a look?” Oh, and I actually learned in the coffee shop that a lot of people working, a lot of people don’t want to be working, so we give them an excuse to be distracted, and they’re very happy to do that too. But I’ve actually kind of made tons of product decisions in the hotel lobby or the coffee shop with collaborators, just like that.

Send prototypes to a hundred testers, if you have access to larger groups of users, send prototypes to more users, and these are, these get to be slow and slower tactics.

And I know Silicon Valley, you know, we like to talk about A-B testing. Of course, I do a ton of A-B testing, but contrary to what many people think, A-B testing is now one of the slowest tactics in my menu, because it’s just slow to ship it. It depends on how many users you have, right?

And then the other thing is, as you use anything but the first tactic, some teams will look at the data, they make a decision, but the missing piece is, when I A-B test something, I don’t just use the result of A-B test to pick product A or product B. My team will often sit down and look carefully at the data to hone our instincts, to speed up, to improve the rate at which we’re able to use the first tactic, to make high-quality decisions.

We often sit down and think, “gee, I thought, you know, this product name will work better than that product name. Clearly, my mental model that I use is wrong.” So we sit down and think, to update our mental model, using all of that data to improve the quality of our guts on how to make product decisions faster. That turns out to be really important.

{% endnote %}

- For myself for getting quick feedback

- Ask 3 friends or teammates

- Ask 3-10 strangers.

Less accuracy but more speed..

**Using A/B test**, but not with intent.

### The importance of technical understanding

{% note primary %}
All right, so we talked about concrete ideas, speed up engineering, speed up product feedback. This is one last thing I want to touch on, which is, I’ve seen that understanding AI actually makes you go faster, and here’s why. As an AI person, maybe I’m biased to be pro-AI, but I want to share with you why.

So it turns out that when it comes to mature technology, like mobile, you know, many people have had smartphones for a long time. We kind of know what a mobile app can do, right? So many people, including non-technical people, have good glimpses of what a mobile app can do.

If you look at mature job roles, like sales marketing, HR, legal, they’re all really important, all really difficult, but, you know, there are enough marketers that have done marketing for long enough, and the marketing tactics haven’t changed that much in the last year. So there are a lot of people that are really good at marketing, and it’s really important, really hard, but that knowledge is relatively diffused because, you know, the knowledge of how to do HR, it hasn’t changed dramatically, you know, in the last six months. But AI is emerging technology,

And so the knowledge of how to do AI really well is not widespread, and so teams that actually get it, that understand AI, do have an advantage over teams that don’t. Whereas if you need an HR problem solved, you can find someone that knows how to do it well, probably, but if you have an AI problem, knowing how to actually do that could put you ahead of other companies.

Things like, what accuracy can you get for a customer service chatbot? Should you prompt or fine-tune using a RAG workflow? How do you get a voice out to those low latencies? There are a lot of these decisions that, if you make the right technical decision, you can solve the problem in a couple of days. If they make the wrong technical decision, you could chase a blind alley for three months.

One thing I’ve been surprised by: it turns out if you have two possible architecture decisions, it’s one bit of information. It feels like, if you don’t know the right answer, at most, you’re twice as slow, right? It’s one bit. Try both. It feels like one bit of information can, at most, buy you a two-week speed-up. And I think, in some theoretical sense, that is true, but what I see in practice, if you flip the wrong bit, you’re not twice as slow, you spend like ten times longer chasing a blind alley, which is why I think having the right technical judgment really makes startups go so much faster.

{% endnote %}

- The mature of job like HR and other job roles, the knowledge of how to do that is quite confuse.

- **A deep understanding of AI is a differentiator**.

### Building with AI Building Blocks: About AI tools

{% note primary %}
The other reason why I find sitting on top of AI really helpful for startups is, over the last two years, we have just had a ton of wonderful Gen-AI tools, or Gen-AI building blocks. A partial list includes prompting, agentic workflows, evals, guardrails, RAG, voice stack, async programming, lots of ETL, embeddings, fine-tuning, GraphDB, how to compute used, MCP, using models. Just a long and wonderful list of building blocks that you can quickly combine to build software that no one on the planet could have built even a year ago. And this creates a lot of new opportunities for startups to build new things.

When I learned about these building blocks, this is actually a picture that I had in mind. If you own one building block, like you have a basic white building block, you can build some cool stuff. Maybe you know how to prompt, so you have one building block. You build some amazing stuff. But if you get a second building block, like you also know how to build chatbots, so you have a white Lego brick and a black Lego brick, you can build something more interesting. And if you acquire a blue building brick as well, you can build something even more interesting. Get a few red building bricks, maybe a little yellow one, more interesting. Get more building bricks, get more building bricks, and very rapidly, the number of things you can combine them into grows kind of combinatorially or grows exponentially.

So knowing all these wonderful building blocks lets you combine them in a much richer combination. One thing that DeepLearning.AI does, I actually take a lot of DeepLearning.AI short courses myself, because I work with all the leading AI companies in the world and try to hand out building blocks. But when I look at the DeepLearning.AI course catalog, this is actually what I see. And whenever I take new courses and learn these building blocks, I feel like I’m getting new things that can combine to form kind of combinatorially or exponentially more software applications that were not possible just one or two years ago.

{% endnote %}

Tools are coding blocks. (prompting, agentic workflows, evals, guardrails, RAG, voice stack, async programming, lots of ETL, embeddings, fine-tuning, GraphDB, how to compute used, MCP, reasoning models)

The more blocks you yield, the more software apps you can create!

> But use one block deeply is more useful than knowing how to use mountains of blocks just at the interface level.

[DeepLearning AI](https://www.deeplearning.ai/) is a great course for building blocks with high quality videos.

- [geeksforgeeks](https://www.geeksforgeeks.org/)

- [freecodecamp](https://www.freecodecamp.org/news/)

### Conclusion

{% note primary %}

So just to wrap up, this is my last slide. Anyone want to take questions if you all have any? I find that there are many things that matter for a startup, not just speed. But when I look at the startups that AI Fund is building, I find that the management team’s ability to execute at speed is highly correlated with its odds of success.

And some things we’ve learned to get you speed is, work on concrete ideas. It’s got to be good concrete ideas. I find that as an executive, I’m judged on the speed and quality of my decisions. Both do matter, but speed absolutely matters. Rapid entry with AI coding assistance makes you go much faster, but that shifts the bottleneck to getting user feedback and the product decisions. And so having a portfolio of tactics to go get rapid feedback. And if you haven’t learned to go to a coffee shop and talk to strangers, it’s not easy, but just be respectful of people. That’s actually a very valuable skill for entrepreneurs to have, I think. And I think also staying on top of AI technology buys you speed.

{% endnote %}

### Q&A Session

{% note success %}
AUDIENCE QUESTION: I have a couple of quick questions. As AI advances, do you think it’s more important for humans to develop the tools or learn how to use the tools better? How can we position ourselves to remain essential in a world where intelligence is becoming democratized?

ANDREW NG: I feel like AGI has been overhyped. And so for a long time, there’ll be a lot of things that humans can do that AI cannot. And I think in the future, **the people that are most powerful are the people that can make computers do exactly what you want it to do**. And so I think staying on top of the tools, some of us will build tools sometimes, but there are a lot of other tools that others will build that we can just use. But people that know how to use AI to get computers to do what you want it to do will be much more powerful. Not worry about people running out of things to do, but people that can use AI will be much more powerful than people that don’t.

{% endnote %}

{% note success %}

AUDIENCE QUESTION: So, first of all, thank you so much. I have a huge respect for you. And I think that you are a true inspiration for a lot of us. My question is about the future of compute. So as we move towards a more powerful AI, where do you think that compute is heading? I mean, we see people saying, let’s ship GPUs to space. Some people talking about nuclear power data centers. What do you think about it?

ANDREW NG: There’s something I was debating what I wanted to say in response to the last question about kind of AGI. Maybe I’ll answer this and go over the last question.

> Hype: 炒作

So it turns out there’s one framework you can use for deciding what’s hyped and what’s not hyped. I think over the last two years, there’s been a handful of companies that hyped up certain things for promotional, PR, fundraising, influence purposes. And because AI was so new, a handful of companies got away with saying almost anything without anyone fact-checking them because the technology was not understood.

So one of my mental filters is there’s certain hype narratives that make these businesses look more powerful that’s been amplified. And so for example, **this idea that AI is so powerful, we might accidentally lead to human extinction. That’s just ridiculous**. But it is a hype narrative that made certain businesses look more powerful and it got ramped up and actually helped certain businesses’ fundraising goals.

AI is so powerful soon no one will even have a job anymore. That’s not true, right? But again, that made these businesses look more powerful, got hyped up. Or we are so powerful, so went the hype narrative, we’re so powerful that by training a new model, we will casually wipe out thousands of startups. That’s just not true. Yes, Jasper ran into trouble, a small number of companies got wiped out, but it’s not that easy to casually wipe out thousands of startups.

AI needs so much electricity, only nuclear power is good enough for that. That wind, solar stuff, this is not true. So I think a lot of these GPUs in space, I don’t know, go for it. I think we have a lot of room to run still for terrestrial GPUs. But **I think some of these hype narratives have been amplified that I think are a distortion of what actually will be done**.

{% endnote %}

{% note success %}

AUDIENCE QUESTION: There’s a lot of hype in AI and nobody’s really certain about how we’re going to be building the future with it, but what are some of the most dangerous biases or overhyped narratives that you’ve seen people talk about or get poisoned by that they end up running with that we should try

I think the dangerous AI narrative has been overhyped. AI is a fantastic tool, but it’s like any other powerful tool, like electricity, lots of ways to use it for beneficial purposes, also some ways to use it in harmful ways. I find myself not using the term AI safety that much, not because I think we should build dangerous things, but because I think safety is not a function of technology, it’s a function of how we apply it.

So like electric motor, you know, you can’t, **the maker of electric motor can’t guarantee that no one will ever use it for an unsafe downstream task**, like using electric motor can be used to build a Dallas machine, electric vehicle, can be used to build a smart bomb, but the electric motor manufacturer can’t control how it’ll be used downstream. So safety is not a function of the electric motor, it’s a function of how you apply it.

> AI 的安全性取决于 AI 的使用方式。

And I think the same thing for AI. AI is neither safe nor unsafe. It is how you apply it that makes it safe or unsafe. So instead of thinking about AI safety, I often think about responsible AI because it’s how we use it responsibly, hopefully, or irresponsibly that determines whether or not what we build with AI technology ends up being harmful or beneficial.

And I feel like sometimes the really weird corner cases, they get hyped up in the news. I think just one or two days ago, there was a Wall Street Journal article about AI losing control of AI or something. And I feel like that article took a corner case experiments run in a lab and sensationalized it in a way that I think was really disproportionate relative to the lab experiment that was being run. And unfortunately, technology is hard enough to understand that many people don’t know better. And so these hype narratives do keep on getting amplified. And I feel like this has been used as a weapon against open source software as well, which is really unfortunate.

{% endnote %}

{% note success %}

AUDIENCE QUESTION: Thank you for your work. I think your impact is remarkable. My question is, as aspiring founders, how should we be thinking about business in the world where anything can be disrupted in a day?**Whatever great mode, product, or feature you have can be replicated with vibe code and competitors and basically ours.**

ANDREW NG: It turns out when you start a business, there are a lot of things to worry about. The number thing I worry about is, are you building a product that users love? It turns out that when you build a business, there are lots of things to think about. Go-to-market, channel, competitors, technology, mode, all that is important. But if I were to have a singular focus on one thing, it is, **are you building a product that users really want**? Until you solve that, it’s very difficult to build a valuable business.

After you solve that, the other questions do come to play. Do you have a channel to get the customers? What is pricing long-term? What is your moat? I find that moats tend to be overhyped, actually. I find that more businesses tend to start off with a product and then evolve eventually into a moat. But consumer products’ brand is somewhat more defensible and if you have a lot of momentum, it becomes harder to catch you. But enterprise products, sometimes if you have a, maybe moat is more of a consideration if there are channels that are hard to get into enterprises.

So I think, when the AI fund looks at businesses, we actually wind up doing a fairly complex analysis of these factors and writing a two to six page narrative memo to analyze it before we decide whether or not to proceed or not. And I think all of these things are important, but I feel like at this moment in time, the number of opportunities, meaning the amount of stuff that is possible that no one’s built yet in the world, seems much greater than the number of people with the skill to build them.

So definitely at the application layer, it feels like there’s a lot of white space for new things you can build that no one else seems to be working on. And I would say, focus on building a product that people want, that people love, and then figure out the rest of it along the way, although that’s important to figure out along the way.

{% endnote %}

{% note success %}

AI Tool Integration and Agent Architecture
AUDIENCE QUESTION: Hi, Professor, thanks for your wonderful speech. I’m an analyst researcher from Stanford, and I think your metaphor in your speech is very interesting. You said the current AI tools are like bricks and can be built upon accumulation. However, so far it is difficult to see the accumulative functional expansion of the integration of AI tools, because they often align on a stacking of functions based on intent distribution, and are accompanied by dynamic problems of tokens and time overhead. So which is different from static engineering? So what do you think will be the perspective of a possible Agent 2 accumulation effect in the future?

ANDREW NG: Just some quick remarks to that. You mentioned agent token cost. My most common advice to developers is to first approximation, just don’t worry about how much tokens cost. Only a small number of startups are lucky enough to have users use so much of your product that the cost of tokens becomes a problem. It can become a problem. I’ve definitely been on a bunch of teams where the cost, users like our product, and we start to look at our GenEI builds, and it was definitely climbing in a way that really became a problem. But it’s actually really difficult to get to a point where your token usage costs are a problem.

And for the teams I’m on, where we were lucky enough that users made our token cost a problem, we often had engineering solutions to then bend the curves and bring it back down through prompting, fine-tuning, your CS5s optimized, or whatever.

And then what I’m seeing is that I’m seeing a lot of agentic workflows that actually integrate a lot of different steps. So, for example, if you build a customer service chatbot, we’ll often have to use prompting, maybe optimize some of the results in DSPy, build evals, build guardrails. Maybe the customer service chatbot needs RAG a part of the way to get information to feedback to the user. So, I actually do see these things grow.

But one tip for many of you as well is I will often architect my software to **make switching between different building block providers relatively easy**. So, for example, I have a lot of products that build on top of OMs, but sometimes they point to a specific product and ask me, which OM are we using? I honestly don’t know, because we’ll build up evals, and when there’s a new model that’s released, we’ll quickly run evals to see if the new model is better than the old one. And then just switch to the new model or the new model that’s better on evals.

And so the model we use week by week, sometimes our agents will change it without even bothering to tell me because the evals show the new model works better. So, it turns out that switching costs for foundation models is relatively low, and we often architect our software. Oh, AIC, there’s an open sourcing that my friends and I worked on to make switching easier. Switching costs for the orchestration platforms is a little bit harder, but I find that preserving that flexibility in your choice of building blocks often lets you go faster, even if you’re building more and more things on top of each other. So, hope that helps.

> But models are products! It is an interesting problem.

{% endnote %}

{% note success %}

The Future of AI in Education
AUDIENCE QUESTION: In the world of education and AI, there are two paradigms mostly. So, one is AI can make teachers more productive, automating grading and automating homeworks. But another school of thought is that there’ll be personal tutors for every student. So, every student can have one tutor that gets feedback from an AI and gets personal questions from them. So, how do you see these two paradigms converge and how would education look like in the next five years?

ANDREW NG: I think everyone feels like a change is coming in their tech. I don’t think the disruption is here yet. I think a lot of people are experimenting with different things. So, Coursera has Coursera Coach, which actually works really well. Deep Learner.AI is more focused on teaching AI, also has some built-in chatbots. A lot of teams are experimenting with auto-grading.

There’s an avatar of me on the DeepLearning.AI website you can talk to if you want at DeepLearning.AI/avatar. And I think for some things like language learning with Speak, Duolingo, that has become clearer, some of the ways that I would transform it. For the broader educational landscape, the exact ways that AI would transform it, I see a lot of experimentation. I think what Khanmigo learning, which I’ve been doing some work with, is doing is very promising for K-12 education.

But I think what I’m seeing is frankly, tons of experimentation, but the final end state is still not clear. I do think education will be hyper-personalized, but that workflow is an avatar, is a text chatbot. What’s the workflow? I feel like the hype from a couple of years ago that with AGI soon and it’ll be all so easy, that was hype. The reality is work is complex, right? Teachers, students, people do really complex workflows. And for the next decade, we’ll be looking at the work that needs to be done and figuring out how to map it to agentic workflows.

And education is one of the sectors where this mapping is still underway, but it’s not yet mature enough to the point where the end state is clear. So I think we should all just keep working on it.

{% endnote %}

{% note success %}

AUDIENCE QUESTION: Hey, my question is, I think AI has a lot of great potential for good, but there’s also a lot of potential for bad consequences as well, such as exacerbating economic inequality and things like that. And I think a lot of our startups here, while they’ll be doing a lot of great things, will also be, just by virtue of their product, be contributing to some of those negative consequences. So I was curious, how do you think us as AI builders should kind of balance our product building with also the potential societal downsides of some AI products? And essentially, how can we both move fast and be responsible, as you mentioned in your talk?

ANDREW NG: Look in your heart, and the fundamentals of what you’re building, if you don’t think it’ll make people at large better off, don’t do it, right? I know it sounds simple, but it’s actually really hard to do at the moment. But AI Fund, we’ve killed multiple projects, not on financial grounds, but on ethical grounds, where there are multiple projects. We looked at the economic case is very solid, but we said, “You know what? We don’t want this to exist in the world,” and we just killed it on that basis. So I hope more people will do that.

And then I worry about bringing everyone with us. One interesting thing I’m seeing is people in all sorts of job roles that are not engineering are much more productive if they know AI than if they don’t. And so, for example, on my marketing team, my marketers, they know how to code, frankly. They were running circles around the ones that don’t. So then everyone learned to code, and then they got better. So I feel like trying to bring everyone with us to make sure everyone is empowered to build with AI. That’ll be an important part of what all of us do, I think.

{% endnote %}

{% note success %}

AUDIENCE QUESTION: I’m one of your big fans, and thank you for your online courses. Your courses make the deep learning much more accessible to the world. And my question is also about education. As AI becomes more powerful and widespread, there seems to be a growing gap between what can it actually do and what people perceive it. So what do you think about, is it important to educate the general public about deep learning stuff and not only educate those technical people and make people understand more what AI really do and how it works?

ANDREW NG: I think that knowledge will diffuse. DeepLearning.AI, we want to empower everyone to build with AI. So we’re working on it. Many of us are working on it. I’ll just tell you what I think is the main thing. I think there are maybe two dangers. One is if you don’t bring people with us fast enough, I hope we’ll solve that.

There’s one other danger, which is, it turns out that if you look at the mobile ecosystem, mobile phones, it’s actually not that interesting. And one of the reasons is there are two gatekeepers, Android and iOS. And unless they let you do certain things, you’re not allowed to try certain things on mobile. And I think this hampers innovators.

These dangers of AI have been used by certain businesses that are trying to shut down open source because a number of businesses don’t love to be a gatekeeper to large-scale foundation models. So I think hyping up dangers, supposed false dangers of AI, in order to get regulators to pass laws, like to propose the SB1047 in California, which thank goodness we shut down, would have put in place really burdensome regulatory requirements that don’t make anyone safer, but would make it really difficult for teams to release open source and open weight software.

So one of the dangers, the inequality as well, is that these regulatory, awful regulatory approaches, and I’ve been in the room, right? Some of these businesses said stuff to regulators that was just not true. So I think that some of these arguments, the danger is that these regulatory proposals succeed and end up stifling regulations, leaving us with a small number of gatekeepers where everyone needs the permission of a small number of companies to fine-tune the model, come to a certain way. That’s what would stifle innovation and prevent the diffusion of this information to let lots of startups build whatever they want responsibly, but at a freedom to innovate.

So I think so long as we prevent this line of attack on open source, open weight models from succeeding, and we’ve made good progress, but the threat is still there, then I think eventually we’ll get to the diffusion of knowledge and we can hopefully then bring everyone with us. But this fight to protect open source, we’ve been winning, but the fight is still on and we still have to keep up that work to protect open source.

{% endnote %}

## Conclusion

AI Coding Assistant?

- Most of the people do not know how ti use AI in a proper way. Tha lack of AI usage capability still exists.

- The Core principle and core capabilities makes no changes.

- **Memorization is less important in the era of AI**, for AI will handle those stuffs and grunt work. (For example, the boss don't need to capture all the details of the product, instead, it needs to capture the market and the future development, which is critical to the development)

- Language is the bound of communication and philosophy.

- Tech Stack is less important as well, but we still need it! **Efficiency** is the only measurement for the AI era, great efficiency means the great tools.

- You need to transform these important abilities:

  - Fanaticism （狂热）
  - Logic from Math （数理）
  - Creation （创造）

The pyramids below are borrowed from [This lecture](https://yixi.tv/#/speech/detail?id=1352)

![The new pyramid in the AI era](https://s1.imagehub.cc/images/2025/08/08/2ed9375467d09c0cf478702610c4978c.jpg)
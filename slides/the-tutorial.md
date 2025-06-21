<style>
:root {--r-code-font: "FiraCode Nerd Font";}
.reveal .hljs {min-height: 50%;}
</style>

![[git-logo.png|500]]

# The Tutorial

notes:
Hi all, I spent quite a while thinking about what to base this talk on. After thinking for a while about my experiences and the opinions of people who might not know anything about game development, I decided to call this talk; The Tutorial.

A tutorial in a game is indispensable. It teaches the players how to play the game, what controls are there, what the backstory is. But most of all, it gives the player a taste of how fun the game can be. If a tutorial is able to communicate the essence of a game to its players, it’s perfect.

---

![[alex.png|400x400]]
## About Me

notes:

Before that, let me just introduce myself briefly. I’m Alex Kam, compared to a lot of you guys, I’m actually very inexperienced and probably shouldn’t be in a position to give any lectures. But maybe you might find whatever I say to be useful one day.

---
<grid drag="40 50" drop="5 15">
![[enter_the_arena.png]]
</grid>

<grid drag="40 50" drop="-5 15" >
![[ascension.png]]
</grid>

<grid drag="45 20" drop="3 -15">
[Enter the Arena](https://alexander-kam.itch.io/enter-the-arena) (*2019*)
<br>
<font size ="5">*2D simple top-down survival shooter*</font>
</grid>

<grid drag="45 20" drop="-3 -15">
[Ascension](https://alexander-kam.itch.io/ascension) (*2020*)
<br>
<font size ="5">*2D platformer rogue-like arcade game*</font>
</grid>

notes:
I made my first game with a friend in 2019 for a school project. It was a top-down shooter where you customised your shooting rate and fire power to fight waves of enemies called Enter the Arena.

In 2020, I made another game by myself for my final project, it was a rogue-like dungeon platformer with randomised levels. Both of these games are available online, but were just small projects at the time.

---

<grid drag="100 55" drop="0 10">
![[postknight2_banner.png]]
</grid>

<grid drag="100 20" drop="0 -10">
[Kurechii](kurechii.com) | [Postknight 2](https://postknight.com/) (*2021*)
<br>
<font size ="6">**iOS & Android**</font>
<br>
<font size ="5">*Fight through nasty enemy-infested trails and deliver goods to the adorable people of Prism!*</font>
</grid>

notes:

When COVID hit, I thought I might try my hand at finding an internship instead of starting university online. After a lot of emailing my unimpressive resume and showcasing my projects, I was offered an internship at a Malaysian game studio called Kurechii in 2021. I worked here as a programmer on a mobile game called Postknight 2 about delivering mail in a fantasy world where the routes between cities are filled with monsters.

---
<grid drag="40 55" drop="5 10">
![[unimelb.png|400x400]]
</grid>

<grid drag="40 55" drop="-5 10" >
![[kyodai.png|400x400]]
</grid>

<grid drag="45 20" drop="3 -15">
<font size = "6">[University of Melbourne](https://alexander-kam.itch.io/enter-the-arena) (*2022*)</font>
<br>
<font size ="5">*1st Year Computer Science*</font>
</grid>

<grid drag="45 20" drop="-3 -15">
<font size = "6">[University of Kyoto](https://alexander-kam.itch.io/ascension) (*2023*)</font>
<br>
<font size ="5">*Exchange Program*</font>
</grid>

notes:

After this, I entered the University of Melbourne in 2022 and underwent an exchange to Kyoto University in the first half of 2023.

---
<grid drag="100 55" drop="0 12">
![[deathgamehotel.png]]
</grid>

<grid drag="100 20" drop="0 -13">
[Skeleton Crew Studio](https://skeletoncrew.co.jp/) | [Death Game Hotel](https://www.meta.com/experiences/7371422382882960/) (*2023/4*)
<br>
<font size ="6">**Meta Quest VR**</font>
<br>
<font size ="5">*An online death game where players stake their own body parts to win.*</font>
</grid>
notes:
I was also searching for internship opportunities to fill my spare time after exchange and was offered a position at Skeleton Crew Studio, where I am now, working on an unreleased VR game for the Meta Quest called Death Game Hotel.

So, you can see from my background that I’m very young. And also very lucky to have found these studios willing to take-in a student on a whim. I don’t have a lot of experience, but I thought what I can share with you is what I learned stepping into the gaming industry.

With a broad audience of different careers, you might be involved in games, you might not. But I think there’s one thing we all can agree on.

---
# Games are fun
notes:
Games keep us entertained. They can tell nuanced stories. Their worlds can inspire our imaginations. Games are a unique interactive medium that, on one hand, can be played casually, but on the other hand, can leave such a deep impact on your worldview or personal development. I’d like to poll the audience with my favourite question.

---
Do you have a game that you hold close to your heart?

notes:
Do you have a game that you hold close to your heart? It may not be well-developed. It may not have  the prettiest graphics. But it did something to you, made you feel, made you laugh, made you cry. Does anyone have anything they’d like to share?

---
![[undertale.png]]

notes:
Personally, the game that I hold close to my heart is Undertale. I won't explain it entirely, but all of the music in the game has a recurring melody, called a motif. This melody has become such a core part of my teenage years that I'm able to remember all the emotions I felt when I played the game just through its music.

---

*Video games are bad for you? That's what they said about rock-n-roll."
<br>* - [Shigeru Miyamoto](https://www.imdb.com/name/nm0594427/quotes/) (*Creator of Mario*)
notes:
With this example, I wanted to show you is that games can be art. The same way indie films or music can be art. But the most important difference is that; if a game isn’t fun, why bother? You can have something silly and childish, like Fall Guys, yet something serious and tackles complicated issues, like Last of Us. Games are art. However, that being said:

---
# Games are a Job
notes:
Just like brushing your teeth, or wiping your ass after a messy shit, game developers have to make games to get through the day. I’m sure you all have had tired days at work. Days where you wish you were just at home laying in bed. Days where you stared at your computer wishing it could write for itself. Game-making is just like that.

I like making games. I like writing code that uses niche design patterns and frameworks to solve problems and create mechanics. But, making games is not the same as playing games. You run into bugs, workplace issues, and burnout. 

---
## Scenario
- Upcoming deadline for release
- Features are incomplete
- Countless bugs are in the game
- The game isn't fun
notes:
Imagine you’re in charge of a studio and you have a deadline from your publisher to release the game in a month’s time. But features aren’t ready yet. There are countless bugs in the game. The game is not fun yet. What do you do? 

---

![[shakespeare.png]]
*"To postpone, or not to postpone. That is the question."*
<br>- [William Shakespeare](https://en.wikipedia.org/wiki/To_be,_or_not_to_be), probably

notes:

Some studios choose to postpone their game, negotiate with the publisher that it needs more time in the oven. This is a pretty common practice. The latest Zelda game was postponed, Cyberpunk 2077, Starfield, and a lot of indie games get this treatment too.

But what if you don't have a choice? What if you can't postpone?

There’s only one option. 

---
![[crunch.png|500x500]]
## Crunch

notes:
You work harder. Work more hours, work more days, work through the weekends. This is crunch.

Crunch time is real and it has its consequences. It ruins the health of your employees, intensifies workplace relationships, and worst of all, it impacts the quality of your game.

This is an unfortunate reality of game development. It’s a field where people filled with passion can get exploited and traumatised by the harsh working conditions and unreasonable deadlines.

---

<split even>
![[crunch_before.png|400x400]]
![[crunch_after.png|400x400]]
</split>

notes:
But when all this is over. You're done with the game. You and your team has poured their hearts and souls into it. If, thankfully, the game does well, and the positive comments and reviews come in...

---

## It feels amazing 
notes:

To see people enjoying the things you made. Complimenting design choices, making fan art. It feels amazing to see the reaction from the community. 

That’s what made me want to make games. 

---
<grid drag="45 50" drop="2 25">
![[enter_the_arena_customise.png]]
</grid>

<grid drag="45 50" drop="-2 25" >
![[enter_the_arena_gameplay.png]]
</grid>


<grid drag="100 20" drop="0 -10">
My First Game
</grid>
notes:

My first game was really dumb. It was a top-down shooter where you fight waves of enemies until you die. You could customise your player’s stats and earn points the further you got. I showcased this game during our school’s science day, and seeing hundreds of kids have fun with what I made in front of my eyes was an indescribable feeling. Game-making is a job. But it is rewarding. 


---
## Now what?
Where do I start?

notes:
So then, now what? Maybe you knew this already. Maybe you knew that game-making is a tough industry that exploits talent and passion. Maybe you knew how fun game-making could be. Where do you start?


---

# Explore
What's available to me?

note:

Game development is not restricted to programmers. It includes one of the widest range of professional fields that finding a game-related role is actually really easy. Let’s look at our options and the skills and software required for them.


---
![[animation.png]]
notes:
Animators animate characters and environments. They might use Maya, Adobe Premier, or in-house tools. They’re good at storyboarding, key frame animation, and rigging.

---
![[art.png]]
notes:

Artists create concepts and models for everything. Characters, environments, UI, VFX. If you know how to draw or make things look pretty, you should be an artist. They have a wide range of software and skills. You don’t have to just be good at drawing. You might be a 3D sculptor, create textures, or just design interfaces. This is the most broad field of game development.

---
![[audio.png]]
notes:
Audio involves recording, producing, and mixing background music, ambience, and sound effects. This covers musicians, composers, sound effects artists, and more. You need skills in making music or sound effects and the software required for them, such as Ableton or FMOD.

---
![[design.png]]
notes:
Design is one of the most vague fields in game dev, yet it is one of the most important. The role of the designer is to create the game play experience. They design mechanics, economies, user experience, balance games, and more. Designers can have technical skills, but they don’t need to program. You can use game engines like Unity or Unreal, or you can just use Powerpoint and Excel. Designers draft ideas, polish systems, and have the hardest job; making the game fun.

---
![[production.png]]
notes:
Production involves management and handling of business and financial sides. You need to know how to manage people, how to maintain communication, and utilise team-management software to do this. This graphic notes down experience with Agile/Scrum, but personally, I find these to be ineffective. Producers should find a workflow that best fits the team for their use case to ensure everyone is working efficiently.


---
![[programming.png]]
notes:
Programmers are the people that bring the game to life. You have to know how to use game engines, testing suites, networking, and more. There are many types of programmers, people that make gameplay elements, program audio, or even specific tools to help make games.


---
![[qa.png]]
notes:
Quality Assurance ensures that the game is of good quality. They find, document, and report bugs and user feedback. They utilise team-management tools and documentation software to ensure this information is easily found. You need good communication and testing skills for QA.


---
![[brand_marketing.png]]
notes:
No one is going to play your game if you can’t sell it. People in this field advertise their games wherever possible, on social media, face-to-face events, online communities, and more. They can largely determine the success of a game.


---
![[more_careers.png]]
notes:
There are even more careers in games, such as Devops, HR, and Web Dev. A lot of developers are not working on the game directly, but are helping to bring the game into reality.


Now that you’ve chosen a role, what do you do now? This is the most important thing I can tell you today.

---
# Make Something

notes:
Just make something. Anything. Draw some character designs, plan and document your game idea, compose a game soundtrack, or even program a game. If you make something, you are one step closer. Use what you made and apply somewhere. Or make it bigger and better. Nothing is done unless you do something first.

---
<grid drag="33 50" drop="0 15">
![[one_last_drink.png]]
</grid>

<grid drag="33 50" drop="35 15" >
![[upbeet.png]]
</grid>

<grid drag="33 50" drop="70 15" >
![[sleepwalker.png]]
</grid>

<grid drag="33 50" drop="0 -10">
<font size = "6">[One Last Drink](https://alexander-kam.itch.io/one-last-drink) (*2021*)</font>
<br>
<font size ="5">*Roleplaying bartending game*</font>
</grid>

<grid drag="33 50" drop="35 -10" >
<font size = "6">[Upbeet](https://alexander-kam.itch.io/upbeet) (*2023*)</font>
<br>
<font size ="5">*Highway-style rhythm game*</font>
</grid>

<grid drag="33 50" drop="70 -10" >
<font size = "6">[Sleepwalker](https://alexander-kam.itch.io/sleepwalker) (*2023*)</font>
<br>
<font size ="5">*Sleepwalking assassin game*</font>
</grid>

notes:
Personally, I kept making games, even when I was working or studying. They're not amazing, but each of them brought me a step closer to being the developer that I am today.


---
Just make something. Really.

<font size = "5">Draw something, write something, code something. 
<br>
It doesn’t matter what it is. 
<br>
As long as it is something.</font>

---
Thank You
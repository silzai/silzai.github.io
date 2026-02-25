---
layout: post
---

This is my first post, and I want to write about the first proper project I made using rust, called `anim`, check it out at [github here](https://github.com/silzai/anim).

---

So one day when I was free and unemployed, I suddenly got the idea of creating my own anime, one that would have the target audience be my own country men (I'm from Pakistan) and this anime would inspire, motivate, and provide knowledge, all the while having the best plot for these people who have not touched Japanese entertainment before. Of course, the anime would incorporate various languages and culture of my homeland so it would be relatable and in turn, inspire. I wanted every person to quote my characters, look up to them as role models, in a society where many people have lost hope for their country and government and don't see the need to try and be nice or do something different and better for those around them, if this could have even a tiny impact, it would be worth it.

With this motivation, I quickly searched up Youtube for "japanese animation studio" to see how the professionals carry out their workflow and started watching videos on how I would plan and start animating. Now I knew animation was hard, and I had heard stories of Japanese anime studios overworking their employees, but after watching these videos, to my dismay, I learned it takes a _long_ time to do this, and I had not even accounted for the skill it took to draw even one stroke (and coloring, background, voice-acting); to put into perspective, it takes one full day for a key animator (or genga man) to make 5 second of animation[^1] (having 24 frames for 1 second).

This was heartbreaking and I lost motivation to start, I had a few story plots in mind up till then, but I never got to concretely write them and- wait. I remembered that I can code, and this idea `anim` was born. If I could code and have moving pictures on the screen, that would be great, I was not looking for very dynamic animations with personality at this point that you get from actual hand drawn animators, but just something to get the point across. 

---

I had been meaning to learn and code in Rust for a while, and this was the perfect opportunity. I had come across bevy[^2] and it seemed like a good multimedia library, even though its main use is as an ECS. Now anime is mainly done in 2d, and let's be honest, it looks the best that way, but to predict next frames in 2d seemed like the work of a non-deterministic model instead of a rule-based one, especially for complex animations, so I opted to go for a 3d character animation where it would be possible to do it the way I wanted, using animation as code.

I planned the DSL and architecture with chatgpt, and got to work.

---

The piece of software called `anim` I have envisioned is something that all hobbyists, artists, programmers and animators can use, with a central repository for snippets of `anim` called `actions` uploaded by different contributors that can be imported and used by anyone to easily make their whole animation. By the way, an `action` is sort of like a `procedure` in programming languages to make the code portable and readable. For now, all of these are future plans.

## Technicals

Ok, I'll be documenting my journey with this project below, and writing everything that I found interesting.

---
{: data-content="Architecture"}
The sequence diagram below (made using plantuml) shows how the crates communicate with each other, however, I have a `anim_timeline` crate which is not actually being used in the mvp at the time of posting this blog because a compilation step is unnecessary as of now since I have not incorporated `action`s in the mvp, so the ast is sent directly to the `anim_bevy` to be executed.

![Sequence Diagram]({{ '/assets/animation-as-code/anim-seq.svg' | relative_url }})

---
{: data-content="clap"}
One thing I want to appreciate is the simplicity of `clap` which I did not know before this project, meaning look at the snippet below:
```rust
use clap::Parser;

#[derive(Parser)]
struct Cli {
    /// The animation as code script to parse
    #[arg(short, long)]
    script: String,
    /// The 3d model file to animate
    #[arg(short, long)]
    model: String,
}
```

The thing which I basically found interesting was that I was looking at how I could put `help` in here for the cli tool, and it turns out, the doc comments are converted to the help command script. That is neat.

---
{: data-content="pest"}
To define my DSL, I used `pest` as the parser generator. Good stuff, I got a concrete syntax tree very quickly for my DSL in my project, all with automatic syntax error checking.

---
{: data-content="polymorphism"}
Coming from an OOP background, I had to see how to make a function take two different types, there was a hierarchy in my ast where I had Script -> Statements -> Movement or Action. So basically a `statement` could either be a `movement` or `action`, and I wanted a function to just take `statement`s. The way to achieve this is you make a `Statement` enum and its variants would be those types that "inherit" it, like so:
```rust
pub enum Statement {
    Movement(Movement), // have a Movement struct
    Action(Action), // have an Action struct
}
```
If there is a better to achieve this, I would love to know.

---
{: data-content="Quaternions"}
Apparently there is something called "Gimbal Lock" that occurs when normal eular angles axes are rotated (pitch/yaw/roll), and then you are not able to rotate one axis. To avoid this, these are converted to quaternions, and `anim_bevy` used these to move the mesh bones also. So good to know.

---
I hope you found this as interesting as I did, maybe I'll add more stuff to this blog post as they come along, and feel free to correct me anywhere.

---
{: data-content="References"}
[^1]: [Day in the Life of a Japanese Anime Director ](https://youtu.be/UCPZzEbhNJw?si=sBXV1GXYsUCPit2x)
[^2]: [Bevy](https://bevy.org/)

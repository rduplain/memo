## NIH is FOMO

*Code does not have wheels.         <br>
Please stop saying NIH.             <br>
Boring has value.*

---

Saying that a team is "reinventing the wheel" because of *Not Invented Here* (NIH) syndrome doesn't communicate anything other than the observer's fear of missing out (FOMO).

NIH is meaningful when an individual or team is working on the wrong problem or is blissfully unaware of relevant reference implementations. In craft, NIH loses definition. In craft, the practitioner knows *How to Solve It* and has put in the years reading more code than they write. In craft, NIH must be unlearned.

NIH assumes that the solution is out there, and you just need to find it. The tradespace becomes that of the available libraries. Open source becomes commercial, a vendor just like any proprietary product. Any problem in the solution you've picked is a Google search away, an explainer and a layer of indirection awaits your clever query.

A blanket NIH claim is not just a lack of sophistication; its pretense is a hidden drain on the productivity of teams. When reuse is incumbent, the burden of demonstrating value is on the in-house alternative. Every cycle is distracted by the lament that "this should be easy" and "I shouldn't have to write this from scratch."

A useful library passes *The 5-Minute Test*. In a few minutes, the programmer is up and running with a simple example, and is off to the API docs to adapt the tutorial. The 80/20 principle applies, as with everything, and 80% of the use cases have a doc or a blog to get 80% of the way there. For the remaining 20% of functionality, the 80/20 principle dutifully leaves 80% of the work.

The hidden drain of NIH becomes an outright unmitigated risk realized in the fear of bumping major versions in *SemVer*. Pre-1.0 tools ship to production... for *years*. Version 2.4 breaks the 2.0 API. The programmer is left fixing the build (or ignoring the error) when they'd rather dive into the code that they set aside for deep work when they finally managed to mark themselves as optional for the planning meeting. Teams embrace the effort to fix third-party code as essential or even heroic.

The perfect library doesn't exist. Assume for a moment that it does. Now, adapt it to your requirements. Instead of writing code, you are configuring the platform day in, day out. Salesforce. Amazon Web Services. Any project management tool. If it doesn't have opinions, you are left teaching it yours.

A layer of configuration is unavoidable. The choice is whether to embrace the solution developed by people who do not know you or to sit with the problem yourself. To claim NIH is to fear that you are missing out on someone smarter than you instead of developing the necessary skills.

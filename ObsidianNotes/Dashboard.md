
```button
name Open Today's Note
type command
action Daily notes: Open today's daily note
```

I need here courses for communication that I have bought from this Asian guy. Basically I need to describe every my learning block with the detailed information what to do during this block.

For example - English:
- Grammar - what to practice
- Speaking - what do master except of grammar? (pitch, tone, tempo etc.)
- Listening
- etc.
Aslo I can include there part of Communication + Story-telling practice. But I firstly need to learn them. I feel like I need some sort of Theory blocks for every new skill and Practice block for exercising Theory knowledge and mastering skills I already have.
I need to decide how many blocks should I have.
I definitely need some work block, but in general it means the same loop - Gain new theory -> Practice -> Gain more theory -> Master your skill -> Gain more theory.....etc.

I need my courses for work and maybe block for work?

**Guided routines**



#### 🧘‍♂️ Breathing practices
Long sessions:
**Inner Journey** 11 Rounds + 1 Hour of Breathing + 20 Minute Meditation
https://www.patreon.com/posts/inner-journey-11-82554250

(Winter Solstice) 11 Rounds + 57 Minutes of Breathing + 24 Minute Meditation
https://www.patreon.com/posts/76230403?collection=83822


![[Breathing practices]]

#### 🎓 Courses

```dataviewjs
// normalize topic value (handles strings and wikilinks)
const norm = v => {
  if (v && v.path) {
    const name = (dv.page(v.path)?.file?.name ?? v.path.split("/").pop() ?? "")
      .replace(/\.(md|canvas)$/i, "");
    return name.trim().toLowerCase();
  }
  return String(v).replace(/^\[\[|\]\]$/g, "").trim().toLowerCase();
};

const rows = dv.pages('"Sources"')
  .where(p => p.Topics && p.Topics.some(t => norm(t) === "course"))
  .sort(p => dv.date(p.StartDate), 'asc')
  .map(p => {
    const topicsShown = (p.Topics ?? [])
      .filter(t => norm(t) !== "course")
      .map(t => (t?.path ? dv.fileLink(t.path) : String(t)));

    const days = p.StartDate
      ? Math.floor((Date.now() - Date.parse(p.StartDate)) / 86400000)
      : "";

    const ref = p["Reference Link"] ? `[link](${p["Reference Link"]})` : "";

    return [
      p.file.link,          // Course
      p.Status ?? "",       // Status
      p.Goal ?? "",         // Goal
      topicsShown,          // Topic (without "Course")
      ref,                  // Reference
      p.StartDate ?? "",    // Start Date
      days                  // Days since starting
    ];
  });

// Single header + compact column titles

dv.table(
  [
    "<small>Course</small>",
    "<small>Status</small>",
    "<small>Goal</small>",
    "<small>Topic</small>",
    "<small>Ref</small>",
    "<small>Start</small>",
    "<small>Days</small>"
  ],
  rows
);

```


#### Protocols
I need a few protocols for:
- Wake up
- Breathing main selection - calm or energizing, and time, precise timing - 5 min, 10 min, 15 min, 20 min, 30 min, 1 hour etc. I should make 
- Learning
- Meditation - Sens AI - they has boosters and practice. Boosters 15 min, practice 15 min minimal and 30 min maximum
- COD protocol
	- [ ] Todoist inbox
	- [ ] Personal mail
	- [ ] All income from the phone
	- [ ] Work mail
- Work
- Evening exercises - like Fieldenkrais or Yin Yoga or Yoga from Siderski
- Communication
	- Storytelling
- English
- Journaling 
- Finances
- AI learning
- Robotic


Protocols have to be low/middle/high standards

	Journaling:

• What worked today and why
• What didn’t work and why
• One cognitive distortion I noticed
• One upgraded behavior for tomorrow

Write tomorrow’s 3 non negotiables only.
+bonus



#### Other
[Music for work](https://www.youtube.com/playlist?list=PLPRYC7qKI3KkgywYBHev2BAGJQ4uh_5fE)

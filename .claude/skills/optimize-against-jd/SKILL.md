---
name: optimize-against-jd
description: Build a one-page resume aimed at one job description. Reads the posting, reconsiders the entire experience bank against it, selects and frames the material that fits, and writes the targeted resume. Use once per target.
---

# Optimize against a JD

`/optimize-against-jd <target>` — `<target>` is a folder name under `jd/`.

Reads `jd/<target>/jd.md`, writes `jd/<target>/resume.html`.

## 1. Read the job description first

Before opening any of the candidate's material, read the posting and separate
what the role actually requires from what it merely lists. Most postings name a
dozen technologies and mean four of them.

Write down, for yourself, the three to five things this employer is really
buying.

Do this in this order. Reading the bank first makes everything in it look
relevant.

## 2. Read the entire bank

Every file under `raw_info/`, every category. Not a subset, and not whatever the
last resume happened to use. Every item is a fresh candidate for every posting.

Each file is frontmatter carrying hard facts plus a long `## Detail` section.
The detail is deliberately longer than any resume can hold — that surplus is
what makes targeting possible.

## 3. Read `jd/non-specific/resume.html` for style only

Take from it: section order, bullet count and length, HTML structure, CSS, the
one-page US Letter constraint.

Take nothing else. It is a format reference and has no authority over what goes
in. Its selection of items is not a starting point to be adjusted; you are
choosing again from scratch.

## 4. Select

Decide which items earn space on one page. This can differ from the non-specific
resume — a different project, a dropped item, a different order. Lead with what
this employer is buying.

## 5. Frame

For each selected item, choose which aspects to foreground. The same experience
file supports genuinely different bullets depending on which part of `## Detail`
you draw from, and that is the point. A pipeline-and-serving story and a
measurement-and-judgment story can both be true of one summer.

## 6. Honor the constants, conditionally

Each bank file's frontmatter may carry a `constant:` list — the aspects that
should land regardless of who is reading.

- **If an item is selected, both of its constants must be present in some form.**
- **Not word for word.** Rephrase freely, shift emphasis, lead with a different
  number. What has to survive is the message, not the wording.
- **If an item is not selected, its constants do not apply.** They are conditions
  on inclusion, not obligations on the document.

## 7. Write the resume

Write `jd/<target>/resume.html`, matching the non-specific file's structure and
CSS. One page, US Letter.

## Boundaries

Selecting, reordering, re-emphasizing, rewriting a bullet, and matching the
posting's vocabulary where it means the same thing — all in scope.

Changing a date, a title, an organization, or a number is not. Those are copied
from frontmatter and from the bank exactly. Do not introduce a claim the bank
does not support; if a bullet would be stronger with a fact that isn't there,
ask for the fact rather than supplying it.

`raw_info/` is read-only.

## Close by saying

- what you selected and what you cut, one line each
- where the same item is framed differently than in the non-specific resume
- what the posting asks for that the bank cannot support

Keep it short. It goes in the reply, not in a file.

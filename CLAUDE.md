# Folder structure

## `raw_info/`

The experience bank. Source material, written long and unpolished.

```
raw_info/<category>/<item>.md
```

Categories are things like `id`, `experience`, `project`. One file per item —
one job, one project — so that items can be selected between. Each file is YAML
frontmatter carrying the hard facts (title, org, dates) plus a `## Detail`
section of unstructured prose.

## `jd/`

One folder per target, named after the role.

```
jd/<target>/jd.md        the job description
jd/<target>/resume.html  the resume aimed at it
```

`jd/non-specific/` holds the untargeted resume: no posting, built from
`raw_info/` alone. Targeted versions follow its style.

Resumes render to one page, US Letter.

---
title: ClipPrompt
context: Personal project
dates: 2026-08 – 2026-09
stack: Swift, SwiftUI, AppKit, URLSession
constant:
  - It ships and is used: daily driver, ~12 outside users, 71 GitHub stars
  - Native client stack: SwiftUI over AppKit, global hotkey, streaming responses
---

## Detail

Voice notes, rambly.

### what it is and why i bothered

> macos menu bar app. copy something, hit control option c, a panel comes up
> with your recipes. a recipe is a saved instruction prompt plus a model — i have
> "to sql" that turns an english description into a postgres query, "commit" that
> takes a diff and writes a commit message, "unhedge" that strips the weaselly
> qualifiers out of my writing, which i need constantly. pick one, it fires, the
> result lands back on your clipboard so you can just paste.
>
> i was doing the copy-switch-paste-wait-copy-switch-back loop maybe thirty times
> a day. each one is twenty seconds of time and much more attention, because
> switching to a browser means i'm now in a browser and there's a tab with email
> in it. the cost isn't the twenty seconds, it's that i went to a browser.

### the part i added after a week of using it

> version one was fire and forget — result to clipboard, panel disappears. great
> maybe seventy percent of the time. the other thirty the answer was almost right,
> and what i'd do is open claude in a browser and paste the whole thing in to fix
> it. which is the exact round trip the app existed to eliminate. i built a tool
> to save me a trip and then took the trip anyway.
>
> so now the panel stays open and shows the result in a chat. it's already on
> your clipboard, but if it's wrong you type "shorter" or "use a left join" and
> keep going in the same window, and every new response overwrites the clipboard
> so it always has the latest. escape when you're happy, then paste.
>
> that's the difference between something i used for a week and something i use
> daily. and i didn't design it — i found it by using my own thing and noticing i
> was annoyed. i had a roadmap file with nine features on it and this wasn't one.

### how it was built, being honest

> i don't know swift. never written it. built this with claude code over three
> weekends and the agent wrote maybe ninety percent of the lines.
>
> "built a macos app in swift" is technically what happened and also a sentence i
> could not survive four minutes of interview questions about — someone asks
> about swiftui state management and i'm done. but "the ai wrote it" isn't true
> in a way that matters either. i made every decision about what it should do, i
> found the chat window by using it, and i decided the clipboard should update on
> every turn rather than at the end, which isn't obvious and is why it feels good.
>
> where i was genuinely useless was anything mac-specific — hotkey registration,
> accessibility permissions, menu bar lifecycle. couldn't have debugged any of
> it, just pasted errors back until it stopped complaining.
>
> i don't know how to put that on a resume. the honest version sounds weak and
> the strong version is a lie.

### the one piece of real judgment

> it reads your clipboard and sends it to an api, so if you copy a password it
> goes to anthropic. macos has a convention — 1password and keychain mark things
> org.nspasteboard.ConcealedType — so i check and refuse to send. it's a
> handshake between apps, not a guarantee, and i should say that in the readme
> and don't. plus a 40k character cap so i don't send a whole file by accident,
> and a local log nobody will read but it felt wrong not to have. two hours of
> work, and it's the difference between a toy and something i'd let someone else
> install.

### stack

> swift 6, swiftui for the panel, appkit underneath because NSStatusItem isn't a
> swiftui thing. hotkey is the HotKey package wrapping carbon's
> RegisterEventHotKey. clipboard is NSPasteboard directly, api keys in the
> keychain via KeychainAccess rather than the config file — one of the few things
> i knew to insist on before the agent suggested anything. URLSession streaming
> so the chat fills in as it goes, talking to the anthropic messages api and
> openai chat completions behind a thin protocol. chat rendering is
> swift-markdown-ui. recipes are a json file, Codable structs. releases through
> github actions and notarytool, which took a whole weekend and which i still
> can't explain — i have a working incantation in a yaml file. four tests.

### other people, even for a solo thing

> - two labmates tried the first build. one never got it running because recipes
>   are hand-edited json and he wasn't going to edit json to try his friend's
>   app. he was right, and that's why twelve people use it and not a hundred.
> - both asked for markdown rendering within a minute of each other. when two of
>   three testers hit the same thing that fast, that's not feedback, that's a bug.
> - a stranger filed a real issue — the 40k cap counted bytes not characters, so
>   emoji or cjk got rejected early. fixed next day. six messages, and i was
>   weirdly nervous writing them, first time someone i don't know depended on
>   something i made.
> - demoed five minutes at lab group. the question i couldn't answer was "what
>   stops it sending my clipboard somewhere i don't expect." i had handled it, i
>   just hadn't thought about how to say it — i said "there's a concealed type
>   check" and watched everyone's eyes go flat.

### numbers

> - ~3 weekends, maybe 25 hours
> - ~90% of lines written by the agent, my honest estimate
> - 6 recipes shipped, i use 3 daily
> - cost per invocation: to-sql ~$0.004, explain ~$0.011, unhedge ~$0.002
> - ~2.1s median round trip on haiku recipes, 4–6s on bigger models
> - 71 github stars, about a dozen identifiable users, 3 issues from strangers

### what i'd do differently

> the hand-edited json config is why twelve people use it. i know the fix and
> haven't done it because editing json doesn't bother me, which is exactly the
> wrong reason. and there's no way to see what a recipe costs before running it —
> i measured the costs in a spreadsheet, so i had the information and didn't put
> it in the product.

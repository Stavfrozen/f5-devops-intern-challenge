# Generative AI Disclosure

Using AI tools for this challenge is allowed. Be honest here — an accurate
"I used AI heavily" is worth more to us than an inaccurate "I did not".

**Did you use AI tools?** (yes / no): yes

**Which ones?** (e.g. ChatGPT, Claude, Copilot, Cursor): ChatGPT and VS Code autocomplete.

> If you answered **no**, write "N/A" in sections 1 and 2 and skip to
> section 3. That is a perfectly good answer.

---

## 1. Key prompts

List the main prompts you used. Paraphrasing is fine.

1. `Help me understand the Docker, Docker Compose, CI, and healthcheck concepts used in this challenge`
2. `Help me read the Docker build and runtime logs and understand the actual root cause before making changes`
3. `Help me understand how to reduce the nginx proxy image size and run nginx as a non-root user`

---

## 2. Where the AI got it wrong

Describe **one concrete case** where the AI's answer was wrong, outdated, or
sub-optimal. Cover: what it suggested, how you noticed it was wrong, and what
you did instead. 3–5 sentences.

> While working on the image size bonus, ChatGPT initially suggested that Docker cache might be the reason the proxy image size did not change after switching to "nginx:alpine".
> I rebuilt the image with "--no-cache", but the size was still unchanged. I then checked "docker history" and the actual contents of "proxy/Dockerfile" and found that the Dockerfile change had not been saved.
> After saving the correct Dockerfile and rebuilding, the image size dropped from about 121MB to 25.9MB.

---

## 3. Anything in your submission you could not fully explain?

We would rather know. Write "nothing" if that is the case.

> Nothing. I used AI mainly to help understand concepts, read logs, and validate troubleshooting ideas. I ran and verified each change myself and only kept changes that I understood and could explain.

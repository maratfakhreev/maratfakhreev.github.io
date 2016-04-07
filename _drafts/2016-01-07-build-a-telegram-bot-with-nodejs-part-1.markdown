---
layout: post
title: "Built a telegram bot with Node.js and ES6. Part 1: Code"
date: 2016-03-07
type: code
---

I think you have seen a lot of articles about how to write Telegram bot. But I will try to provide the most complete instruction how to make it, deploy and get some statistics about your bot. And of course the core technology will be Node.js.

## 1. Prepare environment

I recommend you to use one of my useful plugin [slush npm package](https://github.com/maratfakhreev/slush-npm-package) to prepare code environment for the Telegram bot. It is the simple npm package generator which includes ES6 provided via Babel, linting and testing tools.

```bash
npm install -g slush
npm install -g slush-npm-package
```

Create a new folder for your bot:

```bash
mkdir test-telegram-bot
```
Run the generator from within the new folder:

```bash
cd test-telegram-bot && slush npm-package
```

Follow the instructions set up your project and after this just install dependencies via `npm install`.

## 2. Prepare environment

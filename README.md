# Designer Theme Maker

Claude and Gemini project aimed to guide AI to make Designer themes. 

## What is Designer

Designer is a jailbreak utility that allows users to display "themes" on their lock screen and home screen. These can replace the stock lock screen and home screen with custom designs. It has a built in creator that gives users the ability to create their own themes.
https://junesiphone.com/designer

## Project Structure

```
themeMaker/
├── AIThemes/           # Generated theme files
├── theme_references/   # Example theme files for reference
├── training_data/      # Documentation and data references
│   ├── theme.txt      # Complete theme creation guide
│   ├── data.txt       # Available dynamic data fields
│   ├── final.txt      # Complete system reference
│   ├── advanced.txt   # Advanced features documentation
│   └── fonts.txt      # iOS font reference
└── CLAUDE.md          # AI assistant instructions
```

## Usage

git clone this repo
go to the themeMaker directory
launch claude in that directory
prompt claude to make a theme

## How it was created

The training data was compiled by analyzing the Designer app's source code to extract essential information for theme creation, including available data fields for UILabels and supported custom fonts.

From those files I created the CLAUDE.md file which is a guide for Claude Code when working with the Designer theme creation. 

claude loads the CLAUDE.md in context of the chat. Think of it as a reference manual for claude included with every chat. This gives a very condensed overview of the most important things in Designer.

## Improving

The best way to improve claude is to find what it's missing. If claude makes a theme and messes up the childOrder. What is missing is more detailed information about childOrder. I would then walk claude through fixing the theme giving it the information I needed. When the theme was fixed I ask claude to go over what it learned when fixing that issue and ask if it should be added to CLAUDE.md

## Agents

Agents can do what CLAUDE.md is doing but much better. The issue is how many tokens it will use. While using agents it took longer to make themes, but themes would come out with 0 errors. This was mainly due to the theme-validator and theme-error-fixer. But i'm not rich and I don't have enough tokens to use agents effectively. I just tune CLAUDE.md. 

If you want to use agents then adjust CLAUDE.md to use the agents.

## theme_references

I include theme_references which is a collection of Designer themes. These can be used as a starting point for creating new themes. You can say things like 
"I like the @theme/Neon847.json theme, make another theme inspired by it."
This will prompt Claude to look at Neon847 and create a new theme based on its structure. Adding other themes will give claude more context.
